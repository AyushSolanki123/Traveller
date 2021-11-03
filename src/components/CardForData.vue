<template>
  <q-dialog v-model="showRoute">
    <q-card class="mycard">
      <q-card-section>
        <div class="text-h6">
          Suggestions for Day According to the distance .
        </div>
      </q-card-section>

      <q-card-section class="q-mx-xl">
        <!-- <div class="q-mx-md">
          <q-badge rounded color="black" />
        </div> -->

        <!-- 
        <div class="row">
          <div class="col bg-red-4 q-mt-lg">
            <div class="row q-col-gutter-lg">
              <div
                class="semi-transparent col-6"
                :class="`bg-green-${n + 1}`"
                v-for="n in 5"
                :key="n"
              >
                Child
              </div>
            </div>
          </div>
        </div> -->

        <div>
          <span class="q-pa-md text-caption">Suggested Hotel</span>
          <div class="row">
            
            <q-avatar color="primary" text-color="white">1</q-avatar>
            <span class="text-subtitle1 q-mx-md q-pa-sm">{{suggestedRoute.hotel.result_object.name}}</span>
          </div>
          <div class="q-pa-sm">
            <q-separator size="5px"/>
            <span>{{suggestedRoute.hotel.result_object.time_restaurant}}</span>
          </div>
          
            <span class="q-pa-md text-caption">Suggested Restaurant</span>
          <div class="row">
              <q-avatar color="primary" text-color="white">2</q-avatar>
              <span class="text-subtitle1 q-mx-md q-pa-sm">{{suggestedRoute.restaurant.restaurant.result_object.name}}</span>
            </div>
            <div class="q-pa-sm">
            <q-separator size="5px"/>
          </div>
              <span class="q-pa-md text-caption">Suggested Things to do</span>
            <div class="row">
              <q-avatar color="primary" text-color="white">3</q-avatar>
              <span class="text-subtitle1 q-mx-md q-pa-sm">{{suggestedRoute.attraction.attraction.result_object.name}}</span>
            </div>
        </div>
      </q-card-section>

      <q-card-actions align="right">
        <q-btn
          flat
          label="OK"
          color="primary"
          @click="$emit('closeDialog')" />
      </q-card-actions>
    </q-card>
  </q-dialog>
</template>

<script>
export default {
  props: {
    showRoute: {
      type: Boolean,
      default: false,
    },
    suggestedRoute: {
      type: Object,
      default: null,
    },
  },
};
</script>

<style>
.mycard {
  width: 60vw;
}
</style>