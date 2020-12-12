<template lang="pug">
v-app
  v-container
    v-row.hidden-sm-and-down(justify-md="end")
      v-btn-toggle(
        v-model="order",
        tile="",
        color="deep-purple accent-3",
        group=""
      )
        v-btn(value="left", @click="orderByDefault()")
          | Padrão
        v-btn(value="center", @click="orderByPriceAsc()")
          | Menor Preço
        v-btn(value="right", @click="orderByPriceDesc()")
          | Maior Preço
        v-btn(value="justify", @click="orderByNews()")
          | Lançamentos

    v-row(v-if='!loaded')
      v-col(cols='6' md='3' xs='6' v-for="n in 4"  :key="n")
        v-skeleton-loader.mx-auto(max-width='300' type='card')
    v-row(v-if='!loaded')
      v-col(cols='6' md='3' xs='6' v-for="n in 4" :key="n")
        v-skeleton-loader.mx-auto(max-width='300' type='card')

    v-row
      v-col.pa-5(
        cols="6",
        md="3",
        xs="6",
        v-for="item in products",
        :key="item.message"
      )
        v-row
          img(:src="item.img_cover", width="100%", height="auto")
        v-row.pt-1
          span(style='font-family: Yanone; font-size: 19px; letter-spacing: .1em; font-weight:400; line-height:28.5px') {{ item.name }}
        v-row
          span(style='font-family: Yanone; font-size: 19px; letter-spacing: .1em; font-weight:700; line-height:28.5px') R$ {{ item.price }}
    v-row(justify='center')
      v-progress-circular(indeterminate='' color='amber' v-if='loading')
    v-pagination(v-model="page", @input="next(page)", circle, :length="6")
</template>

<script>
import _ from 'lodash'

export default {
  components: {},
  data () {
    return {
      products: [],
      filters: [],
      order: null,
      page: 1,
      loading: false,
      loaded: false
    }
  },
  async mounted () {
    // for low consume of api requests in development (quota :D)
    // if (localStorage.products) {
    //   this.products = await JSON.parse(localStorage.products)
    //   console.log(this.products, 'local storageae ')
    // }
    await this.getProducts()
  },
  methods: {
    next (page) {
      this.getProducts(page)
    },
    async getProducts (page) {
      this.loading = true
      this.loaded = false
      // pick the rendered page, filter "html string" response data based by patterns and convert to JSON =D
      let response = []
      this.products = []
      if (page) {
        response = await this.$axios.$get(
          `http://api.scraperapi.com?api_key=541e527d2f1c2927e30304af74880286&url=https://chicorei.com/camiseta/masculino/?page=${page}`
        )
      } else {
        response = await this.$axios.$get(
          'http://api.scraperapi.com?api_key=541e527d2f1c2927e30304af74880286&url=https://chicorei.com/camiseta/masculino/'
        )
      }

      // const matches =
      // console.log(response)
      this.products = JSON.parse(
        response.match('"hits":(.*)],"per_page"')[1] + ']'
      ) // products
      // localStorage.products = response.match('"hits":(.*)],"per_page"')[1] + ']'
      // console.log(this.products)
      // const auxFilters = response.match('"stock_filter":(.*)],"prices"')[1]
      // console.log(this.removeAcento(auxFilters))

      // remove accenptuation

      // this.filters = JSON.parse(response.match('"stock_filter":(.*)],"prices"')[1].replace(/[\uE000-\uF8FF]/g, '') + ']') // filters
      // console.log(this.products, 'original products')
      // console.log(this.filters, 'original filters')
      // localStorage.products = matches[1] + ']'
      this.loaded = true
      this.loading = false
    },
    orderByPriceAsc () {
      this.products = _.orderBy(this.products, ['price'], ['asc'])
      console.log(this.products)
    },
    orderByPriceDesc () {
      this.products = _.orderBy(this.products, ['price'], ['desc'])
      console.log(this.products, 'dest price')
    },
    orderByNews () {
      this.products = _.orderBy(this.products, ['is_new'], ['desc']) // bool, starts with 1
      console.log(this.products, 'news')
    },
    orderByDefault () {
      this.products = _.orderBy(this.products, ['id'], ['asc'])
      console.log(this.products, 'news')
    },
    filterByGender () {}
  }
}
</script>
