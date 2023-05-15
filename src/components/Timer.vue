<template>
    <div ref="container" class="container">
        <div v-if="interval"  class="progressBar" :style="{ width: widthPercent + 'px' }"></div>
        <!-- <button @click="interval ? stopTimer() : startTimer()"> {{ interval ?  "stop" : "start"  }}</button> -->
        <p> {{ timer }} </p>
    </div>
</template>

<script lang="ts" setup>

import { ref, watch, computed } from 'vue'

const props = defineProps({
    duration: Number
})

const emit = defineEmits(['getDateInterval'])
const timer = ref(0)
const interval = ref(0)
const container = ref()


let dateInterval = {
    startDate: 0,
    endDate:0
}



let Calcduration = (props.duration) ? props.duration * 100 : 0

const widthPercent = computed(() => {
    return timerPercent.value  * container.value.offsetWidth
})

const timerPercent = computed(() => {
    return (timer.value / Calcduration) 
})



const startTimer = () => {
    timer.value = 0
    dateInterval.startDate = Date.now()
    dateInterval.endDate = 0
    
    interval.value = setInterval(() => {
        timer.value++
    },10)

}

const endTimer = () => {
    console.log('je termine')
    if (interval.value) clearInterval(interval.value)
    dateInterval.endDate = Date.now()
    interval.value = 0
    emit('getDateInterval',dateInterval)
}

watch(timer,() => {
    // console.log(timer.value+' et duration '+duration)
    if (timer.value == Number(Calcduration)) {
        console.log('je suis égale')
        endTimer()
    }

})


defineExpose({
    startTimer,
    endTimer
})
</script>

<style scoped>
.container {
    border: 1px red solid;
}

.progressBar {
    border: 1px blue solid;
    background-color: blue;
    height:1vh;
}
</style>