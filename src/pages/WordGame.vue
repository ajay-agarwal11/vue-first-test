<template lang="en">
  <main style="font-size: 24px">
    <WordTypingGame :wordList='wordArray'></WordTypingGame>
  </main>
</template>

<script lang="ts">
import WordTypingGame from 'src/components/WordTypingGame.vue';
import { defineComponent } from 'vue';

const wordArray = [
  'obtain',
  'marry',
  'spell',
  'abstracted',
  'limit',
  'prickly',
  'clap',
  'beautiful',
  'exciting',
  'connection',
  'kitty',
  'rhetorical',
  'juggle',
  'receipt',
  'cross',
  'whirl',
  'tongue',
  'tumble',
  'include',
  'car',
  'edge',
  'sneaky',
  'obsequious',
  'drawer',
  'warm',
  'trot',
  'pop',
  'shade',
  'attack',
  'trashy',
  'cemetery',
  'tame',
  'scintillating',
  'ship',
  'follow',
  'sticks',
  'grey',
  'misty',
  'cup',
  'taste',
  'wretched',
  'petite',
  'caring',
  'scribble',
  'like',
  'mass',
  'command',
  'crook',
  'skirt',
  'aspiring',
];

export default defineComponent({
  name: 'WordGame',
  components: { WordTypingGame },
  setup() {
    return { wordArray };
  },
});
</script>
