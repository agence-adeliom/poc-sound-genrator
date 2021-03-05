<template>
  <ion-page>
    <ion-header :translucent="true">
      <ion-toolbar>
        <ion-title>Sound Generator</ion-title>
      </ion-toolbar>
    </ion-header>
    
    <ion-content :fullscreen="true">
      <ion-header collapse="condense">
        <ion-toolbar>
          <ion-title size="large">Sound Generator</ion-title>
        </ion-toolbar>
      </ion-header>
    
      <div id="container">
    
        <ion-segment @ionChange="segmentChanged($event)" v-model="type">
          <ion-segment-button value="pure">
            <ion-label>Son Pure</ion-label>
          </ion-segment-button>
          <ion-segment-button value="noise">
            <ion-label>Bruit Blanc</ion-label>
          </ion-segment-button>
          <ion-segment-button value="noise-centred">
            <ion-label>Bruit Blanc centré en fréquence</ion-label>
          </ion-segment-button>
        </ion-segment>

        <ion-text color="dark">
          <h4>Fréquence</h4>
        </ion-text>
        <ion-range v-if="type == 'noise-centred' || type == 'pure'" min="20" max="22000" snaps="true" step="5" color="secondary" debounce="true" pin="true" ticks="true" v-model="frequency" >
          <ion-label slot="start">20 Hz</ion-label>
          <ion-label slot="end">22 000 Hz</ion-label>
        </ion-range>

        <ion-text color="dark">
          <h4>Stimulation</h4>
        </ion-text>
        <ion-range min="1" max="10" snaps="true" step="1" color="secondary" debounce="true" pin="true" ticks="true" v-model="duration" >
          <ion-label slot="start">1 s</ion-label>
          <ion-label slot="end">10 s</ion-label>
        </ion-range>

        <!-- ion-text color="dark">
          <h4>Silence</h4>
        </>
        <ion-range min="1" max="10" snaps="true" step="1" color="secondary" debounce="true" pin="true" ticks="true" v-model="pause" >
          <ion-label slot="start">1 s</ion-label>
          <ion-label slot="end">10 s</ion-label>
        </ion-range -->

        <ion-text color="dark">
          <h4>Position</h4>
        </ion-text>
        <ion-segment color="secondary" @ionChange="segmentChanged($event)" v-model="pan" mode="ios">
          <ion-segment-button value="-1">
            <ion-label>Oreille gauche</ion-label>
          </ion-segment-button>
          <ion-segment-button value="0">
            <ion-label>Bineural</ion-label>
          </ion-segment-button>
          <ion-segment-button value="1">
            <ion-label>Oreille droite</ion-label>
          </ion-segment-button>
        </ion-segment>

        <ion-button @click.prevent="generateSound" :disabled="running" style="margin-top: 40px" color="success" expand="block" >Jouer le son</ion-button>
      </div>
    </ion-content>
  </ion-page>
</template>

<script lang="ts">
import { IonContent, IonHeader, IonPage, IonTitle, IonToolbar, IonButton, IonLabel, IonRange, IonSegment, IonSegmentButton } from '@ionic/vue';
import { defineComponent } from 'vue';
import * as Tone from 'tone';

export default defineComponent({
  name: 'Home',
  components: {
    IonContent,
    IonHeader,
    IonPage,
    IonTitle,
    IonToolbar,
    IonButton,
    IonLabel,
    IonRange,
    IonSegment,
    IonSegmentButton
  },
  data: () => {
    return {
      frequency: 200,
      duration: 2,
      pause: 1,
      type: "pure",
      pan: 0,
      running: false
    }
  },
  methods: {
    segmentChanged(ev: CustomEvent) {
      console.log('Segment changed', ev);
    },
    generateSound() {

      const panner = new Tone.Panner(this.pan).toDestination();
      switch (this.type) {
        case "pure": {
          const synth = new Tone.FMSynth();
          synth.connect(panner);
          synth.triggerAttackRelease(this.frequency, this.duration);
          this.running = true;
          setTimeout(() => {
            this.running = false;
          }, this.duration * 1000)
          break;
        }
        case "noise": {
          const synth = new Tone.Noise();
          synth.connect(panner);
          synth.start();
          this.running = true;
          setTimeout(() => {
            synth.stop();
            this.running = false;
          }, this.duration * 1000)
          break;
        }
        case "noise-centred": {
          const synth = new Tone.Noise();

          const filter = new Tone.AutoFilter({
            frequency: this.frequency
          }).toDestination().start();
          synth.connect(filter);
          synth.connect(panner);
          synth.start();
          this.running = true;
          setTimeout(() => {
            synth.stop();
            this.running = false;
          }, this.duration * 1000)
          break;
        }
        default: {
          break;
        }
      }
      

      //;

    }
  }
});
</script>

<style scoped>
#container {
  position: absolute;
  left: 0;
  right: 0;
  top: 50%;
  transform: translateY(-50%);
  max-width: 624px;
  margin: 0 auto;;
}

#container strong {
  font-size: 20px;
  line-height: 26px;
}

#container p {
  font-size: 16px;
  line-height: 22px;
  
  color: #8c8c8c;
  
  margin: 0;
}

#container a {
  text-decoration: none;
}
</style>