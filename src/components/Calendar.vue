<template>
  <div id="calendar" class="calendar">
    <section class="calendar__header">
      <div>
        <button @click="prevMonth">&#60;</button>
      </div>
      <div>
        {{ currentMonthText }}
        {{ currentYearText }}
      </div>
      <div>
        <button @click="nextMonth">&#62;</button>
      </div>
    </section>
    <section class="calendar__body">
      <table>
        <thead>
          <tr>
            <th v-for="(day, idx) in days" :key="idx">
              {{ day }}
            </th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(week, index) in mappedDays" :key="index">
            <td
              v-for="(day, idx) in week"
              @click="selectDate(day.date)"
              :key="idx"
              :class="{
                'current-month': day.currentMonth,
                'current-date': isCurrentDate(day.date),
              }"
            >
              {{ day.day }}
            </td>
          </tr>
        </tbody>
      </table>
    </section>
  </div>
</template>
<script>
import moment from "moment";
const format = "YYYY-MM-DD";

export default {
  props: {
    initialDate: {
      type: String,
      required: false,
      default: null,
    },
    days: {
      type: Array,
      required: false,
      default: function () {
        return moment.weekdaysShort();
      },
    },
    months: {
      type: Array,
      required: false,
      default: function () {
        return moment.months();
      },
    },
  },
  mounted() {
    if (
      this.initialDate &&
      moment(this.initialDate, format, true).isValid() === false
    ) {
      throw new Error("Invalid date format");
    }
    if (this.initialDate) {
      this.currentDate = moment(this.initialDate, format).format(format);
    }
    this.collectMappedDays();
  },
  beforeCreate: function () {},
  created: function () {},
  data() {
    return {
      mappedDays: [],
      currentDate: moment().format(format),
    };
  },
  methods: {
    collectMappedDays() {
      this.mappedDays = [];
      const firstDateInCurrentMonth = moment(this.currentDate).date(1).day();
      const currentMonthDays = moment(this.currentDate).daysInMonth();
      const lastDateInCurrentMonth = moment(this.currentDate)
        .date(moment(this.currentDate).daysInMonth())
        .day();
      const prevMonthDays = moment(this.currentDate)
        .subtract(1, "M")
        .daysInMonth();

      this.mappedDays.push([]);
      for (let i = firstDateInCurrentMonth - 1; i >= 0; i--) {
        const date = moment(this.currentDate)
          .date(1)
          .subtract(i + 1, "d")
          .format(format);
        this.mappedDays[0].push({
          day: prevMonthDays - i,
          currentMonth: false,
          date,
        });
      }
      let fillArrayIndex = 0;
      for (let i = 1; i <= currentMonthDays; i++) {
        const date = moment(this.currentDate)
          .date(1)
          .add(i - 1, "d")
          .format(format);
        if (this.mappedDays[fillArrayIndex].length % 7 === 0) {
          this.mappedDays.push([]);
          fillArrayIndex++;
        }
        this.mappedDays[fillArrayIndex].push({
          day: i,
          currentMonth: true,
          date,
        });
      }
      this.mappedDays.push([]);
      for (let i = 1; i <= 6 - lastDateInCurrentMonth; i++) {
        const date = moment(this.currentDate)
          .add(1, "M")
          .date(1)
          .add(i - 1, "d")
          .format(format);
        this.mappedDays[fillArrayIndex].push({
          day: i,
          currentMonth: false,
          date,
        });
      }
    },
    prevMonth() {
      this.currentDate = moment(this.currentDate)
        .subtract(1, "M")
        .format(format);
      this.collectMappedDays();
    },
    nextMonth() {
      this.currentDate = moment(this.currentDate).add(1, "M").format(format);
      this.collectMappedDays();
    },
    selectDate(date) {
      const momentCurrentDate = moment(this.currentDate);
      const momentSelectedDate = moment(date);
      this.currentDate = date;
      if (
        momentCurrentDate.format("MM") !== momentSelectedDate.format("MM") ||
        momentCurrentDate.format("YYYY") !== momentSelectedDate.format("YYYY")
      ) {
        this.collectMappedDays();
      }
      this.$emit("date-selected", date);
    },
    isCurrentDate: function (checkingDate) {
      return (
        moment(checkingDate).format(format) ===
        moment(this.currentDate).format(format)
      );
    },
  },
  computed: {
    currentMonthText: function () {
      return this.months
        ? this.months[moment(this.currentDate).format("M")]
        : "";
    },
    currentYearText: function () {
      return moment(this.currentDate).format("YYYY") || "";
    },
  },
};
</script>
<style scoped>
#calendar {
  width: 500px;
  height: 300px;
  background-color: #b4b3b3;
}
.calendar__header {
  display: flex;
  justify-content: space-between;
  padding: 0.25rem 1rem;
}
table {
  padding: 1.25rem 0;
  width: 100%;
  height: 100%;
}
table td {
  opacity: 0.7;
  color: gray;
}
table td:hover {
  cursor: pointer;
}
table td.current-month {
  color: blue;
  opacity: 1;
}
table td.current-date {
  box-sizing: border-box;
  outline: 1px blue solid;
}
</style>
