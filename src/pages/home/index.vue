<template>
  <Page>
    <div slot="header">
      <PagePath></PagePath>
      <SearchBar :options="searchOptions" @search="onSearch"></SearchBar>
      <ButtonBar :buttons="buttons" @buttonClick="onButtonClick"></ButtonBar>
    </div>
    <div slot-scope="scope" style="margin:5px;">
      <!--数据列表-->
      <DataGridEx
        ref="datagrid"
        :style="{'height': scope.height + 'px'}"
        url="/api/admin/system/system/loadlist"
        :params="searchParams">
        <template slot="operation" slot-scope="scope">
          <LinkButtonEx iconCls="icon-edit" btnCls="btn-warning" @click="onEdit(scope.row)" :disabled="!getRight('edit')"></LinkButtonEx>
        </template>
        <GridColumn field="systemId" title="系统ID" :sortable="false" :width="300"></GridColumn>
        <GridColumn field="systemName" title="系统名称"></GridColumn>
      </DataGridEx>
      <!--编辑页面-->
      <DialogEx ref="dialog" title="系统信息" :height="200" :width="400">
        <EditPage
          :params="pageParams"
          url="/api/admin/system/system/loadedit"
          post="/api/admin/system/system/savedata"
          @submitSuccess="onSubmitSuccess">
        </EditPage>
      </DialogEx>
    </div>
  </Page>
</template>

<script>
import ListPageBase from '../../components/mixins/list-page-base'
import EditPage from './edit'
export default {
  name: 'HomeIndex',
  mixins: [ListPageBase],
  components: {
    EditPage
  },
  data () {
    return {
      searchOptions: [
        { field: 'systemName', text: '系统名称', component: 'TextBox', propsData: {placeholder: '输入关键字搜索'} }
      ],
      buttons: [
        'add',
        'delete',
        'import',
        'export',
        { text: '编辑', iconCls: 'icon-scan', btnCls: 'btn-success', handler: this.onEdit }
      ],
      searchParams: [],
      pageParams: { id: '' }
    }
  },
  methods: {
    onSearch (params) {
      this.searchParams = params
    },
    onButtonClick (type) {
      switch (type) {
        case 'add':
          this.onAdd()
          break
        case 'delete':
          this.onDelete()
          break
        default:
          break
      }
    },
    onAdd () {
      this.pageParams.id = ''
      this.$refs.dialog.open(false)
    },
    onDelete () {
      this.$refs.datagrid.remove({
        url: '/api/admin/system/system/deletedata',
        valueField: 'systemId'
      })
    },
    onEdit (row) {
      this.pageParams.id = row.systemId
      this.$refs.dialog.open()
    },
    onSubmitSuccess () {
      this.$refs.datagrid.reload()
    }
  }
}
</script>

<style lang="less">

</style>
