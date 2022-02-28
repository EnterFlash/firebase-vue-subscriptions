<template>
  <div>
    <TheNavbar />

    <div class="container mt-3">
      <slot />
    </div>
  </div>
</template>

<script>
import TheNavbar from "@/components/TheNavbar";

export default {
  components: { TheNavbar },
};
</script>