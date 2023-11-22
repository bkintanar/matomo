<!--
  Matomo - free/libre analytics platform

  @link https://matomo.org
  @license http://www.gnu.org/licenses/gpl-3.0.html GPL v3 or later
-->

<template>
  <div class="timezone-component">
    <ul>
      <li>{{labelLocalTimezone}}: {{valueLocalTimezone}}</li>
      <li>{{labelSiteTimezone}}: {{valueSiteTimezone}}</li>
    </ul>
  </div>
</template>

<style lang="less" scoped>
</style>

<script lang="ts">
import { defineComponent } from 'vue';

interface ExampleComponentState {
  valueLocalTimezone: string;
  valueSiteTimezone: string;
  refreshIntervalLocalTimezone: string;
}

export default defineComponent({
  props: {
    labelLocalTimezone: String,
    labelSiteTimezone: String,
    valueSiteTimezoneFromBackend: String,
  },
  data(): ExampleComponentState {
    return {
      valueLocalTimezone: '',
      valueSiteTimezone: '',
      refreshIntervalLocalTimezone: '',
    };
  },
  mounted() {
    this.getLocalTimezone();
    this.getSiteTimezone(true);

    this.refreshIntervalLocalTimezone = setInterval(() => {
      this.getLocalTimezone();
      this.getSiteTimezone();
    }, 60000);
  },
  beforeUnmount() {
    // Clear the interval when the component is destroyed to avoid memory leaks
    clearInterval(this.refreshIntervalLocalTimezone);
  },
  methods: {
    getLocalTimezone() {
      const date = new Date();

      this.valueLocalTimezone = this.formatDate(date);
    },

    getSiteTimezone(init = false) {
      // if just initializing, set site timezone to value from backend
      if (init) {
        this.valueSiteTimezone = this.valueSiteTimezoneFromBackend;

        return;
      }

      const dateString = `${this.valueSiteTimezone.replaceAll('/', '-').replace(' ', 'T')}:00`;
      const parsedDate = new Date(dateString);

      parsedDate.setMinutes(parsedDate.getMinutes() + 1);

      this.valueSiteTimezone = this.formatDate(parsedDate);
    },

    formatDate(date) {
      return `${date.getFullYear()}/${(`00${date.getMonth() + 1}`).slice(-2)}/${(`00${date.getDate()}`).slice(-2)} ${
        (`00${date.getHours()}`).slice(-2)}:${(`00${date.getMinutes()}`).slice(-2)}`;
    },
  },
});
</script>
