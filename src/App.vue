<script lang="ts">
import { defineComponent, ref, onMounted, computed, watch } from 'vue'
import ToDo from './components/ToDo.vue'

export default defineComponent({
  name: 'App',
  components: { ToDo },

})
</script>

<template>
  <ToDo />
</template>