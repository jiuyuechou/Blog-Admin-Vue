<template>
  <page-header-wrapper title=" ">
    <a-card :bordered="false">
      <SearchForm ref="SearchCategoryForm" @reloadData="reloadData"/>
      <div class="table-operator">
        <a-button type="primary" icon="plus" @click="createHandler">新建</a-button>
      </div>
      <s-table
        ref="table"
        size="default"
        rowKey="id"
        :columns="columns"
        :data="loadData"
        :alert="options.alert"
        :rowSelection="options.rowSelection"
        showPagination="true"
      >
        <span slot="action" slot-scope="text, record">
          <template>
            <a @click="handleEdit(record)">编辑</a>
            <a-divider type="vertical" />
            <a-popconfirm
              title="确定删除这个标签？"
              @confirm="handleDelete(record)"
              @cancel="cancel"
              okText="Yes"
              cancelText="No"
            >
              <a href="#">删除</a>
            </a-popconfirm>
          </template>
        </span>
      </s-table>
      <CreateForm
        :formType="formType"
        :visible="visible"
        ref="createTagsForm"
        @resetData="resetData"
        @refreshTable="refreshTable"
      />
    </a-card>
  </page-header-wrapper>
</template>

<script>
import { STable, Ellipsis } from '@/components'
import { table, filters } from './tags-constants'
import { fetchTagsList, deleteTags } from '@/api/tags'
import CreateForm from './modules/CreateForm'
import SearchForm from './modules/SearchForm'
export default {
  name: 'TagsList',
  components: {
    STable,
    Ellipsis,
    CreateForm,
    SearchForm
  },
  filters: filters,
  data () {
    return {
      queryParam: {},
      loadData: parameter => {
        return fetchTagsList(Object.assign(parameter, this.queryParam))
          .then(res => {
            return res
          })
      },
      options: {
        alert: { show: true, clear: () => { this.selectedRowKeys = [] } },
        rowSelection: {
          selectedRowKeys: this.selectedRowKeys,
          onChange: this.handleSelectChange
        }
      },
      columns: table.columns,
      visible: false,
      formType: 'create'
    }
  },
  created () { },
  beforeCreate () {},
  methods: {
    resetData (flag) {
      this.visible = flag
      this.record = null
    },
    refreshTable () {
      this.$refs.table.refresh()
    },
    reloadData (queryParam) {
      this.queryParam = queryParam
      this.refreshTable()
    },
    handleSub () {
      this.visible = true
    },
    createHandler () {
      this.formType = 'create'
      this.visible = true
      this.$refs.createTagsForm.resetForm()
    },
    handleEdit (record) {
      this.formType = 'edit'
      this.visible = true
      this.$refs.createTagsForm.handleEdit(record)
    },
    handleDelete (row) {
      deleteTags(row.id).then(res => {
        this.$notification.success({
          message: '删除成功'
        })
        this.$refs.table.refresh()
      })
    },
    cancel () { },
    handleSelectChange (value) {
    }
  }
}
</script>

<style scoped>

</style>
