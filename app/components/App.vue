<template>
  <Page>
    <ActionBar title="Palliative NativeScript Test"/>
    <ScrollView :class="{red : flash}">
      <StackLayout class="home-panel">
        <ActivityIndicator
          :busy="loading"
          v-if="loading"
          class="activity-indicator"
          width="100"
          height="100"
        />
        <Label :text="question" col="0" row="0" horizontalAlignment="center" class="question"/>
        <Button
          v-for="answer in answers"
          v-bind:key="answer.id"
          :text="answer.text"
          @tap="selectAnswer(answer)"
          col="0"
          row="0"
          class="answerButton"
        />
        <Label />
        <Button
          text="Restart"
          @tap="restart"
          col="0"
          row="0"
          class="restartButton"
        />
        <!-- <Label
          v-for="protocol in protocols"
          v-bind:key="protocol.id"
          :text="protocol.name"
          col="0"
          row="0"
        />-->
        <!-- <Label :text="JSON.stringify(intro)"/> -->
      </StackLayout>
    </ScrollView>
  </Page>
</template>

<script lang="ts">
import {
  request,
  getFile,
  getImage,
  getJSON,
  getString
} from "tns-core-modules/http";
import { setInterval, setTimeout } from "tns-core-modules/timer";

export default {
  data() {
    return {
      msg: "Turn Up!!!!",
      intro: "",
      currentBranch: undefined,
      loading: true,
      protocols: [],
      flash: false,
      question: "",
      answers: []
    };
  },
  mounted() {
    getJSON("https://palliative.vchlearn.ca/_/custom/main").then(
      (result: any) => {
        this.protocols = result.data.protocols;
        this.loading = false;
        this.intro = result.data.intro;
        this.currentBranch = result.data.intro.starting_branch;
      },
      error => {
        console.log(error);
        this.loading = false;
      }
    );

    // setInterval(() => {
    //   this.flash = !this.flash;
    // }, 100);
  },
  watch: {
    currentBranch(value) {
      if (value.is_outcome) {
        this.question = "Outcome: " + value.text;
        this.answers = [];
      } 
      else {
        this.question = value.text;
        this.answers = value.answers;
      }
    }
  },
  methods: {
    selectAnswer(answer) {
      this.currentBranch = answer.next_branch;
    },
    restart(){
      this.currentBranch = this.intro.starting_branch;
    }
  }
};
</script>

<style scoped>
ActionBar {
  background-color: #539dba;
  color: #ffffff;
}

.question {
  font-size: 30px;
  font-weight: 500;
  margin-bottom: 50px;
  margin-top: 25px;
}

ActivityIndicator {
  height: 100px;
  width: 100px;
}

.message {
  vertical-align: center;
  text-align: center;
  font-size: 20;
  color: #333333;
}

.restartButton {
  margin-top: 100px;
}

.answerButton{
  /* border-width: 2px; */
  color: #fff;
  border-radius: 15px;
  background-color: #539dba;
  padding: 15px;
  margin: 25px;
}
</style>
