<template>
  <div>
    <div v-if = "isShowingGhosts">
      <span class = "rowheader">Possiblities:</span>
      <div style = "display: flex">
        <clue v-for = "(item, index) in remainingghosts" :title="item"></clue>

      </div>
      <br>
    </div>

    <div>
      <span class = "rowheader">Found: </span>
      <div style = "display: flex">
        <clue v-for = "(item, index) in foundclues" :title="item"></clue>

      </div>
      <br>
    </div>

    <div>
      <span class = "rowheader">Looking For: </span>
      <div style = "display: flex">
        <clue v-for = "(item, index) in possibleclues" :title="item"></clue>
      </div>
    </div>

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
      ghostRevealModes: {
        'ALWAYS': 0,
        'ONFIRSTCLUE': 1,
        'ONLASTCLUE': 2,
        'NEVER': 3
      },
      ghostRevealMode: 0,
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
      let args = [];
      if(message != ""){
        args = message.split(' ');
      }

      if(flags.mod || flags.broadcaster || flags.vip){

        if (this.clues.includes(command)) {
          this.addClue(command);
          this.calculateGhosts();
        } else if (command === "phasmo-reset") {
          this.resetClues();
          this.resetGhosts();
        } else if (command === "phasmo-ghostmode") {
          if(args.length > 0){
            let ghostMode = args[0].toUpperCase();
            console.log(ghostMode)
            if(this.ghostRevealModes[ghostMode] != null){
              console.log(this.ghostRevealModes[ghostMode]);
              this.ghostRevealMode =  this.ghostRevealModes[ghostMode];
            }
          }
        }
      }
    }
    ComfyJS.Init( "vanessienessie" );
  },
  computed: {
    isShowingGhosts(){
      var isShowing = false;
      switch(this.ghostRevealMode){
        case this.ghostRevealModes['ALWAYS']:
          isShowing = true;
          break;
        case this.ghostRevealModes['ONFIRSTCLUE']:
          if(this.foundclues.length >= 1){
            isShowing = true;
          }
          break;
        case this.ghostRevealModes['ONLASTCLUE']:
          if(this.foundclues.length >= 2){
            isShowing = true;
          }
          break;
      }
      return isShowing;
    }
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
