```js
import { reactive, ref } from 'vue'
// 一个用于重置对象字段为原始值的函数
import { resetObjToPrimitiveType } from '@/utils/tool'

/**
 * @description usePage 接收一个 opts 参数，返回列表所需数据
 * @param {Object} opts.searchForm - 默认查询参数
 * @param {Function} opts.getListApi  - 获取列表数据的接口
 * @param {Function} opts.customQueryParameters  - 自定义查询参数
 * @param {Function} opts.getListFunc  - 执行完 getList 成功后执行的逻辑 有一个opts参数
 * @param {Function} opts.resetFunc  - 执行完 reset 后执行的逻辑
 * @param {Function} opts.sizeChangeFunc  - 执行完 sizeChange 后执行的逻辑
 * @param {Function} opts.currentChangeFunc  - 执行完 currentChange 后执行的逻辑
 */
export const usePage = (opts) => {
  // searchForm 由外部传入，内部传入导出的数据无法推导类型即无法知道对象里有什么也会失去代码提示
  const {
    searchForm = {},
    getListApi,
    customQueryParameters = () => {},
    getListFunc = (opts) => {},
    resetFunc = () => {},
    sizeChangeFunc = () => {},
    currentChangeFunc = () => {}
  } = opts

  const reset = () => {
    Object.assign(searchForm, resetObjToPrimitiveType(searchForm))
    resetFunc()
    handleCurrentChange(1)
  }

  const page = reactive({
    pageSize: 10,
    pageNo: 1,
    total: 0
  })

  const tableData = ref([])
  const getList = () => {
    const opts = {
      ...page,
      ...searchForm,
      ...customQueryParameters()
    }

    getListApi(opts).then((res) => {
      if (res.code === 0) {
        tableData.value = res.data?.rows || []
        page.total = res.data?.total || 0

        getListFunc(opts)
      }
    })
  }

  const handleSizeChange = (size) => {
    page.pageSize = size
    sizeChangeFunc()
    getList()
  }

  const handleCurrentChange = (cur) => {
    page.pageNo = cur
    currentChangeFunc()
    getList()
  }

  return {
    searchForm,
    reset,
    page,
    tableData,
    handleSizeChange,
    handleCurrentChange
  }
}
```

`resetObjToPrimitiveType`

```js
// 重置对象数据为基本数据类型
// 数字、数组、对象、其余重置为空字符串
export const resetObjToPrimitiveType = (data) => {
  if (!data) {
    return data
  } else {
    Object.keys(data).map((item) => {
      if (typeof data[item] === 'number') {
        data[item] = 0
        return
      }

      if (Array.isArray(data[item])) {
        data[item] = []
        return
      }

      if (Object.prototype.toString.call(data[item]) === '[object Object]') {
        data[item] = resetObjToPrimitiveType(data[item])
      } else {
        data[item] = ''
      }
    })
    return data
  }
}
```

### 组件内使用

```js
import { reactive, ref, computed } from 'vue'
import { usePage } from '@/composables/usePage'
import testModel from '@/model/test'

// 查询参数
const searchForm = reactive({
  createEndTime: '',
  createStartTime: ''
})

// 接收 查询参数、获取列表的接口 返回 列表所需要的数据、分页参数、分页函数等
const { reset, page, tableData, handleSizeChange, handleCurrentChange } = usePage({
  searchForm,
  getListApi: testModel.getList
})

// 首次获取数据使用 reset方式即可 tableData 的数据自动更新
reset()
```

`组合式函数`其实就是一个函数接收一些参数返回一些东西，将逻辑进行封装、共用。

### 如果函数依赖 store、router 等

```javascript
import {useStore} from 'vuex'
import {computed} from 'vue'

export const useTest = () => {
    // 获取store
  const store = useStore()
  
  const getOrgById = (id) => {
      // 使用
    const orgObj = computed(() => store.state.orgObj)
    return orgObj.value[id]
  }

  return {
    getOrgById
  }
}
```
