<script setup>
import trophy from '@images/cards/trophy.png'

const pet_most_payment_selected = ref({
  variation_percentage: 0,
  payments_total_before: 0,
  pet_most_payments: {
    name: '',
    payment_totals: 0,
    pet_id: 0,
  }
});
const PetMostPayments = async() => {
  try {
    const resp =  await $api('/kpi_pets_most_payments',{
        method: 'POST',
        body:{},
        onResponseError({response}){
            console.log(response);
            error_exists.value = response._data.error;
        }
    })
    console.log(resp);
    pet_most_payment_selected.value.variation_percentage = resp.VPPets;
    pet_most_payment_selected.value.payments_total_before = resp.payments_total_before;
    pet_most_payment_selected.value.pet_most_payments = resp.pet_most_payments;
  } catch (error) {
    console.log(error);
  }
}

onMounted(() => {
  PetMostPayments();
})
</script>

<template>
  <VCard class="position-relative">
    <VCardText>
      <div class="mb-3">
        <h5 class="text-h5 text-wrap">
          <strong>{{pet_most_payment_selected.pet_most_payments.name}}!</strong> <span class="text-high-emphasis">🎉</span>
        </h5>
        <div class="text-subtitle-1">
         Mascota con mas gastos en nuestros servicios
        </div>
      </div>
      <h4 class="text-h4 text-primary">
        {{ pet_most_payment_selected.pet_most_payments.payment_totals }} PEN
      </h4>
      Mes anterior ({{ pet_most_payment_selected.payments_total_before }} PEN)
      <VCardSubtitle class="d-flex align-center gap-x-2 px-0">
        <div class="d-flex align-center text-success font-weight-medium" v-if="pet_most_payment_selected.variation_percentage >= 0">
          +{{pet_most_payment_selected.variation_percentage}}%
          <VIcon
            icon="ri-arrow-up-s-line"
            size="20"
          />
        </div>
        <div class="d-flex align-center text-error font-weight-medium" v-if="pet_most_payment_selected.variation_percentage < 0">
          {{pet_most_payment_selected.variation_percentage}}%
          <VIcon
            icon="ri-arrow-down-s-line"
            size="20"
          />
        </div>
      </VCardSubtitle>
    </VCardText>

    <!-- Trophy -->
    <VImg
      src="https://cdn-icons-png.flaticon.com/256/437/437493.png"
      class="trophy flip-in-rtl"
    />
  </VCard>
</template>

<style lang="scss">
.v-card .triangle-bg {
  position: absolute;
  inline-size: 10.375rem;
  inset-block-end: 0;
  inset-inline-end: 0;
}

.v-card .trophy {
  position: absolute;
  inline-size: 6.625rem;
  inset-block-end: 0;
  inset-inline-end: 1.25rem;
}
</style>
