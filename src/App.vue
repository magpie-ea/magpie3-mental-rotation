<template>
  <Experiment title="Mental rotation experiment">

    <InstructionScreen title="Welcome!">
      <p>Thank you for participating in our experiment!</p>
      <p>
        You will need around 10 min to complete the experiment. Please make sure
        that you will not be distracted. Switch off all messaging systems, your
        phone, any background music etc., and try to concentrate as much as
        possible on the task at hand.
      </p>
      <p>Click on the button below to receive instructions.</p>
    </InstructionScreen>

    <InstructionScreen :title="'Instructions'">
      <p>
        You will see pictures showing pairs of geometrical objects. Your task is
        to compare both objects in the pair and decide whether they are the same
        or different. You must press button 'f' if you think that the objects
        are {{f_key_string}}; otherwise you press button 'j'.
        Please try to answer as quick and accurately as possible!
      </p>
      <p>We will practice this first.</p>
    </InstructionScreen>

    <template v-for="(trial, i) of training_trials">
      <KeypressScreen
      :key="'training-' + i"
      :keys="key_allocation"
      :fixationTime="sample([250,300,1000])"
      :responseTime="3000"
      :feedbackTime="1000"
      :progress="i / training_trials.length"
      >
      <template #stimulus>
        <Record :data="{
                       'trial_nr'   : i+1,
                       'angle'      : trial.angle,
                       'expected'   : trial.expected,
                       'item'       : trial.item,
                       'trial_type' : 'training'
                       }" />
        <img :src="trial.picture" />
      </template>

      <template #feedback>
        <p v-if="! $magpie.measurements.hasOwnProperty('response')">Please respond faster.
        </p>
         <p v-else-if="$magpie.measurements.response === trial.expected">Correct</p>
        <p v-else>Incorrect</p>
       </template>
      </KeypressScreen>
    </template>

    <InstructionScreen :title="'Instructions'">
      <p> Great! You are now ready to start the main experiment.</p>
    </InstructionScreen>

    <template v-for="(trial, i) of main_trials">
      <KeypressScreen
      :key="'training-' + i"
      :keys="key_allocation"
      :fixationTime="sample([250,300,1000])"
      :responseTime="3000"
      :feedbackTime="1000"
      :progress="i / main_trials.length"
      >
      <template #stimulus>
        <Record :data="{
                       'trial_nr'   : i+1,
                       'angle'      : trial.angle,
                       'expected'   : trial.expected,
                       'item'       : trial.item,
                       'trial_type' : 'main'
                       }" />
        <img :src="trial.picture" />
      </template>

      <template #feedback>
        <p v-if="! $magpie.measurements.hasOwnProperty('response')">Please respond faster.
        </p>
         <p v-else-if="$magpie.measurements.response === trial.expected">Correct</p>
        <p v-else>Incorrect</p>
       </template>
      </KeypressScreen>
    </template>

    <PostTestScreen />

    <SubmitResultsScreen />
  </Experiment>
</template>

<script>
// Load data from csv files as javascript arrays with objects
import main_trials from '../trials/mr_main_trials.csv';
import training_trials from '../trials/mr_training_trials.csv';
import _ from 'lodash';

var same_key = _.sample(['j','f'])
var f_key_string = same_key == 'f' ? 'the same' : 'different';
console.log("here same_key:: " + same_key)
var key_allocation = same_key == 'f' ? {'f': 'same', 'j': 'different'} : {'f': 'different', 'j': 'same'} ;

export default {
  name: 'App',
  data() {
    return {
      main_trials,
      //training_trials : _.slice(_.shuffle(training_trials),0,2),
      training_trials : _.shuffle(training_trials),
      //main_trials : _.slice(_.shuffle(main_trials),0,2),
      main_trials : _.shuffle(main_trials),
      // Expose lodash.range to template above
      range : _.range,
      sample : _.sample,
      f_key_string : f_key_string,
      key_allocation :  key_allocation,
      same_key :  same_key
    };
  },
  mounted() {
    this.$magpie.addExpData({
      same_key : same_key
    });
  },
};
</script>

<style>
.stimulus {
  width: 20%;
  border: 2px solid blue;
}
</style>
