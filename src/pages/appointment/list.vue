<script setup>

    const router = useRouter()
    const searchPets = ref(null);
    const searchVets = ref(null);
    const specie = ref(null);
    const species = ref(['Perro','Gato','Hámster','Loro','Tortuga','Vaca','Caballo','Cuy','Toro']);
    const appointments = ref([]);
    const currentPage = ref(1);
    const totalPage = ref(1);
    const appointment_selected_deleted = ref(null);
    const isDeleteAppointmentDialogVisible = ref(false);

    const dateRange = ref(null);
    const type_date = ref(1);
    const state_pay = ref(null);
    const state_appointment = ref(null);
    const list = async() => {
        let data = {
            type_date: type_date.value,
            start_date: dateRange.value ? dateRange.value.split("to")[0] : null,
            end_date: dateRange.value ? dateRange.value.split("to")[1] : null,
            state_pay: state_pay.value,
            state: state_appointment.value,
            specie: specie.value,
            search_pets: searchPets.value,
            search_vets: searchVets.value,
        }
        const resp =  await $api('/appointments/index?page='+currentPage.value
        ,{
            method: 'POST',
            body:data,
            onResponseError({response}){
              console.log(response);
            }
        })
        console.log(resp);
        totalPage.value = resp.total_page;
        appointments.value = resp.appointments.data;
    }
    const downloadExcel = () => {
        let LINK = "";
        if(dateRange.value){
            LINK += "&type_date="+type_date.value;
            LINK += "&start_date="+dateRange.value.split("to")[0]; 
            LINK += "&end_date="+dateRange.value.split("to")[1]; 
        }
        if(state_pay.value){
            LINK += "&state_pay="+state_pay.value;
        }
        if(state_appointment.value){
            LINK += "&state="+state_appointment.value;
        }
        if(specie.value){
            LINK += "&specie="+specie.value;
        }
        if(searchPets.value){
            LINK += "&search_pets="+searchPets.value;
        }
        if(searchVets.value){
            LINK += "&search_vets="+searchVets.value;
        }
        window.open(import.meta.env.VITE_API_BASE_URL+"/appointment-excel?k=1"+LINK,"_blank");
    }
    const editItem = (item) => {
        router.push({
            name: 'appointment-edit-id',
            params: {
                id: item.id
            }
        })
    }
    const deleteItem = (item) => {
        appointment_selected_deleted.value = item;
        isDeleteAppointmentDialogVisible.value = true;
    }
    const deleteAppointment = (item) => {
        let INDEX = appointments.value.findIndex((appointment) => appointment.id == item.id);
        if(INDEX != -1){
            appointments.value.splice(INDEX,1);
        }
    }
    const reset = () => {
        searchPets.value = null;
        searchVets.value = null;
        specie.value = null;
        dateRange.value = null;
        state_pay.value = null;
        state_appointment.value = null;
        type_date.value = 1;
        currentPage.value = 1;
        list();
    }
    const avatarText = value => {
        if (!value)
            return ''
        const nameArray = value.split(' ')
        
        return nameArray.map(word => word.charAt(0).toUpperCase()).join('')
    }
    watch(currentPage,(val) => {
        console.log(val);
        list();
    })
    watch(isDeleteAppointmentDialogVisible,(val) => {
        if(val == false){
            appointment_selected_deleted.value = null;
        }
    })
    onMounted(() => {
        list()
    })
    definePage({
        meta: {
            permisssion: 'list_appointment'
        },
    })
</script>
<template>
    <div>
        <VCard title="📆 Appointments">
            <VCardText class="d-flex flex-wrap gap-4">
                <VRow>
                    <VCol cols="2">
                        <VSelect
                            :items="[
                                {
                                    name: 'Fecha de la cita',
                                    id: 1,
                                },
                                {
                                    name: 'Fecha de registro',
                                    id: 2,
                                },
                            ]"
                            v-model="type_date"
                            label="Tipo"
                            item-title="name"
                            item-value="id"
                            placeholder="Select Type"
                            eager
                        />
                    </VCol>
                    <VCol cols="3">
                        <AppDateTimePicker
                            v-model="dateRange"
                            label="Fechas de cita"
                            placeholder="Select fecha"
                            :config="{ mode: 'range' }"
                        />
                    </VCol>
                    <VCol cols="2">
                        <VSelect
                            :items="species"
                            v-model="specie"
                            label="Especie"
                            item-title="name"
                            item-value="id"
                            placeholder="Select Especie"
                            eager
                        />
                    </VCol>
                    
                    <VCol cols="2">
                        <div class="d-flex">
                            <VBtn
                                color="info"
                                class="mx-1"
                                prepend-icon="ri-search-2-line"
                                @click="list()"
                            >
                            </VBtn>
                            <VBtn
                                color="secondary"
                                class="mx-1"
                                prepend-icon="ri-restart-line"
                                @click="reset()"
                            >
                            </VBtn>
                            <VBtn
                                color="success"
                                class="mx-1"
                                prepend-icon="ri-file-excel-2-line"
                                @click="downloadExcel()"
                            >
                            </VBtn>
                        </div>
                    </VCol>
                    <VCol cols="2">
                        <VBtn
                            color="primary"
                            prepend-icon="ri-add-line"
                            v-if="isPermission('register_appointment')"
                            @click="router.push({name: 'appointment-add'})"
                        >
                            Add Appointment
                        </VBtn>
                    </VCol>

                    <VCol cols="2">
                        <VSelect
                            :items="[
                                {
                                    name: 'Pendiente',
                                    id: 1,
                                },
                                {
                                    name: 'Cancelado',
                                    id: 2,
                                },
                                {
                                    name: 'Atendido',
                                    id: 3,
                                }
                            ]"
                            v-model="state_appointment"
                            label="Estado de la cita"
                            item-title="name"
                            item-value="id"
                            placeholder="Select Estado"
                            eager
                        />
                    </VCol>

                    <VCol cols="3">
                        <VTextField
                            v-model="searchPets"
                            placeholder="Search Pets"
                            density="compact"
                            class="me-3"
                            @keyup.enter="list"
                        />
                    </VCol>
                    <VCol cols="3">
                        <VTextField
                            v-model="searchVets"
                            placeholder="Search Veterinaries"
                            density="compact"
                            class="me-3"
                            @keyup.enter="list"
                        />
                    </VCol>

                    <VCol cols="2">
                        <VSelect
                            :items="[
                                {
                                    name: 'Pendiente',
                                    id: 1,
                                },
                                {
                                    name: 'Parcial',
                                    id: 2,
                                },
                                {
                                    name: 'Completo',
                                    id: 3,
                                }
                            ]"
                            v-model="state_pay"
                            label="Estado de pago"
                            item-title="name"
                            item-value="id"
                            placeholder="Select Estado"
                            eager
                        />
                    </VCol>
                </VRow>
            </VCardText>

            <VCardText class="pa-5">
                <VTable>
                    <thead>
                        <tr>
                            <th class="text-uppercase">
                                Mascota
                            </th>
                            <th class="text-uppercase">
                                Especie
                            </th>
                            <th class="text-uppercase">
                                Fecha de cita
                            </th>
                            <th class="text-uppercase">
                                Veterinario
                            </th>
                            <th class="text-uppercase">
                                Estado
                            </th>
                            <th class="text-uppercase">
                                Costo
                            </th>
                            <th class="text-uppercase">
                                Acción
                            </th>
                        </tr>
                    </thead>
    
                    <tbody>
                        <tr
                            v-for="item in appointments"
                            :key="item.id"
                        >
                            <td>
                                <div class="d-flex align-center">
                                    <VAvatar
                                    size="32"
                                    :color="item.pet.photo ? '' : 'primary'"
                                    :class="item.pet.photo ? '' : 'v-avatar-light-bg primary--text'"
                                    :variant="!item.pet.photo ? 'tonal' : undefined"
                                    >
                                    <VImg
                                        v-if="item.pet.photo"
                                        :src="item.pet.photo"
                                    />
                                        <span
                                            v-else
                                            class="text-sm"
                                            >{{ avatarText(item.pet.name) }}</span>
                                    
                                    </VAvatar> 
                                    <div class="d-flex flex-column ms-3">
                                    <span class="d-block font-weight-medium text-high-emphasis text-truncate">{{ item.pet.name }}</span>
                                    </div>
                                </div>

                            </td>
                            <td>
                                {{ item.pet.specie }}
                            </td>
                            <td>
                                {{ item.date_appointment }}
                            </td>
                            <td>
                                {{ item.veterinarie.full_name }}
                            </td>
                            <td>
                                <VChip v-if="item.state == 1" color="warning">
                                    Pendiente
                                </VChip>   
                                <VChip v-if="item.state == 2" color="danger">
                                    Cancelado
                                </VChip> 
                                <VChip v-if="item.state == 3" color="primary">
                                    Atendido
                                </VChip>   
                            </td>
                            <td>
                                {{ item.amount }} PEN
                            </td>
                            <td>
                                <div class="d-flex gap-1">
                                    <IconBtn
                                        size="small"
                                        v-if="isPermission('edit_appointment')"
                                        @click="editItem(item)"
                                    >
                                        <VIcon icon="ri-pencil-line" />
                                    </IconBtn>
                                    <IconBtn
                                        size="small"
                                        v-if="isPermission('delete_appointment')"
                                        @click="deleteItem(item)"
                                    >
                                        <VIcon icon="ri-delete-bin-line" />
                                    </IconBtn>
                                </div>
                            </td>
                        </tr>
                    </tbody>
                </VTable>

                <VPagination
                    v-model="currentPage"
                    :length="totalPage"
                />
            </VCardText>

            <DeleteAppoinmentDialog v-if="appointment_selected_deleted" :appointmentSelected="appointment_selected_deleted" @deleteAppointment="deleteAppointment" v-model:is-dialog-visible="isDeleteAppointmentDialogVisible" />
        </VCard>
    </div>
</template>
<style>
    .v-btn__prepend {
        margin-inline: 0 !important;
    }
</style>