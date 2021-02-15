<template>
  <AppInput v-bind="$attrs" :type="type" :value="inputValue" @change="onChange">
    <!-- Так можно передать все слоты в дочерний компонент -->
    <template v-for="slot of Object.keys($slots)" v-slot:[slot]>
      <slot :name="slot" />
    </template>
  </AppInput>
</template>

<script>
import AppInput from './AppInput';

export default {
  name: 'DateInput',

  components: { AppInput },

  props: {
    type: {
      type: String,
      default: 'date',
      validator: function(value) {
        return ['date', 'time', 'datetime-local'].includes(value);
      },
    },
    valueAsNumber: {
      type: Number,
    },
    valueAsDate: {
      type: Date,
    },
    value: {
      type: String,
    },
  },

  computed: {
    withSeconds() {
      const step = this.$attrs['step'];
      return this.type === 'time' && step && step % 60 !== 0;
    },
    isoStringFirstIndex() {
      return this.type.includes('date') ? 0 : 11;
    },
    isoStringLastIndex() {
      return this.type.includes('time') ? (this.withSeconds ? 19 : 16) : 10;
    },
    inputValue() {
      let date = this.valueAsDate;

      if (this.valueAsNumber) {
        date = new Date(this.valueAsNumber);
      }

      if (!date) {
        return this.value;
      }

      return date
        .toISOString()
        .slice(this.isoStringFirstIndex, this.isoStringLastIndex);
    },
  },

  methods: {
    onChange(event) {
      this.$emit('update:valueAsNumber', event.target.valueAsNumber);
      this.$emit('update:valueAsDate', new Date(event.target.valueAsNumber));
    },
  },
};
</script>

<style scoped></style>
