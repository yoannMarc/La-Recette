<script lang="ts" setup>
    import {ref, watch} from 'vue'

    const options = ref([
        { text: 'One', value: 'A' },
        { text: 'Two', value: 'B' },
        { text: 'Three', value: 'C' }
    ])

    const TestSelect = ref("")

    //WATCHER EXEMPLE :
    const nom = ref("")
    const answer = ref("")
    //FUNCTION WITH ASYNC BEHAVIOR
    
    watch(nom, async (newQuestion) => {
    if (newQuestion.indexOf('?') > -1) {
        answer.value = 'Thinking...'
        try {
        const res = await fetch('https://yesno.wtf/api')
        answer.value = (await res.json()).answer
        } catch (error) {
        answer.value = 'Error! Could not reach the API. ' + error
        }
    }})
    
    
    // const wordList = ref('')
   
    // //Initialise une liste de nom
    // try {
    //     const res = await fetch('https://httpip.es/api/words?letters=a,r,s,t,n,e,i,o')
    //     wordList.value = (await res.json()).data
    //     console.log(wordList.value)
    // } catch(error) {
    //     console.log('erreur')
    // }

    // //
    // function pickRandomWord() {
    //     if (wordList.value.length > -1) {
    //         const rndInt = Math.floor(Math.random() * wordList.value.length) + 1
    //         return wordList.value[rndInt]
    //     }
    // }

    // console.log(pickRandomWord())

</script>

<template>
    <div class="container">
        <div>

        </div>
        <label for="watcher"> Watcher exemple : {{ answer }} </label>
        <input v-model="nom" type="text" id="watcher" name="watcher" placeholder="test" />
       
        <select name="liste" id="liste" v-model="TestSelect">
            <option disabled value="">Fait-on choix</option>
            <option v-for="option in options" :value="option.value" >{{ option.text }}</option>
        </select>
    </div>
</template>


<style scoped>
.container {
    display: flex;
    flex-direction: column;
}

.container > * {
    margin: 20px;
}
</style>