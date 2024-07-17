---
title: 前端
date: 2024-07-17 09:45:24
---
vue v-for 循环顺序不对
原因：在data目录里面没有定义要循环的数据变量tableData
```bash
<div class="table-item" v-for="(item, i) in tableData" :key="i">
```

代码片段
```bash
<Select v-model="sortvalue" style="width: 100px" placeholder="排序方式"  @on-change="changeorder(sortvalue)">
    <Option v-for="(k, v) in sortList" :value="v" :key="k">{{ k }}</Option>
</Select>
```
