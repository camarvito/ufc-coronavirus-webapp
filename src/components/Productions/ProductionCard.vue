<template>
  <div>
    <highcharts :options="chartOptions"></highcharts>
  </div>
</template>

<script>
import { Chart } from 'highcharts-vue'

export default {
    props: ['chartProps'],
    components: {
        highcharts: Chart
    },
    data() {
        return {
            chartOptions: {
                title: {
                    text: this.chartProps.title,
                    style: {
                        fontSize: 16,
                        fontWeight: 'bold'
                    }
                },
                xAxis: {
                    visible: false
                },
                yAxis: {
                    title: {
                        text: null
                    },
                    labels: {
                        enabled: false
                    }
                },
                series: this.chartProps.series,
                chart: {
                    type: 'column',
                    height: 320,
                    width: 280,
                    spacing: [30, 30, 30, 30],
                    borderRadius: 4,
                    shadow: {
                        color: '#000000',
                        offsetX: 0,
                        offsetY: 0,
                        opacity: 0.05,
                        width: 4
                    }
                },
                legend: {
                    itemMarginTop: 8
                },
                credits: {
                    enabled: false
                }
            }
        }
    }
}
</script>

<style>

</style>
