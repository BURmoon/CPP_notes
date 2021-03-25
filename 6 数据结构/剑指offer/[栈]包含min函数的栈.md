## 题目

定义栈的数据结构，请在该类型中实现一个能够得到栈中所含最小元素的min函数

## 示例

## 题解

```c++
class Solution {
public:
    void push(int value) {
        normal.push(value);
        if(minval.empty())
        {
            minval.push(value);
        }
        else
        {
            if(value <= minval.top())
            {
                minval.push(value);
            }
            else
            {
                minval.push(minval.top());
            }
        }
    }
    void pop() {
        normal.pop();
        minval.pop();
    }
    int top() {
        return normal.top();
    }
    int min() {
        return minval.top();
    }
    stack<int> normal;
    stack<int> minval;
};
```



