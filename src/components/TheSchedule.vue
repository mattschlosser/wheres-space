<template>
  <div class="row m-2">
    <div class='col-12'>
    <form @submit="doSomething">
      <div class='form-row'>
        <div class='col-md-4 p-2'>
          <label for='uni' class='font-weight-bold'>University:</label>
          <select placeholder='University' class='form-control' id='uni' v-model='edit.university'>
            <option value="" selected>University of Alberta</option>
          </select>
        </div>

        <div class='col-md-4 p-2'>
          <label for='building' class='font-weight-bold'>Building: </label>
          <select placeholder='Building' class='form-control' v-model='edit.building'>
              <option value="">All</option>
              <option id='building' :key="building.id" v-for="building in buildings" :value="building.id">
                {{building.name}}
              </option>
          </select>
        </div>
        <div class='col-md-4 p-2'>
          <label for='weekday' class='font-weight-bold'>Weekday: </label>
          <select class='form-control' id='weekday' v-model='edit.weekday'>
            <option value="" selected>Select a day</option>
            <option value='U'>Sunday</option>
            <option value='M'>Monday</option>
            <option value='T'>Tuesday</option>
            <option value='W'>Wednesday</option>
            <option value='R'>Thursday</option>
            <option value='F'>Friday</option>
            <option value='S'>Saturday</option>
          </select>
        </div>
      </div>
    </form>
    </div>
    <div class='col-12 row mt-4'>
      <div class='col-md-4'><b>Legend:</b></div>
      <div class='col-md-4'>
        <span style='display: inline-block; border: 1px solid black; background: #aa4444; height: 24px; width: 48px;'></span>
        Unavailable
      </div>
      <div class='col-md-4'>
        <span style='display: inline-block; border: 1px solid black; background: #ffffff; height: 24px; width: 48px;'></span>
        Available
      </div>
    </div>
    <div class='col'>
      <TheCalendar v-if="edit.weekday" :weekday="edit.weekday" :rooms="filtered_rooms" />
      <div v-else class='alert alert-primary mt-4'>Select a weekday to view the schedule</div>
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
        this.edit.building ? e.name.match(/([A-Z]+)/)[1] == this.edit.building : 1 
      )},
    // filtered_hours() {
    //   return this.filtered_rooms.map(e => {
    //     let f = {...e};
    //     f.hours = f.hours.filter(e => this.edit.weekday ? e.dow.indexOf(this.edit.weekday) != -1 : 1);
    //     return f;
    //   })
    // }
  },
  async created() {
    let that = this;
    this.rooms = await fetch('/db.txt').then(f => f.text()).then(r => {
      let times = r.split('\n');
      console.log(times.length);
      let x = times.reduce((A,e) => {
        let days_of_week = e.match(/^([MTWRFUS]+)\s/) || [];
        let classroom = e.match(/\(([-A-Za-z0-9\s]+)\)\s*?$/);
        let times = e.matchAll(/(\d{2,}:\d{2,}):\d{2}/g);
        let building = classroom[1].match(/^([A-Za-z]+)/)[1];
        classroom = classroom[1];
        if (!A.buildings[building]) {
          A.buildings[building] = building;
        }
        if (!A.rooms[classroom]) {
          A.rooms[classroom] = {
            name: classroom,
            hours: [],
            U: [],
            M: [],
            T: [],
            W: [],
            R: [],
            F: [],
            S: []
          }
        }
        let start = times.next().value[1];
        let end = times.next().value[1];
        A.rooms[classroom].hours.push(
          {
            dow: days_of_week[1],
            start,
            end
          }
        )
        for (let i of ['U','M','T','W','R','F','S']) {
          if (days_of_week[1].includes(i)) {
            A.rooms[classroom][i].push({
              start,
              end
            })
          }
        }
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
      building: ''
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