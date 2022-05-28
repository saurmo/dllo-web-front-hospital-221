<template>
  <div>
    {{ invoces }}
  </div>
</template>
<script>
import config from "@/assets/config";
export default {
  data() {
    return {
      invoces: [],
    };
  },
  beforeMount() {
    this.loadPage();
  },
  methods: {
    async loadPage() {
      try {
        let url = config.API_HOST + "/api/v1/factures";
        let { data } = await this.$axios.get(url);
        this.invoces = data.info;
      } catch (error) {
        console.log(error);
      }
    },
  },
};
</script>