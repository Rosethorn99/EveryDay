## 安装依赖

> 指定版本是为了防止在使用过程中出现 utils 报错

```kotlin
npm install xlsx@0.17.0
npm install file-saver@2.0.5
```

## Export2Excel.js

```js
/* eslint-disable */
import { saveAs } from "file-saver";
import XLSX from "xlsx";

function generateArray(table) {
  var out = [];
  var rows = table.querySelectorAll("tr");
  var ranges = [];
  for (var R = 0; R < rows.length; ++R) {
    var outRow = [];
    var row = rows[R];
    var columns = row.querySelectorAll("td");
    for (var C = 0; C < columns.length; ++C) {
      var cell = columns[C];
      var colspan = cell.getAttribute("colspan");
      var rowspan = cell.getAttribute("rowspan");
      var cellValue = cell.innerText;
      if (cellValue !== "" && cellValue == +cellValue) cellValue = +cellValue;

      //Skip ranges
      ranges.forEach(function(range) {
        if (
          R >= range.s.r &&
          R <= range.e.r &&
          outRow.length >= range.s.c &&
          outRow.length <= range.e.c
        ) {
          for (var i = 0; i <= range.e.c - range.s.c; ++i) outRow.push(null);
        }
      });

      //Handle Row Span
      if (rowspan || colspan) {
        rowspan = rowspan || 1;
        colspan = colspan || 1;
        ranges.push({
          s: {
            r: R,
            c: outRow.length,
          },
          e: {
            r: R + rowspan - 1,
            c: outRow.length + colspan - 1,
          },
        });
      }

      //Handle Value
      outRow.push(cellValue !== "" ? cellValue : null);

      //Handle Colspan
      if (colspan) for (var k = 0; k < colspan - 1; ++k) outRow.push(null);
    }
    out.push(outRow);
  }
  return [out, ranges];
}

function datenum(v, date1904) {
  if (date1904) v += 1462;
  var epoch = Date.parse(v);
  return (epoch - new Date(Date.UTC(1899, 11, 30))) / (24 * 60 * 60 * 1000);
}

function sheet_from_array_of_arrays(data, opts) {
  var ws = {};
  var range = {
    s: {
      c: 10000000,
      r: 10000000,
    },
    e: {
      c: 0,
      r: 0,
    },
  };
  for (var R = 0; R != data.length; ++R) {
    for (var C = 0; C != data[R].length; ++C) {
      if (range.s.r > R) range.s.r = R;
      if (range.s.c > C) range.s.c = C;
      if (range.e.r < R) range.e.r = R;
      if (range.e.c < C) range.e.c = C;
      var cell = {
        v: data[R][C],
      };
      if (cell.v == null) continue;
      var cell_ref = XLSX.utils.encode_cell({
        c: C,
        r: R,
      });

      if (typeof cell.v === "number") cell.t = "n";
      else if (typeof cell.v === "boolean") cell.t = "b";
      else if (cell.v instanceof Date) {
        cell.t = "n";
        cell.z = XLSX.SSF._table[14];
        cell.v = datenum(cell.v);
      } else cell.t = "s";

      ws[cell_ref] = cell;
    }
  }
  if (range.s.c < 10000000) ws["!ref"] = XLSX.utils.encode_range(range);
  return ws;
}

function Workbook() {
  if (!(this instanceof Workbook)) return new Workbook();
  this.SheetNames = [];
  this.Sheets = {};
}

function s2ab(s) {
  var buf = new ArrayBuffer(s.length);
  var view = new Uint8Array(buf);
  for (var i = 0; i != s.length; ++i) view[i] = s.charCodeAt(i) & 0xff;
  return buf;
}

export function export_table_to_excel(id) {
  var theTable = document.getElementById(id);
  var oo = generateArray(theTable);
  var ranges = oo[1];

  /* original data */
  var data = oo[0];
  var ws_name = "SheetJS";

  var wb = new Workbook(),
    ws = sheet_from_array_of_arrays(data);

  /* add ranges to worksheet */
  // ws['!cols'] = ['apple', 'banan'];
  ws["!merges"] = ranges;

  /* add worksheet to workbook */
  wb.SheetNames.push(ws_name);
  wb.Sheets[ws_name] = ws;

  var wbout = XLSX.write(wb, {
    bookType: "xlsx",
    bookSST: false,
    type: "binary",
  });

  saveAs(
    new Blob([s2ab(wbout)], {
      type: "application/octet-stream",
    }),
    "test.xlsx"
  );
}

export function export_json_to_excel({
  multiHeader = [],
  header,
  data,
  filename,
  merges = [],
  autoWidth = true,
  bookType = "xlsx",
} = {}) {
  /* original data */
  filename = filename || "excel-list";
  data = [...data];
  data.unshift(header);
  console.log("data", data);
  // return
  for (let i = multiHeader.length - 1; i > -1; i--) {
    data.unshift(multiHeader[i]);
  }

  var ws_name = "SheetJS";
  var wb = new Workbook(),
    ws = sheet_from_array_of_arrays(data);

  if (merges.length > 0) {
    if (!ws["!merges"]) ws["!merges"] = [];
    merges.forEach((item) => {
      ws["!merges"].push(XLSX.utils.decode_range(item));
    });
  }

  if (autoWidth) {
    /*设置worksheet每列的最大宽度*/
    const colWidth = data.map((row) =>
      row.map((val) => {
        /*先判断是否为null/undefined*/
        if (val == null) {
          return {
            wch: 10,
          };
        } else if (val.toString().charCodeAt(0) > 255) {
          /*再判断是否为中文*/
          return {
            wch: val.toString().length * 2,
          };
        } else {
          return {
            wch: val.toString().length,
          };
        }
      })
    );
    /*以第一行为初始值*/
    let result = colWidth[0];
    for (let i = 1; i < colWidth.length; i++) {
      for (let j = 0; j < colWidth[i].length; j++) {
        if (result[j]["wch"] < colWidth[i][j]["wch"]) {
          result[j]["wch"] = colWidth[i][j]["wch"];
        }
      }
    }
    ws["!cols"] = result;
  }

  /* add worksheet to workbook */
  wb.SheetNames.push(ws_name);
  wb.Sheets[ws_name] = ws;

  var wbout = XLSX.write(wb, {
    bookType: bookType,
    bookSST: false,
    type: "binary",
  });
  saveAs(
    new Blob([s2ab(wbout)], {
      type: "application/octet-stream",
    }),
    `${filename}.${bookType}`
  );
}
```

## 1.导出使用

```javascript
import { export_json_to_excel } from "Export2Excel";

methods: {
    //导出事件
    exportExcel() {
        const tHeader = [] 
        const filterVal = [] 
        const data = this.formatJson(filterVal, this.list)
        export_json_to_excel({
          header: tHeader,
          data,
          filename: '',
          autoWidth: true,
          bookType: 'xlsx' 
        })
    },
    formatJson(filterVal, jsonData) {
      return jsonData.map(v => filterVal.map(j => v[j]))
    }
},
```

`导出demo`

```vue
<template>
  <div class="excelOut">
    <el-card>
      <div slot="header">
        <span>导出表格</span>
      </div>
      <el-input
        type="text"
        v-model="filename"
        placeholder="输入导出的文件名称"
        clearable
        class="wid300 mtrt20"
      ></el-input>
      <el-button type="primary" @click="excelDow" class="btnStyle1"
        >导出</el-button
      >
      <el-table
        :data="tabList"
        border
        stripe
        style="width: 100%;margin-top: 30px;"
      >
        <el-table-column prop="id" label="序号" width="80"> </el-table-column>
        <el-table-column prop="date" label="出生日期"> </el-table-column>
        <el-table-column prop="name" label="姓名"> </el-table-column>
        <el-table-column prop="sex" label="性别"> </el-table-column>
        <el-table-column prop="position" label="职业"> </el-table-column>
        <el-table-column prop="address" label="地址"> </el-table-column>
      </el-table>
    </el-card>
  </div>
</template>

<script>
export default {
  data() {
    return {
      tabList: [
        {
          id: 1,
          date: '1992-05-02',
          name: '王小虎',
          sex: '男',
          position: '医生',
          address: '上海市普陀区金沙江路 1518 弄'
        },
        {
          id: 2,
          date: '1999-05-04',
          name: '小明',
          sex: '男',
          position: '老师',
          address: '北京市朝阳区'
        },
        {
          id: 3,
          date: '1980-05-01',
          name: '小敏',
          sex: '女',
          position: '护士',
          address: '上海市浦东区 1519 弄'
        },
        {
          id: 4,
          date: '2016-05-03',
          name: '小黑',
          sex: '男',
          position: '学者',
          address: '上海市普陀区金沙江路 1516 弄'
        },
        {
          id: 5,
          date: '2016-05-03',
          name: '小虎',
          sex: '男',
          position: '运动员',
          address: '上海市普陀区金沙江路 1516 弄'
        },
        {
          id: 6,
          date: '2016-05-03',
          name: '小黑',
          sex: '男',
          position: '学者',
          address: '上海市普陀区金沙江路 1516 弄'
        },
        {
          id: 7,
          date: '2016-05-03',
          name: '小黑',
          sex: '男',
          position: '学者',
          address: '上海市普陀区金沙江路 1516 弄'
        },
        {
          id: 8,
          date: '2016-05-03',
          name: '小黑',
          sex: '男',
          position: '学者',
          address: '上海市普陀区金沙江路 1516 弄'
        },
        {
          id: 9,
          date: '2016-05-03',
          name: '小黑',
          sex: '男',
          position: '学者',
          address: '上海市普陀区金沙江路 1516 弄'
        },
        {
          id: 10,
          date: '2016-05-03',
          name: '小黑',
          sex: '男',
          position: '学者',
          address: '上海市普陀区金沙江路 1516 弄'
        }
      ],
      autoWidth: true,
      bookType: 'xlsx',
      filename: ''
    }
  },
  methods: {
    excelDow() {
      import('@/vendor/Export2Excel.js').then(moudle => {
        const tHeader = ['序号', '日期', '姓名', '性别', '职业', '地址']
        const filterVal = ['id', 'date', 'name', 'sex', 'position', 'address']
        const list = this.tabList
        const data = this.formatJson(filterVal, list)
        moudle.export_json_to_excel({
          header: tHeader,
          data,
          filename: this.filename === '' ? 'filename' : this.filename,
          autoWidth: this.autoWidth,
          bookType: this.bookType
        })
      })
    },
    formatJson(filterVal, jsonData) {
      return jsonData.map(v => filterVal.map(j => v[j]))
    }
  }
}
</script>
```

### 2.导入使用

封装组件`UploadExcel`

```vue
<template>
  <div>
    <input
      type="file"
      class="upFile"
      ref="fileExcel"
      accept=".xlsx, .xls"
      @change="uploadFile"
    />
    <el-button type="primary" @click="handleClick">导入</el-button>
  </div>
</template>

<script>
import XLSX from 'xlsx'
export default {
  props: {
    breforeUpload: Function,
    onSuccess: Function
  },
  data() {
    return {
      excelList: {
        header: null,
        results: null
      }
    }
  },
  methods: {
    handleClick() {
      this.$refs.fileExcel.click()
    },
    generateData({ header, results }) {
      this.excelList.header = header
      this.excelList.results = results
      this.onSuccess && this.onSuccess(this.excelList)
    },
    uploadFile(e) {
      const file = e.target.files[0]
      this.$refs.fileExcel.value = null
      this.readData(file)
    },
    readData(file) {
      return new Promise(resolve => {
        const readfile = new FileReader()
        readfile.onload = e => {
          const data = e.target.result
          const workbook = XLSX.read(data, { type: 'array' })
          const firstSheetName = workbook.SheetNames[0]
          const worksheet = workbook.Sheets[firstSheetName]
          const header = this.getHeaderRow(worksheet)
          const results = XLSX.utils.sheet_to_json(worksheet)
          this.generateData({ header, results })
          resolve()
        }
        readfile.readAsArrayBuffer(file)
      })
    },
    getHeaderRow(sheet) {
      const headers = []
      const range = XLSX.utils.decode_range(sheet['!ref'])
      let C
      const R = range.s.r
      /* start in the first row */
      for (C = range.s.c; C <= range.e.c; ++C) {
        /* walk every column in the range */
        const cell = sheet[XLSX.utils.encode_cell({ c: C, r: R })]
        /* find the cell in the first row */
        let hdr = 'UNKNOWN ' + C // <-- replace with your desired default
        if (cell && cell.t) hdr = XLSX.utils.format_cell(cell)
        headers.push(hdr)
      }
      return headers
    }
  }
}
</script>
<style lang="scss" scoped>
.upFile {
  display: none;
}
</style>
```

使用导入组件UploadExcel：

```vue
<script>
import UploadExcel from '@/components/UploadExcel'
export default {
  data() {
    return {
      tabList: [],
      tabHeader: []
    }
  },
  methods: {
    generateData({ header, results }) {
      this.tabHeader = header
      this.tabList = results
    }
  },
  components: {
    UploadExcel
  }
}
</script>
```

`引入demo`，页面引用导入组件`UploadExcel`。

```vue
<template>
  <div class="excelIn">
    <el-card>
      <div slot="header">
        <span>导入表格</span>
      </div>
      <upload-excel :on-success="generateData"></upload-excel>
      <el-table
        :data="tabList"
        border
        stripe
        style="width: 100%;margin-top: 30px;"
      >
        <el-table-column
          v-for="item in tabHeader"
          :prop="item"
          :key="item"
          :label="item"
        ></el-table-column>
      </el-table>
    </el-card>
  </div>
</template>

<script>
import UploadExcel from '@/components/UploadExcel'
export default {
  data() {
    return {
      tabList: [],
      tabHeader: []
    }
  },
  methods: {
    generateData({ header, results }) {
      this.tabHeader = header
      this.tabList = results
    }
  },
  components: {
    UploadExcel
  }
}
</script>
<style></style>
```
