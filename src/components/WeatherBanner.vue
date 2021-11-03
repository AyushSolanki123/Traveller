<template>
  
      <div class="banner-container banner bg-primary" v-if="showBanner">
            <q-banner class=" bg-grey-3 q-mb-md">
              <span class="text-bold text-h6">The weather in {{search}} is {{weatherInfo.day.condition.text}} today</span>
              <template v-slot:avatar>
                <img
                  :src="`${weatherInfo.day.condition.icon}`"
                  style="width: 80px; height: 64px"
                >
              </template>

              <template v-slot:action>
                <q-btn
                  color="primary"
                  font-size="22px"
                  flat
                  label="Ok"
                  text-color="grey-10"
                  @click="$emit('closeBanner')" />
              </template>
            </q-banner>
      </div>
    
</template>

<script>
export default {
    props:{
        showBanner:{
            type: Boolean,
            default: false
        },
        weatherInfo:{
            type: Object,
            default: null
        },
        search:{
            type: String,
            default: "Nagpur"
        }
    }
}
</script>

<style>

</style>