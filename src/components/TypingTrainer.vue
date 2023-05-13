<script setup>
   
    import {ref, reactive, computed, watch} from 'vue'

    
    // WordGenerator = new WordGenerator()
    // await WordGenerator.generateTree()


    const Words =  reactive({
        done:'',
        error:'',
        todo:''
    })
    
    const wordsDone = ref('')
    const errorsList = ref('')
    const wordsTodo = ref('')
    const input = ref('')
    const setDifficulty = ref('')
    const isLoading = ref(true)
    
    let allWords = []
    let wordTab = []
    let newWord = ''

 
    //Initialise une liste de nom
    function loadData(letters = 'arstneio' ) {
        
        isLoading.value = true
       
        const testRegex = new RegExp('\\b['+letters+']+\\b')

        if (allWords.length == 0) {
            fetch('https://random-word-api.herokuapp.com/all') 
            .then((res) => res.json())
            .then((json) => {
                if (json) {
                    allWords = json
                    wordTab = json.filter( (a) => testRegex.test(a))
                    console.log('LOADED !')
                    isLoading.value = false 
                }   
            })
            .catch((err) => console.log('Erreur : '+ err))
        } else {
           console.log('COMPUTED !')
            wordTab = allWords.filter( (a) => testRegex.test(a))
            isLoading.value = false 
        }

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

    watch(setDifficulty,() => {
        console.log('je change de difficulté')
        loadData(setDifficulty.value)
    })

    

</script>


<template>
    <div class="todo">
        <h4 style="color: green;">DONE</h4>
        <ul>
            <li style="color: green;">Optimisation de l'API de recherche de Mot + filtrage par regex... </li>
            <li style="color: green;">Proposer une liste de choix de difficulité (=> 5 niveaux) avec rechargement de la base</li>
            <li style="color: green;">Stocker la base en cache pour ne pas la recharger en cas de modification</li>
        </ul>
        <h4>TODO :</h4>
        <ul>
            <li>Améliorer le CSS</li>           
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
        <p v-else>{{ wordTab.length }} chargés !</p>
        <p>
            <span class="done">{{ wordsDone  }}</span>
            <span v-if="errorsList" class="error">{{ errorsList }}</span>
            <span class="">{{ wordsTodo }}</span></p>
        <p>{{ input }}</p>
        <div>
            <input type="button" value="test" @click="SetWordsTodo">
            <input type="text"  placeholder="saisie ici !" v-model="input">
            <select name="difficulty" id="difficulty" v-model="setDifficulty">
                <option value="arstneio">Niveau 1</option>
                <option value="arstneiogm">Niveau 2</option>
                <option value="arstneiogmbljp">Niveau 3</option>
                <option value="arstneiogmbljpvkhd">Niveau 4</option>
                <option value="a-z">Niveau 5</option>
            </select>
         </div>
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