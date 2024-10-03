# C 語言 Clean Code 規則與範例

本指南展示了 C 語言開發中的 **Clean Code** 原則，並附有 **Bad** 和 **Good** 範例，幫助開發者撰寫更具可讀性、可維護性和高效的程式碼。

---

## 1. 命名和程式碼風格

### 1.1 保持一致的程式碼風格
- **Bad**:
  <pre><code>c
  void processData( int data){
  if(data>0)
  printf( "Data is positive");
  }
  </code></pre>

- **Good**:
  <pre><code>c
  void processData(int data) {
      if (data > 0) {
          printf("Data is positive\n");
      }
  }
  </code></pre>

### 1.2 避免巨大的標頭檔
- **Bad**:
  <pre><code>c
  // big_header.h
  #include <stdio.h>
  #include <stdlib.h>
  #include <string.h>

  void processData(int data);
  </code></pre>

- **Good**:
  <pre><code>c
  // small_header.h
  void processData(int data);  // 只包含必要的聲明
  </code></pre>

### 1.3 善用 `const` 關鍵字
- **Bad**:
  <pre><code>c
  void printData(int *data, int size) {
      for (int i = 0; i < size; i++) {
          printf("%d\n", data[i]);
      }
  }
  </code></pre>

- **Good**:
  <pre><code>c
  void printData(const int *data, int size) {
      for (int i = 0; i < size; i++) {
          printf("%d\n", data[i]);
      }
  }
  </code></pre>

---

## 2. 函數設計與模組化

### 2.1 單一職責原則
- **Bad**:
  <pre><code>c
  void handleUserLogin() {
      // 驗證用戶
      // 讀取用戶資料
      // 記錄登入時間
      // 返回登入結果
  }
  </code></pre>

- **Good**:
  <pre><code>c
  void validateUser() {
      // 驗證用戶邏輯
  }

  void readUserData() {
      // 讀取用戶資料邏輯
  }

  void logUserLogin() {
      // 記錄登入時間
  }

  void handleUserLogin() {
      validateUser();
      readUserData();
      logUserLogin();
  }
  </code></pre>

### 2.2 函數簽名簡潔化
- **Bad**:
  <pre><code>c
  void createUser(char *name, int age, char *address, char *phone, int id) {
      // 創建用戶邏輯
  }
  </code></pre>

- **Good**:
  <pre><code>c
  typedef struct {
      char *name;
      int age;
      char *address;
      char *phone;
      int id;
  } User;

  void createUser(User user) {
      // 創建用戶邏輯
  }
  </code></pre>

### 2.3 避免過度使用巨型函數
- **Bad**:
  <pre><code>c
  void processAll() {
      // 包含許多邏輯，函數過大且難以理解
  }
  </code></pre>

- **Good**:
  <pre><code>c
  void readData() {
      // 讀取數據
  }

  void analyzeData() {
      // 分析數據
  }

  void writeResults() {
      // 寫入結果
  }

  void processAll() {
      readData();
      analyzeData();
      writeResults();
  }
  </code></pre>

### 2.4 合理使用內聯函數
- **Bad**:
  <pre><code>c
  #define SQUARE(x) ((x) * (x))  // 使用宏定義，無類型檢查
  </code></pre>

- **Good**:
  <pre><code>c
  static inline int square(int x) {
      return x * x;
  }
  </code></pre>

### 2.5 分層設計
- **Bad**:
  <pre><code>c
  void processRequest() {
      // 直接處理數據並顯示結果，未有分層設計
  }
  </code></pre>

- **Good**:
  <pre><code>c
  // 資料存取層
  int fetchDataFromDatabase() {
      // 讀取資料庫邏輯
  }

  // 邏輯層
  void processData(int data) {
      // 處理數據邏輯
  }

  // 表示層
  void displayData() {
      int data = fetchDataFromDatabase();
      processData(data);
      printf("Data: %d\n", data);
  }
  </code></pre>

---

## 3. 錯誤處理與邊界檢查

### 3.1 強制執行邊界檢查
- **Bad**:
  <pre><code>c
  int getElement(int *arr, int index) {
      return arr[index];  // 沒有檢查邊界條件
  }
  </code></pre>

- **Good**:
  <pre><code>c
  int getElement(int *arr, int size, int index) {
      if (index < 0 || index >= size) {
          printf("Index out of bounds\n");
          return -1;  // 返回錯誤代碼
      }
      return arr[index];
  }
  </code></pre>

### 3.2 提供有意義的錯誤訊息
- **Bad**:
  <pre><code>c
  FILE *file = fopen("data.txt", "r");
  if (file == NULL) {
      printf("Error\n");  // 錯誤訊息不具體
  }
  </code></pre>

- **Good**:
  <pre><code>c
  FILE *file = fopen("data.txt", "r");
  if (file == NULL) {
      printf("Error: Unable to open file 'data.txt'.\n");  // 錯誤訊息具體
  }
  </code></pre>

### 3.3 專門的錯誤處理函數
- **Bad**:
  <pre><code>c
  FILE *file = fopen("data.txt", "r");
  if (file == NULL) {
      printf("Error opening file\n");
      return;
  }
  // 文件處理邏輯
  fclose(file);
  </code></pre>

- **Good**:
  <pre><code>c
  void handleFileError(FILE *file) {
      if (file == NULL) {
          printf("File error\n");
      }
  }

  void processFile(char *filename) {
      FILE *file = fopen(filename, "r");
      handleFileError(file);
      // 處理文件
      fclose(file);
  }
  </code></pre>

### 3.4 避免隱式類型轉換
- **Bad**:
  <pre><code>c
  int result = 3.5;  // 隱式轉換，可能導致資料丟失
  </code></pre>

- **Good**:
  <pre><code>c
  int result = (int)3.5;  // 顯式轉換，明確告知轉換過程
  </code></pre>

---

## 4. 資源管理與記憶體管理

### 4.1 手動記憶體管理與對稱釋放
- **Bad**:
  <pre><code>c
  char *data = (char *)malloc(100);
  // 使用 data
  // 忘記釋放記憶體
  </code></pre>

- **Good**:
  <pre><code>c
  char *data = (char *)malloc(100);
  if (data == NULL) {
      // 處理記憶體分配錯誤
  }
  // 使用 data
  free(data);  // 確保釋放記憶體
  </code></pre>

### 4.2 資源釋放順序
- **Bad**:
  <pre><code>c
  void processResources() {
      FILE *file = fopen("data.txt", "r");
      char *buffer = (char *)malloc(100);

      if (file == NULL || buffer == NULL) {
          // 忘記釋放資源
          return;
      }
  }
  </code></pre>

- **Good**:
  <pre><code>c
  void processResources() {
      FILE *file = fopen("data.txt", "r");
      char *buffer = (char *)malloc(100);

      if (file == NULL || buffer == NULL) {
          if (file != NULL) fclose(file);  // 釋放文件資源
          if (buffer != NULL) free(buffer);  // 釋放記憶體資源
          return;
      }

      // 使用資源
      free(buffer);  // 先釋放 buffer
      fclose(file);  // 再釋放文件
  }
  </code></pre>

### 4.3 避免使用裸指標
- **Bad**:
  <pre><code>c
  int *ptr = malloc(sizeof(int) * 10);
  ptr[10] = 42;  // 錯誤：越界操作
  free(ptr);
  </code></pre>

- **Good**:
  <pre><code>c
  void safeArrayAccess(int *arr, int size, int index, int value) {
      if (index < 0 || index >= size) {
          printf("Index out of bounds\n");
          return;
      }
      arr[index] = value;
  }
  </code></pre>

### 4.4 善用記憶體池
- **Bad**:
  <pre><code>c
  for (int i = 0; i < 1000; i++) {
      int *ptr = (int *)malloc(sizeof(int));  // 每次分配記憶體，效能低下
      free(ptr);
  }
  </code></pre>

- **Good**:
  <pre><code>c
  int *ptr = (int *)malloc(1000 * sizeof(int));  // 一次性分配較大的記憶體塊
  // 使用 ptr
  free(ptr);
  </code></pre>

### 4.5 模仿 RAII 模式
- **Bad**:
  <pre><code>c
  FILE *file = fopen("data.txt", "r");
  // 使用文件
  // 忘記釋放文件資源
  </code></pre>

- **Good**:
  <pre><code>c
  FILE *openFile(const char *filename) {
      FILE *file = fopen(filename, "r");
      if (file == NULL) {
          printf("Error opening file\n");
          exit(1);
      }
      return file;
  }

  void closeFile(FILE *file) {
      if (file != NULL) {
          fclose(file);
      }
  }

  void processFile(const char *filename) {
      FILE *file = openFile(filename);
      // 處理文件
      closeFile(file);  // 確保資源正確釋放
  }
  </code></pre>

---

## 5. 性能優化

### 5.1 避免過早優化
- **Bad**:
  <pre><code>c
  int sum(int a, int b) {
      return (a << 1) - (-b);  // 過於晦澀的操作，難以理解
  }
  </code></pre>

- **Good**:
  <pre><code>c
  int sum(int a, int b) {
      return a + b;  // 清晰明了
  }
  </code></pre>

### 5.2 考慮空間與時間的折衷
- **Bad**:
  <pre><code>c
  for (int i = 0; i < 1000000; i++) {
      // 使用簡單查找，時間複雜度高
  }
  </code></pre>

- **Good**:
  <pre><code>c
  // 使用額外的記憶體來優化查詢速度
  typedef struct {
      int *lookupTable;
      int size;
  } LookupTable;

  int query(LookupTable *table, int index) {
      if (index < 0 || index >= table->size) {
          return -1;  // 無效查詢
      }
      return table->lookupTable[index];
  }
  </code></pre>

### 5.3 效能分析與測試
- **Bad**:
  <pre><code>c
  // 沒有進行任何效能分析的程式碼
  </code></pre>

- **Good**:
  <pre><code>c
  gcc -pg my_program.c -o my_program  // 編譯啟用 gprof
  ./my_program  // 執行程式
  gprof my_program gmon.out > analysis.txt  // 產生效能分析報告
  </code></pre>

---

## 6. 多執行緒與同步化

### 6.1 同步化共享資料
- **Bad**:
  <pre><code>c
  int counter = 0;

  void incrementCounter() {
      counter++;  // 沒有同步機制，可能導致競爭條件
  }
  </code></pre>

- **Good**:
  <pre><code>c
  pthread_mutex_t mutex = PTHREAD_MUTEX_INITIALIZER;

  void incrementCounter(int *counter) {
      pthread_mutex_lock(&mutex);
      (*counter)++;
      pthread_mutex_unlock(&mutex);
  }
  </code></pre>

### 6.2 使用原子操作
- **Bad**:
  <pre><code>c
  int counter = 0;

  void incrementCounter() {
      counter++;  // 非原子操作，可能導致競爭條件
  }
  </code></pre>

- **Good**:
  <pre><code>c
  _Atomic int counter = 0;

  void incrementCounter() {
      counter++;  // 使用原子操作確保多執行緒安全
  }
  </code></pre>

---

## 7. 測試與開發流程

### 7.1 測試驅動開發 (TDD)
- **Bad**:
  <pre><code>c
  // 沒有測試，直接開發功能
  int add(int a, int b) {
      return a + b;
  }
  </code></pre>

- **Good**:
  <pre><code>c
  void testAddFunction() {
      assert(add(2, 3) == 5);
      assert(add(-1, 1) == 0);
  }

  int add(int a, int b) {
      return a + b;
  }

  int main() {
      testAddFunction();
      printf("All tests passed\n");
      return 0;
  }
  </code></pre>

### 7.2 單元測試和邊界測試
- **Bad**:
  <pre><code>c
  // 沒有對邊界條件進行測試
  </code></pre>

- **Good**:
  <pre><code>c
  void testProcessArray() {
      int arr[5] = {1, 2, 3, 4, 5};
      processArray(0, arr);  // 測試零大小
      processArray(5, arr);  // 正常大小
      processArray(SIZE_MAX, arr);  // 測試極端情況
  }
  </code></pre>

### 7.3 利用編譯器警告
- **Bad**:
  <pre><code>c
  // 沒有啟用警告選項的程式碼
  gcc my_program.c -o my_program
  </code></pre>

- **Good**:
  <pre><code>c
  gcc -Wall -Wextra my_program.c -o my_program  // 啟用所有警告
  </code></pre>

---

## 8. 平台相關與跨平台設計

### 8.1 封裝平台相關邏輯
- **Bad**:
  <pre><code>c
  void processFile() {
      #ifdef _WIN32
      // Windows 平台的特殊處理
      #endif
  }
  </code></pre>

- **Good**:
  <pre><code>c
  // 將平台相關邏輯封裝在模組內
  #ifdef _WIN32
  void platformSpecificFunction() {
      // Windows 實作
  }
  #else
  void platformSpecificFunction() {
      // 其他平台實作
  }
  #endif
  </code></pre>

### 8.2 使用條件編譯來處理平台差異
- **Bad**:
  <pre><code>c
  #ifdef _WIN32
  // Windows 代碼直接混雜在主邏輯中
  #endif
  </code></pre>

- **Good**:
  <pre><code>c
  #ifdef _WIN32
  void processWindows() {
      // Windows 平台的處理邏輯
  }
  #else
  void processUnix() {
      // Unix 平台的處理邏輯
  }
  #endif
  </code></pre>

---

## 9. 模組化設計與接口設計

### 9.1 降低模組耦合
- **Bad**:
  <pre><code>c
  void performTask() {
      readFile();
      processFile();
      writeFile();  // 模組之間高度耦合，難以測試和維護
  }
  </code></pre>

- **Good**:
  <pre><code>c
  void performTask(FileReader reader, FileProcessor processor, FileWriter writer) {
      reader();
      processor();
      writer();  // 使用回呼函數降低耦合，易於測試和修改
  }
  </code></pre>

### 9.2 使用抽象資料類型 (ADT)
- **Bad**:
  <pre><code>c
  int *stack;
  int top;

  void push(int value) {
      stack[++top] = value;  // 直接操作全局資料結構，沒有封裝
  }
  </code></pre>

- **Good**:
  <pre><code>c
  typedef struct Stack {
      int *data;
      int top;
      int capacity;
  } Stack;

  Stack* createStack(int capacity) {
      Stack *stack = (Stack *)malloc(sizeof(Stack));
      stack->capacity = capacity;
      stack->top = -1;
      stack->data = (int *)malloc(capacity * sizeof(int));
      return stack;
  }

  void push(Stack *stack, int value) {
      if (stack->top == stack->capacity - 1) {
          printf("Stack overflow\n");
          return;
      }
      stack->data[++stack->top] = value;
  }
  </code></pre>

### 9.3 保持 API 設計簡單且一致
- **Bad**:
  <pre><code>c
  // 複雜且不一致的 API 設計，使用起來困難
  void initialize();
  void runTask(int, int);
  void terminateTask();
  </code></pre>

- **Good**:
  <pre><code>c
  // API 設計簡單且一致
  void initSystem();
  void shutdownSystem();
  int performAction(int actionCode);
  </code></pre>
