<script setup>
import Welcome from './components/pages/Welcome.vue';
import Layout from './components/layout/Layout.vue';
import Dashboard from './components/pages/Dashboard.vue';
import Workout from './components/pages/Workout.vue';
import { computed, onMounted, ref } from 'vue';
import { workoutProgram } from './utils';

const defaultData = {};
for (let workoutIdx in workoutProgram) {
    const workoutData = workoutProgram[workoutIdx]
    //create a for loop where we iterate over every workout
    defaultData[workoutIdx] = {} // initialize the workout data obj

    // nested loop to initialize warmup exercises
    for (let e of workoutData.warmup) {
        defaultData[workoutIdx][e.name] = ''
    }

    // nested loop to loop over every exercise within a workout, and initialize it's input value to an empty string
    for (let e of workoutData.workout) {
        defaultData[workoutIdx][e.name] = ''
    }
}

const selectDisplay = ref(1);
const data = ref(defaultData);
const selectWorkout = ref(-1);

const isWorkoutComplete = computed(()=> {
    const currWorkout = data.value?.[selectWorkout.value]
    if (!currWorkout) {return false} // guard close to exit function

    const isCompleteCheck = Object.values(currWorkout).every(ex=> !!ex)
    console.log('ISCOMPLETE: ', isCompleteCheck)
    return isCompleteCheck
});

const firstIncompleteWorkoutIndex = computed(()=> {
    const allWorkouts = data.value
    if (!allWorkouts) {return -1}

    // loop over every key value pair, and check if the workout is complete or not 
    for (const [index, workout] of Object.entries(allWorkouts)) {
        const isComplete = Object.values(workout).every(ex=> !!ex)
        if (!isComplete) {
            return parseInt(index)
        }
    }
    return -1; // return -1 if all workouts are complete
})

function handleChangeDisplay(idx) {
    selectDisplay.value = idx;
}

function handleSelectWorkout(idx) {
    selectDisplay.value = 3;
    selectWorkout.value = idx;
}

function handleSaveWorkout() {
    // save the current data snapshot to localstorage so that we can retrieve it next time
    localStorage.setItem('workouts', JSON.stringify(data.value))

    // show the dashboard
    selectDisplay.value = 2

    // deselect a workout
    selectWorkout.value = -1
}

function handleResetPlan() {
    selectDisplay.value = 2
    selectWorkout.value = -1
    data.value = defaultData
    localStorage.removeItem('workouts')
}

onMounted(()=> {
    if (!localStorage) {return}
    if (localStorage.getItem('workouts')) {
        const saveData = JSON.parse(localStorage.getItem('workouts'))
        data.value = saveData
        selectDisplay.value = 2
    } 
})

</script>

<template>
    <Layout>
        <!-- Page 1 -->
        <Welcome :handleChangeDisplay="handleChangeDisplay" v-if="selectDisplay == 1" />
        <!-- Page 2 -->
        <Dashboard :handleResetPlan="handleResetPlan" :firstIncompleteWorkoutIndex="firstIncompleteWorkoutIndex"
          :handleSelectWorkout="handleSelectWorkout" v-if="selectDisplay == 2" />
        <!-- Page 3 -->
        <Workout :handleSaveWorkout="handleSaveWorkout"  :isWorkoutComplete="isWorkoutComplete" :data="data" 
        :selectWorkout="selectWorkout" v-if="selectDisplay == 3 && workoutProgram?.[selectWorkout]" />
    </Layout>
</template>

<style scoped></style>
