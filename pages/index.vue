<template>
    <div class="hero min-h-screen">
        <div class="hero-content text-center" v-if="currentQuestion === 0">
            <div class="max-w-md">
                <h1 class="text-3xl font-bold">Test di smistamento!</h1>
                <p class="py-6">
                    Il compito del Cappello Parlante è assai arduo e ricco di responsabilità. Rispondi sinceramente alle
                    prossime domande senza cercare di forzarne la scelta e scopri in quale delle quattro Case di
                    Hogwarts sarai smistato.
                </p>
                <button class="btn btn-primary" @click="currentQuestion++">Iniziamo</button>
            </div>
        </div>

        <div class="card card-border bg-base-100 max-w-xl shadow-sm "
            v-if="currentQuestion > 0 && currentQuestion <= domande.length">
            <div class="card-body">
                <div class="text-end">
                    <span class="badge justify-self-end">{{ currentQuestion }}/{{ domande.length }}</span>
                </div>

                <h2 class="card-title mb-3" v-html="domande[currentQuestion - 1].question"></h2>
                <div v-for="(answer, index) in domande[currentQuestion - 1].answers" :key="index" class=" w-full">
                    <label class="relative ml-8 block       ">
                        <input type="radio" name="radio" class="radio radio-sm absolute -left-8 top-1"
                            v-model="userAnswers[currentQuestion - 1]" :value="answer" />
                        {{ answer.text }}
                    </label>
                </div>
                <div class="card-actions justify-center mt-3">
                    <button class="btn btn-secondary" @click="previousQuestion" :disabled="currentQuestion === 1">
                        <svg xmlns="http://www.w3.org/2000/svg" fill="currentColor" viewBox="0 0 24 24"
                            class="size-[1.2em]">
                            <path
                                d="M12 2C17.52 2 22 6.48 22 12C22 17.52 17.52 22 12 22C6.48 22 2 17.52 2 12C2 6.48 6.48 2 12 2ZM12 20C16.42 20 20 16.42 20 12C20 7.58 16.42 4 12 4C7.58 4 4 7.58 4 12C4 16.42 7.58 20 12 20ZM12 11H16V13H12V16L8 12L12 8V11Z">
                            </path>
                        </svg>
                        Torna indietro</button>
                    <button class="btn btn-primary" @click="nextQuestion"
                        :disabled="!userAnswers[currentQuestion - 1]">Rispondi
                        <svg xmlns="http://www.w3.org/2000/svg" fill="currentColor" viewBox="0 0 24 24"
                            class="size-[1.2em]">
                            <path
                                d="M12 11V8L16 12L12 16V13H8V11H12ZM12 2C17.52 2 22 6.48 22 12C22 17.52 17.52 22 12 22C6.48 22 2 17.52 2 12C2 6.48 6.48 2 12 2ZM12 20C16.42 20 20 16.42 20 12C20 7.58 16.42 4 12 4C7.58 4 4 7.58 4 12C4 16.42 7.58 20 12 20Z">
                            </path>
                        </svg></button>

                </div>
            </div>
        </div>


        <!-- Mostra i punteggi finali dopo l'ultima domanda -->
        <div class="card card-border bg-base-100 max-w-xl shadow-sm " v-if="currentQuestion > domande.length">
            <div class="card-body">

                <div v-for="(casa, index) in casate" :key="index">
                    <div v-if="casata(scores) === casa.name">
                        <div class="card-title" v-html="casa.label">
                        </div>
                        {{ casa.descrizione }}
                    </div>
                </div>

                <div class="collapse bg-base-100 border-base-300 border">
                    <input type="checkbox" />
                    <div class="collapse-title font-semibold flex flex-start"><span class="me-3">stats</span>
                        <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor"
                            class="size-[1.2em]">
                            <path
                                d="M2 13H8V21H2V13ZM16 8H22V21H16V8ZM9 3H15V21H9V3ZM4 15V19H6V15H4ZM11 5V19H13V5H11ZM18 10V19H20V10H18Z">
                            </path>
                        </svg>
                    </div>
                    <div class="collapse-content text-sm">
                        <div v-for="houses in housePercentage" :key="houses.house">
                            <progress class="progress w-56" :value="houses.percentage" max="100"></progress>
                            {{ houses.percentage }}%
                            ({{ houses.score }}) {{ houses.house }}
                        </div>
                    </div>
                </div>


                <div class="card-actions justify-center mt-3">
                    <button class="btn btn-accent" @click="reset">Ricomincia
                        <svg xmlns="http://www.w3.org/2000/svg" fill="currentColor" viewBox="0 0 24 24"
                            class="size-[1.2em]">
                            <path
                                d="M22 12C22 17.5228 17.5229 22 12 22C6.4772 22 2 17.5228 2 12C2 6.47715 6.4772 2 12 2V4C7.5817 4 4 7.58172 4 12C4 16.4183 7.5817 20 12 20C16.4183 20 20 16.4183 20 12C20 9.25022 18.6127 6.82447 16.4998 5.38451L16.5 8H14.5V2L20.5 2V4L18.0008 3.99989C20.4293 5.82434 22 8.72873 22 12Z">
                            </path>
                        </svg></button>
                </div>
            </div>

            <div>
            </div>
        </div>
        <footer
            class="footer sm:footer-horizontal footer-center bg-base-300 text-base-content p-4 absolute bottom-0 w-full">
            <aside>
                <small>
                    <NuxtLink to="https://github.com/enricoopezzo/smistamento" target="_blank" class="link">codice
                    </NuxtLink> - smistamento
                    sviluppato da
                    <NuxtLink to="https://www.instagram.com/mereciao/" target="_blank" class="link">Lorenzo</NuxtLink>
                </small>
            </aside>
        </footer>
    </div>




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

const reset = () => {
    currentQuestion.value = 0;
    userAnswers.value = Array(domande.length).fill(null);
    scores.value = {
        gryffindor: 0,
        hufflepuff: 0,
        ravenclaw: 0,
        slytherin: 0
    };
};

const casata = (scores) => {
    let max = Math.max(...Object.values(scores));
    console.log(max);
    return Object.keys(scores).find((key) => scores[key] === max); 
};

const totalScore = computed(() => {
    return Object.values(scores.value).reduce((acc, score) => acc + Math.max(score, 0), 0);
});

const housePercentage = computed(() => {
    return Object.keys(scores.value)
        .map((house) => {
            const positiveScore = Math.max(scores.value[house], 0);
            return {
                house,
                score: scores.value[house], // Mantiene il punteggio originale
                percentage: totalScore.value > 0 ? Math.round((positiveScore / totalScore.value) * 100) : 0
            };
        })
        .sort((a, b) => b.score - a.score); // Ordina i punteggi dal più alto al più basso
});

</script>
