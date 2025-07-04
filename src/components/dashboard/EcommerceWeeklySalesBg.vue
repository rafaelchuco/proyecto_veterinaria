<script setup>
import appleIphone from '@images/cards/apple-iphone-x-lg.png'
import appleWatch from '@images/cards/apple-watch-green-lg.png'
import ps4Joystick from '@images/cards/ps4-joystick-lg.png'

const websiteAnalytics = ref([]);

const ReportForService = async() => {
  try {
    const resp =  await $api('/kpi_report_for_servicies',{
        method: 'POST',
        body:{},
        onResponseError({response}){
            console.log(response);
            error_exists.value = response._data.error;
        }
    })
    console.log(resp);

    websiteAnalytics.value = [
        {
          name: 'Citas Medicas',
          slideImg: 'https://cdn-icons-png.flaticon.com/512/5389/5389424.png',
          income_bruto: resp.bruto_income_appoinments,
          variation_percentage: resp.VPAppointments,
          data: [
            {
              number: resp.n_appointments_total,
              text: 'N° programados',
            },
            {
              number: resp.n_appointments_pending,
              text: 'N° pendientes',
            },
            {
              number: resp.n_appointments_attend,
              text: 'N° atendidos',
            },
            {
              number: resp.n_appointments_cancel,
              text: 'N° cancelados',
            },
          ],
        },
        {
          name: 'Vacunas',
          slideImg: 'https://cdn-icons-png.flaticon.com/512/2867/2867078.png',
          income_bruto: resp.bruto_income_vaccinations,
          variation_percentage: resp.VPVaccinations,
          data: [
            {
              number: resp.n_vaccinations_total,
              text: 'N° programados',
            },
            {
              number: resp.n_vaccinations_pending,
              text: 'N° pendientes',
            },
            {
              number: resp.n_vaccinations_attend,
              text: 'N° atendidos',
            },
            {
              number: resp.n_vaccinations_cancel,
              text: 'N° cancelados',
            },
          ],
        },
        {
          name: 'Cirujías',
          slideImg: 'https://cdn-icons-png.flaticon.com/512/4787/4787134.png',
          income_bruto: resp.bruto_income_surgeries,
          variation_percentage: resp.VPsurgeries,
          data: [
            {
              number: resp.n_surgeries_total,
              text: 'N° programados',
            },
            {
              number: resp.n_surgeries_pending,
              text: 'N° pendientes',
            },
            {
              number: resp.n_surgeries_attend,
              text: 'N° atendidos',
            },
            {
              number: resp.n_surgeries_cancel,
              text: 'N° cancelados',
            },
          ],
        },
    ];

  } catch (error) {
    console.log(error);
  }
}

onMounted(() => {
  ReportForService();
})
</script>

<template>
  <VCard color="primary">
    <VCarousel
      v-if="websiteAnalytics.length > 0"
      :continuous="false"
      :show-arrows="false"
      hide-delimiter-background
      delimiter-icon="ri-circle-fill"
      height="auto"
      class="carousel-delimiter-top-end weekly-sale-carousel"
    >
      <VCarouselItem
        v-for="item in websiteAnalytics"
        :key="item.name"
      >
        <VCardItem>
          <VCardTitle class="text-white">
           {{ item.name }}
          </VCardTitle>
          <VCardSubtitle class="text-white">
            Total Bruto: {{ item.income_bruto }} PEN

            <div class="d-inline-block text-success font-weight-medium" v-if="item.variation_percentage >= 0">
              <div class="d-flex align-center">
                +{{item.variation_percentage}}%
                <VIcon icon="ri-arrow-up-s-line" />
              </div>
            </div>
            <div class="d-inline-block text-error font-weight-medium" v-if="item.variation_percentage < 0">
              <div class="d-flex align-center">
                {{item.variation_percentage}}%
                <VIcon icon="ri-arrow-up-s-line" />
              </div>
            </div>
          </VCardSubtitle>
        </VCardItem>

        <VCardText>
          <VRow>
            <VCol
              cols="12"
              sm="8"
              lg="7"
              order="2"
              order-sm="1"
            >
              <VRow no-gutters>
                <VCol
                  cols="12"
                  class="pb-0"
                >
                  <h6 class="text-h6 text-white mb-3">
                    {{ item.name }}
                  </h6>
                </VCol>

                <VCol
                  v-for="d in item.data"
                  :key="d.number"
                  cols="6"
                  class="text-no-wrap text-truncate text-xs d-flex align-center gap-x-3 pb-5"
                >
                  <div
                    style="background-color: rgba(var(--v-theme-primary-darken-1));block-size: 30px; inline-size: 34px;"
                    class="rounded px-2 py-1 text-body-1 font-weight-medium text-white"
                  >
                    {{ d.number }}
                  </div>
                  <div class="text-body-1 text-white text-truncate">
                    {{ d.text }}
                  </div>
                </VCol>
              </VRow>
            </VCol>

            <VCol
              cols="12"
              sm="4"
              lg="5"
              order="1"
              order-sm="2"
              class="position-relative text-center"
            >
              <img
                :src="item.slideImg"
                class="card-weekly-sales-img"
                style="filter: drop-shadow(0 4px 40px rgba(0, 0, 0, 40%));"
              >
            </VCol>
          </VRow>
        </VCardText>
      </VCarouselItem>
    </VCarousel>
  </VCard>
</template>

<style lang="scss">
.card-weekly-sales-img {
  block-size: 200px;
}

@media screen and (min-width: 600px) {
  .card-weekly-sales-img {
    position: absolute;
    margin: auto;
    inset-block: 0 40px;
    inset-inline-end: 1rem;
  }
}

.weekly-sale-carousel {
  .v-btn--icon.v-btn--density-default {
    color: rgba(var(--v-theme-primary-darken-1));
  }
}
</style>
