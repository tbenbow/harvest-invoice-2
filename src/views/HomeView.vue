<script setup>
import TheWelcome from '../components/TheWelcome.vue'
</script>

<template>
  <div>
    <div class="hide-for-print">
      <select @change="onMonthChange($event)">
        <option v-for="(month,index) in months" v-bind:key="index" :selected="invoiceMonthDigit === index+1" :value="index+1">{{ month }}</option>
      </select>
      <select @change="onYearChange($event)">
        <option v-for="(year,index) in years" v-bind:key="index" :selected="invoiceYearDigit === year" :value="year">{{ year }}</option>
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
          Bust Out Solutions<br />
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
