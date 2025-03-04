<template>
  <div class="default-header expandHeader">
    <v-container class="pl-4 pb-7 pr-4 d-flex align-center pt-8">
      <v-row align="center" no-gutters>
        <v-col class="d-md-none" cols="2" md="4">
          <the-default-mobile-navigation class="ml-n2" />
        </v-col>
        <v-col cols="8" md="8" class="d-flex align-center">
          <v-img
            :class="$vuetify.breakpoint.smAndDown ? 'mx-auto' : ''"
            class="cursor--pointer mr-md-14"
            src="@/assets/images/icons/logo-mew.svg"
            max-height="36"
            max-width="130"
            @click="routeToHome"
          />

          <div class="d-none d-md-flex">
            <router-link
              class="white--text text-decoration--none menu-item"
              :to="{ name: ROUTES_HOME.HOW_IT_WORKS.NAME }"
              @click.native="trackHowItWorks"
            >
              {{ $t('header.what-is-mew') }}
            </router-link>
            <div class="mx-8">
              <mew-menu
                top-arrow
                activator-text-color="white--text"
                :list-obj="menuObj"
                @goToPage="routeTo"
              />
            </div>
            <div @click="trackBuySellLanding">
              <a
                class="white--text text-decoration--none menu-item"
                @click="openBuySell"
              >
                {{ $t('header.buy-eth') }}
              </a>
            </div>
          </div>
        </v-col>
        <v-col cols="2" md="4" class="d-flex justify-end">
          <mew-tools class="ml-auto" />
        </v-col>
      </v-row>
    </v-container>
  </div>
</template>

<script>
import { mapGetters, mapActions } from 'vuex';

import { ROUTES_HOME, ROUTES_WALLET } from '@/core/configs/configRoutes';
import buyMore from '@/core/mixins/buyMore.mixin.js';
import handlerAnalytics from '@/modules/analytics-opt-in/handlers/handlerAnalytics.mixin';
import { HEADER } from '@/modules/analytics-opt-in/handlers/configs/events.js';

export default {
  name: 'TheDefaultHeader',
  components: {
    MewTools: () => import('@/core/components/MewTools'),
    TheDefaultMobileNavigation: () => import('./TheDefaultMobileNavigation')
  },
  mixins: [buyMore, handlerAnalytics],
  data: () => ({
    menuObj: {
      name: 'Wallet actions',
      items: [
        {
          title: 'Popular actions',
          items: [
            {
              title: 'Send transaction',
              to: { name: ROUTES_WALLET.SEND_TX.NAME }
            },
            {
              title: 'Explore DApps',
              to: { name: ROUTES_WALLET.DAPPS.NAME }
            },
            {
              title: 'Swap tokens',
              to: { name: ROUTES_WALLET.SWAP.NAME }
            },
            {
              title: 'Sign message',
              to: { name: ROUTES_WALLET.SIGN_MESSAGE.NAME }
            }
          ]
        },
        {
          title: 'More actions',
          items: [
            {
              title: 'Verify message',
              to: { name: ROUTES_HOME.TOOLS.NAME, query: { tool: 'verify' } }
            },
            {
              title: 'Convert Units',
              to: { name: ROUTES_HOME.TOOLS.NAME, query: { tool: 'convert' } }
            },
            {
              title: 'Send Offline Helper',
              to: { name: ROUTES_HOME.TOOLS.NAME, query: { tool: 'offline' } }
            }
          ]
        }
      ]
    },
    ROUTES_HOME: ROUTES_HOME
  }),
  computed: {
    ...mapGetters('global', ['swapLink', 'network'])
  },
  mounted() {
    if (!this.network) return;
    this.network.type.tokens.then(res => {
      const tokenMap = new Map();
      res.forEach(item => {
        tokenMap.set(item.address.toLowerCase(), item);
      });
      this.setNetworkTokens(tokenMap);
    });
  },
  methods: {
    ...mapActions('external', ['setNetworkTokens']),
    routeToHome() {
      this.trackHeaderAmplitude(HEADER.LOGO);
      this.$router.push({ name: ROUTES_HOME.HOME.NAME });
    },
    trackHowItWorks() {
      this.trackHeaderAmplitude(HEADER.WHAT_IS_MEW);
    },
    routeTo(route) {
      this.trackHeaderAmplitude(HEADER.WALLET_ACTIONS, route);
      this.$router.push(route);
    },
    trackBuySellLanding() {
      this.trackHeaderAmplitude(HEADER.BUY_ETH);
    }
  }
};
</script>

<style lang="scss" scoped>
.menu-item:hover {
  font-weight: 500;
}
</style>
