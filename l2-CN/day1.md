# 🚀 函数优化练习

## 📝 问题描述

给定一个对象数组 `items`，每个对象包含至少 3 个属性，以及一个排除条件数组 `excludes`，需要实现一个函数来根据排除条件过滤数组。

### 💡 输入示例

```javascript
// 输入数组示例
let items = [
  {color: 'red', type: 'tv', age: 18}, 
  {color: 'silver', type: 'phone', age: 20},
  {color: 'blue', type: 'book', age: 17}
] 

// 排除条件数组示例
const excludes = [ 
  {k: 'color', v: 'silver'}, 
  {k: 'type', v: 'tv'}, 
  // ... 更多排除条件
] 
```

### ⚙️ 当前实现

```javascript
function excludeItems(items, excludes) { 
  excludes.forEach( pair => { 
    items = items.filter(item => item[pair.k] === item[pair.v])
  })
  return items
}
```

## 🔍 问题分析

1. **函数功能**：这个函数 `excludeItems` 的目的是什么？
2. **正确性**：当前实现是否按预期工作？
3. **时间复杂度**：当前实现的时间复杂度是多少？
4. **优化建议**：如何优化这个函数？

## 💪 解决方案

### 1. 函数分析
当前函数试图根据 `excludes` 数组中的条件来过滤 `items` 数组，但实现存在问题。

### 2. 正确性分析
当前实现存在以下问题：
- ❌ 比较逻辑错误：`item[pair.k] === item[pair.v]` 是错误的，应该是 `item[pair.k] !== pair.v`
- ⚠️ 每次循环都重新赋值 `items`，可能导致性能问题

### 3. 时间复杂度分析

#### 📊 当前实现复杂度
- **外层循环**：`excludes.forEach` 遍历 m 次（m 为 excludes 数组长度）
- **内层操作**：每次循环都执行 `filter` 操作，需要遍历 n 次（n 为 items 数组长度）
- **总时间复杂度**：O(m * n)

#### 💾 空间复杂度
- 每次 `filter` 操作都会创建新数组
- 空间复杂度：O(n)

#### 🔄 优化后的实现
```javascript
function excludeItems(items, excludes) {
  return items.filter(item => {
    return !excludes.some(pair => item[pair.k] === pair.v);
  });
}
```

**优化分析**：
- **时间复杂度**：仍然是 O(m * n)
  - `filter` 操作需要遍历 n 次
  - 每次遍历中，`some` 操作最多需要遍历 m 次
- **空间复杂度**：O(1)
  - 不再创建中间数组
  - 只使用常量额外空间

#### 🚀 使用 Map 的进一步优化
```javascript
function excludeItems(items, excludes) {
  // 预处理 excludes 数组，创建查找表
  const excludeMap = new Map();
  excludes.forEach(({k, v}) => {
    if (!excludeMap.has(k)) {
      excludeMap.set(k, new Set());
    }
    excludeMap.get(k).add(v);
  });

  return items.filter(item => {
    return !Object.entries(item).some(([key, value]) => {
      return excludeMap.has(key) && excludeMap.get(key).has(value);
    });
  });
}
```

**Map 解决方案的性能**：
- **时间复杂度**：O(n + m)
  - 预处理 excludes 数组：O(m)
  - 过滤 items 数组：O(n)
- **空间复杂度**：O(m)
  - 存储 excludeMap 需要 O(m) 空间

### 4. 优化建议
1. ✅ 修正比较逻辑
2. ✅ 使用单次 filter 操作
3. ✅ 使用 Map 和 Set 数据结构优化查找性能
4. ✅ 考虑数据预处理，减少重复计算

### 📈 性能对比

| 实现方式 | 时间复杂度 | 空间复杂度 | 说明 |
|:--------:|:----------:|:----------:|:-----|
| 原始实现 | O(m * n) | O(n) | 每次迭代都创建新数组 |
| 首次优化 | O(m * n) | O(1) | 无中间数组 |
| Map 实现 | O(n + m) | O(m) | 最优时间复杂度，但占用更多空间 |

### 🎯 使用场景建议

1. **原始实现** ❌
   - 不推荐使用，存在逻辑错误且性能较差

2. **首次优化** ⚡
   - 适用于小数据集且内存受限的场景
   - 实现简单直观

3. **Map 实现** 🏆
   - 适用于大数据集且性能要求高的场景
   - 时间复杂度最优
   - 推荐在生产环境使用

---

> 💡 **提示**：根据具体使用场景选择合适的实现方式：
> - 小数据集：使用首次优化方案
> - 大数据集：使用 Map 实现方案
> - 永远不要使用原始实现 