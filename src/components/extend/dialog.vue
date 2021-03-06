<template>
  <Dialog
    ref="dialog"
    :title="title"
    :dialogStyle="dialogStyle"
    :modal="modal"
    :border="border"
    :borderType="borderType"
    :closed="closed"
    :draggable="draggable"
    bodyCls="f-column"
    @open="onOpen"
    @close="onClose">
    <div class="f-full" ref="body" v-loading="loading" v-scrollbar :style="{'height': bodyHeight + 'px'}">
      <slot></slot>
    </div>
    <div class="dialog-button" ref="buttons">
      <LinkButtonEx
        v-for="(item, index) in dialogButtons"
        :key="index"
        :iconCls="item.iconCls"
        :btnCls="item.btnCls"
        @click="item.handler"
        :text="item.text">
      </LinkButtonEx>
    </div>
  </Dialog>
</template>

<script>
// import Vue from 'vue'
export default {
  name: 'DialogEx',
  componentName: 'DialogEx',
  props: {
    title: {
      type: String
    },
    width: {
      type: [Number, String],
      default: 800
    },
    height: {
      type: [Number, String],
      default: 500
    },
    modal: {
      type: Boolean,
      default: true
    },
    draggable: {
      type: Boolean,
      default: true
    },
    border: {
      type: Boolean,
      default: false
    },
    borderType: {
      type: String,
      default: 'none'
    },
    closed: {
      type: Boolean,
      default: true
    },
    buttons: {
      type: Array,
      default: () => ['save', 'close']
    }
  },
  data () {
    return {
      dialogButtons: [],
      defaultButtons: {
        save: { text: '保存', iconCls: 'icon-save', btnCls: 'btn-info', handler: () => { this.onButtonClick('save') } },
        close: { text: '关闭', iconCls: 'icon-roundclose', btnCls: 'btn-default', handler: () => { this.onButtonClick('close') } }
      },
      componentInstance: null,
      defaultLoad: true,
      loading: false,
      bodyHeight: null
    }
  },
  created () {
    this.initButtons()
    this.$on('pageInstance', componentInstance => {
      this.componentInstance = componentInstance
      if (this.defaultLoad) {
        this.load()
      }
      this.componentInstance.$emit('open', this.defaultLoad)
    })
  },
  mounted () {
  },
  computed: {
    dialogStyle () {
      return {
        width: typeof this.width === 'number' ? `${this.width}px` : this.width,
        height: typeof this.height === 'number' ? `${this.height}px` : this.height
      }
    }
  },
  methods: {
    open (load = true) {
      this.defaultLoad = load
      this.$refs.dialog.open()
    },
    close () {
      this.$refs.dialog.close()
    },
    onOpen () {
      this.$emit('open')
      this.$nextTick(() => {
        this.bodyHeight = this.$refs.body.offsetHeight
      })
    },
    onClose () {
      this.$emit('close')
    },
    onButtonClick (type) {
      this.$emit('buttonClick', type)
      if (type === 'close') {
        this.close()
      } else if (type === 'save') {
        this.submit()
      }
    },
    initButtons () {
      let dialogButtons = []
      this.buttons.forEach(item => {
        if (typeof item === 'string') {
          dialogButtons.push(this.defaultButtons[item])
        } else if (typeof item === 'object') {
          dialogButtons.push(item)
        }
      })
      this.dialogButtons = dialogButtons
    },
    load () {
      if (this.componentInstance && this.componentInstance.load) {
        this.loading = true
        this.componentInstance.load().then(() => {
          this.loading = false
        }).catch(() => {
          this.loading = false
        })
      }
    },
    submit () {
      if (this.componentInstance && this.componentInstance.submit) {
        this.loading = true
        this.componentInstance.submit(this.defaultLoad).then(() => {
          this.loading = false
          this.close()
        }).catch(err => {
          this.loading = false
          this.$messager.alert({
            title: '警告',
            borderType: 'none',
            icon: 'error',
            msg: err.message
          })
        })
      }
    }
  }
}
</script>

<style>

</style>
