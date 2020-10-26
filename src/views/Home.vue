<template>
  <div class="home">
    <img style="width: 80px;" alt="Vue logo" src="../assets/logo.png" />
    <Calendar />
  </div>
</template>

<script>
import Calendar from "@/components/calendar.vue";

export default {
  name: "Home",
  components: {
    Calendar
  }
};
</script>
