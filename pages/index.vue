<template>
  <CryptoMarket></CryptoMarket>
</template>

<script lang="ts">
import Vue from 'vue'
import CryptoMarket from '~/components/CryptoMarket.vue';

export default Vue.extend({
  name: "IndexPage",
  components: { CryptoMarket }
})
</script>
