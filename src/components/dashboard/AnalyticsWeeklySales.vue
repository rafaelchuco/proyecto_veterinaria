<script setup>
import { useTheme } from 'vuetify'
import { hexToRgb } from '@layouts/utils'

const moreList = [
  {
    title: 'Refresh',
    value: 'refresh',
  },
  {
    title: 'Update',
    value: 'update',
  },
  {
    title: 'Share',
    value: 'share',
  },
]
const year_list = ref(['2023','2024','2025','2026','2027','2028']);
const year_selected = ref(new Date().getFullYear());
const vuetifyTheme = useTheme()
const total_payment_current = ref(0);
const total_payment_before = ref(0);
const total_payment_all = ref(0);

const PaymentForMonthOfYear = async() => {
  try {
    let data = {
      year: year_selected.value,
    }
    chartConfig.value = null;
    const resp =  await $api('/kpi_payments_x_month_of_year',{
        method: 'POST',
        body:data,
        onResponseError({response}){
            console.log(response);
            error_exists.value = response._data.error;
        }
    })
    console.log(resp);
    total_payment_current.value = resp.total_payment_current;
    total_payment_before.value = resp.total_payment_before;
    total_payment_all.value = Number(total_payment_current.value) + Number(total_payment_before.value);
    console.log(total_payment_all.value);
    let categories_labels = [];
    let payment_year_current = [];
    let payment_year_before = [];

    resp.payment_x_month_of_year.forEach((month_of_year) => {
      payment_year_current.push(month_of_year.total_payments);
      categories_labels.push(month_of_year.created_at_format);
    });

    resp.payment_x_month_of_year_before.forEach((month_of_year) => {
      payment_year_before.push(month_of_year.total_payments);
    });

    series.value = [
      {
        type: 'column',
        name: 'Pagos - '+year_selected.value,
        data: payment_year_current
        // [
        //   90,
        //   52,
        //   67,
        //   45,
        //   75,
        //   55,
        //   48,
        // ],
      },
      {
        type: 'column',
        name: '',
        data: [
          0,
          0,
          0,
          0,
          0,
          0,
          0,
          0,
          0,
          0,
          0,
          0,
        ],
      },
      {
        type: 'line',
        name: 'Pagos - '+(parseInt(year_selected.value) - 1),
        data: payment_year_before
        // [
        //   73,
        //   20,
        //   50,
        //   -20,
        //   58,
        //   15,
        //   31,
        // ],
      },
    ]
    const themeColors = vuetifyTheme.current.value.colors
    const variableTheme = vuetifyTheme.current.value.variables
    const disabledText = `rgba(${ hexToRgb(String(themeColors['on-background'])) },${ variableTheme['disabled-opacity'] })`

    chartConfig.value = {
        chart: {
          stacked: true,
          parentHeightOffset: 0,
          toolbar: { show: false },
        },
        tooltip: { enabled: true },
        markers: {
          size: 4,
          strokeWidth: 3,
          fillOpacity: 1,
          strokeOpacity: 1,
          colors: 'rgba(var(--v-theme-surface), 1)',
          strokeColors: themeColors.warning,
        },
        stroke: {
          curve: 'smooth',
          width: [
            0,
            0,
            3,
          ],
          colors: [themeColors.warning],
        },
        colors: [
          `rgba(${ hexToRgb(String(themeColors.primary)) }, 1)`,
          `rgba(${ hexToRgb(String(themeColors.primary)) }, 0.12)`,
        ],
        dataLabels: { enabled: false },
        states: {
          hover: { filter: { type: 'none' } },
          active: { filter: { type: 'none' } },
        },
        legend: { show: false },
        grid: {
          yaxis: { lines: { show: false } },
          padding: {
            top: -28,
            left: -6,
            right: -8,
            bottom: -5,
          },
        },
        plotOptions: {
          bar: {
            borderRadius: 8,
            columnWidth: '57%',
            endingShape: 'flat',
            borderRadiusApplication: 'start',
            borderRadiusWhenStacked: 'all',
          },
        },
        xaxis: {
          tickAmount: 10,
          axisTicks: { show: false },
          axisBorder: { show: false },
          categories: categories_labels,
          // [
          //   'Jan',
          //   'Feb',
          //   'Mar',
          //   'Apr',
          //   'May',
          //   'Jun',
          //   'Jul',
          // ],
          labels: {
            style: {
              fontSize: '13px',
              colors: disabledText,
              letterSpacing: '0.4px',
            },
          },
        },
        yaxis: {
          // max: 100,
          // min: -100,
          show: true,
        },
    }

    salesReport.value = [
      {
        title: 'Pagos - '+year_selected.value,
        amount: total_payment_current.value+" PEN",
        avatarColor: 'primary',
        avatarIcon: 'ri-funds-line',
      },
      {
        title: 'Pagos -'+(parseInt(year_selected.value) - 1),
        amount: total_payment_before.value+" PEN",
        avatarColor: 'warning',
        avatarIcon: 'ri-money-dollar-circle-line',
      },
    ]
  } catch (error) {
    console.log(error);
  }
}
onMounted(() => {
  PaymentForMonthOfYear();
})
watch(year_selected,() => {
  PaymentForMonthOfYear();
})
const chartConfig = ref(null);

const series = ref([]);

const salesReport = ref([]);
</script>

<template>
  <VCard
    title="Pagos por mes del año"
    :subtitle="'Total '+total_payment_all+' PEN'"
  >
    <template #append>
      <VRow style="width: 350px;">
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
          </VCol>
        </VRow>
      <!-- <div class="mt-n8 me-n3">
        <MoreBtn :menu-list="moreList" />
      </div> -->
    </template>

    <VCardText>
      <VRow class="mb-12">
        <VCol
          v-for="sale in salesReport"
          :key="sale.title"
          cols="6"
        >
          <div class="d-flex align-center gap-x-3">
            <VAvatar
              rounded
              variant="tonal"
              :color="sale.avatarColor"
            >
              <VIcon :icon="sale.avatarIcon" />
            </VAvatar>
            <div>
              <div class="text-body-1">
                {{ sale.title }}
              </div>
              <h6 class="text-h6">
                {{ sale.amount }}
              </h6>
            </div>
          </div>
        </VCol>
      </VRow>

      <VueApexCharts
        v-if="chartConfig"
        id="weekly-sales-chart"
        type="line"
        height="225"
        :options="chartConfig"
        :series="series"
      />
    </VCardText>
  </VCard>
</template>

<style lang="scss">
#weekly-sales-chart {
  .apexcharts-series[rel="2"] {
    transform: translateY(-8px);
  }
}
</style>
