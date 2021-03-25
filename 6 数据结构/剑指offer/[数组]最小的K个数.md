## 题目

给定一个数组，找出其中最小的K个数

如果K>数组的长度，那么返回一个空的数组

## 示例

- 输入

  ```
  [4,5,1,6,2,7,3,8],4
  ```
  
- 输出

  ```
  [1,2,3,4]
  ```

## 题解

- 暴力

  ```c++
  vector<int> GetLeastNumbers_Solution(vector<int> input, int k) {
          vector<int> ret;
          if(k==0 || k>input.size())
              return ret;
          sort(input.begin(), input.end());
          return vector<int>({input.begin(), input.begin()+k});
      }
  ```
  
- 堆排序

  > 建立一个容量为k的大根堆的优先队列
  >
  > 遍历一遍元素，如果队列大小<k,就直接入队，否则，让当前元素与队顶元素相比，如果队顶元素大，则出队，将当前元素入队

  ```c++
  vector<int> GetLeastNumbers_Solution(vector<int> input, int k) {
          vector<int> ret;
          if (k==0 || k > input.size()) return ret;
          priority_queue<int, vector<int>> pq;
          for (const int val : input) {
              if (pq.size() < k) {
                  pq.push(val);
              }
              else {
                  if (val < pq.top()) {
                      pq.pop();
                      pq.push(val);
                  }
   
              }
          }
   
          while (!pq.empty()) {
              ret.push_back(pq.top());
              pq.pop();
          }
          return ret;
      }
  ```

- 快排

  > 对数组[l, r]一次快排partition过程可得到，[l, p), p, [p+1, r)三个区间
  >
  > [l,p)为小于等于p的值，[p+1,r)为大于等于p的值

  ```c++
  int partition(vector<int> &input, int l, int r) {
          int pivot = input[r-1];
          int i = l;		//慢指针，记录已排好序的下标
          for (int j=l; j<r-1; ++j) {
              if (input[j] < pivot) {
                  swap(input[i++], input[j]);
              }
          }
          swap(input[i], input[r-1]);	//保证前i+1个为成功排序
          return i;
      }
  vector<int> GetLeastNumbers_Solution(vector<int> input, int k) {
          vector<int> ret;
          if (k==0 || k > input.size()) return ret;
           int l = 0, r = input.size();
          while (l < r) {
              int p = partition(input, l, r);
              if (p+1 == k) {
                  return vector<int>({input.begin(), input.begin()+k});
              }
              if (p+1 < k) {
                  l = p + 1;
              }  
              else {
                  r = p;
              }
   
          }
          return ret;
      }
  ```

  


