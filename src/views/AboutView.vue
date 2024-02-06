<template>
  <div class="about">
    <h1>This is an about page</h1>
    <HelloWorld msg="Danilo did this" />
  </div>
</template>

<script>
import HelloWorld from "@/components/HelloWorld.vue";

export default {
  name: "AboutView",
  components: {
    HelloWorld,
  },
};
</script>
