# C Language Clean Code Guidelines with Examples

This guide illustrates **Clean Code** principles in C language development, along with **Bad** and **Good** examples, helping developers write more readable, maintainable, and efficient code.

---

## 1. Naming and Code Style

### 1.1 Maintain Consistent Code Style
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

### 1.2 Avoid Large Header Files
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
  void processData(int data);  // Only include necessary declarations
  </code></pre>

### 1.3 Use `const` to Protect Variables
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

## 2. Function Design and Modularization

### 2.1 Single Responsibility Principle
- **Bad**:
  <pre><code>c
  void handleUserLogin() {
      // Validate user
      // Read user data
      // Log login time
      // Return login result
  }
  </code></pre>

- **Good**:
  <pre><code>c
  void validateUser() {
      // User validation logic
  }

  void readUserData() {
      // User data reading logic
  }

  void logUserLogin() {
      // Log login time
  }

  void handleUserLogin() {
      validateUser();
      readUserData();
      logUserLogin();
  }
  </code></pre>

### 2.2 Simplify Function Signatures
- **Bad**:
  <pre><code>c
  void createUser(char *name, int age, char *address, char *phone, int id) {
      // User creation logic
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
      // User creation logic
  }
  </code></pre>

### 2.3 Avoid Overly Large Functions
- **Bad**:
  <pre><code>c
  void processAll() {
      // Contains many different logics, function is too big and hard to understand
  }
  </code></pre>

- **Good**:
  <pre><code>c
  void readData() {
      // Read data
  }

  void analyzeData() {
      // Analyze data
  }

  void writeResults() {
      // Write results
  }

  void processAll() {
      readData();
      analyzeData();
      writeResults();
  }
  </code></pre>

### 2.4 Use Inline Functions Appropriately
- **Bad**:
  <pre><code>c
  #define SQUARE(x) ((x) * (x))  // Using macro, lacks type safety
  </code></pre>

- **Good**:
  <pre><code>c
  static inline int square(int x) {
      return x * x;
  }
  </code></pre>

### 2.5 Layered Architecture
- **Bad**:
  <pre><code>c
  void processRequest() {
      // Directly processing data and showing result, lacks layered design
  }
  </code></pre>

- **Good**:
  <pre><code>c
  // Data Access Layer
  int fetchDataFromDatabase() {
      // Database read logic
  }

  // Logic Layer
  void processData(int data) {
      // Data processing logic
  }

  // Presentation Layer
  void displayData() {
      int data = fetchDataFromDatabase();
      processData(data);
      printf("Data: %d\n", data);
  }
  </code></pre>

---

## 3. Error Handling and Boundary Checking

### 3.1 Enforce Boundary Checks
- **Bad**:
  <pre><code>c
  int getElement(int *arr, int index) {
      return arr[index];  // No boundary checks
  }
  </code></pre>

- **Good**:
  <pre><code>c
  int getElement(int *arr, int size, int index) {
      if (index < 0 || index >= size) {
          printf("Index out of bounds\n");
          return -1;  // Return error code
      }
      return arr[index];
  }
  </code></pre>

### 3.2 Provide Meaningful Error Messages
- **Bad**:
  <pre><code>c
  FILE *file = fopen("data.txt", "r");
  if (file == NULL) {
      printf("Error\n");  // Vague error message
  }
  </code></pre>

- **Good**:
  <pre><code>c
  FILE *file = fopen("data.txt", "r");
  if (file == NULL) {
      printf("Error: Unable to open file 'data.txt'.\n");  // Clear error message
  }
  </code></pre>

### 3.3 Dedicated Error Handling Functions
- **Bad**:
  <pre><code>c
  FILE *file = fopen("data.txt", "r");
  if (file == NULL) {
      printf("Error opening file\n");
      return;
  }
  // File processing logic
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
      // Process file
      fclose(file);
  }
  </code></pre>

### 3.4 Avoid Implicit Type Conversion
- **Bad**:
  <pre><code>c
  int result = 3.5;  // Implicit conversion, may cause data loss
  </code></pre>

- **Good**:
  <pre><code>c
  int result = (int)3.5;  // Explicit conversion, makes the conversion clear
  </code></pre>

---

## 4. Resource Management and Memory Handling

### 4.1 Manual Memory Management and Symmetric Release
- **Bad**:
  <pre><code>c
  char *data = (char *)malloc(100);
  // Use data
  // Forget to free memory
  </code></pre>

- **Good**:
  <pre><code>c
  char *data = (char *)malloc(100);
  if (data == NULL) {
      // Handle memory allocation failure
  }
  // Use data
  free(data);  // Ensure memory is freed
  </code></pre>

### 4.2 Ensure Proper Resource Release Order
- **Bad**:
  <pre><code>c
  void processResources() {
      FILE *file = fopen("data.txt", "r");
      char *buffer = (char *)malloc(100);

      if (file == NULL || buffer == NULL) {
          // Forget to release resources
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
          if (file != NULL) fclose(file);  // Free file resource
          if (buffer != NULL) free(buffer);  // Free memory resource
          return;
      }

      // Use resources
      free(buffer);  // Free buffer first
      fclose(file);  // Free file later
  }
  </code></pre>

### 4.3 Avoid Using Raw Pointers
- **Bad**:
  <pre><code>c
  int *ptr = malloc(sizeof(int) * 10);
  ptr[10] = 42;  // Error: Out-of-bounds access
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

### 4.4 Use Memory Pools Effectively
- **Bad**:
  <pre><code>c
  for (int i = 0; i < 1000; i++) {
      int *ptr = (int *)malloc(sizeof(int));  // Memory allocation is inefficient
      free(ptr);
  }
  </code></pre>

- **Good**:
  <pre><code>c
  int *ptr = (int *)malloc(1000 * sizeof(int));  // Allocate larger memory block at once
  // Use ptr
  free(ptr);
  </code></pre>

### 4.5 Mimic RAII (Resource Acquisition Is Initialization)
- **Bad**:
  <pre><code>c
  FILE *file = fopen("data.txt", "r");
  // Use file
  // Forget to free file resource
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
      // Process file
      closeFile(file);  // Ensure resources are freed
  }
  </code></pre>

---

## 5. Performance Optimization

### 5.1 Avoid Premature Optimization
- **Bad**:
  <pre><code>c
  int sum(int a, int b) {
      return (a << 1) - (-b);  // Overly complex and hard to understand
  }
  </code></pre>

- **Good**:
  <pre><code>c
  int sum(int a, int b) {
      return a + b;  // Simple and clear
  }
  </code></pre>

### 5.2 Balance Time and Space Trade-offs
- **Bad**:
  <pre><code>c
  for (int i = 0; i < 1000000; i++) {
      // Using simple lookup, high time complexity
  }
  </code></pre>

- **Good**:
  <pre><code>c
  // Use additional memory to optimize query speed
  typedef struct {
      int *lookupTable;
      int size;
  } LookupTable;

  int query(LookupTable *table, int index) {
      if (index < 0 || index >= table->size) {
          return -1;  // Invalid query
      }
      return table->lookupTable[index];
  }
  </code></pre>

### 5.3 Performance Profiling and Testing
- **Bad**:
  <pre><code>c
  // Code without any performance profiling
  </code></pre>

- **Good**:
  <pre><code>c
  gcc -pg my_program.c -o my_program  // Compile with gprof enabled
  ./my_program  // Run the program
  gprof my_program gmon.out > analysis.txt  // Generate performance report
  </code></pre>

---

## 6. Multithreading and Synchronization

### 6.1 Synchronize Shared Data
- **Bad**:
  <pre><code>c
  int counter = 0;

  void incrementCounter() {
      counter++;  // No synchronization, potential race condition
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

### 6.2 Use Atomic Operations
- **Bad**:
  <pre><code>c
  int counter = 0;

  void incrementCounter() {
      counter++;  // Non-atomic operation, potential race condition
  }
  </code></pre>

- **Good**:
  <pre><code>c
  _Atomic int counter = 0;

  void incrementCounter() {
      counter++;  // Use atomic operation for thread safety
  }
  </code></pre>

---

## 7. Testing and Development Process

### 7.1 Test-Driven Development (TDD)
- **Bad**:
  <pre><code>c
  // No testing, directly developing features
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

### 7.2 Unit Testing and Boundary Testing
- **Bad**:
  <pre><code>c
  // No boundary condition tests
  </code></pre>

- **Good**:
  <pre><code>c
  void testProcessArray() {
      int arr[5] = {1, 2, 3, 4, 5};
      processArray(0, arr);  // Test zero size
      processArray(5, arr);  // Normal size
      processArray(SIZE_MAX, arr);  // Test extreme case
  }
  </code></pre>

### 7.3 Use Compiler Warnings
- **Bad**:
  <pre><code>c
  // No compiler warnings enabled
  gcc my_program.c -o my_program
  </code></pre>

- **Good**:
  <pre><code>c
  gcc -Wall -Wextra my_program.c -o my_program  // Enable all warnings
  </code></pre>

---

## 8. Platform-Specific and Cross-Platform Design

### 8.1 Encapsulate Platform-Specific Logic
- **Bad**:
  <pre><code>c
  void processFile() {
      #ifdef _WIN32
      // Windows platform-specific code
      #endif
  }
  </code></pre>

- **Good**:
  <pre><code>c
  // Encapsulate platform-specific logic in modules
  #ifdef _WIN32
  void platformSpecificFunction() {
      // Windows implementation
  }
  #else
  void platformSpecificFunction() {
      // Other platform implementation
  }
  #endif
  </code></pre>

### 8.2 Use Conditional Compilation for Platform Differences
- **Bad**:
  <pre><code>c
  #ifdef _WIN32
  // Windows code mixed directly into main logic
  #endif
  </code></pre>

- **Good**:
  <pre><code>c
  #ifdef _WIN32
  void processWindows() {
      // Windows platform-specific logic
  }
  #else
  void processUnix() {
      // Unix platform-specific logic
  }
  #endif
  </code></pre>

---

## 9. Modular Design and API Design

### 9.1 Reduce Module Coupling
- **Bad**:
  <pre><code>c
  void performTask() {
      readFile();
      processFile();
      writeFile();  // Modules are tightly coupled, hard to test and maintain
  }
  </code></pre>

- **Good**:
  <pre><code>c
  void performTask(FileReader reader, FileProcessor processor, FileWriter writer) {
      reader();
      processor();
      writer();  // Use callback functions to reduce coupling, easier to test and modify
  }
  </code></pre>

### 9.2 Use Abstract Data Types (ADT)
- **Bad**:
  <pre><code>c
  int *stack;
  int top;

  void push(int value) {
      stack[++top] = value;  // Directly manipulating global data structure, lacks encapsulation
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

### 9.3 Keep API Design Simple and Consistent
- **Bad**:
  <pre><code>c
  // Complex and inconsistent API, hard to use
  void initialize();
  void runTask(int, int);
  void terminateTask();
  </code></pre>

- **Good**:
  <pre><code>c
  // API design is simple and consistent
  void initSystem();
  void shutdownSystem();
  int performAction(int actionCode);
  </code></pre>
