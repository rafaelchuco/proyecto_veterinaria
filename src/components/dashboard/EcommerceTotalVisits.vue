<script setup>

const income_bruto_selected = ref({
  total_bruto_general: 0,
  percentage_not_payments: 0,
  percentage_payments: 0,
  total_bruto_general_before: 0,
  total_not_payments: 0,
  total_payments: 0,
  variation_percentage: 0,
});
const TotalIncomeBruto = async() => {
  try {
    const resp =  await $api('/kpi_total_bruto',{
        method: 'POST',
        body:{},
        onResponseError({response}){
            console.log(response);
            error_exists.value = response._data.error;
        }
    })
    console.log(resp);
    income_bruto_selected.value.total_bruto_general = resp.total_bruto_general;
    income_bruto_selected.value.percentage_not_payments = resp.percentage_not_payments;
    income_bruto_selected.value.percentage_payments = resp.percentage_payments;
    income_bruto_selected.value.total_bruto_general_before = resp.total_bruto_general_before;
    income_bruto_selected.value.total_not_payments = resp.total_not_payments;
    income_bruto_selected.value.total_payments = resp.total_payments;
    income_bruto_selected.value.variation_percentage = resp.variation_percentage;
  } catch (error) {
    console.log(error)
  }
}

onMounted(() => {
  TotalIncomeBruto();
})
</script>
<template>
  <VCard>
    <VCardText>
      <div class="d-flex align-center justify-space-between">
        <div class="text-body-1">
          Total Bruto
        </div>
        <div class="d-flex justify-center" v-if="income_bruto_selected.variation_percentage >= 0">
          <span class="text-success">+{{income_bruto_selected.variation_percentage}}%</span>
          <VIcon
            icon="ri-arrow-up-s-line"
            color="success"
            size="20"
          />
        </div>
        <div class="d-flex justify-center" v-if="income_bruto_selected.variation_percentage < 0">
          <span class="text-error">{{income_bruto_selected.variation_percentage}}%</span>
          <VIcon
            icon="ri-arrow-down-s-line"
            color="success"
            size="20"
          />
        </div>
      </div>
      <h4 class="text-h4">
        {{ income_bruto_selected.total_bruto_general }} PEN
      </h4>
    </VCardText>

    <VCardText>
      <VRow no-gutters>
        <VCol cols="5">
          <div class="d-flex align-center mb-2">
            <VAvatar
              rounded
              color="warning"
              variant="tonal"
              :size="24"
              class="me-2"
            >
              <VIcon
                size="16"
                icon="ri-pie-chart-2-line"
              />
            </VAvatar>

            <span>Pagado</span>
          </div>
          <h4 class="text-h4 mb-2">
            {{income_bruto_selected.percentage_payments}}%
          </h4>
          <div class="text-body-1">
            {{ income_bruto_selected.total_payments }} PEN
          </div>
        </VCol>

        <VCol cols="2">
          <div class="d-flex flex-column justify-center align-center h-100">
            <VDivider
              vertical
              class="mx-auto mb-2"
            />
            <VAvatar
              size="28"
              variant="tonal"
              class="text-body-2"
            >
              VS
            </VAvatar>
            <VDivider
              vertical
              class="mx-auto mt-2"
            />
          </div>
        </VCol>

        <VCol
          cols="5"
          class="text-end"
        >
          <div class="d-flex align-center justify-end mb-2">
            <span class="me-2">Deuda</span>

            <VAvatar
              rounded="sm"
              color="primary"
              variant="tonal"
              :size="24"
            >
              <VIcon
                size="16"
                icon="ri-hand-coin-line"
              />
            </VAvatar>
          </div>
          <h4 class="text-h4 mb-2">
            {{income_bruto_selected.percentage_not_payments}}%
          </h4>
          <div class="text-body-1">
            {{ income_bruto_selected.total_not_payments }} PEN
          </div>
        </VCol>
      </VRow>

      <div class="mt-4">
        <VProgressLinear
          :model-value="income_bruto_selected.percentage_payments"
          color="warning"
          bg-color="primary"
          bg-opacity="1"
          :rounded-bar="false"
          height="8"
          rounded
        />
      </div>
    </VCardText>
  </VCard>
</template>
