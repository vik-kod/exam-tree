<template>
  <div class="tree-list">
    <tree-item
      v-for="(item, idx) in filterlist"
      :ids="ids"
      :ids-with-parent="idsWithParent"
      :model="item"
      :options="termOptions"
      :depth="0"
      :state="state"
      :key="idx"
      @handle="handle"
    ></tree-item>
  </div>
</template>
<script>
  import treeItem from './tree-item'

  /**
   * @param filter - filter string search
   * @param treeData - data
   * @param options - for customize
   */

  export default {
    components: { treeItem },
    model: {
      prop: 'ids',
      event: 'change'
    },

    props: {
      filter: {
        type: String,
        default: ''
      },
      treeData: {
        type: Object,
        default: () => {}
      },
      options: {
        type: Object,
        default: () => {}
      },
      ids: {
        type: Array,
        default: () => []
      }
    },

    data () {
      return {
        defaultOptions: {
          label: 'label',
          checkbox: true,
          checkedOpen: true,
          folderBold: true,
          idsWithParent: true,
          depthOpen: 0
        },
        termOptions: {},
        idsWithParent: [],
        state: 0
      }
    },

    created () {
      this.initOptions()
      this.idsWithParent = this.ids.slice(0)
    },

    watch: {
      options: {
        handler: function (val) {
          this.initOptions()
        },
        deep: true
      }
    },

    computed: {
      filterlist () {
        let self = this;
        const exp = new RegExp(self.filter, 'ig');

        function deepFilter(children){
          return children.filter(function(item){
            let c = item.children ? deepFilter(item.children) : exp.test(item.label)
            if(c.length === 0){
              return false;
            } else {
              return c;
            }
          })
        }
       return deepFilter(this.treeData.children);
      },
    },

    methods: {
      handle (item) {
        this.$emit('handle', item)
      },

      initOptions () {
        this.termOptions = Object.assign({}, this.defaultOptions, this.options)
        this.idsWithParent = this.ids.slice(0)
      }
    }
  }
</script>
