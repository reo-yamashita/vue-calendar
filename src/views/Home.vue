<template>
  <div class="flex flex-1 font-quick tracking-wide">
    <div class="flex-1 flex flex-col select-none">
      <div class="p-2 pl-8">
        <div class="inline-block">
          <div class="text-white text-xs sm:text-sm flex items-center bg-blue-300 rounded-lg">
            <div
              class="rounded-l-lg cursor-pointer text-lg bg-orange-200 hover:bg-orange-300 py-2 px-4 mr-4 transition-colors"
              @click="DoPrevMonth()"
            >
              <p class="text-xs">{{ prevMonth.format("M月") }}</p>
            </div>
            <div @click="Resetcurrent()" class="cursor-pointer">
              <p>{{ today.format("YYYY年 MM月 DD日") }}</p>
            </div>
            <div
              class="rounded-r-lg cursor-pointer text-lg bg-red-200 hover:bg-red-300 py-2 px-4 ml-4 transition-colors"
              @click="DoNextMonth()"
            >
              <p class="text-xs">{{ nextMonth.format("M月") }}</p>
            </div>
          </div>
        </div>
      </div>

      <div class="flex-1 flex sm:px-4 sm:pb-4">
        <div class="flex-1 flex flex-col border-t border-l">
          <div class="flex bg-blue-100">
            <div v-for="head in heads" :key="head" class="flex-1">
              <div class="border-r flex justify-center items-center">
                <p class="select-none leading-none text-xs tracking-wider py-1 text-blue-900">
                  {{ head }}
                </p>
              </div>
            </div>
          </div>

          <div v-for="item in dates" :key="item.name" class="flex flex-1">
            <div
              v-for="dater in item.days"
              :key="dater.date_num"
              class="py-1 border-b border-r flex-grow"
              :class="isSame(selectedMonth, dater.date, 'month') ? 'bg-white' : 'bg-warmGray-100'"
            >
              <div class="flex justify-center items-center mb-1" @click="selectDate(dater.date)">
                <div
                  class="date_box sm:box rounded-full cursor-pointer flex justify-center items-center hover:bg-blue-200 hover:text-white"
                  :class="{
                    selected_Color: dater.date == selectedDate,
                    today_Color: dater.date == today.format('YYYY-MM-DD'),
                  }"
                >
                  <p class="select-none leading-none text-xs sm:text-sm tracking-wide">
                    {{ dater.date_num }}
                  </p>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import moment from "moment";

export default {
  name: "Dashboard",
  data() {
    return {
      heads: ["Sun", "Mon", "Tue", "Wed", "Thu", "Fri", "Sat"],
      dates: [
        {
          name: "week1",
          days: [],
        },
        {
          name: "week2",
          days: [],
        },
        {
          name: "week3",
          days: [],
        },
        {
          name: "week4",
          days: [],
        },
        {
          name: "week5",
          days: [],
        },
        {
          name: "week6",
          days: [],
        },
      ],
      create_events: null,
      selectedMonth: null,
      selectedDate: null,
      today: null,
      currentLabel: null,
    };
  },
  computed: {
    nextMonth() {
      return moment(this.selectedMonth).add(1, "months");
    },
    prevMonth() {
      return moment(this.selectedMonth).subtract(1, "months");
    },
  },
  methods: {
    DoNextMonth() {
      //go to Next Month
      this.selectedMonth = this.nextMonth.format("YYYY-MM-DD");
    },
    DoPrevMonth() {
      //go to Previous Month
      this.selectedMonth = this.prevMonth.format("YYYY-MM-DD");
    },
    selectDate(date) {
      //select Date
      this.selectedDate = date;
    },
    Resetcurrent() {
      //select Date
      this.selectedMonth = moment().format("YYYY-MM-DD");
    },
    isSame(exm, compared, base) {
      //exm equals to compared ? , base is week or day or month
      return moment(exm).isSame(compared, base);
    },
  },
  watch: {
    selectedMonth: function() {
      let SelectMonth = moment(this.selectedMonth);
      this.currentLabel = SelectMonth.format("M");

      let start_num = SelectMonth.startOf("month").day(); //from 0 (Sunday) to 6 (Saturday)
      let startDayofMonth = SelectMonth.startOf("month")
        .subtract(start_num, "days")
        .format("YYYY-MM-DD");

      this.dates.map((week_item, index) => {
        //separate next or prev month and current month and colored
        for (let i = 0; i < 7; i++) {
          week_item.days.splice(i, 1, {
            date: moment(startDayofMonth)
              .add(i + 7 * index, "days")
              .format("YYYY-MM-DD"),
            date_num: moment(startDayofMonth)
              .add(i + 7 * index, "days")
              .format("D"),
          });
        }
      });
    },
  },
  created() {
    //create today
    this.selectedMonth = moment().format("YYYY-MM-DD"); // formatted
    this.today = moment();
  },
};
</script>

<style lang="scss">
.box {
  height: 40px;
  width: 40px;
}

.date_box {
  height: 30px;
  width: 30px;
}

.selected_Color {
  color: white;
  background-color: #fcb9ad !important;
}

.today_Color {
  color: white;
  background-color: #a7c1f8;
}
</style>
