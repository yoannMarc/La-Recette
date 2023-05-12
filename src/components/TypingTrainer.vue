<script setup>
   
   import {ref, reactive, computed, watch} from 'vue'
    
    const Words =  reactive({
        done:'',
        error:'',
        todo:''
    })
    
    const wordsDone = ref('')
    const errorsList = ref('')
    const wordsTodo = ref('')
    const input = ref('')
    
    const isLoading = ref(true)

    const nbWords = computed(() => {
        return  wordTab.length
    })

    let wordTab = []
    let newWord = ''

 
    //Initialise une liste de nom
    function loadData(letters = 'a,r,s,t,n,e,i,o') {
        fetch('https://httpip.es/api/words?letters='+letters)
        .then((res) => res.json())
        .then((json) => {
            wordTab = json.data.filter(s => s.length > 4)
            console.log('base chargée !')
            isLoading.value = false
        })
        .catch((err) => console.log('Erreur : '+ err))

    }

    loadData()

    function pickRandomWord() {
        if (wordTab.length > -1) {
            const rndInt = Math.floor(Math.random() * wordTab.length) + 1
            return wordTab[rndInt]
        }
    }

    function SetWordsTodo() {
        console.log('je passe pas la fonction !')
        if (wordTab.length > -1) {
            let newWordsList = ''
            for (let i = 0; i < 5; i++) {
                //if (newWordsList) newWordsList += ' '
                newWordsList += ' ' + pickRandomWord() 
            }
            wordsTodo.value = newWordsList.trim()
        }
    }

    function removeFirstChar(value) {
        console.log('je supprimer le premier caractère de : '+value)
        return value.substring(1,value.length)
    }


    watch(input,() => {
           
        if(input.value == wordsTodo.value.charAt(0)) {   
            wordsDone.value += input.value
            if (!newWord) newWord += ' ' + pickRandomWord()
            if (wordsDone.value.length > 5) wordsDone.value = removeFirstChar(wordsDone.value)
            errorsList.value = '' 
            wordsTodo.value = removeFirstChar(wordsTodo.value) + newWord.charAt(0)
            newWord = removeFirstChar(newWord)

        } else {
            if (input.value === ' ') errorsList.value += '_'
            else errorsList.value += input.value
            if (errorsList.value.length > 10) errorsList.value = removeFirstChar(errorsList.value)
        }
        
        input.value = ''
    })

    

</script>


<template>
    <div class="todo">
        <h4>TODO :</h4>
        <ul>
            <li>Améliorer le CSS</li>           
            <li>Proposer une liste de choix de difficulité (=> 5 niveaux) avec rechargement de la base</li>
            <li>Stocker la base en cache pour ne pas la recharger en cas de modification</li>
            <li>Faire un peu de reporting :</li>
            <ul>
                <li>Lettres /min</li>
                <li>Mots par minutes ! (compter les espaces)</li>
                <li>Graph du nombre de fautes vs nombres de réussite (batons)</li>
                <li>Graph de la vitesse de saisie / min !! (courbe)</li>
            </ul>
        </ul>
    </div>
    <div class="trainer">
        <p v-if="isLoading">Loading . . . </p>
        <p v-else>{{ nbWords }} chargés !</p>
        <p>
            <span class="done">{{ wordsDone  }}</span>
            <span v-if="errorsList" class="error">{{ errorsList }}</span>
            <span class="">{{ wordsTodo }}</span></p>
        <p>{{ input }}</p>
        <div>
            <input type="button" value="test" @click="SetWordsTodo">
            <input type="text"  placeholder="saisie ici !" v-model="input">
         </div>
        <!-- <select name="typer" id="typer" @select="SetWordsTodo()">
            <option disabled value="">Fait-on choix</option>
            <option>Selectionne moi !</option>
            <option>Selectionne moi !</option>
        </select> -->
    </div>
</template>

<style scoped>
.error  {
    color:red
}

.trainer {
    display: flex;
    flex-direction: column;
    align-items: center;
}

.done  {
    color:cyan
}

.todo {
    margin-bottom: 40px;
}
</style>