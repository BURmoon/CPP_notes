## 题目

输出所有和为S的连续正数序列

## 示例

- 输入

  ```
  9
  ```

- 输出

  ```
  [[2,3,4],[4,5]]
  ```

## 题解

- 暴力

  ```c++
  vector<vector<int> > FindContinuousSequence(int sum) {
          vector<vector<int>> ret;
           // 左边界
           for (int i=1; i<=sum/2; ++i) 
           {
           // 右边界
               for (int j=i+1; j<sum; ++j) 
               {
                   int tmp = 0;
                   // 求区间和
                   for (int k = i; k<=j; ++k) 
                       tmp += k;
                   if (sum == tmp) 
                   {
                       vector<int> ans;
                       for (int k=i; k<=j; ++k) ans.push_back(k);
                       ret.push_back(ans);
                   }
                   else if (tmp > sum) 
                       break;
           }
       }
       return ret;
      }
  ```
  

- 双指针

  ```c++
  vector<vector<int> > FindContinuousSequence(int sum) {
          vector<vector<int>> ret;
          int l = 1, r = 1;
          int tmp = 0;
          while (l <= sum / 2) {
              if (tmp < sum) {
                  tmp += r;
                  ++r;
              }
              else if (tmp > sum) {
                  tmp -= l;
                  ++l;
              }
              else {
                  vector<int> ans;
                  for (int k=l; k<r; ++k)
                      ans.push_back(k);
                  ret.push_back(ans);
                  tmp -= l;
                  ++l;
              }
          }
          return ret;
      }
  ```

  




