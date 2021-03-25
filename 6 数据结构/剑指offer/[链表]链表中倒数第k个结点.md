## 题目

输入一个链表，输出该链表中倒数第k个结点

## 示例

- 输入

  ```
  {1,2,3,4,5},1
  ```
  
- 输出

  ```
  {5}
  ```

## 题解

- 倒数的+顺数的长度等于链表总长度

  ```c++
  /**
   * struct ListNode {
   *	int val;
   *	struct ListNode *next;
   *	ListNode(int x) : val(x), next(nullptr) {}
   * };
   */
  
  ListNode* FindKthToTail(ListNode* pHead, int k) {
          ListNode* temp = pHead;
          ListNode* p = pHead;
          
          for(int i=0; i<k; ++i)
          {
              if(temp == NULL)
                  return NULL;
              temp = temp->next;
          }
          while(temp != NULL)
          {
              temp = temp->next;
              p = p->next;
          }
          
          return p;
      }
  ```
  
  
  

