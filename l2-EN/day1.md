# 🚀 Function Optimization Exercise

## 📝 Problem Description

Given an array of objects `items`, where each object has at least 3 properties, and an array of exclusion conditions `excludes`, implement a function to filter the array based on the exclusion conditions.

### 💡 Input Example

```javascript
// Input array example
let items = [
  {color: 'red', type: 'tv', age: 18}, 
  {color: 'silver', type: 'phone', age: 20},
  {color: 'blue', type: 'book', age: 17}
] 

// Exclusion conditions array example
const excludes = [ 
  {k: 'color', v: 'silver'}, 
  {k: 'type', v: 'tv'}, 
  // ... more exclusion conditions
] 
```

### ⚙️ Current Implementation

```javascript
function excludeItems(items, excludes) { 
  excludes.forEach( pair => { 
    items = items.filter(item => item[pair.k] === item[pair.v])
  })
  return items
}
```

## 🔍 Problem Analysis

1. **Function Purpose**: What is the purpose of the `excludeItems` function?
2. **Correctness**: Is the current implementation working as expected?
3. **Time Complexity**: What is the time complexity of the current implementation?
4. **Optimization**: How can we optimize this function?

## 💪 Solution

### 1. Function Analysis
The current function attempts to filter the `items` array based on conditions in the `excludes` array, but has implementation issues.

### 2. Correctness Analysis
The current implementation has the following problems:
- ❌ Incorrect comparison logic: `item[pair.k] === item[pair.v]` is wrong, should be `item[pair.k] !== pair.v`
- ⚠️ Reassigning `items` in each loop iteration, which may cause performance issues

### 3. Time Complexity Analysis

#### 📊 Current Implementation Complexity
- **Outer loop**: `excludes.forEach` iterates m times (m is the length of excludes array)
- **Inner operation**: Each iteration performs a `filter` operation, requiring n iterations (n is the length of items array)
- **Total time complexity**: O(m * n)

#### 💾 Space Complexity
- Each `filter` operation creates a new array
- Space complexity: O(n)

#### 🔄 Optimized Implementation
```javascript
function excludeItems(items, excludes) {
  return items.filter(item => {
    return !excludes.some(pair => item[pair.k] === pair.v);
  });
}
```

**Optimization Analysis**:
- **Time complexity**: Still O(m * n)
  - `filter` operation requires n iterations
  - Each iteration may require up to m iterations for `some` operation
- **Space complexity**: O(1)
  - No intermediate arrays created
  - Only uses constant extra space

#### 🚀 Further Optimization Using Map
```javascript
function excludeItems(items, excludes) {
  // Preprocess excludes array to create lookup table
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

**Map Solution Performance**:
- **Time complexity**: O(n + m)
  - Preprocessing excludes array: O(m)
  - Filtering items array: O(n)
- **Space complexity**: O(m)
  - Storage for excludeMap requires O(m) space

### 4. Optimization Recommendations
1. ✅ Fix the comparison logic
2. ✅ Use a single filter operation
3. ✅ Utilize Map and Set data structures for better lookup performance
4. ✅ Consider data preprocessing to reduce redundant computations

### 📈 Performance Comparison

| Implementation | Time Complexity | Space Complexity | Notes |
|:--------------:|:---------------:|:----------------:|:------|
| Original | O(m * n) | O(n) | Creates new array in each iteration |
| First Optimization | O(m * n) | O(1) | No intermediate arrays |
| Map-based | O(n + m) | O(m) | Best time complexity, uses more space |

### 🎯 Usage Recommendations

1. **Original Implementation** ❌
   - Not recommended due to incorrect logic and poor performance

2. **First Optimization** ⚡
   - Good for small datasets where memory is a concern
   - Simple and straightforward implementation

3. **Map-based Solution** 🏆
   - Best for large datasets where performance is critical
   - Optimal time complexity
   - Recommended for production use

---

> 💡 **Tip**: Choose the implementation based on your specific use case:
> - Small datasets: Use the first optimization
> - Large datasets: Use the Map-based solution
> - Never use the original implementation 