<script setup>
import { isPermission } from '@/utils/constants';


    const router = useRouter()
    const searchQuery = ref(null);
    const specie = ref(null);
    const species = ref(['Perro','Gato','Hámster','Loro','Tortuga','Vaca','Caballo','Cuy','Toro']);
    const pets = ref([]);
    const currentPage = ref(1);
    const totalPage = ref(1);
    const pet_selected_deleted = ref(null);
    const isDeletePetDialogVisible = ref(false);
    const list = async() => {
        const resp =  await $api('/pets?page='+currentPage.value+'&search='+(searchQuery.value ? searchQuery.value : '')
        +'&species='+(specie.value ? specie.value : '')
        ,{
            method: 'GET',
            onResponseError({response}){
              console.log(response);
            }
        })
        console.log(resp);
        totalPage.value = resp.total_page;
        pets.value = resp.pets.data;
    }
    const editItem = (item) => {
        router.push({
            name: 'pet-edit-id',
            params: {
                id: item.id
            }
        })
    }
    const deleteItem = (item) => {
        pet_selected_deleted.value = item;
        isDeletePetDialogVisible.value = true;
    }
    const deletePet123 = (item) => {
        let INDEX = pets.value.findIndex((pet) => pet.id == item.id);
        if(INDEX != -1){
            pets.value.splice(INDEX,1);
        }
    }
    const reset = () => {
        searchQuery.value = null;
        specie.value = null;
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
    watch(isDeletePetDialogVisible,(val) => {
        if(val == false){
            pet_selected_deleted.value = null;
        }
    })
    onMounted(() => {
        list()
    })
    definePage({
        meta: {
            permisssion: 'list_pet'
        },
    })
</script>
<template>
    <div>
        <VCard title="🐶 Pets">
            <VCardText class="d-flex flex-wrap gap-4">
                <div class="d-flex align-center">
                <!-- 👉 Search  -->
                <VTextField
                    v-model="searchQuery"
                    placeholder="Search Pets"
                    style="inline-size: 300px;"
                    density="compact"
                    class="me-3"
                    @keyup.enter="list"
                />
                </div>
                <div>
                    <VSelect
                        :items="species"
                        v-model="specie"
                        style="inline-size: 300px;"
                        label="Especie"
                        item-title="name"
                        item-value="id"
                        placeholder="Select Rol"
                        eager
                    />
                </div>
                <div>
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
                </div>
                <VSpacer />
    
                <div class="d-flex gap-x-4 align-center">
                    <VBtn
                        color="primary"
                        v-if="isPermission('register_pet')"
                        prepend-icon="ri-add-line"
                        @click="router.push({name: 'pet-add'})"
                    >
                        Add Pet
                    </VBtn>
                </div>
            </VCardText>

            <VCardText class="pa-5">
                <VTable>
                    <thead>
                        <tr>
                            <th class="text-uppercase">
                                Nombre
                            </th>
                            <th class="text-uppercase">
                                Especie
                            </th>
                            <th class="text-uppercase">
                                Raza
                            </th>
                            <th class="text-uppercase">
                                Genero
                            </th>
                            <th class="text-uppercase">
                                Peso
                            </th>
                            <th class="text-uppercase">
                                Color
                            </th>
                            <th class="text-uppercase">
                                Acción
                            </th>
                        </tr>
                    </thead>
    
                    <tbody>
                        <tr
                            v-for="item in pets"
                            :key="item.id"
                        >
                            <td>
                                <div class="d-flex align-center">
                                    <VAvatar
                                    size="32"
                                    :color="item.photo ? '' : 'primary'"
                                    :class="item.photo ? '' : 'v-avatar-light-bg primary--text'"
                                    :variant="!item.photo ? 'tonal' : undefined"
                                    >
                                    <VImg
                                        v-if="item.photo"
                                        :src="item.photo"
                                    />
                                        <span
                                            v-else
                                            class="text-sm"
                                            >{{ avatarText(item.name) }}</span>
                                    
                                    </VAvatar> 
                                    <div class="d-flex flex-column ms-3">
                                    <span class="d-block font-weight-medium text-high-emphasis text-truncate">{{ item.name }}</span>
                                    </div>
                                </div>

                            </td>
                            <td>
                                {{ item.specie }}
                            </td>
                            <td>
                                {{ item.breed }}
                            </td>
                            <td>
                                {{ item.gender }}
                            </td>
                            <td>
                                {{ item.weight }} KG
                            </td>
                            <td>
                                {{ item.color }}
                            </td>
                            <td>
                                <div class="d-flex gap-1">
                                    <IconBtn
                                        size="small"
                                        v-if="isPermission('edit_pet')"
                                        @click="editItem(item)"
                                    >
                                        <VIcon icon="ri-pencil-line" />
                                    </IconBtn>
                                    <IconBtn
                                        size="small"
                                        v-if="isPermission('delete_pet')"
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

            <DeletePetDialog v-if="pet_selected_deleted" :petSelected="pet_selected_deleted" @deletePet="deletePet123" v-model:is-dialog-visible="isDeletePetDialogVisible" />
        </VCard>
    </div>
</template>
<style>
    .v-btn__prepend {
        margin-inline: 0 !important;
    }
</style>