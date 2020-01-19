<template>
  <div class="row m-2">
    <div class='col-12'>
    <form @submit="doSomething">
      <div class='form-row'>
        <div class='col-md-4 p-2'>
          <label for='uni'>University</label>
          <select placeholder='University' class='form-control' id='uni' v-model='edit.university'>
            <option value="" selected>University of Alberta</option>
          </select>
        </div>
        <div class='col-md-4 p-2'>
          <label for='weekday'>Weekday: </label>
          <select class='form-control' id='weekday' v-model='edit.weekday'>
            <option value="" selected>Day of the week</option>
            <option value='M'>Monday</option>
            <option value='T'>Tuesday</option>
            <option value='W'>Wednesday</option>
            <option value='R'>Thursday</option>
            <option value='F'>Friday</option>
          </select>
        </div>
        <div class='col-md-4 p-2'>
          <label for='building'>Building: </label>
          <select placeholder='Building' class='form-control' v-model='edit.building'>
              <option value="">All</option>
              <option id='building' :key="building.id" v-for="building in buildings" :value="building.id">
                {{building.name}}
              </option>
          </select>
        </div>
      </div>
    </form>
    </div>
    <div class='col'>
      <TheCalendar v-if="edit.weekday" :rooms="filtered_hours" />
    </div>
  </div>
</template>
<script>
import TheCalendar from './TheCalendar';

export default {
  name: 'theSchedule',
  components: {
    TheCalendar
  },
  computed: {
    filtered_rooms() {
      return this.rooms.filter(e => 
        this.edit.building ? e.name.indexOf(this.edit.building) >= 0 : 1 
      )},
    filtered_hours() {
      return this.filtered_rooms.map(e => {
        let f = {...e};
        f.hours = f.hours.filter(e => this.edit.weekday ? e.dow.indexOf(this.edit.weekday) != -1 : 1);
        return f;
      })
    }
  },
  async created() {
    let that = this;
    this.rooms = await fetch('/db.txt').then(f => f.text()).then(r => {
      let times = r.split('\n');
      console.log(times.length);
      let x = times.reduce((A,e) => {
        let days_of_week = e.match(/^([MTWRF])+\s/) || [];
        let classroom = e.match(/\(([A-Za-z0-9\s]+)\)\s*?$/);
        let times = e.matchAll(/(\d{2,}:\d{2,}):\d{2}/g);
        let building = classroom[1].match(/^([A-Za-z]+)/)[1];
        classroom = classroom[1];
        if (!A.buildings[building]) {
          A.buildings[building] = building;
        }
        if (!A.rooms[classroom]) {
          A.rooms[classroom] = {
            name: classroom,
            hours: []
          }
        }
        A.rooms[classroom].hours.push(
          {
            dow: days_of_week[1],
            start: times.next().value[1],
            end: times.next().value[1],
          }
        )
        return A;
      },{rooms: {}, buildings: {}})
      let buildings = Object.keys(x.buildings).map(e => ({name: e, id: e})).sort((a,b) => a.name > b.name ? 1 : a.name < b.name ? -1 : 0);
      that.buildings = buildings;
      return Object.keys(x.rooms).map(e => x.rooms[e]).sort((a,b) => a.name > b.name ? 1 : a.name < b.name ? -1 : 0);
    });
  },
  data: () => ({
    edit: {
      weekday: '',
      university: '',

    }, 
    rooms: [],
    buildings: []
  }),
  methods: {
    doSomething() {
      return false;
    }
  }
}
</script>