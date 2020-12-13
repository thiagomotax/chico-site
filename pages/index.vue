<template lang="pug">
v-app
  v-container
    v-row
      v-col(cols="12", md="8", xs="12")
        //- img(src='@/assets/logo-text.svg' height='150' width='200').green.pa-0.ma-0
    v-row.hidden-sm-and-down.red(justify-md="end", self-align="end")
      v-col.pt-0.mt-0.mb-0.pb-0(cols="12", md="6")
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

  v-container
    v-row
      v-col.pr-md-10(cols="12", md="3", xs="12")
        v-row
          span(
            style="font-family: Yanone; font-size: 33px; font-weight: 400; line-height: 36.5pxs"
          ) FILTROS
        v-row.pa-3.mt-3.mb-3.rounded(style="background-color: #ededed")
          v-col
            v-row
              span(
                style="font-family: Yanone; font-size: 24.5px; font-weight: 400; line-height: 27.3pxs"
              ) TIPO
            v-row
              v-radio-group(v-model="radioType")
                v-radio(
                  v-for="n in types",
                  :key="n.name",
                  :label="`${n.name}`",
                  :value="n.name",
                  @click="getProducts()"
                )

        v-row.pa-3.mt-3.mb-3.rounded(style="background-color: #ededed")
          v-col
            v-row
              span(
                style="font-family: Yanone; font-size: 24.5px; font-weight: 400; line-height: 27.3pxs"
              ) GÊNERO
            v-row
              v-radio-group(v-model="radioGender")
                v-radio(
                  v-for="n in genders",
                  :key="n.name",
                  :label="`${n.name}`",
                  :value="n.name",
                  @click="getProducts()"
                )

        v-row.pa-3.mt-3.mb-3.rounded(style="background-color: #ededed")
          v-col
            v-row
              span(
                style="font-family: Yanone; font-size: 24.5px; font-weight: 400; line-height: 27.3pxs"
              ) TEMA
            v-row
              v-radio-group(v-model="radioCategory")
                v-radio(
                  v-for="n in categories",
                  :key="n.name",
                  :label="`${n.name}`",
                  :value="n.name",
                  @click="getProducts()"
                )

      v-col(cols="12", md="9", xs="12")
        v-row(v-if="!loaded")
          v-col(cols="6", md="3", xs="6", v-for="n in 4", :key="n")
            v-skeleton-loader.mx-auto(max-width="300", type="card")
        v-row(v-if="!loaded")
          v-col(cols="6", md="3", xs="6", v-for="n in 4", :key="n")
            v-skeleton-loader.mx-auto(max-width="300", type="card")
        v-row
          v-col.pa-5(
            cols="6",
            md="3",
            xs="6",
            v-for="item in products",
            :key="item.message"
          )
            v-row
              img.rounded(
                :src="radioGender == 'Feminino' && item.img_female != null ? item.img_female : radioGender == 'Masculino' && item.img_male != null ? item.img_male : item.img_cover",
                width="100%",
                height="auto"
              )
            v-row.pt-1
              span(
                style="font-family: Yanone; font-size: 19px; letter-spacing: 0.1em; font-weight: 400; line-height: 28.5px"
              ) {{ item.name }}
            v-row
              span(
                style="font-family: Yanone; font-size: 19px; letter-spacing: 0.1em; font-weight: 700; line-height: 28.5px"
              ) R$ {{ item.price }}
        v-row(justify="center")
          v-progress-circular(indeterminate="", color="amber", v-if="loading")
        v-pagination(v-model="page", @input="next(page)", circle, :length="6")
</template>

<script>
import _ from 'lodash'

export default {
  components: {},
  data () {
    return {
      products: [],
      categories: [],
      genders: [],
      types: [],
      order: null,
      page: 1,
      loading: false,
      loaded: false,
      radioType: null,
      radioGender: null,
      radioCategory: null,
      baseURL: 'https://chicorei.com',
      URL: ''
    }
  },
  watch: {
    radioType (val) {
      console.log(val, ' radio value')
    }
  },
  async mounted () {
    // for low consume of api requests in development (quota :D)
    // if (localStorage.products) {
    //   this.products = await JSON.parse(localStorage.products)
    //   this.loaded = true
    //   this.loading = false
    // }
    // if (localStorage.categories) {
    //   this.categories = await JSON.parse(localStorage.categories)
    //   this.loaded = true
    //   this.loading = false
    //   console.log(this.categories, 'categorias')
    // }
    // if (localStorage.genders) {
    //   this.genders = await JSON.parse(localStorage.genders)
    //   this.loaded = true
    //   this.loading = false
    //   console.log(this.genders, 'generos')
    // }
    // if (localStorage.types) {
    //   this.types = await JSON.parse(localStorage.types)
    //   this.loaded = true
    //   this.loading = false
    //   console.log(this.types, 'types')
    // }
    await this.getProducts()
  },
  methods: {
    next (page) {
      this.getProducts(page)
    },
    async getProducts (page) {
      // pick the rendered page, filter "html string" response data based by patterns and convert to JSON =D
      this.loading = true
      this.loaded = false
      let response = []
      this.products = []
      this.URL = null
      // qd n tem tipo, eh so roupas / coisa
      // when user interacts with radio button, this function is called and the endpoint uri is created
      // if (this.radioType == null && this.radioCategory == null && this.radioGender == null) {
      //   this.URL = `${this.baseURL}/roupas`
      // } else if (this.radioType != null) {
      //   this.URL = `${this.baseURL}/${this.radioType}`
      //   console.log('base url utilizada: ', this.URL)
      // }

      // if(this.radioType == null && )
      // fazer os ifs com varias condicoes de existencia aqui em cima
      // if (this.radioType != null) {
      //   this.URL += `/${this.radioType}`
      // }
      // if (this.radioCategory != null) {
      //   this.URL += `/${this.radioCategory}`
      // }
      // if (this.radioGender != null) {
      //   this.URL += `/${this.radioGender}`
      // }

      // 2 casos - quanto tem tipo e quando não tem tipo
      // se tem tipo, o tipo fica no inicio de tudo
      // funcionando ok, porem, se o gender for feminino, usar a image woman
      if (this.radioType != null) {
        this.URL = `${this.baseURL}/${this.radioType}`
        if (this.radioGender != null && this.radioCategory != null) {
          this.URL += `/${this.slugify(
            this.radioCategory
          )}/${this.slugify(this.radioGender)}`
        } else if (this.radioGender != null) {
          this.URL += `/${this.slugify(this.radioGender)}`
        } else if (this.radioCategory) {
          this.URL += `/${this.slugify(this.radioCategory)}`
        }
      } else {
        this.URL = `${this.baseURL}/roupas` // / a coisa
        if (this.radioGender != null && this.radioCategory != null) {
          this.URL += `/${this.slugify(
            this.radioCategory
          )}/${this.slugify(this.radioGender)}`
        } else if (this.radioGender != null) {
          this.URL += `/${this.slugify(this.radioGender)}`
        } else if (this.radioCategory) {
          this.URL += `/${this.slugify(this.radioCategory)}`
        }
      }

      console.log('url disparada', this.URL)

      // if (page) {
      //   response = await this.$axios.$get(
      //     `http://api.scraperapi.com?api_key=541e527d2f1c2927e30304af74880286&url=${this.URL}?page=${page}`
      //   )
      // } else {
      response = await this.$axios.$get(
        `http://api.scraperapi.com?api_key=541e527d2f1c2927e30304af74880286&url=${this.URL}`
      )
      // }

      this.products = JSON.parse(
        response.match('"hits":(.*)],"per_page"')[1] + ']'
      ) // products

      this.categories = JSON.parse(
        response.match('"categories":(.*)],"colors":')[1] + ']'
      ) // categories

      this.genders = JSON.parse(
        response.match('"genders":(.*)],"prices":')[1] + ']'
      ) // genders

      this.types = JSON.parse(
        response.match('"types":(.*)],"sizes_adult":')[1] + ']'
      ) // types

      // console.log(this.types)

      // localStorage.products = response.match('"hits":(.*)],"per_page"')[1] + ']'
      // localStorage.categories = response.match('"categories":(.*)],"colors":')[1] + ']'
      // localStorage.genders = response.match('"genders":(.*)],"prices":')[1] + ']'
      // localStorage.types = response.match('"types":(.*)],"sizes_adult":')[1] + ']'

      this.loaded = true
      this.loading = false
      console.log(this.products)
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
    filterByGender () {},
    slugify (text) {
      text = text.replace(/^\s+|\s+$/g, '') // trim
      text = text.toLowerCase()

      // remove accents, swap ñ for n, etc
      const from = 'àáãäâèéëêìíïîòóöôùúüûñç·/_,:;'
      const to = 'aaaaaeeeeiiiioooouuuunc------'

      for (let i = 0, l = from.length; i < l; i++) {
        text = text.replace(new RegExp(from.charAt(i), 'g'), to.charAt(i))
      }

      text = text
        .replace(/[^a-z0-9 -]/g, '') // remove invalid chars
        .replace(/\s+/g, '-') // collapse whitespace and replace by -
        .replace(/-+/g, '-') // collapse dashes

      return text
    }
  }
}
</script>
