<template>
  <ion-page class="ion-page">
    <ion-header class="beep-breaches-header">
      <ion-toolbar class="beep-breaches-toolbar">
        <ion-buttons slot="start">
          <ion-back-button
            class="beep-color-light"
            default-href="/acc"
          />
        </ion-buttons>
        <ion-title v-html="account" />
      </ion-toolbar>
      <ion-searchbar
        class="beep-searchbar beep-safe-padding"
        type="email"
        :value="filter"
        @ionChange="filterChanged"
      />
    </ion-header>
    <ion-content padding>
      <div class="counter">{{ breaches.length }} results found</div>
      <ion-list class="beep-safe-padding">
        <BreachItem
          v-for="(breach, index) in breaches"
          :key="index"
          :breach="breach"
          :show-full-details="true"
        >
          <ion-icon
            slot="end"
            class="beep-breach-item-icon"
            name="arrow-forward"
          />
        </BreachItem>
      </ion-list>
    </ion-content>
  </ion-page>
</template>

<script>
import toggleStatusbarColor from '@/mixins/toggleStatusbarColor'

export default {
  name: 'PageBreaches',
  components: {
    BreachItem: () => import('@/components/BreachItem.vue'),
  },
  mixins: [toggleStatusbarColor],
  data() {
    return {
      filter: this.$breachesService.filter,
      account: this.$breachesService.account,
      newStatusbarColor: this.$helpers.env('UNSAFE_STATUSBAR_COLOR'),
    }
  },
  computed: {
    breaches() {
      if (!this.filter.length) {
        return this.$breachesService.breaches
      }

      return this.$breachesService.breaches.filter(breach => {
        return breach.Title.toLowerCase().startsWith(this.filter.toLowerCase())
      })
    },
  },
  methods: {
    filterChanged(event) {
      this.filter = event.target.value
      this.$breachesService.filter = this.filter
    },
  },
}
</script>

<style scoped>
.beep-breaches-header {
  box-shadow: 0 1px 4px 0 rgba(255, 92, 93, 0.5), 0 1px 4px 0 rgba(0, 0, 0, 0.3);
}

.beep-breaches-toolbar {
  --background: var(--beep-danger);
  color: var(--beep-light);
  text-align: center;
}

.beep-searchbar {
  --background: rgba(0, 0, 0, 0.15);
  --color: var(--beep-light);
  --icon-color: var(--beep-light);
  --clear-button-color: rgba(255, 255, 255, 0.7);
  --placeholder-color: rgba(255, 255, 255, 0.7);
  background-color: var(--beep-primary);
}

.counter {
  width: 100%;
  background-color: var(--beep-primary);
  color: var(--beep-light);
  font-size: 14px;
  font-weight: 900;
  letter-spacing: -0.34px;
  line-height: 17px;
  padding: 7px 10px;
}

.beep-breach-item-icon {
  color: #c7c7cc;
}

/* iPhone support
 * first selector: X/Xs/X-MAX
 * second selector: XR
 */
@media only screen and (min-device-width: 375px) and (max-device-width: 812px) and (-webkit-min-device-pixel-ratio: 3) and (orientation: landscape),
  only screen and (min-device-width: 414px) and (max-device-width: 767px) and (-webkit-min-device-pixel-ratio: 2) and (orientation: landscape) {
  .beep-safe-padding {
    padding-right: env(safe-area-inset-right);
    padding-left: env(safe-area-inset-left);
  }
  .counter {
    padding: 7px env(safe-area-inset-right) 7px env(safe-area-inset-left);
  }
}
</style>
