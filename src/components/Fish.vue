<template>
    <div class="container-md w-50">
        <h1 class="text-center pb-3">Is this fish locked in or tweaking?</h1>

        <div class="text-center">
            <h3>Progress: {{ counterIndex }} / {{ imageCount }}</h3>
        </div>

        <div v-if="counterIndex < imageCount">
            <div class="row">
                <div class="pt-3">
                    <div class="img card shadow-lg w-100">
                        <img :src="currentFishUrl" alt="Fish Image" />
                    </div>
                </div>
            </div>

            <div class="row">
                <div class="col-md-6 pt-5">
                    <button class="btn btn-primary w-100" @click="checkAnswer(true)">Locked In</button>
                </div>
                <div class="col-md-6 pt-5">
                    <button class="btn btn-primary w-100" @click="checkAnswer(false)">Tweaking</button>
                </div>
            </div>

            <div class="row">
                <div class="text-center pt-5">
                    <div v-if="showResult" :class="{ 'alert-success': correct, 'alert-danger': !correct }"
                        class="card alert d-flex">
                        <h1>{{ resultMessage }}</h1>
                        <div class="flex-grow-1">
                            <h2 class="ml-3">{{ isLockedIn ? 'That fish is LOCKED IN' : 'That fish is TWEAKING' }}</h2>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <div v-else>
            <div class="text-center">
                <div class="alert alert-success mt-5">
                    <h3>Game Complete</h3>
                    <h4>You scored {{ correctCount }} / {{ imageCount }}</h4>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    data() {
        return {
            imageCount: 25,
            currentFishIndex: 1,
            counterIndex: 0,
            showResult: false,
            resultMessage: '',
            correct: false,
            correctCount: 0,
            isLockedIn: false,
            areButtonsDisabled: false,
            userAnswers: {},
            fishList: []
        };
    },
    created() {
        for (let i = 1; i <= this.imageCount; i++) {
            this.fishList.push({
                index: i,
                lockedIn: i === 1 // Example: Fish 1 is locked in, others are not
            });
        }
        this.loadNextFish();
    },
    computed: {
        currentFishUrl() {
            return (`./src/assets/Fish/Fish${this.currentFishIndex}.jpg`);
        }
    },
    methods: {
        loadNextFish() {
            const remainingFish = this.fishList.filter(fish => !this.userAnswers[fish.index]);
            if (remainingFish.length > 0) {
                const randomIndex = Math.floor(Math.random() * remainingFish.length);
                this.currentFishIndex = remainingFish[randomIndex].index;
                this.showResult = false;
                this.resultMessage = '';
            } else {
                this.userAnswers = {};
            }
        },
        async checkAnswer(userAnswer) {
            if (this.areButtonsDisabled) return;
            this.areButtonsDisabled = true;

            const correctAnswer = this.fishList.find(fish => fish.index === this.currentFishIndex).lockedIn;
            this.userAnswers[this.currentFishIndex] = userAnswer;
            this.isLockedIn = userAnswer;

            if (userAnswer === correctAnswer) {
                this.resultMessage = 'Correct';
                this.correct = true;
                this.correctCount++;
            } else {
                this.resultMessage = 'Incorrect';
                this.correct = false;
            }

            this.showResult = true;
            await new Promise(resolve => setTimeout(resolve, 2000));

            this.counterIndex++;

            if (this.counterIndex <= this.imageCount) {
                this.loadNextFish();
            }

            this.areButtonsDisabled = false;
        }
    }
};
</script>

