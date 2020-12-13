<template lang="pug">
v-app
  v-bottom-navigation(v-model='value' fixed='' bottom='' style='align-items: center !important').hidden-md-and-up
    v-btn(value='recent' :disabled='!loaded' @click='sheetOrder = true')
      span Ordenar
      v-icon mdi-sort
    v-btn(value='favorites' :disabled='!loaded' @click='sheetFilter = true')
      span Filtrar
      v-icon mdi-filter
  v-bottom-sheet(v-model='sheetOrder')
    v-sheet(height='auto')
      v-btn.mt-6(text='' color='red' @click='sheetOrder = !sheetOrder')
        | fechar
      .py-3
        v-btn-toggle(@change='getScrapedPage()'
          v-model="order",
          tile="",
          color="#FF8A1D",
          group=""
        )
          v-list
            v-list-item(height='30')
              v-btn(text='' @click='sheetOrder = false' value="" style='color: black').text-none
                | Padrão
            v-list-item(height='30')
              v-btn(text='' @click='sheetOrder = false' value="menor-preco" style='color: black').text-none
                | Menor Preço
            v-list-item(height='30')
              v-btn(text='' @click='sheetOrder = false' value="maior-preco" style='color: black').text-none
                | Maior Preço
            v-list-item(height='30')
              v-btn(text='' @click='sheetOrder = false' value="lancamentos" style='color: black').text-none
                | Lançamentos

  v-bottom-sheet(v-model='sheetFilter')
    v-sheet(height='auto' )
      v-btn.mt-6(text='' color='red' @click='sheetFilter = !sheetFilter')
        | fechar
      .py-3
        v-list( style='max-height: 500px; overflow: hidden;').overflow-y-auto
          v-list-item
            span(
              style="font-family: Yanone; font-size: 24.5px; font-weight: 400; line-height: 27.3px"
            ) TIPO
          v-list-item(justify='center')
            v-radio-group(v-model="radioType" :disabled='!loaded')
              v-radio(
                v-for="n in types",
                :key="n.name",
                :label="`${n.name}`",
                :value="n.name",
                @click="sheetFilter=false; getScrapedPage()"
              )
          v-list-item
            span(
              style="font-family: Yanone; font-size: 24.5px; font-weight: 400; line-height: 27.3px"
            ) GÊNERO
          v-list-item
            v-radio-group(v-model="radioGender" :disabled='!loaded')
                v-radio(
                  v-for="n in genders",
                  :key="n.name",
                  :label="`${n.name}`",
                  :value="n.name",
                  @click="sheetFilter=false; getScrapedPage()"
                )
          v-list-item
            span(
              style="font-family: Yanone; font-size: 24.5px; font-weight: 400; line-height: 27.3px"
            ) CATEGORIA
          v-list-item
            v-radio-group(v-model="radioCategory" :disabled='!loaded')
              v-radio(
                v-for="n in categories",
                :key="n.name",
                :label="`${n.name}`",
                :value="n.name",
                @click="sheetFilter=false; getScrapedPage()"
              )

  v-container
    v-row
      v-col(cols="12", md="12", xs="12")
        v-row(justify='center' justify-md='start')
          img(src='@/assets/images/chico-cut.svg' width='150' height='auto')

  v-container
    v-row(no-gutters).hidden-sm-and-down
      v-col(cols="12", md="12")
        v-row(justify-md="end")
          v-btn-toggle(@change='getScrapedPage()'
            v-model="order",
            tile="",
            color="#FF8A1D",
            group=""
          )
            v-btn(value="" style='color: black').text-none
              | Padrão
            v-btn(value="menor-preco" style='color: black').text-none
              | Menor Preço
            v-btn(value="maior-preco" style='color: black').text-none
              | Maior Preço
            v-btn(value="lancamentos" style='color: black').text-none
              | Lançamentos

    v-row
      v-col.pr-md-10(cols="12", md="3", xs="12").hidden-sm-and-down
        v-row
          span(
            style="font-family: Yanone; font-size: 33px; font-weight: 400; line-height: 36.5pxs"
          ).pa-2.pa-md-0 FILTROS
        v-row.pa-3.mt-3.mb-3.rounded(style="background-color: #ededed")
          v-col
            v-row
              span(
                style="font-family: Yanone; font-size: 24.5px; font-weight: 400; line-height: 27.3px"
              ) TIPO
            v-row
              v-radio-group(v-model="radioType" :disabled='!loaded')
                v-radio(
                  v-for="n in types",
                  :key="n.name",
                  :label="`${n.name}`",
                  :value="n.name",
                  @click="getScrapedPage()"
                )

        v-row.pa-3.mt-3.mb-3.rounded(style="background-color: #ededed")
          v-col
            v-row
              span(
                style="font-family: Yanone; font-size: 24.5px; font-weight: 400; line-height: 27.3pxs"
              ) GÊNERO
            v-row
              v-radio-group(v-model="radioGender" :disabled='!loaded')
                v-radio(
                  v-for="n in genders",
                  :key="n.name",
                  :label="`${n.name}`",
                  :value="n.name",
                  @click="getScrapedPage()"
                )

        v-row.pa-3.mt-3.mb-3.rounded(style="background-color: #ededed")
          v-col
            v-row
              span(
                style="font-family: Yanone; font-size: 24.5px; font-weight: 400; line-height: 27.3pxs"
              ) TEMA
            v-row
              v-radio-group(v-model="radioCategory" :disabled='!loaded')
                v-radio(
                  v-for="n in categories",
                  :key="n.name",
                  :label="`${n.name}`",
                  :value="n.name",
                  @click="getScrapedPage()"
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
        v-row(justify='center')
          v-col(cols='12' md='12')
            v-row(align='center')
              v-col(cols='12' md='9' xs='12')
                v-pagination(v-model="page", @input="getScrapedPage()", :disabled='!loaded',circle, :total-visible="5", :length="Math.ceil(totalProducts / itensDisplay)")
              v-col(cols='12' md='3' xs='12')
                v-row(justify='end').mt-10
                  span(style="font-family: Yanone; font-size: 15.9x; font-weight: 400; line-height: 24px").pt-4 Produtos por página
                  v-select(v-model='itensDisplay' style='max-width: 90px', :disabled='!loaded', :items='listItensDisplay' label=''  @change='page = 1; getScrapedPage()' dense='' outlined='').pa-2

</template>

<script>
// import _ from 'lodash'

export default {
  components: {},
  data () {
    return {
      api_key: '541e527d2f1c2927e30304af74880286',
      products: [],
      categories: [],
      genders: [],
      types: [],
      order: '',
      page: 1,
      loading: false,
      loaded: false,
      radioType: null,
      radioGender: null,
      radioCategory: null,
      baseURL: 'https://chicorei.com',
      URL: '',
      listItensDisplay: [36, 60, 120],
      itensDisplay: 36,
      totalProducts: null,
      sheetFilter: false,
      sheetOrder: false,
      value: 0
    }
  },
  watch: {
    radioType (val) {
      console.log(val, ' radio value')
    },
    order: (val) => {
      console.log(val, 'value order ')
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
    await this.getScrapedPage()
  },
  methods: {
    async getScrapedPage (page) {
      // pick the rendered page, filter "html string" response data based by patterns and convert to JSON =D
      this.loading = true
      this.loaded = false
      let response = []
      this.products = []
      this.URL = null
      // when user interacts with radio button, this function is called and the endpoint uri is created
      // 2 cases - when has type(/tipo) (camiseta ou moletom) or not (/roupas)
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
        this.URL = `${this.baseURL}/roupas` 
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

      // add page selected, itens amount and page
      this.URL += `?per_page=${this.itensDisplay}&page=${this.page}&sort=${this.order}`

      console.log('url disparada', this.URL)
      response = await this.$axios.$get(`https://api.scraperapi.com?api_key=${this.api_key}&url=${this.URL}`)

      //colect data
      this.totalProducts = parseInt(response.match('"totalHits":(.*),"hits":')[1]) // total products
      this.products = JSON.parse(
        response.match('"hits":(.*)],"per_page"')[1] + ']'
      )
      this.categories = JSON.parse(
        response.match('"categories":(.*)],"colors":')[1] + ']'
      )
      this.genders = JSON.parse(
        response.match('"genders":(.*)],"prices":')[1] + ']'
      )
      this.types = JSON.parse(
        response.match('"types":(.*)],"sizes_adult":')[1] + ']'
      )

      // local storages (for development use [save unecessary requests])
      // localStorage.products = response.match('"hits":(.*)],"per_page"')[1] + ']'
      // localStorage.categories = response.match('"categories":(.*)],"colors":')[1] + ']'
      // localStorage.genders = response.match('"genders":(.*)],"prices":')[1] + ']'
      // localStorage.types = response.match('"types":(.*)],"sizes_adult":')[1] + ']'
      this.loaded = true
      this.loading = false
      console.log(this.products)
    },
    // order local (not to good, local)
    // orderByPriceAsc () {
    //   this.products = _.orderBy(this.products, ['price'], ['asc'])
    //   console.log(this.products)
    // },
    // orderByPriceDesc () {
    //   this.products = _.orderBy(this.products, ['price'], ['desc'])
    //   console.log(this.products, 'dest price')
    // },
    // orderByNews () {
    //   this.products = _.orderBy(this.products, ['is_new'], ['desc']) // bool, starts with 1
    //   console.log(this.products, 'news')
    // },
    // orderByDefault () {
    //   this.products = _.orderBy(this.products, ['id'], ['asc'])
    //   console.log(this.products, 'news')
    // },
    slugify (text) {
      text = text.replace(/^\s+|\s+$/g, '') // trim
      text = text.toLowerCase()
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
