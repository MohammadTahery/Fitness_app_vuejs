<script setup>
    import Grid from '../Grid.vue';
    import { gymHealthFacts } from '../../utils';

    const { handleSelectWorkout, firstIncompleteWorkoutIndex } = defineProps({
        handleSelectWorkout: Function,
        firstIncompleteWorkoutIndex: Number
    });

    // generate a random whole integer number between 0 and array length -1 
    const randomNumber = Math.floor(Math.random() * gymHealthFacts.length);
    const todayFact = gymHealthFacts[randomNumber];

</script>

<template>
    <section id="dashboard">
        <div class="card tip-container">
            <h2>Welcome Smoldier</h2>
            <div>
                <p class="tip"><strong>Daily Tip</strong><br/>{{todayFact}}</p>
            </div>
            <button @click="handleSelectWorkout(firstIncompleteWorkoutIndex < 0 ? 0 : firstIncompleteWorkoutIndex)">Start workout &rarr;</button>
        </div>
        <Grid :handleSelectWorkout="handleSelectWorkout" :firstIncompleteWorkoutIndex="firstIncompleteWorkoutIndex"/>
    </section>
</template>

<style scoped>
    .tip-container,
    .tip-container div,
    #dashboard {
        display: flex;
    }

    .tip-container,
    #dashboard {
        flex-direction:column ;
    }

    #dashboard {
        gap: 2rem;
    }

    .tip-container {
        gap: 0.5rem;
    }

    @media (min-width: 640px) {
        .tip-container {
            gap: 1rem;
        }
    }
</style>

