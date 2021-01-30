<template>
    <span class = "rowheader">Possiblities:</span>
    <div style = "display: flex">
      <clue v-for = "(item, index) in remainingghosts" :title="item"></clue>

    </div>
    <br>


    <span class = "rowheader">Found: </span>
  <div style = "display: flex">
    <clue v-for = "(item, index) in foundclues" :title="item"></clue>

  </div>
    <br>

    <span class = "rowheader">Looking For: </span>
  <div style = "display: flex">
    <clue v-for = "(item, index) in possibleclues" :title="item"></clue>


  </div>
</template>

<script>
import { ref } from 'vue';
import ComfyJS from 'comfy.js';
import clue from './clue.vue';

export default {
  components: {
    clue
  },
  data: function() {
    return {
      alwaysshow: false,
      username: "vanessienessie",
      clues: ["orbs", "freezing", "fingerprints", "box", "writing", "emf"],
      ghosts: {
        Banshee: ["freezing", "fingerprints", "emf"],
        Demon: ["freezing", "box", "writing"],
        Jinn: ["orbs", "box", "emf"],
        Mare: ["freezing", "orbs", "box"],
        Oni: ["box", "writing", "emf"],
        Phantom: ["freezing", "orbs", "emf"],
        Poltergeist: ["orbs", "fingerprints", "box"],
        Revenant: ["fingerprints", "writing", "emf"],
        Shade: ["orbs", "emf", "writing"],
        Spirit: ["box", "fingerprints", "writing"],
        Wraith: ["freezing", "fingerprints", "box"],
        Yurei: ["freezing", "orbs", "writing"]
      },
      foundclues: [],
      possibleclues: [],
      remainingghosts: []
    };
  },
  mounted() {
    this.resetClues();
    this.resetGhosts();

    ComfyJS.onCommand = ( user, command, message, flags, extra ) => {
      if(flags.mod || flags.broadcaster || flags.vip){

        if (this.clues.includes(command)) {
          this.addClue(command);
          this.calculateGhosts();
        } else if (command === "phasmo-reset") {
          this.resetClues();
          this.resetGhosts();
        }
      }
    }
    ComfyJS.Init( "vanessienessie" );
  },
  methods: {
    addClue(command){
      this.foundclues.push(command);
      this.possibleclues.splice(this.possibleclues.indexOf(command), 1);
      console.log(this.possibleclues);
    },
    resetClues(){
      this.foundclues = [];
      this.possibleclues = this.clues.slice();
    },
    calculateGhosts(){
      for(let i = this.remainingghosts.length - 1; i >= 0; i--){
        for(let j = 0; j < this.foundclues.length; j++){
          if(!this.ghosts[this.remainingghosts[i]].includes(this.foundclues[j])){
            this.remainingghosts.splice(i, 1);
            break;
          }
        }
      }


    },
    resetGhosts(){
      this.remainingghosts = [];
      for(let ghost in this.ghosts){
        this.remainingghosts.push(ghost);
      }
      console.log(this.remainingghosts);

    },
  }
}
</script>

<style scoped>
.rowheader {
  color: #aeaeae;
  font-family: 'East Sea Dokdo', cursive;
}
</style>
