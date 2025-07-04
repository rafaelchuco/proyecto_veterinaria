<script setup>
import { isPermission } from '@/utils/constants';

    // import data from '@/views/js/datatable'
    const data = ref([]);

    const headers = [
        {
            title: 'ID',
            key: 'id',
        },
        {
            title: 'Avatar',
            key: 'imagen',
        },
        {
            title: 'Nombre y apellido',
            key: 'full_name',
        },
        {
            title: 'Rol',
            key: 'role_name',
        },
        {
            title: 'Correo',
            key: 'email',
        },
        {
            title: 'Telefono',
            key: 'phone',
        },
        {
            title: 'Documento',
            key: 'document_full',
        },
        {
            title: 'Op',
            key: 'action',
        },
    ]
    const roles = ref([]);
    const avatarText = value => {
        if (!value)
            return ''
        const nameArray = value.split(' ')
        
        return nameArray.map(word => word.charAt(0).toUpperCase()).join('')
    }
    const searchQuery = ref(null);
    const staff_selected = ref(null);
    const staff_selected_deleted = ref(null);
    const isAddStaffDialogVisible = ref(false);
    const isEditStaffDialogVisible = ref(false);
    const isDeleteStaffDialogVisible = ref(false);

    const list = async () => {
        const resp =  await $api('/staffs?search='+(searchQuery.value ? searchQuery.value : ''),{
            method: 'GET',
            onResponseError({response}){
              console.log(response);
            }
        })
        console.log(resp);

        data.value = resp.users.data;
        roles.value = resp.roles;
    }

    const addUser = (newUser) => {
        data.value.unshift(newUser);
    }
    const editUser = (editUser) => {
        let INDEX = data.value.findIndex((user) => user.id == editUser.id);
        if(INDEX != -1){
            data.value[INDEX] = editUser;
        }
    }
    const deleteUser = (User) => {
        let INDEX = data.value.findIndex((user) => user.id == User.id);
        if(INDEX != -1){
            data.value.splice(INDEX,1);
        }
    }
    const editItem = (item) => {
        isEditStaffDialogVisible.value = true;
        staff_selected.value = item;
    }

    const deleteItem = (item) => {
        isDeleteStaffDialogVisible.value = true;
        staff_selected_deleted.value = item;
    }

    onMounted(() => {
        list();
    })
    watch(isEditStaffDialogVisible, (event) => {
        console.log(event);
        if(event == false){
            staff_selected.value = null;
        }
    })
    watch(isDeleteStaffDialogVisible, (event) => {
        console.log(event);
        if(event == false){
            staff_selected_deleted.value = null;
        }
    })
    definePage({
        meta: {
            permisssion: 'list_staff'
        },
    })
</script>

<template>
    <div>
        <VCard title="🧑‍🏭 Staffs">
            <VCardText class="d-flex flex-wrap gap-4">
                <div class="d-flex align-center">
                <!-- 👉 Search  -->
                <VTextField
                    v-model="searchQuery"
                    placeholder="Search Staff"
                    style="inline-size: 300px;"
                    density="compact"
                    class="me-3"
                    @keyup.enter="list"
                />
                </div>
    
                <VSpacer />
    
                <div class="d-flex gap-x-4 align-center">
                    <VBtn
                        color="primary"
                         v-if="isPermission('register_staff')"
                        prepend-icon="ri-add-line"
                        @click="isAddStaffDialogVisible = !isAddStaffDialogVisible"
                    >
                        Add Staff
                    </VBtn>
                </div>
            </VCardText>

            <VDataTable
                :headers="headers"
                :items="data"
                :items-per-page="5"
                class="text-no-wrap"
            >
                <template #item.id="{ item }">
                <span class="text-h6">{{ item.id }}</span>
                </template>
                
                <template #item.imagen="{ item }">
                    <div class="d-flex align-center">
                        <VAvatar
                        size="32"
                        :color="item.avatar ? '' : 'primary'"
                        :class="item.avatar ? '' : 'v-avatar-light-bg primary--text'"
                        :variant="!item.avatar ? 'tonal' : undefined"
                        >
                        <VImg
                            v-if="item.avatar"
                            :src="item.avatar"
                        />
                            <span
                                v-else
                                class="text-sm"
                                >{{ avatarText(item.full_name) }}</span>
                           
                        </VAvatar> 
                        <!-- <div class="d-flex flex-column ms-3">
                        <span class="d-block font-weight-medium text-high-emphasis text-truncate">{{ item.fullName }}</span>
                        <small>{{ item.post }}</small>
                        </div> -->
                    </div>
                </template>
                <template #item.document_full="{ item }">
                    <div class="d-flex align-center">
                        <div class="d-flex flex-column ms-3">
                            <span class="d-block font-weight-medium text-high-emphasis text-truncate">{{ item.n_document }}</span>
                            <small>{{ item.type_document }}</small>
                        </div>
                    </div>
                </template>
                
                <template #item.action="{ item }">
                    <div class="d-flex gap-1">
                      <IconBtn
                        size="small"
                        v-if="isPermission('edit_staff')"
                        @click="editItem(item)"
                      >
                        <VIcon icon="ri-pencil-line" />
                      </IconBtn>
                      <IconBtn
                        size="small"
                         v-if="isPermission('delete_staff')"
                        @click="deleteItem(item)"
                      >
                        <VIcon icon="ri-delete-bin-line" />
                      </IconBtn>
                    </div>
                    </template>
            </VDataTable>

             <AddStaffDialog v-if="roles.length > 0" v-model:is-dialog-visible="isAddStaffDialogVisible" :roles="roles" @addUser="addUser" />
            <EditStaffDialog v-if="staff_selected" :userSelected="staff_selected" :roles="roles" v-model:is-dialog-visible="isEditStaffDialogVisible"  @editUser="editUser" />
            <DeleteStaffDialog v-if="staff_selected_deleted" :userSelected="staff_selected_deleted" @deleteUser="deleteUser" v-model:is-dialog-visible="isDeleteStaffDialogVisible" />
        </VCard>


    </div>
</template>