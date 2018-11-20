# Vue.js定制组件

2018年11月18日

### 1. 定制 `element-table`

🏹 目标

1. 抽离 `headers` 配置
2. 根据业务定制 `td`
3. 分页 `e-pagination`

💡 方案

```html
<template>
  <div id="b-table">
    <el-table :data="listData" stripe fit border class="el-table">
      <template v-for="(item, index) in headers">
        <!-- 展开行功能 -->
        <el-table-column v-if="item.children" :key="index" type="expand">
          <template slot-scope="props">
            <el-table :data="props.row[item.keyName]" stripe>
              <el-table-column :label="item.label" :width="item.width"
              :align="item.align" :fixed="item.fixed">
                <el-table-column v-for="(child, childIndex) in item.children"
                :label="child.label" :prop="child.keyName" :width="child.width"
                :align="child.align" :fixed="child.fixed" :key="childIndex"/>
              </el-table-column>
            </el-table>
          </template>
        </el-table-column>
        <!-- 复杂的td <采用插槽> -->
        <slot v-if="item.type === 'slot'" :name="item.slot"/>
        <!-- 简单的td <单纯文本框> -->
        <el-table-column v-if="item.type === 'text'"
        :label="item.label" :prop="item.keyName"
        :width="item.width" :align="item.align"
        :fixed="item.fixed" :key="index"/>
      </template>
    </el-table>
    <!-- 分页 -->
    <el-pagination
      :current-page.sync="pagination.currentPage"
      :page-size="pagination.size"
      :total="pagination.totalCount"
      background
      class="b-table-pagination"
      layout="total, prev, pager, next, jumper"
      @current-change="handleCurrentChange"/>
  </div>
</template>
```

```javascript
props: {
    listData: {
        type: Array,
        required: true,
    },
    headers: {
        type: Array,
        required: true,
    },
    expand: {
        type: Boolean,
        required: false,
        default: false,
    },
    pagination: {
        type: Object,
        required: false,
        default: function () {
            return {
                currentPage: 0, // 当前页码
                size: 0,        // 每页大小
                totalCount: 0,  // 总数
            };
        },
    }
}
```

```javascript
// headers配置示例
const price = [
    {
        label: 'td 名称',
        width: 'td 宽度',
        keyName: 'td 对应数据字段',
        align: 'td 是否居中',
        fixed: false, // td 是否固定列
        type: 'slot', // td 类型, 可选择值 ‘text’，‘slot’
        slot: 'action', 
    },
    {   ...
        type: 'text',
    },
    {   ...
        children: [
            { ... },
            { ... },
        ],
    },
];
export default price;
```

参考出处如下：

https://jsfiddle.net/g5jykkrn/2/