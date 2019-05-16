<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <h2>Vuex</h2>
    <router-link to="/vuex">ShoppingCart Example</router-link>
  </div>
</template>

<script lang="ts">
import { Component, Vue, Watch, Prop } from 'vue-property-decorator';

@Component
class HelloWorld extends Vue {
  @Prop({ default: 'hello world', type: String })
  msg: string;
}
export default HelloWorld;
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1,
h2 {
  font-weight: normal;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #42b983;
}
</style>
