<template>
  <div class="home">
    <!-- <img src="../assets/logo.png"> -->
    <HelloWorld msg="Welcome to Your Vue.js + TypeScript App" />
    <button @click="jumpPage">jump test</button>
  </div>
</template>

<script lang="ts">
import { Component, Vue, Watch } from 'vue-property-decorator';
import HelloWorld from '@/components/HelloWorld.vue'; // @ is an alias to /src

@Component({
  components: {
    HelloWorld
  }
})
class Home    extends Vue {
  name: string = 'xiaowu';

  beforeRouteEnter(to: any, from: any, next: any) {
    console.log('beforeRouter enter');
    next();
  }

  // computed
  get getMyName(): string {
    return `my name is ${this.name}`;
  }

  // method
  jumpPage(): void {
    console.log('jump page');
  }

  // lifecycle hook
  mounted() {
    console.log('mounted !!');
  }
}

export default Home;
</script>
