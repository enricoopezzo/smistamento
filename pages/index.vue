<template>
    <div class="hero min-h-screen">
        <div class="hero-content flex-col lg:flex-row" v-if="currentQuestion === 0">
            <img src="https://img.daisyui.com/images/stock/photo-1635805737707-575885ab0820.webp"
                class="max-w-sm rounded-lg shadow-2xl" />
            <div>
                <h1 class="text-5xl font-bold">Smistamento!</h1>
                <p class="py-6">
                    Il compito del Cappello Parlante è assai arduo e ricco di responsabilità. Rispondi sinceramente alle
                    prossime domande senza cercare di forzarne la scelta e scopri in quale delle quattro Case di
                    Hogwarts sarai smistato.
                </p>
                <button class="btn btn-primary" @click="currentQuestion++">Iniziamo</button>
            </div>
        </div>

        <div class="hero-content flex-col" v-if="currentQuestion > 0 && currentQuestion <= domande.length">
            <div>
                <h2>{{ currentQuestion }}/{{ domande.length }}</h2>
                <h2 class="text-xl font-bold">{{ domande[currentQuestion - 1].question }}</h2>
                <div v-for="(answer, index) in domande[currentQuestion - 1].answers" :key="index" class=" w-full">
                    <label>
                        <input type="radio" name="radio" class="radio" v-model="userAnswers[currentQuestion - 1]"
                            :value="answer" />
                        {{ answer.text }}
                    </label>
                </div>
            </div>

            <div class="flex justify-between w-full">
                <button class="btn btn-secondary" @click="previousQuestion"
                    :disabled="currentQuestion === 1">Previous</button>
                <button class="btn btn-primary" @click="nextQuestion"
                    :disabled="!userAnswers[currentQuestion - 1]">Next</button>
            </div>
        </div>

        <!-- Mostra i punteggi finali dopo l'ultima domanda -->
        <div class="hero-content flex-col" v-if="currentQuestion > domande.length">
            <h2 class="text-3xl font-bold">Risultati finali:</h2>
            <div>
                {{ casata(scores) }}
                     {{ casate }}

            </div>
            <p>Gryffindor: {{ scores.gryffindor }}</p>
            <p>Slytherin: {{ scores.slytherin }}</p>
            <p>Ravenclaw: {{ scores.ravenclaw }}</p>
            <p>Hufflepuff: {{ scores.hufflepuff }}</p>
            <p v-if="casata(scores)">
                {{ casata(scores) }}
            </p>
        </div>
    </div>
    {{scores}}
</template>

<script setup>
import model from "~/assets/models/model.json";
import houses from "~/assets/models/houses.json";

const domande = model.questions;
const currentQuestion = ref(0);
const scores = ref({
    gryffindor: 0,
    hufflepuff: 0,
    ravenclaw: 0,
    slytherin: 0
});

const casate = houses.casate;
const userAnswers = ref(Array(domande.length).fill(null)); // Memorizza le risposte selezionate

const nextQuestion = () => {
    const selectedAnswer = userAnswers.value[currentQuestion.value - 1];

    if (selectedAnswer) {
        // Aggiunge i punteggi della risposta selezionata
        Object.keys(selectedAnswer.score).forEach((house) => {
            scores.value[house] += selectedAnswer.score[house];
        });
    }

    currentQuestion.value++;
};

const previousQuestion = () => {
    if (currentQuestion.value > 1) {
        const previousAnswer = userAnswers.value[currentQuestion.value - 2];

        if (previousAnswer) {
            // Sottrae i punteggi della risposta precedente
            Object.keys(previousAnswer.score).forEach((house) => {
                scores.value[house] -= previousAnswer.score[house];
            });
        }

        currentQuestion.value--;
    }
};

const casata = (scores) => {
    let max = Math.max(...Object.values(scores));
    console.log(max);
    return Object.keys(scores).find((key) => scores[key] === max);
    
};


</script>
