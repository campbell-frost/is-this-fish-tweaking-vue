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
            fishList: [
                { index: 1, lockedIn: true },
                { index: 2, lockedIn: false },
                { index: 3, lockedIn: false },
                { index: 4, lockedIn: true },
                { index: 5, lockedIn: false },
                { index: 6, lockedIn: true },
                { index: 7, lockedIn: false },
                { index: 8, lockedIn: true },
                { index: 9, lockedIn: true },
                { index: 10, lockedIn: true },
                { index: 11, lockedIn: true },
                { index: 12, lockedIn: true },
                { index: 13, lockedIn: false },
                { index: 14, lockedIn: true },
                { index: 15, lockedIn: true },
                { index: 16, lockedIn: true },
                { index: 17, lockedIn: false },
                { index: 18, lockedIn: true },
                { index: 19, lockedIn: false },
                { index: 20, lockedIn: false },
                { index: 21, lockedIn: false },
                { index: 22, lockedIn: false },
                { index: 23, lockedIn: false },
                { index: 24, lockedIn: false },
                { index: 25, lockedIn: false }
            ]
        };
    },
    created() {
        for (let i = 1; i <= this.imageCount; i++) {
            this.fishList.push({
                index: i,
                lockedIn: i === 1
            });
        }
        this.loadNextFish();
    },
    computed: {
        currentFishUrl() {
            return (`/Fish/fish${this.currentFishIndex}.jpg`);
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
            const currentFish = this.fishList.find(fish => fish.index === this.currentFishIndex);
            const isLockedIn = currentFish.lockedIn;
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

