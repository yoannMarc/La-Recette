<script setup>
import { computed } from '@vue/reactivity';
import { reactive,  } from 'vue';

const state = reactive({ count:0})
const obj = reactive({
        nest: {count:0},
        arr:['foo','bar']})

function inc() {
    state.count++
}

function mutateDeeply(){
    obj.nest.count++
    obj.arr.push('baz')
}

const author = reactive({
    name: 'John Doe',
    books : [
        'Book 1',
        'Book 2',
        'Book 3'
    ]
})
//A computed ref (une référence calculées quoi)
//Utile car la données est mis en cache une fois la calculation effectuée
//La methode est appellée uniquement si la données "reactive/ref" 
const publishedBookMessage = computed(() => {
    return author.books.length > 0 ? 'Yes' : 'No'
})
const test = 2
const publishedBookMessagePlusOne = computed(() => {
    return 2 + test
})


</script>

<template>

    <div id="ComputedExemple">
        <p>Has published book :</p>
        <span>{{ publishedBookMessage }}</span><br>
        <span>{{ publishedBookMessagePlusOne }}</span>
    </div>
    <div>
       <button @click="mutateDeeply">
        test
        {{ obj.nest.count }}
        </button>    
    </div>
 
</template>


<style  scoped>

</style>