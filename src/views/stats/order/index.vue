<template>
  <cs-container>
    <page-header
      slot="header"
      ref="header"
      :loading="loading"
      @submit="handleSubmit"/>

    <page-main
      :today-data="todayData"
      :order-data="orderData"
      :source-data="sourceData"
      :update-time="updateTime"/>
  </cs-container>
</template>

<script>
import { get } from 'lodash'
import { getStatsData } from '@/api/aided/stats'

export default {
  name: 'stats-data-order',
  components: {
    PageHeader: () => import('../index/components/PageHeader'),
    PageMain: () => import('./components/PageMain')
  },
  data() {
    return {
      loading: false,
      todayData: {},
      orderData: {
        columns: ['day', 'order', 'payment', 'percent'],
        rows: []
      },
      sourceData: {
        columns: ['name', 'count'],
        rows: []
      },
      updateTime: ''
    }
  },
  mounted() {
    this.handleSubmit()
  },
  methods: {
    handleSubmit(form) {
      this.loading = true
      getStatsData({ type: 'order', ...form })
        .then(res => {
          this.todayData = get(res, 'data.today', {})
          this.orderData.rows = get(res, 'data.chart.order', [])
          this.sourceData.rows = get(res, 'data.chart.source', [])
          this.updateTime = get(res, 'data.update_time')
        })
        .finally(() => {
          this.loading = false
        })
    }
  }
}
</script>
