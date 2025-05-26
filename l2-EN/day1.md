# 🚀 improve-a-function

## 📝 Problem Description


### 💡 Input Example

```javascript

// Given an input of array, 
// which is made of items with >= 3 properties
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

1. **Function Purpose**: What does this function `excludeItems` do?
2. **Correctness**: Is this function working as expected?
3. **Time Complexity**: What is the time complexity of this function?
4. **Optimization**: How would you optimize it?

## 💪 Function Behavior Analysis
```javascript
function excludeItems(items, excludes) { 
  excludes.forEach(pair => { 
    items = items.filter(item => item[pair.k] === item[pair.v])
  })
  return items;
}
```
#### 🚀 Further Optimization Using Map
```javascript
function excludeItems(items, excludes) {
  // Convert excludes to a Set of stringified keys for fast lookup
  const excludeSet = new Set(excludes.map(e => `${e.k}:${e.v}`));

  return items.filter(item => {
    // Exclude if any (k,v) matches
    return !Object.entries(item).some(([key, val]) => excludeSet.has(`${key}:${val}`));
  });
}
```

**What does this function do?**:
- The function is intended to exclude items from items that match any key:value pair in excludes. However, it actually retains only the items that match all the specified key-value pairs, which is the opposite of the intended behavior.

**Is the function working as expected?**:
- No.
  - The name `excludeItems` and the structure of the `excludes` array imply we should remove any item that matches any of the key-value pairs in `excludes`.
  - But this function keeps only those items that match all key-value pairs due to repeated `filter()` with `===` condition (logical AND behavior across filters).

**Example**:
```javascript
items = [
  {color: 'red', type: 'tv', age: 18}, 
  {color: 'silver', type: 'phone', age: 20},
  {color: 'blue', type: 'book', age: 17}
];

excludes = [ 
  {k: 'color', v: 'silver'}, 
  {k: 'type', v: 'tv'}
];
```
- Expected behavior: remove any item with color silver OR type tv.
- Current behavior: keeps only items where color === 'silver' AND type === 'tv' — none in this case → [].


## 💪 Time Complexity
**For n = items.length, m = excludes.length**:
- excludes.forEach -> O(m)
- items.filter -> O(n)

➡️ Total: O(m × n)

- Each filter() scans the whole items array → inefficient if both are large.

**✅ Corrected + Optimized Function (Expected Behavior)**:
#### Goal: Remove any item that matches any key-value pair in excludes.
```javascript
function excludeItems(items, excludes) {
  // Convert excludes to a Set of stringified keys for fast lookup
  const excludeSet = new Set(excludes.map(e => `${e.k}:${e.v}`));

  return items.filter(item => {
    // Exclude if any (k,v) matches
    return !Object.entries(item).some(([key, val]) => excludeSet.has(`${key}:${val}`));
  });
}
```
#### Explanation
- We use a Set to allow O(1) lookup for key-value exclusion conditions.
- For each item, we check if any of its property matches an exclude pair.
- We exclude the item if any match is found (some + !).

#### Time Complexity (Optimized)
- Set construction: O(m)
- filter over n items × p properties (typically 3–5): O(n × p)
- Set.has() lookup: O(1)
#### Total: O(m + n × p) — efficient and correct.

### Final Answer Summary
- Q1 What does the function do?
- A1 Retains only items matching all exclude key-value pairs (wrong)

- Q2 Is it working as expected?
- A2 ❌ No — logic is incorrect (AND instead of OR, and retain instead of exclude)

- Q3 Time complexity
- A3 O(m × n) — for m excludes and n items

- Q4 Optimized solution
- A4 Use Set for exclusion matching and filter + some for correctness
