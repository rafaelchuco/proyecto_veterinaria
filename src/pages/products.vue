<template>
    <div>
      <VRow>
        <VCol
          sm="6"
          cols="12"
        >
          <VCard title="Nueva vista 🙌">
            <div class="d-flex justify-space-between flex-wrap flex-md-nowrap flex-column flex-md-row">
              <div
                  class="ma-auto pa-5 border-e"
              >
                <VImg
                  width="137"
                  height="176"
                  src="https://demos.pixinvent.com/materialize-vuejs-admin-template/demo-1/assets/2-xjy_pXoW.png"
                />
              </div>
              <div>
                <VCardItem>
                  <VCardTitle>Apple iPhone 11 Pro</VCardTitle>
                </VCardItem>
        
                <VCardText>
                  Apple iPhone 11 Pro smartphone. Announced Sep 2019. Features 5.8″ display Apple A13 Bionic
                </VCardText>
              </div>
            </div>
          </VCard>
        </VCol>

        <VCol
          sm="6"
          cols="12"
        >
          <VCard class="px-4 py-4">
            <VRow>
              <VCol
                cols="12"
              >
                  <VTextField
                    label="Regular"
                    placeholder="Placeholder Text"
                  />
              </VCol>

              <VCol
                cols="12"
              >
                <VTextarea
                  label="Default"
                  placeholder="Placeholder Text"
                />
              </VCol>

              <VCol
                cols="12"
              >
                <VSelect
                  :items="items"
                  label="Standard"
                  placeholder="Select Item"
                  eager
                />
              </VCol>
              <VCol
                cols="12"
              >
              <VBtn color="primary">
                Primary
              </VBtn>
            </VCol>
            </VRow>
          </VCard>
        </VCol>
      </VRow>
      <VTable>
            <thead>
            <tr>
                <th class="text-uppercase">
                Horas
                </th>
                <th class="text-uppercase">
                Dia 1
                </th>
                <th class="text-uppercase">
                Dia 2
                </th>
            </tr>
            </thead>

            <tbody>
            <tr
                v-for="item in desserts"
                :key="item.dessert"
            >
                <td>
                {{ item.dessert }}
                </td>
                <td>
                    <VCheckbox
                        @click="onCheckboxClickedAll($event,['8:00','8:15','8:30'],item.dessert)"
                        label="Todos"
                    />

                    <VCheckbox
                        @click="onCheckboxClicked($event,'8:00',item.dessert)"
                        v-model="selected"
                        label="8:00 - 8:15"
                        :value="'8:00'+item.dessert"
                    />

                    <VCheckbox
                        @click="onCheckboxClicked($event,'8:15',item.dessert)"
                        v-model="selected"
                        label="8:15 - 8:30"
                        :value="'8:15'+item.dessert"
                    />

                    <VCheckbox
                        @click="onCheckboxClicked($event,'8:30',item.dessert)"
                        v-model="selected"
                        label="8:30 - 8:45"
                        :value="'8:30'+item.dessert"
                    />
                </td>
                <td>
                    <VCheckbox
                        @click="onCheckboxClicked($event)"
                        label="8:00 - 8:15"
                        value="8:00"
                    />

                    <VCheckbox
                        @click="onCheckboxClicked($event)"
                        label="8:15 - 8:30"
                        value="8:15"
                    />

                    <VCheckbox
                        @click="onCheckboxClicked($event)"
                        label="8:30 - 8:45"
                        value="8:30"
                    />
                </td>
            </tr>
            </tbody>
        </VTable>
    </div>
</template>
<script setup>

const items = [
  'Foo',
  'Bar',
  'Fizz',
  'Buzz',
]

// definePage({
//   meta: {
//     not_autenticacion: false,
//   },
// })
const selected = ref([]);
    const desserts = [
        {
            dessert: '8:00 AM',
            DIA1: null,
            DIA2: null,
        },
        {
            dessert: '9:00 AM',
            DIA1: null,
            DIA2: null,
        },
        {
            dessert: '10:00 AM',
            DIA1: null,
            DIA2: null,
        },
        {
            dessert: '11:00 AM',
            DIA1: null,
            DIA2: null,
        },
        {
            dessert: '12:00 PM',
            DIA1: null,
            DIA2: null,
        },
    ]
    const onCheckboxClicked = ($event,hour,item) => {
        console.log($event.target.checked)
        if($event.target.checked){
            // if(INDEX != -1){
            //     selected.value.splice(INDEX,1);
            // }
            selected.value.push(hour+''+item);
            console.log(selected.value);
        }else{
            let INDEX = selected.value.findIndex((item2) => item2 == hour+''+item);
            if(INDEX != -1){
                console.log(INDEX);
                selected.value.splice(INDEX,1);
                console.log(selected.value);
            }
        }
    }
    const onCheckboxClickedAll = ($event,hours,item) => {
        console.log($event.target.checked)
        hours.forEach(hour => {
            let INDEX = selected.value.findIndex((item2) => item2 == hour+''+item);
            if($event.target.checked){
                if(INDEX != -1){
                    selected.value.splice(INDEX,1);
                }
                selected.value.push(hour+''+item);
            }else{
                if(INDEX != -1){
                    selected.value.splice(INDEX,1);
                }
            }
        });
        console.log(selected.value);
    }
</script>