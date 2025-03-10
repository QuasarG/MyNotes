---
笔记: 数据结构C语言
---
## 指路：[[数据结构Py版]]
## 一、线性表

### **（一）链表**
#### 1. 基本定义
```C
#include <stdio.h>
#include <stdlib.h>

typedef struct Node {
	int data;
	struct Node* next;
	} Node; // typedef [原始类型] [新别名]
```

#### 2. 基本操作函数

* **创建节点**，意思是声明一个节点，开辟出一个空间存储数据和指针，但是**此时还没有构建连接关系**；
* **插入节点**，意思是将创建好的节点插入到链表中

##### * **创建新节点：**
```C
// 动态分配一个新节点并初始化数据
Node* createNode(int data) {
    Node* newNode = (Node*)malloc(sizeof(Node));
    if (!newNode) return NULL;
    
    newNode->data = data;
    newNode->next = NULL; // 新节点初始指向空
    return newNode;
}
```

##### **插入节点**
* *头插法*
```C
// 在头部插入新节点，更新头指针
Node* insertAtHead(Node** head, int data) {
    Node* newNode = createNode(data);
    if (!newNode) return NULL;

    // 新节点的next指向原头节点
    newNode->next = *head; 
    // 更新头指针为新节点（注意传入的是指针的地址）
    *head = newNode;
    
    return *head;
}
```
- `Node** head`：因为要修改链表头部，需传递二级指针。
- `newNode->next = *head` 将原链表接到新节点之后。
- 更新后的新头指针通过 `*head = newNode` 完成。

* *尾插法*
```C
// 在尾部插入新节点，遍历到末尾后再添加
Node* insertAtTail(Node** head, int data) {
    Node* newNode = createNode(data);
    if (!newNode) return NULL;

    // 如果链表为空，则直接设为头节点
    if (*head == NULL) {
        *head = newNode;
        return *head;
    }

    Node* current = *head;
    while (current->next != NULL) { 
        current = current->next;  // 遍历到尾部
    }
    current->next = newNode;      // 将新节点连接到末尾
    return *head;
}
```
- 需要遍历链表直到 `current->next` 为 NULL。
- 特殊情况处理：当链表为空时直接赋值头指针。
##### **删除节点**
* *删除头部节点*
```C
// 删除并释放头部节点，返回新头节点
Node* deleteHead(Node** head) {
    if (*head == NULL) return NULL;

    Node* temp = *head;
    *head = (*head)->next; // 更新头指针指向下一个节点

    free(temp);            // 释放旧头节点内存
    return *head;
}
```
- `temp` 存储原头节点，防止丢失链表后续部分。
- `free()` 是释放内存的关键操作，否则会导致内存泄漏。

* *删除指定值的节点*
```C
// 删除第一个匹配 data 值的节点，返回新头节点
Node* deleteByData(Node** head, int data) {
    if (*head == NULL) return NULL;

    Node* current = *head;
    Node* prev = NULL;

    // 查找目标节点及其前驱
    while (current != NULL && current->data != data) {
        prev = current;
        current = current->next;
    }

    if (current == NULL) return *head;  // 没找到

    // 处理头节点情况（prev为NULL）
    if (prev == NULL) { 
        *head = current->next;
    } else {
        prev->next = current->next;
    }
    
    free(current);
    return *head;
}
```
- 需要跟踪前驱节点 `prev`，以便断开目标节点的链接。
- 若删除头节点，则直接更新头指针为 `current->next`。

##### **查找节点**
* *按值查找*
```C
// 返回第一个匹配 data 值的节点指针，未找到返回NULL
Node* search(Node* head, int data) {
    Node* current = head;
    while (current != NULL) {
        if (current->data == data)
            return current;  // 找到直接返回
        current = current->next;
    }
    return NULL;             // 遍历结束未找到
}
```
- 线性时间复杂度 **O(n)**，适合无序链表的查找

* *按索引查找*
```C
// 根据索引获取节点指针，索引从0开始，失败返回NULL
Node* getNodeAt(Node* head, int index) {
    if (index < 0) return NULL;
    
    Node* current = head;
    while (current != NULL && index > 0) { 
        current = current->next;  
        index--;
    }
    return current == NULL ? NULL : current;
}
```
- 遍历次数由索引决定，每次循环 `index--`。

##### **打印链表**
```C
// 打印整个链表内容
void printList(Node* head) {
    Node* current = head;
    while (current != NULL) {
        printf("%d -> ", current->data);
        current = current->next;
    }
    printf("NULL\n");
}
```

### **链表有此一题大概足矣**

**【问题描述】**
	构建一个线性链表，读取并存储每行的输入数据，每行数据由定位数字k和不定数量的整数构成，定位数字k表明需要输出当前行及当前行之前的所有行中的第k大（不包括定位数字）的整数。
	整数**若重复视作相同排名**，定位数字不会超过整数数量之和。  
**【输入形式】**
	若干行整数，长度不固定，由空格隔开  
**【输出形式】**
	每行输入对应的一个整数输出  
**【样例输入】**
```plaintext
2 7 9 10 4 8
4 10 -1 0 4 9
2 7 6 5 3 1 9 12 
```
**【样例输出】**
```plaintext
9
7
10
```
**【样例说明】**
第一行需要存储7 9 10 4 8,并输出第2大的数字；
第二行需要在第一行的基础上存储10 -1 0 4 9，并输出第4大的数字；

**参考代码**
```C
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <ctype.h>

// 定义链表节点结构体
typedef struct Node {
    int data;           // 节点存储的数据
    struct Node* next;  // 指向下一个节点的指针
} Node;
  
// 创建一个新节点
Node* createNode(int data) {
    Node* newNode = (Node*)malloc(sizeof(Node));  // 动态分配内存
    if (newNode != NULL) {
        newNode->data = data;  // 设置节点数据
        newNode->next = NULL;  // 初始化下一个节点指针为NULL
    }
    return newNode;
}

// 在链表末尾添加一个新节点
void appendNode(Node** head, int data) {
    if (*head == NULL) {  // 如果链表为空，直接创建新节点作为头节点
        *head = createNode(data);
        return;
    }
    Node* curr = *head;
    while (curr->next != NULL) {  // 遍历到链表末尾
        curr = curr->next;
    }
    curr->next = createNode(data);  // 在末尾添加新节点
}

// 释放链表占用的内存
void freeList(Node* head) {
    Node* temp;
    while (head != NULL) {
        temp = head;        // 保存当前节点
        head = head->next;  // 移动到下一个节点
        free(temp);         // 释放当前节点内存
    }
}

// 用于qsort的比较函数，按降序排序
int compare(const void* a, const void* b) {
    return (*(int*)b - *(int*)a);
}

int main() {
    char line[1024];        // 用于存储输入的行
    int* results = NULL;    // 用于存储每行的结果
    int resultCount = 0;    // 结果数组的当前大小
    Node* List = NULL;      // 链表的头节点

    // 从标准输入逐行读取数据
    while (fgets(line, sizeof(line), stdin) != NULL) {
        line[strcspn(line, "\n")] = '\0';  // 去掉行末的换行符
        if (strlen(line) == 0) break;      // 如果输入为空行，结束循环

        // 使用strtok分割输入行，获取第一个token（即k值）
        char *token = strtok(line, " \t\n");
        if (token == NULL) continue;       // 如果没有token，跳过该行
        
        int k = atoi(token);  // 将k值转换为整数
        // 继续分割输入行，将剩余的数字添加到链表中
        while ((token = strtok(NULL, " \t\n")) != NULL) {
            appendNode(&List, atoi(token));
        }
        // 计算链表的长度
        int count = 0;

		Node* curr = List;
        while (curr != NULL) {
            count++;
            curr = curr->next;
        }

        // 将链表中的数据复制到数组中
        int* numbers = (int*)malloc(count * sizeof(int));
        curr = List;
        for (int i = 0; i < count; i++) {
            numbers[i] = curr->data;
            curr = curr->next;
        }
  
        // 对数组进行降序排序
        qsort(numbers, count, sizeof(int), compare);

        // 找到第k大的元素
        int kth = numbers[0];  // 默认取第一个元素（即最大值）
        if (k == 1) {
            kth = numbers[0];  // 如果k为1，直接取最大值
        } else {
            int currentK = 1;  // 当前已找到的不同的元素个数
            for (int i = 1; i < count; i++) {
                if (numbers[i] != numbers[i-1]) {  // 如果当前元素与前一个不同
                    currentK++;                    // 增加currentK
                    if (currentK == k) {          // 如果currentK等于k
                        kth = numbers[i];         // 找到第k大的元素
                        break;
                    }
                }
            }
        }
  
        // 将结果保存到results数组中
        results = (int*)realloc(results, (resultCount + 1) * sizeof(int));
        results[resultCount++] = kth;
  
        // 释放数组内存
        free(numbers);
    }

    // 输出所有结果
    for (int i = 0; i < resultCount; i++) {
        printf("%d\n", results[i]);
    }

    // 释放链表和结果数组的内存
    freeList(List);
    free(results);
    return 0;
}
```

