<template>
  <div>
    <div v-if = "isShowingGhosts">

      <div style = "display: flex">
        <div class = "rowheader">Possiblities:</div>
        <clue v-for = "(item, index) in remainingghosts" :title="item"></clue>

      </div>
      <br>
    </div>

    <div v-if = "isShowingClue">
      <div style = "display: flex">
        <div class = "rowheader">Found: </div>

        <clue v-for = "(item, index) in foundclues" :title="item"></clue>

      </div>
      <br>
    </div>

    <div v-if = "isShowingClue">
      <div style = "display: flex">
        <div class = "rowheader">Looking For: </div>

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
      revealModes: {
        'ALWAYS': 0,
        'ONFIRSTCLUE': 1,
        'ONLASTCLUE': 2,
        'NEVER': 3
      },
      ghostRevealMode: 0,
      clueRevealMode: 0,
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
    const urlParams = new URLSearchParams(window.location.search);
    const channelName = urlParams.get('channel');
    if(channelName != null){
      this.username = channelName;
    }

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
          this.calculatePossibleClues();
        } else if (command === "phasmo-reset") {
          this.resetClues();
          this.resetGhosts();
        } else if (command === "phasmo-ghostmode") {
          if(args.length > 0){
            let ghostMode = args[0].toUpperCase();
            if(this.revealModes[ghostMode] != null){
              this.ghostRevealMode =  this.revealModes[ghostMode];
            }
          }
        } else if (command === "phasmo-cluemode") {
          if(args.length > 0){
            let clueMode = args[0].toUpperCase();
            if(this.revealModes[clueMode] != null){
              this.clueRevealMode =  this.revealModes[clueMode];
            }
          }
        } else if (command === "phasmo-mode") {
          if(args.length > 0){
            let clueMode = args[0].toUpperCase();
            if(this.revealModes[clueMode] != null){
              this.clueRevealMode =  this.revealModes[clueMode];
              this.ghostRevealMode =  this.revealModes[clueMode];
            }
          }
        }
      }
    }
    ComfyJS.Init( this.username );
  },
  computed: {
    isShowingGhosts(){
      var isShowing = false;
      switch(this.ghostRevealMode){
        case this.revealModes['ALWAYS']:
          isShowing = true;
          break;
        case this.revealModes['ONFIRSTCLUE']:
          if(this.foundclues.length >= 1){
            isShowing = true;
          }
          break;
        case this.revealModes['ONLASTCLUE']:
          if(this.foundclues.length >= 2){
            isShowing = true;
          }
          break;
      }
      return isShowing;
    },
    isShowingClue(){
      var isShowing = false;
      switch(this.clueRevealMode){
        case this.revealModes['ALWAYS']:
          isShowing = true;
          break;
        case this.revealModes['ONFIRSTCLUE']:
          if(this.foundclues.length >= 1){
            isShowing = true;
          }
          break;
        case this.revealModes['ONLASTCLUE']:
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
    calculatePossibleClues(){
      this.possibleclues = [];
      for(let i = this.remainingghosts.length - 1; i >= 0; i--){
        let currentGhost = this.ghosts[this.remainingghosts[i]];
        for(let j = 0; j < currentGhost.length; j++){
          if(!this.foundclues.includes(currentGhost[j]) && !this.possibleclues.includes(currentGhost[j])){
            this.possibleclues.push(currentGhost[j]);
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
  margin-right: 2px;
}
</style>
