
<script>
import { format, lastDayOfMonth } from 'date-fns'

export default {
  data () {
    return {
      months: ['Janaury','February','March','April','May','June','July','August','September','October','November','December'],
      years: ['2015','2016','2017','2018','2019','2020','2021','2022','2023','2024'],
      projects: null,
      totalHours: 0,
      rate: 0,
      invoiceDate: new Date(),
      currentYear: new Date().getFullYear(),
      currentMonth: new Date().getMonth(),
    }
  },
  computed: {
    title: function () {
      return 'Bust Out - ' + format(this.invoiceDate, 'MMMM yyyy')
    },
    invoiceNumber: function () {
      return 'bustout-' + format(this.invoiceDate, 'MMyy')
    },
    invoiceMonthDigit: function () {
      return Number(format(this.invoiceDate, 'MM'))
    },
    invoiceYearDigit: function () {
      return Number(format(this.invoiceDate, 'yyyy'))
    },
    dateBilled: function () {
      return format(new Date(), 'MM/dd/yyyy')
    }
  },
  mounted () {
    this.getTimeEntries();
  },
  methods: {
    getTimeEntries: async function () {
      const month = format(this.invoiceDate, 'MM');
      const year = format(this.invoiceDate, 'yyyy');
      const from = `${year}-${month}-01`;
      const to = format(lastDayOfMonth(this.invoiceDate), 'yyyy-MM-dd');
      const endpoint = `https://api.harvestapp.com/api/v2/time_entries?from=${from}&to=${to}`;
      const token = '1187198.pt.RUakaA084t0NFpxwdu6ZHvV98Ag0WvFHcJ0JNhAxN95qxcRotLlalt1lVEWsSprC0wOiAo4yJ_yezzBETUiKpQ;';
      const account = '96921';
      const query = `${endpoint}&access_token=${token}&account_id=${account}`
      const res = await fetch(query);
      const data = await res.json();
      this.rate = parseInt(125, 10);
      this.parseResponse(data)
      this.data = data;
    },
    parseResponse: function (data) {
      const timeEntries = data.time_entries;
      let projects = {};
      let totalHours = 0;

      for (let i = 0; i < timeEntries.length; i++) {
        let id = timeEntries[i].project.id;
        let name = timeEntries[i].project.name;
        let client = timeEntries[i].client.name;
        // let hours = Math.ceil(timeEntries[i].hours);
        // let hours = timeEntries[i].hours;
        let hours = Math.ceil(timeEntries[i].hours / 0.25) * 0.25;

        let project = {
          id: id,
          name: name,
          client: client,
        }

        if (projects[id]) {
          project.hours = hours + projects[id].hours
        } else {
          project.hours = hours
        }

        projects[id] = project;
        totalHours += hours;
      }
      this.projects = projects;
      this.totalHours = totalHours;
    },
    onMonthChange: function (event) {
      this.currentMonth = Number(event.target.value);
      this.invoiceDate = new Date(this.currentYear, this.currentMonth, 1);
      this.getTimeEntries();
    },
    onYearChange: function (event) {
      this.currentYear = Number(event.target.value);
      this.invoiceDate = new Date(this.currentYear, this.currentMonth, 1);
      this.getTimeEntries();
    }
  }

}
</script>

<template>
  <div>
    <div class="hide-for-print">
      <select @change="onMonthChange($event)" v-model="currentMonth">
        <option v-for="(month,index) in months" v-bind:key="index" :value="index">{{ month }}</option>
      </select>
      <select @change="onYearChange($event)" v-model="currentYear">
        <option v-for="(year,index) in years" v-bind:key="index" :value="year">{{ year }}</option>
      </select>
    </div>
    <div id="page">
      <div class="title">
        <h1>{{ title }}</h1>
        <p>Software Development Services</p>
      </div>
      <div class="to">
        <p>
          <b>Invoice For</b><br />
          Bust Out<br />
          514 N Third Street<br />
          Minneapolis, MN 55401
        </p>
      </div>
      <div class="from">
        <p>
          <b>From</b><br />
          Hellomoon<br />
          11403 Oak Ridge Lane W<br />
          Minnetonka, MN 55305
        </p>
      </div>
      <div class="details">
        <dl>
          <dt>Invoice #</dt>
          <dd>{{ invoiceNumber }}</dd>
          <dt>Date Billed</dt>
          <dd>{{ dateBilled }}</dd>
          <dt>Terms</dt>
          <dd>On Receipt</dd>
          <dt>Amount</dt>
          <dd>${{ (totalHours * rate).toFixed(2)}}</dd>
        </dl>
      </div>
      <div class="entries">
        <table>
          <thead>
            <tr>
              <th>Description</th>
              <th>Hours</th>
              <th>Rate</th>
              <th>Total</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="item in projects" v-bind:key="item.id">
              <td>{{ item.name }}</td>
              <td>{{ item.hours }}</td>
              <td>${{ rate.toFixed(2) }}</td>
              <td>${{ (item.hours * rate).toFixed(2)}}</td>
            </tr>
          </tbody>
        </table>
      </div>
      <div class="due">
        <h2><span>Amount Due:</span> ${{ (totalHours * rate).toFixed(2)}}</h2>
      </div>
    </div>
  </div>
</template>


<style lang="scss">
  @page {
    margin: 0;
  }
  @media print {
    .hide-for-print {
      display: none;
    }
  }

  * {
    box-sizing: border-box;
  }

  body {
    -webkit-print-color-adjust: exact !important;
    font-family: "Biome Pro", serif;
    font-size: 9px;
    background: #222222;
  }

  #page {
    position: relative;
    margin: 50px auto;
    background-color: white;
    background-size: 670px 867px;
    width: 670px;
    height: 867px;
    padding: 335px 130px 0 130px;
    transform: scale(1.3) translateY(110px);
  }

  .entries {
    table {
      width: 100%;
      text-align: right;
      border-collapse: collapse;
    }
    td, th {
      padding: 0;
      height: 26px;
    }
    th {
      font-weight: bold;
      padding-bottom: 5px;
    }
    tr th:first-child,
    tr td:first-child {
      padding-left: 9px;
      text-align: left;
    }
    tr th:last-child,
    tr td:last-child {
      padding-right: 9px;
      font-weight: bold;
    }
    tbody tr:nth-child(odd) {
      background: rgba(0,0,0,.05);
    }
  }

  h1 {
    font-size: 13px;
    margin: 0;
  }
  h2 {
    font-size: 11px;
    margin: 0;
  }
  p {
    line-height: 13px;
    margin: 0;
  }

  .title {
    position: absolute;
    top: 140px;
    left: 140px;

  }

  .to {
    position: absolute;
    top: 235px;
    right: 140px;
    left: 420px;
  }

  .from {
    position: absolute;
    top: 145px;
    right: 140px;
    left: 420px;
  }

  .details {
    position: absolute;
    top: 235px;
    left: 140px;
    right: 420px;

    dl {
      margin: 0;
      line-height: 13px;
    }
    dt {
      margin: 0;
      padding: 0;
      float: left;
      clear: both;
      font-weight: bold;
    }
    dd {
      margin: 0;
      padding: 0;
      float: right;
    }
  }

  .due {
    text-align: right;
    margin: 40px 10px 0 0;

    span {
      margin-right: 15px;
    }
  }

</style>
