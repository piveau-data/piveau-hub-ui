<template>
  <span
    class="d-inline-block"
    data-toggle="tooltip"
    data-placement="top"
    :title="
      isIncorrectDate()
        ? 'This date is incorrect or incomplete, please contact the data provider.'
        : null
    "
  >
    <span :class="{ 'date-incorrect': isIncorrectDate() }" class="mr-1">{{ filterDateFormatEU }}</span>
    <font-awesome-icon
      v-if="isIncorrectDate()"
      class="date-incorrect-exclamation-triangle"
      :icon="['fas', 'exclamation-triangle']"
    />
  </span>
</template>

<script>
import moment from 'moment';
import dateFilters from '../filters/dateFilters';

export default {
  props: ['date'],
  computed: {
    filterDateFormatUS() {
      return dateFilters.formatUS(this.date);
    },
    filterDateFormatEU() {
      return dateFilters.formatEU(this.date);
    },
  },
  methods: {
    // Checks date plausibility. Returns true, if the date is not plausible.
    isIncorrectDate() {
      // Falsy dates are considered as intentionally blank and are correct.
      if (!this.date) return false;
      const m = moment(String(this.date));
      if (!m.isValid()) {
        this.$root.$emit('date-incorrect');
        return true;
      }

      // Dates in the future are incorrect.
      if (moment().diff(m) < 0) {
        this.$root.$emit('date-incorrect');
        return true;
      }

      return false;
    },
  },
  mounted() {
    this.$nextTick(() => {
      // To force event emit
      this.isIncorrectDate();
    });
  },
};
</script>

<style lang="scss" scoped>
.date-incorrect {
  color: #a1a1a1;
}

.date-incorrect-exclamation-triangle {
  color: #ffaa00;
}
</style>
