<script>
  /* wrap basic component */
  import BasicSelect from './BasicSelect.vue'
  import { commonMixin } from './mixins'

  export default {
    mixins: [commonMixin],
    render: function (createElement) {
      return createElement(BasicSelect, {
        props: {
          id: this.id,
          name: this.name,
          options: this.options,
          selectedOption: this.item,
          isError: this.isError,
          isDisabled: this.isDisabled,
          placeholder: this.placeholder,
          filterPredicate: this.filterPredicate,
          freeText: this.freeText,
          renderType: 'ListSelect'
        },
        on: {
          select: this.onSelect,
          searchchange: (searchText) => this.$emit('searchchange', searchText),
          enter: (element) => {
            if (typeof element === 'undefined' && this.freeText) {
              element = {}
              element[this.optionValue] = option.value
              element[this.optionText] = option.text
            }
            if (element) {
              this.selectItem(element)
            }
          }
        }
      })
    },
    props: {
      list: {
        type: Array
      },
      optionValue: {
        type: String
      },
      optionText: {
        type: String
      },
      customText: {
        type: Function
      },
      selectedItem: {
        type: Object
      }
    },
    computed: {
      options () {
        return this.list.map((e, i) => {
          return { value: e[this.optionValue], text: this.buildText(e), highlight: e.highlight }
        })
      },
      item () {
        if (this.selectedItem) {
          return { value: this.selectedItem[this.optionValue], text: this.buildText(this.selectedItem) }
        } else {
          return { value: '', text: '' }
        }
      }
    },
    methods: {
      buildText (e) {
        if (e[this.optionValue] !== undefined) {
          if (this.customText) {
            return this.customText(e)
          } else {
            return e[this.optionText]
          }
        } else {
          return ''
        }
      },
      onSelect (option) {
        if (Object.keys(option).length === 0 && option.constructor === Object) {
          this.$emit('select', option)
        } else {
          let item = this.list.find((e, i) => {
            return e[this.optionValue] === option.value
          })
          if (typeof item === 'undefined' && this.freeText) {
            item = {}
            item[this.optionValue] = option.value
            item[this.optionText] = option.text
          }
          this.$emit('select', item)
        }
      }
    },
    components: {
      BasicSelect
    }
  }
</script>
