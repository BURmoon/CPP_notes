## 题目

输入一个递增排序的数组和一个数字S，在数组中查找两个数，使得他们的和正好是S

如果有多对数字的和等于S，输出两个数的乘积最小的

## 示例

- 输入

  ```
  [1,2,4,7,11,15],15
  ```

- 输出

  ```
  [4,11]
  ```

## 题解

- 双指针

  ```c++
   vector<int> FindNumbersWithSum(vector<int> array,int sum) {
          if (array.empty()) return vector<int>();
          int tmp = INT_MAX;
          pair<int, int> ret;
          int i = 0, j = array.size();
          while (i < j) {
              if (array[i] + array[j-1] == sum) {
                  if (array[i]*array[j-1] < tmp) {
                      tmp = array[i] * array[j-1];
                      ret = {i, j-1};
                  }
                  ++i, --j;
              }
              else if (array[i] + array[j-1] < sum) {
                  ++i;
              }
              else {
                  --j;
              }
          }
          if (ret.first == ret.second) return vector<int>();
          return vector<int>({array[ret.first], array[ret.second]});
      }
  ```
  





