<template>
  <label for="form-start-date">Start date</label>
  <input type="date" id="form-start-date" v-model.lazy="startDateString">
  <label for="form-end-date">End date</label>
  <input type="date" id="form-end-date" v-model.lazy="endDateString">
  <ul v-if="formErrors">
    <li v-for="(error, index) in formErrors" :key="index">{{error}}</li>
  </ul>
  <div class="btn-container">
    <div v-if="tooManyDays" class="btn" @click="fixStartDate">Set start date {{maximumDays}} days before end</div>
    <div v-if="tooManyDays" class="btn" @click="fixEndDate">Set end date {{maximumDays}} days after start</div>
  </div>
</template>

<script>
export default {
  name: "DateRangeForm",
  data() {
    return {
      startDateString: '2022-01-01',
      endDateString: '2022-01-31',
      maximumDays: 30,
      minimumDays: 0,
    }
  },
  computed: {
    startDate() {
      return new Date(this.startDateString)
    },
    endDate() {
      return new Date(this.endDateString)
    },
    daysDifference() {
      // convert difference in milliseconds to difference in days
      return Math.floor((this.endDate - this.startDate) / (1000 * 60 * 60 * 24))
    },
    tooFewDays() {
      return this.daysDifference < this.minimumDays
    },
    tooManyDays() {
      return this.daysDifference > this.maximumDays
    },
    formErrors() {
      let errors = [];
      if (this.tooFewDays) {
        errors.push("The start date must be on or before the end date")
      }
      if (this.tooManyDays) {
        errors.push(`The start and end date must be no more than 30 days apart. You have selected dates ${this.daysDifference} days apart.`)
      }
      return errors
    },
  },
  methods: {
    addDays(date, days){
      let result = new Date(date);
      result.setDate(result.getDate() + days);
      return result;
    },
    fixStartDate() {
      let newStartDate = this.addDays(this.endDate, -this.maximumDays)
      this.startDateString = `${newStartDate.toISOString().substr(0,10)}`
    },
    fixEndDate() {
      let newEndDate = this.addDays(this.startDate, this.maximumDays)
      this.endDateString = `${newEndDate.toISOString().substr(0,10)}`
    }
  }
}
</script>

<style scoped>
.btn {
  border: 2px solid black;
  border-radius: 2px;
  padding: 0.5rem;
  margin: 0.5rem;
}
.btn-container {
  display: flex;
  flex-flow: row nowrap;
  align-items: center;
  justify-content: center;
}
</style>