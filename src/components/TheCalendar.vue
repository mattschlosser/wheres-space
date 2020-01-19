<template>
  <table class='table table-striped table-sm table-responsive'>
    <thead><th style='position: sticky;'>Room </th><th v-for="i in Array(22).fill(0).map((e,i) => i+16)" :key="i"> {{ hour(i) }}</th></thead>
    <tr v-for="room in rooms" :key="room.name"><th scope='row' style='height: 10px; white-space:nowrap; position: sticky;'>{{room.name}}</th><td v-for="i in Array(22).fill(0).map((e,i) => i+16)" :key="i" :class="{booked: is_booked(room, i)}" >&nbsp;</td></tr>
  </table>
</template>
<script>
export default {
  name: 'TheCalendar',
  props: {
    rooms: {
      type: Array
    }
  },
  methods: {
    hour(i) {
      return (i)%2 ? `${new Intl.NumberFormat('en-US', { minimumIntegerDigits: 2}).format(Math.floor((i)/2))}:00` : 
                     `${new Intl.NumberFormat('en-US', { minimumIntegerDigits: 2}).format(Math.floor((i-1)/2))}:30`
    },
    is_booked(room, i) {
      // console.log(room, i);
      // console.log(this.hour((i)));
      return room.hours.findIndex(e => this.hour(i) >= e.start && this.hour(i) < e.end) != -1;
    }
  }
}
</script>
<style scoped>
  .booked {
    background: #aa4444;
  }
  td:nth-child(2n) {
    border-left: 1px solid grey;
  }
</style>
