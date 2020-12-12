<template lang="pug">
v-app
  v-row
    p adasdlo
</template>

<script>
export default {
  components: {},
  data () {
    return {
      products: []
    }
  },
  async mounted () {
    await this.getProducts()
  },
  methods: {
    async getProducts () {
      // pick the rendered page, filter "html string" response data based by patterns and convert to JSON =D
      const response = await this.$axios.$get(
        'http://api.scraperapi.com?api_key=541e527d2f1c2927e30304af74880286&url=https://chicorei.com/masterchefbrasil/'
      )
      const matches = response.match('"hits":(.*)],"per_page"')
      this.products = JSON.parse(matches[1] + ']')
      console.log(this.products)
    }
  }
}
</script>
