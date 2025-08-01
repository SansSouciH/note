# Lua

---

### **1. 基本语法结构**
- **注释**：
  ```lua
  -- 单行注释
  --[[
      多行注释
  ]]
  ```

- **变量**：
  
  - 无需声明类型，默认全局（除非用 `local` 声明局部变量）。
  ```lua
  local x = 10    -- 局部变量
  y = "Hello"     -- 全局变量
  ```

---

### **2. 数据类型**
Lua 是动态类型语言，基本数据类型有：
- `nil`：空值（未定义的变量默认值为 `nil`）。
- `boolean`：`true` 或 `false`。
- `number`：整数和浮点数（Lua 5.3+ 区分 `integer` 和 `float`）。
- `string`：不可变字符串，用单引号或双引号包裹。
  ```lua
  local str = "Lua"
  local multi_line = [[多行
  字符串]]
  ```
- `table`：唯一的复合数据结构，可作数组、字典或对象。
  ```lua
  local arr = {1, 2, 3}          -- 数组
  local dict = {name="Lua", age=28} -- 字典
  ```
- `function`：一等公民，支持匿名函数。
- `userdata`：C 语言扩展用的自定义类型。
- `thread`：协程（coroutine）。

---

### **3. 控制结构**
- **条件语句**：
  
  ```lua
  if a > 10 then
      print(">10")
  elseif a == 0 then
      print("=0")
  else
      print("<10")
  end
  ```
  
- **循环**：
  ```lua
  -- while 循环
  while i <= 10 do
      print(i)
      i = i + 1
  end
  
  -- for 循环
  for i = 1, 10, 2 do  -- 从1到10，步长2
      print(i)
  end
  
  -- 泛型for循环（遍历表）
  for k, v in pairs({"a", "b", "c"}) do
      print(k, v)
  end
  ```

---

### **4. 函数**
- **定义与调用**：
  ```lua
  local function add(a, b)
      return a + b
  end
  print(add(3, 5))  -- 输出8
  
  -- 匿名函数
  local mul = function(a, b) return a * b end
  ```

- **多返回值**：
  ```lua
  function swap(a, b)
      return b, a
  end
  x, y = swap(1, 2)  -- x=2, y=1
  ```

- **可变参数**：
  ```lua
  function sum(...)
      local s = 0
      for _, v in ipairs{...} do
          s = s + v
      end
      return s
  end
  ```

---

### **5. Table（表）**
- **数组与字典**：
  ```lua
  local t = {1, 2, 3}           -- 数组（索引从1开始）
  print(t[1])                   -- 输出1
  
  local person = {name="Alice", age=25}  -- 字典
  print(person["name"])          -- "Alice"
  print(person.name)             -- 语法糖
  ```

- **常用操作**：
  ```lua
  table.insert(t, 4)       -- 插入元素
  table.remove(t, 2)       -- 删除第2个元素
  table.sort(t)            -- 排序
  ```

---

### **6. 元表（Metatable）**
- 用于重载运算符或自定义行为：
  ```lua
  local mt = {
      __add = function(a, b)  -- 重载+运算符
          return a.value + b.value
      end
  }
  local a = {value=10}
  local b = {value=20}
  setmetatable(a, mt)
  setmetatable(b, mt)
  print(a + b)  -- 输出30
  ```

---

### **7. 错误处理**
- `pcall` 保护调用：
  ```lua
  local success, err = pcall(function()
      error("出错了！")
  end)
  if not success then print(err) end
  ```

---

### **8. 模块与文件操作**
- **模块导入**：
  ```lua
  local math = require("math")  -- 加载标准库
  print(math.sqrt(16))         -- 4.0
  ```

- **文件读写**：
  ```lua
  local file = io.open("test.txt", "r")
  local content = file:read("*a")
  file:close()
  ```

---

### **9. 协程（Coroutine）**
- 轻量级线程：
  ```lua
  local co = coroutine.create(function()
      print("协程启动")
      coroutine.yield()  -- 暂停
      print("恢复执行")
  end)
  coroutine.resume(co)  -- 输出"协程启动"
  coroutine.resume(co)  -- 输出"恢复执行"
  ```

---

### **10. 常用标准库**
- `string`：字符串处理（`gsub`, `match`, `find`）。
- `table`：表操作（`insert`, `remove`, `sort`）。
- `io`：文件输入输出。
- `os`：操作系统功能（`time`, `date`）。
- `math`：数学函数（`sin`, `random`, `sqrt`）。

---

### **特点总结**
- **轻量级**：适合嵌入其他语言（如C/C++/Python）。
- **高效**：基于寄存器的虚拟机。
- **可扩展**：通过C API扩展功能。
