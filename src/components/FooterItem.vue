<script>
export default {
  name: "FooterItem",
};
</script>

<template>
  <footer class="text-black">
    <div class="d-flex justify-content-center">
      <p>&heartsuit; Quran Vue &heartsuit;</p>
      <p></p>
    </div>
  </footer>
</template>

<style scoped>
footer {
  background: linear-gradient(45deg, #e7d2bb, #c3903d);
  animation: hue-rotate 2s linear infinite alternate;
  position: fixed;
  bottom: 0;
  width: 100%;
}
@keyframes hue-rotate {
  to { filter: hue-rotate(90deg);}
}
</style>