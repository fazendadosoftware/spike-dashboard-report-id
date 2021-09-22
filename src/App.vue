<template>
  <div>
    <div>baseUrl: {{baseUrl}}</div>
    <div>reportId: {{reportId}}</div>
    <div>bookmarkId: {{bookmark}}</div>
    <div>
      <button @click="openReport">Open report</button>
    </div>
  </div>
</template>

<script setup>
import { ref, unref } from 'vue'
import '@leanix/reporting' /* global lx */

const baseUrl = ref(null)
const reportId = ref(null)
const bookmark = ref(null)

const initializeReport = async () => {
  const reportSetup = await lx.init()
  await lx.ready({})
  baseUrl.value = reportSetup.settings.baseUrl
  const urlSearchParams = new URLSearchParams(window.location.search)
  reportId.value = urlSearchParams.get('reportId')
  // we can get the bookmark id here, but when the report loads as a dashboard widget
  // the query string in the window.location doesn't come with the bookmark param
  // Ex: Custom Report = https://app.leanix.net/services/pathfinder/v1/reports/files/d0a0a044-0d97-4d8b-9e2d-c93a0b17a1ba/5489e013-669d-428a-b169-c699ae75fdb1/index.html?v=7c3801e898&reportId=spikeReportId&bookmark=5c5715a1-7bd0-4bf8-a342-16654cbdfe95
  // Ex: Dashboard = https://app.leanix.net/services/pathfinder/v1/reports/files/d0a0a044-0d97-4d8b-9e2d-c93a0b17a1ba/5489e013-669d-428a-b169-c699ae75fdb1/index.html?reportId=spikeReportId
  bookmark.value = urlSearchParams.get('bookmark') ?? 'default'
  if (unref(bookmark) === 'default') bookmark.value = 'new'
  console.log('WINDOW LOCATION', window.location)
}

const openReport = () => {
  const url = `${unref(baseUrl)}/reports/${unref(reportId)}/${unref(bookmark)}`
  lx.openRouterLink(url)
  console.log('OPENING', url)
}

initializeReport()
</script>
