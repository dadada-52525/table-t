<template>
    <a-table :columns="columns" :data-source="data"  :pagination="ipagination"  @change="handleTableChange">
  </a-table>
</template>

<script>
import STable from '@/components'

const columns = [
  {
    title: 'id',
    dataIndex: 'id',
    customRender: (text, record, index) => {
       if (record.name === '合计') {
         return '合计'
       } else {
         return text
       }
    }
  },
  {
    title: '金额',
    dataIndex: 'cost'
  },
  {
    title: '项目数',
    dataIndex: 'project',
    scopedSlots: { customRender: 'description' }
  },
  {
    title: '订单数',
    dataIndex: 'order',
    sorter: true,
    needTotal: true,
    customRender: (text) => text + ' 次'
  },
  {
    title: '职级',
    dataIndex: 'level',
    scopedSlots: { customRender: 'status' }
  },
  {
    title: '职级',
    dataIndex: 'type'
  }
]
export default {
  components: {
    STable
  },
  name: 'TestList',
  data () {
    this.columns = columns
    return {
      data: [],
      ipagination: {
        current: 1,
        pageSize: 6,
        total: 0
      }
    }
  },
  created () {
      this.getData(this.ipagination.current, this.ipagination.pageSize)
  },
  methods: {
    handleTableChange (pagination) {
      this.getData(pagination.current, pagination.pageSize)
    },
    getAccount (data) {
      const sum = {}
         let temp
           data.map(item => {
          for (const i in item) {
            sum[i] = sum[i] || 0
            temp = Number(item[i]) || ''
            temp ? sum[i] += temp : sum[i] = temp
          }
        })
        return sum
    },
    getData (current, pageSize) {
      pageSize = pageSize - 0 - 1
      fetch(`http://localhost:3000/posts?_page=${current}&_limit=${pageSize}`, {
       method: 'GET'
    })
      .then((response) => {
        this.ipagination.total = response.headers.get('X-Total-Count') - 0
        this.ipagination.pageSize = pageSize + 1
        this.ipagination.current = current
        return response.json()
      })
      .then((myJson) => {
        const sum = this.getAccount(myJson)
         myJson.push({
             id: 13,
            cost: sum.cost,
            project: sum.project,
            order: sum.order,
            level: sum.level,
            name: '合计'
         })
        this.data = myJson
      })
    }
  }
}
</script>
