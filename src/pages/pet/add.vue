<script setup>
    const warning = ref(null);
    const success = ref(null);
    const error_exists = ref(null);
    const form = ref({
        name: null,
        specie: null,
        breed: null,
        dirth_date: null,
        gender: null,
        color: null,
        weight: 0,
        medical_notes: null,

        first_name:null,
        last_name:null,
        email: null,
        phone: null,
        address: null,
        city: null,
        emergency_contact: null,
        type_document: "DNI",
        n_document: null,
    });
    const type_documents = [
        'DNI',
        'PASAPORTE',
        'CARNET DE EXTRANJERIA'
    ];
    const species = ref(['Perro','Gato','Hámster','Loro','Tortuga','Vaca','Caballo','Cuy','Toro']);
    const FILE_AVATAR = ref(null);
    const IMAGEN_PREVIZUALIZA = ref(null);

    const loadFile = ($event) => {
        if($event.target.files[0].type.indexOf("image") < 0){
            FILE_AVATAR.value = null;
            IMAGEN_PREVIZUALIZA.value = null;
            warning.value = "SOLAMENTE PUEDEN SER ARCHIVOS DE TIPO IMAGEN";
            return;
        }
        warning.value = '';
        FILE_AVATAR.value = $event.target.files[0];
        let reader = new FileReader();
        reader.readAsDataURL(FILE_AVATAR.value);
        reader.onloadend = () => IMAGEN_PREVIZUALIZA.value = reader.result;
    }
    const fieldsClean = () => {
        form.value.name = null;
        form.value.specie = null;
        form.value.breed = null;
        form.value.dirth_date = null;
        form.value.gender = null;
        form.value.color = null;
        form.value.weight = null;
        form.value.medical_notes = null;
        form.value.first_name = null;
        form.value.last_name = null;
        form.value.email = null;
        form.value.phone = null;
        form.value.address = null;
        form.value.city = null;
        form.value.emergency_contact = null;
        form.value.type_document = "DNI";
        form.value.n_document = null;
        FILE_AVATAR.value = null;
        IMAGEN_PREVIZUALIZA.value = null;
    }

    const store = async() => {
        try {
            warning.value = null;
            if(!form.value.name){
                warning.value = "El nombre de la mascota es obligatorio";
                return;
            }
            if(!form.value.specie){
                warning.value = "La especie de la mascota es obligatorio";
                return;
            }
            if(!form.value.breed){
                warning.value = "La raza de la mascota es obligatorio";
                return;
            }
            if(!form.value.dirth_date){
                warning.value = "La fecha de nacimiento de la mascota es obligatorio";
                return;
            }
            if(!form.value.gender){
                warning.value = "El genero de la mascota es obligatorio";
                return;
            }
            if(!form.value.color){
                warning.value = "El color de la mascota es obligatorio";
                return;
            }
            if(!form.value.weight){
                warning.value = "El peso de la mascota es obligatorio";
                return;
            }
            if(!FILE_AVATAR.value){
                warning.value = "La imagen de la mascota es obligatorio";
                return;
            }

            if(!form.value.first_name){
                warning.value = "El nombre del responsable es obligatorio";
                return;
            }
            if(!form.value.last_name){
                warning.value = "El apellido del responsable es obligatorio";
                return;
            }
            if(!form.value.phone){
                warning.value = "El telefono del responsable es obligatorio";
                return;
            }
            if(!form.value.address){
                warning.value = "La dirección del responsable es obligatorio";
                return;
            }
            if(!form.value.emergency_contact){
                warning.value = "El contacto de emergencia es obligatorio";
                return;
            }
            if(!form.value.n_document){
                warning.value = "El numero de documento es obligatorio";
                return;
            }

            let formData = new FormData();
            formData.append("name",form.value.name);
            formData.append("specie",form.value.specie);
            formData.append("breed",form.value.breed);
            formData.append("dirth_date",form.value.dirth_date);
            formData.append("gender",form.value.gender);
            formData.append("color",form.value.color);
            formData.append("weight",form.value.weight);
            if(form.value.medical_notes){
                formData.append("medical_notes",form.value.medical_notes);
            }
            formData.append("first_name",form.value.first_name);
            formData.append("last_name",form.value.last_name);
            if(form.value.email){
                formData.append("email",form.value.email);
            }
            formData.append("phone",form.value.phone);
            formData.append("address",form.value.address);
            if(form.value.city){
                formData.append("city",form.value.city);
            }
            formData.append("emergency_contact",form.value.emergency_contact);
            formData.append("type_document",form.value.type_document);
            formData.append("n_document",form.value.n_document);
            formData.append("imagen",FILE_AVATAR.value);
            const resp =  await $api('/pets',{
                method: 'POST',
                body:formData,
                onResponseError({response}){
                    console.log(response);
                    error_exists.value = response._data.error;
                }
            })
            console.log(resp);
            success.value = "La mascota se ha creado correctamente";
            setTimeout(() => {
                success.value = null;
                warning.value = null;
                error_exists.value = null;
                fieldsClean()
            }, 1500);

        } catch (error) {
            console.log(error);
        }
    }

    definePage({
        meta: {
            permisssion: 'register_pet'
        },
    })
</script>
<template>
    <div>
        <!-- <VCard class="refer-and-earn-dialog pa-3 pa-sm-11"> -->
            <VCardText class="pa-5">
                <div class="mb-1">
                <h4 class="text-h4 text-center mb-1">
                    Add Pet 🐶
                </h4>
                </div>
            </VCardText>

            <VCard title="🐕‍🦺 Mascota:" class="pa-4">
                <VRow>
                    <VCol cols="6">
                        <VTextField
                        label="Nombre"
                        v-model="form.name"
                        placeholder="Example: Boby"
                        />
                    </VCol>
                    <VCol cols="3">
                        <VSelect
                            :items="species"
                            v-model="form.specie"
                            label="Especie"
                            item-title="name"
                            item-value="id"
                            placeholder="Select Rol"
                            eager
                        />
                    </VCol>
                    <VCol cols="3">
                        <VTextField
                        label="Raza"
                        v-model="form.breed"
                        placeholder="Example: Pastor Aleman"
                        />
                    </VCol>
                    <VCol cols="3">
                        <!-- <AppDateTimePicker
                            v-model="form.dirth_date"
                            label="Fecha de nacimiento"
                            placeholder="Select date"
                        /> -->
                        <label for="">Fecha de nacimiento</label>
                        <div class="app-picker-field">
                            <div class="v-input v-input--horizontal v-input--center-affix v-input--density-comfortable v-locale--is-ltr position-relative v-text-field">
                                <div class="v-input__control">
                                    <div class="v-field v-field--center-affix v-field--variant-outlined v-theme--light v-locale--is-ltr">
                                        <div class="v-field__field">
                                            <div class="v-field__input">
                                                <input type="date" class="flat-picker-custom-style flatpickr-input" v-model="form.dirth_date" style="opacity: 1;"  id="">
                                            </div>
                                        </div>
                                        <div class="v-field__outline text-primary"><div class="v-field__outline__start"></div><div class="v-field__outline__notch"><label class="v-label v-field-			label v-field-label--floating" aria-hidden="true" for="input-8" style="">Nombre</label></div><div class="v-field__outline__end"></div></div>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </VCol>
                    <VCol cols="3">
                        <VRadioGroup
                            v-model="form.gender"
                            inline
                        >
                            <VRadio
                            label="Macho"
                            value="M"
                            />
                            <VRadio
                            label="Hembra"
                            value="H"
                            />
                        </VRadioGroup>
                    </VCol>
                    <VCol cols="4">
                        <VTextarea
                            v-model="form.color"
                            label="Color"
                            placeholder="Text"
                        />
                    </VCol>

                    <VCol cols="3">
                        <VTextField
                        label="Peso"
                        type="number"
                        v-model="form.weight"
                        placeholder="Example: 20 KG"
                        />
                    </VCol>
                    <VCol cols="4">
                        <VFileInput label="File input" @change="loadFile($event)" />
                    </VCol>
                    <VCol cols="3">
                        <VImg
                        v-if="IMAGEN_PREVIZUALIZA"
                        width="237"
                        height="276"
                        :src="IMAGEN_PREVIZUALIZA"
                        />
                    </VCol>

                    <VCol cols="4">
                        <VTextarea
                            v-model="form.medical_notes"
                            label="Nota Medica"
                            placeholder="Text"
                        />
                    </VCol>

                </VRow>
            </VCard>

            <VAlert type="warning" class="mt-3" v-if="warning">
            <strong>{{ warning }}</strong>
            </VAlert>
            <VAlert type="error" class="mt-3" v-if="error_exists">
            <strong>En el servidor hubo un error al guardar</strong>
            </VAlert>
            <VAlert type="success" class="mt-3" v-if="success">
            <strong>{{ success }}</strong>
            </VAlert>

            <VCard title="🧔 Responsable:" class="my-2 pa-4">
                <VRow>
                    <!-- 1era fila  -->
                    <VCol cols="6">
                        <VTextField
                        label="Nombre:"
                        v-model="form.first_name"
                        placeholder="Example: Rafael"
                        />
                    </VCol>
                    <VCol cols="6">
                        <VTextField
                        label="Apellido:"
                        v-model="form.last_name"
                        placeholder="Example: Mendoza"
                        />
                    </VCol>
                    <!--  -->

                    <!-- 2da fila  -->
                    <VCol cols="4">
                        <VTextField
                        v-model="form.email"
                        label="Email"
                        type="email"
                        placeholder="johndoe@email.com"
                        />
                    </VCol>
                    <VCol cols="3">
                        <VTextField
                        label="Telefono:"
                        type="number"
                        v-model="form.phone"
                        placeholder="Example: 99999999"
                        />
                    </VCol>
                    <VCol cols="5"></VCol>
                    <!--  -->

                    <!-- 3ra Fila -->
                    <VCol cols="5">
                        <VTextField
                        label="Dirección:"
                        v-model="form.address"
                        placeholder="Example: Calle o Mzt"
                        />
                    </VCol>
                    <VCol cols="3">
                        <VTextField
                        label="Ciudad:"
                        v-model="form.city"
                        placeholder="Example:  Lima"
                        />
                    </VCol>
                    <VCol cols="4"></VCol>
                    <!--  -->

                    <!-- 4ra Fila -->
                    <VCol cols="3">
                        <VSelect
                            :items="type_documents"
                            v-model="form.type_document"
                            label="Tipo de documento"
                            placeholder="Select Item"
                            eager
                        />
                    </VCol>
                    <VCol cols="3">
                        <VTextField
                        label="N° de documento:"
                        type="number"
                        v-model="form.n_document"
                        placeholder="Example: Mendoza"
                        />
                    </VCol>

                    <VCol cols="5">
                        <VTextarea
                            v-model="form.emergency_contact"
                            label="Contacto de emergencia"
                            placeholder="Text"
                        />
                    </VCol>

                </VRow>
                
            </VCard>

            <VCardText class="pa-5 py-0 text-end">
                <VBtn color="primary" class="mb-4" @click="store()">
                Crear
                </VBtn>
            </VCardText>
        <!-- </VCard> -->

    </div>
</template>
<style>
.v-img__img--contain {
    object-fit: cover !important;
}
</style>