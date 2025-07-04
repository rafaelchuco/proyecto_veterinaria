<script setup>
import avatar1 from '@images/avatars/avatar-1.png'
import avatar2 from '@images/avatars/avatar-2.png'
import avatar3 from '@images/avatars/avatar-3.png'
// import pdf from '@images/icons/project-icons/pdf.png'

const dateRange = ref(null);

const loading = ref(false)
const search = ref()
const select_pet = ref(null)
const items = ref([])
const querySelections = async (query) => {
  loading.value = true

  // Simulated ajax query
  setTimeout(async () => {
    // items.value = states.filter(state => (state || '').toLowerCase().includes((query || '').toLowerCase()))
    const resp = await $api("/appointments/search-pets/"+query,{
        method: 'GET',
        onResponseError({response}){
            console.log(response);
            error_exists.value = response._data.error;
        }
    })
    console.log(resp);
    items.value = resp.pets;
    loading.value = false
  }, 500)
}
watch(search, query => {
    if(query &&  query.length > 1){
        querySelections(query)
    }else{
        items.value = [];
    }
//    query && query !== select.value && querySelections(query)
})
</script>
<template>
  <div>
    <!-- <VCard title="Create Awesome 🙌">
      <VCardText>This is your second page.</VCardText>
      <VCardText>
        Chocolate sesame snaps pie carrot cake pastry pie lollipop muffin.
        Carrot cake dragée chupa chups jujubes. Macaroon liquorice cookie
        wafer tart marzipan bonbon. Gingerbread jelly-o dragée
        chocolate.
      </VCardText>
    </VCard> -->
    <VCardText class="pa-5">
      <div class="mb-1">
        <h4 class="text-h4 text-center mb-1">
            Historial Medico 👩‍💻
        </h4>
      </div>
    </VCardText>

    <VCard title="🔎 Busqueda:" class="pa-4">
      <VRow>
        <VCol cols="6">
          <VAutocomplete
              v-model="select_pet"
              v-model:search="search"
              :loading="loading"
              :items="items"
              item-title="name"
              item-value="id"
              return-object
              placeholder="Ingresa información de la mascota"
              label="¿Quien es la mascotita?"
              variant="underlined"
              :menu-props="{ maxHeight: '200px' }"
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
        <VCol cols="3">
            <VBtn
                color="info"
                class="mx-1"
                prepend-icon="ri-search-2-line"
            >
            </VBtn>
            <VBtn
                color="secondary"
                class="mx-1"
                prepend-icon="ri-restart-line"
            >
            </VBtn>
        </VCol>
      </VRow>
    </VCard>

    <VCard title="👩‍💻 Historial Medico:" class="pa-4 my-3">
      <VRow class="justify-center">
        <VCol cols="9">
          <VCardText>
            <VTimeline
              side="end"
              align="start"
              line-inset="9"
              truncate-line="start"
              density="compact"
            >
              <!-- SECTION Timeline Item: Flight -->
              <VTimelineItem
                dot-color="primary"
                size="x-small"
              >
                <!-- 👉 Header -->
                <div class="d-flex justify-space-between align-center gap-2 flex-wrap mb-2">
                  <span class="app-timeline-title">
                    12 Invoices have been paid
                  </span>
                  <span class="app-timeline-meta">12 min ago</span>
                </div>
    
                <!-- 👉 Content -->
                <div class="app-timeline-text mt-3">
                  Invoices have been paid to the company
                </div>
    
                <div class="d-inline-flex align-center timeline-chip my-2">
                  <img
                    :src="'data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAADEAAAAxCAMAAABEQrEuAAABXFBMVEUAAAD/AAD/iIj/jY3JBgbKBgbKBATLBgbKBgbKBQXzd3fKBgbKBQXlVFTLBQXKBQX4hob6h4f5hob5hobLBQXKBgbKBgbLBgbKBgb//////v7//f3+/Pz++/v++Pj99/f99vb99fX99PT88fH88PD87+/87u777e376ur65+f55eX55OT54+P43d343Nz32dn32Nj21tb21dX10dH10ND1zs70yMjzxsbzxMTxvr7wuLjvs7PusLDspqbso6PrnZ3qm5vpl5fplpbplZX7jY3ok5PokpLokZHnjY34hYXmh4fmhobmhYXlg4PlgoLkfHzycnLicnLhbm7uaGjga2vgamrfZmbcWVncV1fjSkraTEzZS0vZSkrYQ0PfPz/XQEDWOTnVNDTUMTHTLCzXKCjSKSnSJibTHh7PGxvPGRnNEhLNERHMDQ3NDAzMDAzMCgrMCQnLCAjLBwfLBgby8IbhAAAAGXRSTlMAAg8SJic6cnmDmJy1tre409XW19709fb++ZLcewAAAUdJREFUeNrt1ddTwkAQBnAUsIsVcRFLEImoWEARewv2ioq9YRcNMd//P+MDMwqSu1zeHIfveX+ztzd7czZbLk6XB8V5UJrsNuM43YChYJIGsITSbEw8bMEg4AhjwhWGhC+UFrtVYUDMRDExFUpjuZB4/hG/uzDEx4bC6sIQOMsTSo2I0K8P979TLyIKUl0S/14cHQDvyfmVjLAIy0CC/CHdkhjrymjcU6Uubre30gAe99ZvwjJOg4Hje66Qgj4v+dN46iWv1ycjSkQpvui7VHdoEUu0pl4FZGAwZHJXUgR4oThi9JqbQ0i80ThGOz5FxQhwTrOYojtoQ0KC4ol+OkGShlejJCQGpju7N3WoCz3S3MwEMBkzn0PNAgCymtheSRGrm7i8W3qDf0F4RERVvnCJiIp84XCbg9rCz9ZR18Ytb2+tLMtVfgHx8NDQuBJofgAAAABJRU5ErkJggg=='"
                    height="20"
                    class="me-2"
                    alt="img"
                  >
                  <span class="app-timeline-text font-weight-medium">
                    invoices.pdf
                  </span>
                </div>
              </VTimelineItem>
              <!-- !SECTION -->
    
              <!-- SECTION Timeline Item: Interview Schedule -->
              <VTimelineItem
                size="x-small"
                dot-color="success"
              >
                <!-- 👉 Header -->
                <div class="d-flex justify-space-between align-center flex-wrap mb-2">
                  <div class="app-timeline-title">
                    Client Meeting
                  </div>
                  <span class="app-timeline-meta">45 min ago</span>
                </div>
    
                <div class="app-timeline-text mt-3">
                  Project meeting with john @10:15am
                </div>
    
                <!-- 👉 Person -->
                <div class="d-flex justify-space-between align-center flex-wrap">
                  <!-- 👉 Avatar & Personal Info -->
                  <div class="d-flex align-center my-2">
                    <VAvatar
                      size="32"
                      class="me-2"
                      :image="avatar1"
                    />
                    <div class="d-flex flex-column">
                      <p class="text-sm font-weight-medium text-medium-emphasis mb-0">
                        Lester McCarthy (Client)
                      </p>
                      <span class="text-sm">CEO of Pixinvent</span>
                    </div>
                  </div>
                </div>
              </VTimelineItem>
              <!-- !SECTION -->
    
              <!-- SECTION Design Review -->
              <VTimelineItem
                size="x-small"
                dot-color="info"
              >
                <!-- 👉 Header -->
                <div class="d-flex justify-space-between align-center flex-wrap mb-2">
                  <span class="app-timeline-title">
                    Create a new project for client
                  </span>
                  <span class="app-timeline-meta">2 Day Ago</span>
                </div>
    
                <!-- 👉 Content -->
                <p class="app-timeline-text mt-3 mb-2">
                  6 team members in a project
                </p>
    
                <div class="v-avatar-group demo-avatar-group mb-5">
                  <VAvatar :size="40">
                    <VImg :src="avatar1" />
                    <VTooltip
                      activator="parent"
                      location="top"
                    >
                      John Doe
                    </VTooltip>
                  </VAvatar>
    
                  <VAvatar :size="40">
                    <VImg :src="avatar2" />
                    <VTooltip
                      activator="parent"
                      location="top"
                    >
                      Jennie Obrien
                    </VTooltip>
                  </VAvatar>
    
                  <VAvatar :size="40">
                    <VImg :src="avatar3" />
                    <VTooltip
                      activator="parent"
                      location="top"
                    >
                      Peter Harper
                    </VTooltip>
                  </VAvatar>
    
                  <VAvatar
                    :size="40"
                    :color="$vuetify.theme.current.dark ? '#383B55' : '#F0EFF0'"
                  >
                    +3
                  </VAvatar>
                </div>
              </VTimelineItem>
              <!-- !SECTION -->
            </VTimeline>
          </VCardText>
        </VCol>
      </VRow>
    </VCard>
  </div>
</template>
