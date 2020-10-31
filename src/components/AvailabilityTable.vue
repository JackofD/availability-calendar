<template>
  <div class="wrapper">
    <div class="job-length-slider">
      <h3>Step 1: Use the slider to estimate</h3>
      <label>{{jobLength}} HR/s</label><br />
      <input type=range v-model="jobLength" @change="updateGridState" min=1 max=5 />
    </div>
    <div class="calendar__wrapper">
      <h3>Step 2: Select and arrival time</h3>
      <div class="calendar">
        <div class="calendar__day calendar__day--times">
          <div class="calendar__day-header"></div>
          <div class="calendar__day-cell">09:00 - 10:00</div>
          <div class="calendar__day-cell">10:00 - 11:00</div>
          <div class="calendar__day-cell">11:00 - 12:00</div>
          <div class="calendar__day-cell">12:00 - 13:00</div>
          <div class="calendar__day-cell">13:00 - 14:00</div>
          <div class="calendar__day-cell">14:00 - 15:00</div>
          <div class="calendar__day-cell">15:00 - 16:00</div>
          <div class="calendar__day-cell">16:00 - 17:00</div>
          <div class="calendar__day-cell">17:00 - 18:00</div>
        </div>
        <div class="calendar__day" v-for="day in gridItems" :key="day.Date">
          <div class="calendar__day-header">{{day.date}}</div>
          <div class="calendar__day-cell" :class="isSelected(day.date, slot.time) ? 'Selected' : slot.status" v-for="slot in day.slots" :key="`day-${slot.time}`" @click="selectSlot(slot, day.date)">
            {{isSelected(day.date, slot.time) ? "Selected" : slot.status}}
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import moment from 'moment';
import CalendarData from "../data/data.json";

export default {
  name: 'AvailabilityTable',
  props: {

  },
  data() {
    return {
      currentDateTime: "2016-05-18T11:27:00",
      gridItems: [],
      slotsInDay: [9,10,11,12,13,14,15,16,17],
      jobLength: 1,
      selectedSlots: [],
    }
  },
  methods: {
    checkSlotAvailability (time, jobLength, date, availability) {

      // check if the slot selected is FULL already
      if(availability.indexOf(time) === -1) {
        //console.log('Full');
        return "Full";
      }

      let requiredTime = [];
      if(time > 9) {
        requiredTime.push(time - 1);
      }

      for(let i = time; i <= time + parseInt(jobLength); i++) {
        requiredTime.push(i);
      }

      let isAvailable = requiredTime.every(slot => availability.indexOf(slot) !== -1);

      //console.log(date, requiredTime)
      // return the slot that is AVAILABLE
      return isAvailable ? "Available" : "Unavailable";
    },
    updateGridState () {
      this.selectedSlots = [];
      this.gridItems.forEach(day => {
        day.slots.forEach(slot => {
          if(slot.status !== "Full") {
            let status = this.checkSlotAvailability(slot.time, this.jobLength, day.date, day.availability);
            if(status !== slot.status) {
              this.$set(slot, "status", status);
            }
          }
        });
      });
    },
    selectSlot (slot, date) {
      if(slot.status === "Available") {
        // Run the code
        // calculate and store the range of required slots
        this.selectedSlots = [];

        for(let i = slot.time; i <= slot.time + parseInt(this.jobLength) - 1; i++) {
          this.selectedSlots.push(`${date}-${i}`);
        }
      }
    },
    isSelected(date, time) {
      return this.selectedSlots.includes(`${date}-${time}`);
    }
  },
  mounted() {
    // Build the data structure for the grid
    this.gridItems = CalendarData.map(day => {
      if(day && day.Date && day.HoursAvailable) {
        return {
          date: day.Date,
          availability: day.HoursAvailable,
          slots: this.slotsInDay.map(time => {
            return {
              time: time,
              status: this.checkSlotAvailability(time, this.jobLength, day.Date, day.HoursAvailable)}
          }),
        }
      }
      
    })
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
  @import url('https://fonts.googleapis.com/css2?family=Roboto:ital,wght@0,300;0,400;0,500;0,700;1,300;1,400;1,500&display=swap');
  .html {
    font-size: 16px;
    font-family: 'Roboto', sans-serif;
  }

  * {
    box-sizing: border-box;
    font-family: 'Roboto', sans-serif;
  }

  .wrapper {
    font-size: 12px;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: flex-start;
    margin: 0 auto;
    padding: 0 0.5rem;
    overflow: hidden;
    width: 95vw;
  }

  .job-length-slider {
    margin-bottom: 1rem;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: flex-start;
  }

  .calendar__wrapper {
    overflow-x: auto;
    width: 100%;
  }
  .calendar__wrapper h3 {
    text-align: left;
  }

  .calendar {
    display: flex;
    flex-direction: row;
    justify-content: flex-start;
  }
  .calendar__day:first-child {
    border-left: 2px solid #dedede;
  }
  .calendar__day--times .calendar__day-cell{
    font-style: normal;
    font-weight: 600;
    min-width: 110px;
  }
  .calendar__day-cell{
    border-bottom: 2px solid #dedede;
    border-right: 2px solid #dedede;
    font-style: italic;
  }
  .calendar__day-header {
    background-color: #333;
    color: white;
    min-height: 51px;
    font-weight: 600;
  }

  .calendar__day-cell, .calendar__day-header {
    padding: 0.5rem 0.6rem;
  }

  .Full {
    background-color: red;
    color: white;
    cursor:not-allowed;
  }
  .Unavailable {
    background-color: #ededed;
    color: #999;
    cursor:not-allowed;
  }
  .Available {
    background-color: white;
    cursor: pointer;
  }
  .Selected {
    background-color: green;
    color: white;
    cursor: pointer;
    font-style: normal;
    font-weight: 600;
  }

  @media (min-width: 768px) {
    .wrapper {
      padding: 0 1rem;
      font-size: 1rem;
    }

    .calendar__day--times {
      min-width: 140px;
    }

    .calendar__day-cell, .calendar__day-header {
      padding: 1rem 1.2rem;
    }
    .calendar__day-header {
      min-width: 130px;
    }
  }
</style>
