<template>
  <div class="form-section form-section_inner">
    <button class="remove-button" type="button" @click="$emit('remove')">
      <img alt="trash" src="../assets/icons/icon-trash.svg" />
    </button>

    <form-group>
      <dropdown-button
        v-model="localAgendaItem.type"
        :options="$options.agendaItemTypes"
        title="Тип"
      />
    </form-group>

    <div class="form__row">
      <div class="form__col">
        <form-group :label="'Начало'">
          <app-input
            :value="localAgendaItem.startsAt"
            placeholder="00:00"
            type="time"
            @input="updateStartTime($event)"
          />
        </form-group>
      </div>
      <div class="form__col">
        <form-group :label="'Окончание'">
          <app-input
            v-model="localAgendaItem.endsAt"
            placeholder="00:00"
            type="time"
          />
        </form-group>
      </div>
    </div>

    <form-group v-for="field in fields" :label="field.title">
      <component
        :is="field.component"
        :key="field.field"
        v-bind="field.props"
        :[field.model.prop]="localAgendaItem[field.field]"
        @[field.model.event]="localAgendaItem[field.field] = $event"
      ></component>
    </form-group>
  </div>
</template>

<script>
import AppInput from './AppInput';
import DropdownButton from './DropdownButton';
import {
  getAgendaItemsFieldSpecifications,
  getAgendaItemTypes,
} from '../meetup-service';
import FormGroup from '../../../03-vue-cli/02-FormGroup/components/FormGroup';

function getDateByTime(time) {
  const date = new Date();
  const times = time.split(':');
  date.setHours(times[0], times[1], 0, 0);
  return date;
}

function doubleTimeFormat(time) {
  return time < 10 ? '0' + time : time;
}

function getFormattedTime(hours, minutes) {
  return `${doubleTimeFormat(hours)}:${doubleTimeFormat(minutes)}`;
}

export default {
  name: 'MeetupAgendaItemForm',

  components: { FormGroup, AppInput, DropdownButton },

  agendaItemTypes: getAgendaItemTypes(),
  fieldSpecifications: getAgendaItemsFieldSpecifications(),

  props: {
    agendaItem: {
      type: Object,
      required: true,
    },
  },

  data() {
    return {
      localAgendaItem: { ...this.agendaItem },
    };
  },

  watch: {
    localAgendaItem: {
      deep: true,
      handler() {
        this.$emit('update:agendaItem', { ...this.localAgendaItem });
      },
    },
  },

  methods: {
    updateStartTime(newStartTime) {
      const time_start_old = getDateByTime(this.localAgendaItem.startsAt);
      const time_end_old = getDateByTime(this.localAgendaItem.endsAt);

      const length = time_end_old - time_start_old;
      let time_end_new = getDateByTime(newStartTime);
      time_end_new.setMilliseconds(time_end_new.getMilliseconds() + length);
      let hours = time_end_new.getHours();
      let minutes = time_end_new.getMinutes();

      this.localAgendaItem.endsAt = getFormattedTime(hours, minutes);
      this.localAgendaItem.startsAt = newStartTime;
    },
  },

  computed: {
    fields() {
      return this.$options.fieldSpecifications[this.localAgendaItem.type];
    },
  },
};
</script>

<style></style>
