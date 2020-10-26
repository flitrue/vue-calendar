<template>
  <div id="screen">
    <div>
      <el-button @click="setfull">全屏</el-button>
      <el-button @click="quitFull">退出全屏</el-button>
      <el-button @click="toggle">自动切换</el-button>
    </div>
    <div style="padding-top: 10px;">
      <el-button @click="selectDate('prev-month')">上个月</el-button>
      <el-button @click="selectDate('today')">今天</el-button>
      <el-button @click="selectDate('next-month')">下个月</el-button>
    </div>

    <div style="padding: 20px;">当前日期：{{ current }}</div>
    <div class="calendar-wrapper">
      <el-calendar ref="calendarRef" v-model="value" :first-day-of-week="7" class="calendar-box">
        <template
          slot="dateCell"
          slot-scope="{date, data}">
          <span class="calendar-item" :class="data.isSelected ? 'is-selected' : ''">
            <div v-if="showPoint(data.day)" class="calendar-point"></div>
            <div class="solar">{{ date.getDate() }}</div>
            <div v-if="showlunar" class="lunar">{{ formatLunar(date) }}</div>
          </span>
        </template>
      </el-calendar>
    </div>
  </div>
</template>

<script>
import screenfull  from "screenfull";
import { solar2lunar } from "@/utils/solar2lunar";

export default {
  name: "MyCalendar",
  props: {
    showlunar: {
      type: Boolean,
      default: true
    },
    data: {
      type: Array,
      default: () => ["2020-08-22", "2020-08-11"]
    }
  },
  data() {
    return {
      value: new Date(),
      current: ""
    };
  },
  watch: {
    value: {
      handler(val) {
        this.current = this.formatDate(val).join("-");
      },
      immediate: true
    }
  },
  methods: {
    formatDate(val) {
      if (!val)  throw new Error('invalid val');
      const date = val instanceof Date ? val : new Date(val);
      const year = date.getFullYear();
      const month = date.getMonth().toString().padStart(2, 0);
      const day = date.getDate().toString().padStart(2, 0);
      return [year, month, day];
    },
    selectDate(type) {
      this.$refs.calendarRef.selectDate(type);
    },
    showPoint(day) {
      return this.data.includes(day);
    },
    formatLunar(val) {
      const arr = this.formatDate(val);
      return solar2lunar(...arr).IDayCn;
    },
    toggle() {
      screenfull.toggle();
    },
    setfull() {
      if (screenfull.isEnabled) {
        // const element = document.getElementById("screen");
        screenfull.request();
      } else {
        this.$message.warning("您的浏览器不支持全屏");
      }
    },
    quitFull() {
      screenfull.exit();
    }
  }
};
</script>

<style scoped>
.calendar-item {
  position: relative;
  width: 100%;
  height: 100%;
  display: block;
  border-radius: 50%;
  border: 1px solid transparent;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  user-select: none;
}

.calendar-point {
  position: absolute;
  top: 0;
  right: 0;
  width: 5px;
  height: 5px;
  border-radius: 50%;
  background-color: #00a2ff;
}

.solar {
  font-size: 14px;
}

.lunar {
  font-size: 12px;
  transform: scale(.8);
}

.is-selected {
  border-color: #EBEEF5;
  background-color: #F2F8FE;
}

.calendar-wrapper {
  position: relative;
  width: 100%;
  height: 0;
  padding-bottom: 100%;
}

.calendar-box {
  position: absolute;
  width: 100%;
  height: 100%;
}

.calendar-box /deep/ .el-calendar__header {
  display: none;
}

.calendar-box /deep/ .el-calendar-table {
  width: 100%;
  height: 100%;
}

.calendar-box /deep/ .el-calendar__body {
  width: 100%;
  height: 100%;
  padding: 0;
}

.calendar-box /deep/ .el-calendar-table td {
  border: none;
  vertical-align: middle;
}

.calendar-box /deep/ .el-calendar-table tr td:first-child {
  border: none;
}

.calendar-box /deep/ .el-calendar-table .el-calendar-day {
  width: 100%;
  height: 100%;
}

.calendar-box /deep/ .el-calendar-table .el-calendar-day:hover {
  background-color: transparent;
}

.calendar-box /deep/ .el-calendar-table td.is-selected {
  background-color: transparent;
}
</style>
