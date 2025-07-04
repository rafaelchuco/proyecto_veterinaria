<script setup>
    import { isPermission } from '@/utils/constants';
import illustration1 from '@images/cards/illustration-1.png'
    import illustration2 from '@images/cards/illustration-2.png'
    const statisticsWithImages = ref([]);
    definePage({
        meta: {
            permisssion: 'all'
        },
    })
    const veterinarieNetIncome = async() => {
        try {
            const resp =  await $api('/kpi_veterinarie_net_income',{
                method: 'POST',
                body:{},
                onResponseError({response}){
                    console.log(response);
                    error_exists.value = response._data.error;
                }
            })
            console.log(resp);
            statisticsWithImages.value.push({
                title: 'V. + Ingreso neto',
                subtitle: resp.veterinarie_most_net_income.full_name,
                stats: resp.veterinarie_most_net_income.net_income_total+" PEN",
                change: resp.variation_percentage,
                image: resp.veterinarie_most_net_income.gender == 'M' ? illustration1 : illustration2,
                imgWidth: 99,
                color: 'primary',
            })
            veterinarieServicesAsigned();
            // [
                //     {
                //         title: 'V. + ingreso neto',
                //         subtitle: 'Year of 2021',
                //         stats: '8.14k',
                //         change: 15.6,
                //         image: illustration1,
                //         imgWidth: 99,
                //         color: 'primary',
                //     },
                //     {
                //         title: 'V. + cantidad de servicio asignado',
                //         subtitle: 'Last Month',
                //         stats: '12.2k',
                //         change: -25.5,
                //         image: illustration2,
                //         imgWidth: 85,
                //         color: 'success',
                //     },
            // ]
        } catch (error) {
            console.log(error);
        }
    }
    const veterinarieServicesAsigned = async() => {
        try {
            const resp =  await $api('/kpi_veterinarie_most_asigned',{
                method: 'POST',
                body:{},
                onResponseError({response}){
                    console.log(response);
                    error_exists.value = response._data.error;
                }
            })
            console.log(resp);
            statisticsWithImages.value.push({
                title: 'V. + Servicios asignado',
                subtitle: resp.veterinarie_most_asigned.full_name,
                stats: resp.veterinarie_most_asigned.n_asigned+"",
                change: resp.variation_percentage,
                image: resp.veterinarie_most_asigned.gender == 'M' ? illustration1 : illustration2,
                imgWidth: 99,
                color: 'primary',
            })
        } catch (error) {
            console.log(error);
        }
    }
    onMounted(() => {
        veterinarieNetIncome();
    })
</script>
<template>
    <div>
        <VRow class="match-height" v-if="isPermission('show_report_grafics')">
            <!-- 👉 Sales Overview -->
            <VCol
                cols="12"
                md="6"
            >
                <EcommerceSalesOverview />
            </VCol>

            <!-- 👉 Images Cards -->
            <VCol
                v-for="statistics in statisticsWithImages"
                :key="statistics.title"
                cols="12"
                sm="6"
                md="3"
            >
                <CardStatisticsWithImages v-bind="statistics" />
            </VCol>
            
            <!-- 👉 Total Visits -->
            <VCol
                cols="12"
                md="3"
                sm="6"
            >
                <EcommerceTotalVisits />
            </VCol>

            <!-- 👉 Weekly Sales -->
            <VCol
                cols="12"
                md="6"
            >
                <EcommerceWeeklySalesBg />
            </VCol>
            <VCol
                cols="12"
                md="3"
                sm="6"
            >
                <CrmCongratulationsNorris />
            </VCol>
            <VCol
                cols="12"
                md="6"
            >
                <LogisticsShipmentStatistics />
            </VCol>

            <!-- 👉 Weekly Sales -->
            <VCol
                cols="12"
                md="6"
            >
                <AnalyticsWeeklySales />
            </VCol>

            

        </VRow>
        <VRow class="match-height d-flex justify-center" v-if="!isPermission('show_report_grafics')">
            <VCol
                cols="6"
            >
                <img src="https://cdn-icons-png.flaticon.com/512/8644/8644515.png" alt="">
            </VCol>
        </VRow>
    </div>
</template>