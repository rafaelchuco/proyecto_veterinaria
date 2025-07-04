<script setup>
const chartColors = {
  line: {
    series1: '#FFB400',
    series2: '#666CFF',
  },
}

const headingColor = 'rgba(var(--v-theme-on-background), var(--v-high-emphasis-opacity))'
const labelColor = 'rgba(var(--v-theme-on-background), var(--v-disabled-opacity))'
const borderColor = 'rgba(var(--v-border-color), var(--v-border-opacity))'

const month_list = ref([
    {
        id: '01',
        name: 'Enero',
    },
    {
        id: '02',
        name: 'Febrero',
    },
    {
        id: '03',
        name: 'Marzo'
    },
    {
        id: '04',
        name: 'Abril',
    },
    {
        id: '05',
        name: 'Mayo',
    },
    {
        id: '06',
        name: 'Junio'
    },
    {
        id: '07',
        name: 'Julio',
    },
    {
        id: '08',
        name: 'Agosto',
    },
    {
        id: '09',
        name: 'Septiembre'
    },
    {
        id: '10',
        name: 'Octubre',
    },
    {
        id: '11',
        name: 'Noviembre',
    },
    {
        id: '12',
        name: 'Diciembre'
    }
]);
const year_list = ref(['2023','2024','2025','2026','2027','2028']);
const year_selected = ref(new Date().getFullYear());
const month_selected = ref(new Date().getMonth() + 1 <= 9 ? "0"+(new Date().getMonth() + 1) : ""+(new Date().getMonth() + 1));
const total_payments = ref(0);
const PaymentXDayOfMonth = async() => {
  try {
    let data = {
      year: year_selected.value,
      month: month_selected.value,
    }
    categories_labels.value = [];
    series.value = [];
    shipmentConfig.value = null;
    const resp =  await $api('/kpi_payments_x_day_month',{
        method: 'POST',
        body:data,
        onResponseError({response}){
            console.log(response);
            error_exists.value = response._data.error;
        }
    })
    console.log(resp);
    total_payments.value = resp.total_payments_month;
    let TOTAL_PAYMENTS = [];
    resp.payment_for_day_months.forEach(day_month => {
      TOTAL_PAYMENTS.push(day_month.total_payments);
      categories_labels.value.push(day_month.day_created_format);
    });
    series.value.push({
      name: 'Pagos',
      type: 'column',
      data: TOTAL_PAYMENTS,
    })

    shipmentConfig.value = {
      chart: {
        type: 'line',
        stacked: false,
        parentHeightOffset: 0,
        toolbar: { show: false },
        zoom: { enabled: false },
      },
      markers: {
        size: 5,
        colors: '#fff',
        strokeColors: chartColors.line.series2,
        hover: { size: 6 },
        borderRadius: 4,
      },
      stroke: {
        curve: 'smooth',
        width: [
          0,
          3,
        ],
        lineCap: 'round',
      },
      legend: {
        show: true,
        position: 'bottom',
        markers: {
          width: 8,
          height: 8,
          offsetX: -3,
        },
        height: 40,
        itemMargin: {
          horizontal: 10,
          vertical: 0,
        },
        fontSize: '15px',
        fontFamily: 'Inter',
        fontWeight: 400,
        labels: {
          colors: headingColor,
          useSeriesColors: !1,
        },
        offsetY: 10,
      },
      grid: {
        strokeDashArray: 8,
        borderColor,
      },
      colors: [
        chartColors.line.series1,
        chartColors.line.series2,
      ],
      fill: {
        opacity: [
          1,
          1,
        ],
      },
      plotOptions: {
        bar: {
          columnWidth: '30%',
          borderRadius: 4,
          borderRadiusApplication: 'end',
        },
      },
      dataLabels: { enabled: false },
      xaxis: {
        tickAmount: 10,
        categories: categories_labels,
        // [
        //   '1 Jan',
        //   '2 Jan',
        //   '3 Jan',
        //   '4 Jan',
        //   '5 Jan',
        //   '6 Jan',
        //   '7 Jan',
        //   '8 Jan',
        //   '9 Jan',
        //   '10 Jan',
        // ],
        labels: {
          style: {
            colors: labelColor,
            fontSize: '13px',
            fontFamily: 'Inter',
            fontWeight: 400,
          },
        },
        axisBorder: { show: false },
        axisTicks: { show: false },
      },
      yaxis: {
        tickAmount: 4,
        // min: 0,
        // max: 50,
        labels: {
          style: {
            colors: labelColor,
            fontSize: '13px',
            fontFamily: 'Inter',
            fontWeight: 400,
          },
          formatter(val) {
            return `${ val } PEN`
          },
        },
      },
      responsive: [
        {
          breakpoint: 1400,
          options: {
            chart: { height: 320 },
            xaxis: { labels: { style: { fontSize: '10px' } } },
            legend: {
              itemMargin: {
                vertical: 0,
                horizontal: 10,
              },
              fontSize: '13px',
              offsetY: 12,
            },
          },
        },
        {
          breakpoint: 1025,
          options: {
            chart: { height: 415 },
            plotOptions: { bar: { columnWidth: '50%' } },
          },
        },
        {
          breakpoint: 982,
          options: { plotOptions: { bar: { columnWidth: '30%' } } },
        },
        {
          breakpoint: 480,
          options: {
            chart: { height: 250 },
            legend: { offsetY: 7 },
          },
        },
      ],
    };

  } catch (error) {
    console.log(error);
  }
}
onMounted(() => {
  PaymentXDayOfMonth()
})

watch(month_selected,() => {
  PaymentXDayOfMonth();
})
watch(year_selected,() => {
  PaymentXDayOfMonth();
})
const series = ref([]);
const categories_labels = ref([]);
const shipmentConfig = ref(null);
// [
//   {
//     name: 'Shipment',
//     type: 'column',
//     data: [
//       38,
//       45,
//       33,
//       38,
//       32,
//       48,
//       45,
//       40,
//       42,
//       37,
//     ],
//   },
// ]


</script>

<template>
  <VCard>
    <VCardItem
      title="Pagos por dia del mes"
      :subtitle="'Total pagado del mes : '+total_payments+' PEN'"
    >
      <template #append>
        <VRow>
          <VCol cols="6">
            <VSelect
            v-model="year_selected"
              :items="year_list"
              label="Año"
              placeholder=""
              eager
            />
          </VCol>
          <VCol cols="6">
            <VSelect
            v-model="month_selected"
              :items="month_list"
              item-title="name"
              item-value="id"
              label="Mes"
              placeholder=""
              eager
            />
          </VCol>
        </VRow>
        <!-- <VBtnGroup
          density="compact"
          variant="outlined"
          divided
        >
          <VBtn color="primary">
            January
          </VBtn>

          <VBtn
            icon="ri-arrow-down-s-line"
            color="primary"
          />
        </VBtnGroup> -->
      </template>
    </VCardItem>

    <VCardText>
      <VueApexCharts
        v-if="shipmentConfig"
        id="shipment-statistics"
        type="line"
        height="320"
        :options="shipmentConfig"
        :series="series"
      />
    </VCardText>
  </VCard>
</template>

<style lang="scss">
@use "@core/scss/template/libs/apex-chart.scss";

.v-btn-group--divided .v-btn:not(:last-child) {
  border-inline-end-color: rgba(var(--v-theme-primary), 0.5);
}

#shipment-statistics {
  .apexcharts-legend-text {
    font-size: 15px !important;
    line-height: 22px;
  }

  .apexcharts-legend {
    .apexcharts-legend-series {
      border: 1px solid rgba(var(--v-theme-on-surface), 0.12);
      border-radius: 0.5rem;
      block-size: 75%;
      margin-inline: 8px !important;
      padding-block: 4px;
      padding-inline: 16px;
    }
  }
}
</style>
