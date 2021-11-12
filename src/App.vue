<template>
  <div class="wrapper">
    <Map />
  </div>
</template>

<script>
import Map from "./Map";

export default {
  name: "ExampleApp",
  components: { Map },
};
</script>

<style>
* {
  box-sizing: border-box;
}
</style>

<style scoped>
.wrapper {
  margin: 0 auto;
  max-width: 800px;
}
</style>
