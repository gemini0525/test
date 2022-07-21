# 算法与数据结构-综合提升

## Note

* 有索引 考虑索引越界

## 排序

### 测试代码程序

### 选择排序

* O(n^2)时间复杂度

* 思路

  * 1. 遍历元素
    2. 寻找[ i ,  n)中最小值
    3. 比较两者大小

    3. 交换

* 代码

  * ~~~c++
    #include <iostream>
    #include "Student.h"
    using namespace std;
    
    template<typename T>
    void selectionSort(T arr[], int n){
    
        for(int i = 0 ; i < n ; i ++){
      // 寻找[i, n)区间里的最小值
            int minIndex = i;
            for( int j = i + 1 ; j < n ; j ++ )
                if( arr[j] < arr[minIndex] )
                    minIndex = j;
    
            swap( arr[i] , arr[minIndex] );
        }
    }
    ~~~

  *

### 插入排序

* 算法描述
  * O(n^2) 时间复杂度
  * 最佳O(n) 时间复杂度：对近似有序的数

* 版本1思路
  * 思路
    * 遍历元素（注意起始位置）
    * 寻找元素arr[i]合适的插入位置（注意边界）
    * 交换位置
  * 代码：写法12

* 优化版本（优化交换方式）

  * 思路

    * 遍历数组
    * 临时保存当前元素
    * 寻找元素arr[i]合适的插入位置（注意边界）
    * 交换位置

  * 代码

    * ~~~C++
      #include <iostream>
      #include <algorithm>
      #include "SortTestHelper.h"
      #include "SelectionSort.h"
      
      using namespace std;
      
      template<typename T>
      void insertionSort(T arr[], int n){
      
          for( int i = 1 ; i < n ; i ++ ) {
      
              // 寻找元素arr[i]合适的插入位置
              // 写法1
      //        for( int j = i ; j > 0 ; j-- )
      //            if( arr[j] < arr[j-1] )
      //                swap( arr[j] , arr[j-1] );
      //            else
      //                break;
      
              // 写法2
      //        for( int j = i ; j > 0 && arr[j] < arr[j-1] ; j -- )
      //            swap( arr[j] , arr[j-1] );
      
              // 写法3
              T e = arr[i];
              int j; // j保存元素e应该插入的位置
              for (j = i; j > 0 && arr[j-1] > e; j--)
                  arr[j] = arr[j-1];
              arr[j] = e;
          }
      
          return;
      }
      ~~~

### 归并排序 Merge Sort

#### 归并排序法（自顶向下）

* 描述
  * 递归方式
  * 分而治之思想
  * 分O(log(n))  归并O(n)
  * 非原地 占据空间

* 图
  * <img src="%E7%AE%97%E6%B3%95bobo.assets/tmpE0E6.png" alt="tmpE0E6" style="zoom:50%;" />
* 思路
  * 递归分化，左边，右边
  * 归并时排序
    * 复制临时空间
    * 建立索引
    * 比较交换
    * 注意越界

* 代码

  * ~~~c++
    #include <iostream>
    #include "SortTestHelper.h"
    #include "InsertionSort.h"
    using namespace std;
    
    // 将arr[l...mid]和arr[mid+1...r]两部分进行归并
    template<typename  T>
    void __merge(T arr[], int l, int mid, int r){
    
        //* VS不支持动态长度数组, 即不能使用 T aux[r-l+1]的方式申请aux的空间
        //* 使用VS的同学, 请使用new的方式申请aux空间
        //* 使用new申请空间, 不要忘了在__merge函数的最后, delete掉申请的空间:)
        T aux[r-l+1];
        //T *aux = new T[r-l+1];
    
        for( int i = l ; i <= r; i ++ )
            aux[i-l] = arr[i];
    
        // 初始化，i指向左半部分的起始索引位置l；j指向右半部分起始索引位置mid+1
        int i = l, j = mid+1;
        for( int k = l ; k <= r; k ++ ){
    
            if( i > mid ){  // 如果左半部分元素已经全部处理完毕
                arr[k] = aux[j-l]; j ++;
            }
            else if( j > r ){  // 如果右半部分元素已经全部处理完毕
                arr[k] = aux[i-l]; i ++;
            }
            else if( aux[i-l] < aux[j-l] ) {  // 左半部分所指元素 < 右半部分所指元素
                arr[k] = aux[i-l]; i ++;
            }
            else{  // 左半部分所指元素 >= 右半部分所指元素
                arr[k] = aux[j-l]; j ++;
            }
        }
    
        //delete[] aux;
    }
    
    // 递归使用归并排序,对arr[l...r]的范围进行排序
    template<typename T>
    void __mergeSort(T arr[], int l, int r){
    
        if( l >= r )
            return;
    
        int mid = (l+r)/2;
        __mergeSort(arr, l, mid);
        __mergeSort(arr, mid+1, r);
        if(arr[mid] > arr[mid+1])
         __merge(arr, l, mid, r);
    }
    
    template<typename T>
    void mergeSort(T arr[], int n){
        __mergeSort( arr , 0 , n-1 );
    }
    ~~~

#### 归并排序法（自底向上）

* 描述
  * 迭代方式
  * 不涉及数组索引，可以运用在链表
* 思路
  * 划分大小size的循环
  * 确定每一轮归并中，起始元素位置（注意越界1 2）
  * 归并排序算法

* 代码

  * ~~~C++
    #include <iostream>
    #include "SortTestHelper.h"
    #include "MergeSort.h"
    using namespace std;
    
    // 使用自底向上的归并排序算法
    template <typename T>
    void mergeSortBU(T arr[], int n){
    
        // Merge Sort Bottom Up 无优化版本
        for( int sz = 1; sz < n ; sz += sz )
            for( int i = 0 ; i < n - sz ; i += sz+sz )
                // 对 arr[i...i+sz-1] 和 arr[i+sz...i+2*sz-1] 进行归并
                if( arr[i+sz-1] > arr[i+sz] )
                 __merge(arr, i, i+sz-1, min(i+sz+sz-1,n-1) );
    
    
        // Merge Sort Bottom Up 优化
        // 对于小数组, 使用插入排序优化
    //    for( int i = 0 ; i < n ; i += 16 )
    //        insertionSort(arr,i,min(i+15,n-1));
    
    //    for( int sz = 16; sz < n ; sz += sz )
    //        for( int i = 0 ; i < n - sz ; i += sz+sz )
    //            // 对于arr[mid] <= arr[mid+1]的情况,不进行merge
    //            if( arr[i+sz-1] > arr[i+sz] )
    //                __merge(arr, i, i+sz-1, min(i+sz+sz-1,n-1) );
    
        // Merge Sort BU 也是一个O(nlogn)复杂度的算法，虽然只使用两重for循环
        // 所以，Merge Sort BU也可以在1秒之内轻松处理100万数量级的数据
        // 注意：不要轻易根据循环层数来判断算法的复杂度，Merge Sort BU就是一个反例
        // 关于这部分陷阱，推荐看我的《玩转算法面试》课程，第二章：《面试中的复杂度分析》：）
    }
    ~~~

### 快速排序 Quick Sort

#### 随机化快速排序法

* 描述

  * 递归

* 图

  * <img src="%E7%AE%97%E6%B3%95bobo.assets/tmpF332.png" alt="tmpF332" style="zoom:50%;" />

* 思路

  * 将数组分左小右大
    * 随机寻找一个位置与第一位置交换 V
    * 遍历数，与第一个数比大小
    * 遍历结束，把第一个数放中间
  * 递归调用左右两边

* 代码

  * ~~~c++
    #include <iostream>
    #include <algorithm>
    #include <ctime>
    #include "SortTestHelper.h"
    #include "MergeSort.h"
    #include "InsertionSort.h"
    using namespace std;
    
    // 对arr[l...r]部分进行partition操作
    // 返回p, 使得arr[l...p-1] < arr[p] ; arr[p+1...r] > arr[p]
    template <typename T>
    int _partition(T arr[], int l, int r){
    
        // 随机在arr[l...r]的范围中, 选择一个数值作为标定点pivot
        swap( arr[l] , arr[rand()%(r-l+1)+l] );
    
        T v = arr[l];
        int j = l;
        for( int i = l + 1 ; i <= r ; i ++ )
            if( arr[i] < v ){
                j ++;
                swap( arr[j] , arr[i] );
            }
    
        swap( arr[l] , arr[j]);
    
        return j;
    }
    
    // 对arr[l...r]部分进行快速排序
    template <typename T>
    void _quickSort(T arr[], int l, int r){
    
        // 对于小规模数组, 使用插入排序进行优化
        if( l >= r)
            return;
    
        int p = _partition(arr, l, r);
        _quickSort(arr, l, p-1 );
        _quickSort(arr, p+1, r);
    }
    
    template <typename T>
    void quickSort(T arr[], int n){
    
        srand(time(NULL));
        _quickSort(arr, 0, n-1);
    }
    
    ~~~

#### 双路快速排序法

* 图

  * <img src="%E7%AE%97%E6%B3%95bobo.assets/tmpC172.png" alt="tmpC172" style="zoom:50%;" />

* 思路

  *

* 代码

  * ~~~c++
    #include <iostream>
    #include <algorithm>
    #include <ctime>
    #include "SortTestHelper.h"
    #include "MergeSort.h"
    #include "InsertionSort.h"
    using namespace std;
    
    // 对arr[l...r]部分进行partition操作
    // 返回p, 使得arr[l...p-1] < arr[p] ; arr[p+1...r] > arr[p]
    template <typename T>
    int _partition(T arr[], int l, int r){
    
        // 随机在arr[l...r]的范围中, 选择一个数值作为标定点pivot
        swap( arr[l] , arr[rand()%(r-l+1)+l] );
    
        T v = arr[l];
        int j = l;
        for( int i = l + 1 ; i <= r ; i ++ )
            if( arr[i] < v ){
                j ++;
                swap( arr[j] , arr[i] );
            }
        swap( arr[l] , arr[j]);
        return j;
    }
    
    // 双路快速排序的partition
    // 返回p, 使得arr[l...p-1] <= arr[p] ; arr[p+1...r] >= arr[p]
    // 双路快排处理的元素正好等于arr[p]的时候要注意，详见下面的注释：）
    template <typename T>
    int _partition2(T arr[], int l, int r){
    
        // 随机在arr[l...r]的范围中, 选择一个数值作为标定点pivot
        swap( arr[l] , arr[rand()%(r-l+1)+l] );
        T v = arr[l];
    
        // arr[l+1...i) <= v; arr(j...r] >= v
        int i = l+1, j = r;
        while( true ){
            // 注意这里的边界, arr[i] < v, 不能是arr[i] <= v
            // 思考一下为什么?
            while( i <= r && arr[i] < v )
                i ++;
    
            // 注意这里的边界, arr[j] > v, 不能是arr[j] >= v
            // 思考一下为什么?
            while( j >= l+1 && arr[j] > v )
                j --;
    
            // 对于上面的两个边界的设定, 有的同学在课程的问答区有很好的回答:)
            // 大家可以参考: http://coding.imooc.com/learn/questiondetail/4920.html
    
            if( i > j )
                break;
            swap( arr[i] , arr[j] );
            i ++;
            j --;
        }
        swap( arr[l] , arr[j]);
        return j;
    }
    
    // 对arr[l...r]部分进行快速排序
    template <typename T>
    void _quickSort(T arr[], int l, int r){
    
        // 对于小规模数组, 使用插入排序进行优化
        if( r - l <= 15 ){
            insertionSort(arr,l,r);
            return;
        }
    
        // 调用双路快速排序的partition
        int p = _partition2(arr, l, r);
        _quickSort(arr, l, p-1 );
        _quickSort(arr, p+1, r);
    }
    
    template <typename T>
    void quickSort(T arr[], int n){
    
        srand(time(NULL));
        _quickSort(arr, 0, n-1);
    }
    ~~~

#### 三路快速排序法

* 对处理存在大量重复值（或者数值范围小），对重复值不用递归排序，速度很快

* 图

  * <img src="%E7%AE%97%E6%B3%95bobo.assets/tmp7399.png" alt="tmp7399" style="zoom:50%;" />

* 思路

* 代码

  * ~~~c++
    #include <iostream>
    #include <algorithm>
    #include <ctime>
    #include "SortTestHelper.h"
    #include "MergeSort.h"
    #include "InsertionSort.h"
    #include "QuickSort.h"
    using namespace std;
    
    // 递归的三路快速排序算法
    template <typename T>
    void __quickSort3Ways(T arr[], int l, int r){
    
        // 对于小规模数组, 使用插入排序进行优化
        if( r - l <= 15 ){
            insertionSort(arr,l,r);
            return;
        }
    
        // 随机在arr[l...r]的范围中, 选择一个数值作为标定点pivot
        swap( arr[l], arr[rand()%(r-l+1)+l ] );
    
        T v = arr[l];
    
        int lt = l;     // arr[l+1...lt] < v
        int gt = r + 1; // arr[gt...r] > v
        int i = l+1;    // arr[lt+1...i) == v
        while( i < gt ){
            if( arr[i] < v ){
                swap( arr[i], arr[lt+1]);
                i ++;
                lt ++;
            }
            else if( arr[i] > v ){
                swap( arr[i], arr[gt-1]);
                gt --;
            }
            else{ // arr[i] == v
                i ++;
            }
        }
        swap( arr[l] , arr[lt] );
    
        __quickSort3Ways(arr, l, lt-1);
        __quickSort3Ways(arr, gt, r);
    }
    
    template <typename T>
    void quickSort3Ways(T arr[], int n){
    
        srand(time(NULL));
        __quickSort3Ways( arr, 0, n-1);
    }
    
    ~~~

* 归并 快速 总结

  * 分治算法
    * 顾名思义，分而治之，就是将原问题，分割成同等结构的子问题，之后将子问题逐一解决后，原问题也就得到了解决。
    * 归并：如何合？
    * 快排：如何分？

  * 解决问题：
    * 逆序对
    * 取数组中第n大的元素

### 堆 及 堆排序

#### 构造堆

* shiftUp
* shiftDown
  * 思路
    * 1. 判断头是否有孩子（左孩子）
      2. 判断是否有有孩子（判断，若有）
      3. 判断头与孩子大小
      4. 交换索引

* 代码

  * ~~~c++
    #include <algorithm>
    #include <cassert>
    using namespace std;
    
    template<typename Item>
    class MaxHeap{
    
    private:
        Item *data;
        int count;
        int capacity;
    
        void shiftUp(int k){
            while( k > 1 && data[k/2] < data[k] ){
                swap( data[k/2], data[k] );
                k /= 2;
            }
        }
    
        void shiftDown(int k){
            while( 2*k <= count ){
                int j = 2*k;
                if( j+1 <= count && data[j+1] > data[j] ) j ++;
                if( data[k] >= data[j] ) break;
                swap( data[k] , data[j] );
                k = j;
            }
        }
    
    public:
    
        // 构造函数, 构造一个空堆, 可容纳capacity个元素
        MaxHeap(int capacity){
            data = new Item[capacity+1];
            count = 0;
            this->capacity = capacity;
        }
    
        // 构造函数, 通过一个给定数组创建一个最大堆
        // 该构造堆的过程, 时间复杂度为O(n)
        MaxHeap(Item arr[], int n){
            data = new Item[n+1];
            capacity = n;
    
            for( int i = 0 ; i < n ; i ++ )
                data[i+1] = arr[i];
            count = n;
    
            for( int i = count/2 ; i >= 1 ; i -- )
                shiftDown(i);
        }
    
        ~MaxHeap(){
            delete[] data;
        }
    
        // 返回堆中的元素个数
        int size(){
            return count;
        }
    
        // 返回一个布尔值, 表示堆中是否为空
        bool isEmpty(){
            return count == 0;
        }
    
        // 像最大堆中插入一个新的元素 item
        void insert(Item item){
            assert( count + 1 <= capacity );
            data[count+1] = item;
            shiftUp(count+1);
            count ++;
        }
    
        // 从最大堆中取出堆顶元素, 即堆中所存储的最大数据
        Item extractMax(){
            assert( count > 0 );
            Item ret = data[1];
            swap( data[1] , data[count] );
            count --;
            shiftDown(1);
            return ret;
        }
    
        // 获取最大堆中的堆顶元素
        Item getMax(){
            assert( count > 0 );
            return data[1];
        }
    };
    
    
    ~~~

#### 堆化方法 **Heapify**

* 思路
  * 初始化方法把数组放入堆
  * 对不是叶子节点的节点 做shiftDown
  * shiftDown
    * 1. 判断头是否有孩子（左孩子）
      2. 判断是否有有孩子（判断，若有）
      3. 判断头与孩子大小
      4. 交换索引
  * 从头依次取出

~~~C++
#include <algorithm>
#include <cassert>
using namespace std;

template<typename Item>
class MaxHeap{
private:
    Item *data;
    int count;
    int capacity;
    void shiftDown(int k){
        while( 2*k <= count ){
            int j = 2*k;
            if( j+1 <= count && data[j+1] > data[j] ) j ++;
            if( data[k] >= data[j] ) break;
            swap( data[k] , data[j] );
            k = j;
        }
    }
public:
    // 构造函数, 通过一个给定数组创建一个最大堆
    // 该构造堆的过程, 时间复杂度为O(n)
    MaxHeap(Item arr[], int n){
        data = new Item[n+1];
        capacity = n;

        for( int i = 0 ; i < n ; i ++ )
            data[i+1] = arr[i];
        count = n;

        for( int i = count/2 ; i >= 1 ; i -- )
            shiftDown(i);
    }

    ~MaxHeap(){
        delete[] data;
    }

    // 从最大堆中取出堆顶元素, 即堆中所存储的最大数据
    Item extractMax(){
        assert( count > 0 );
        Item ret = data[1];
        swap( data[1] , data[count] );
        count --;
        shiftDown(1);
        return ret;
    }

};
template<typename T>
void heapSort2(T arr[], int n){

    MaxHeap<T> maxheap = MaxHeap<T>(arr,n);
    for( int i = n-1 ; i >= 0 ; i-- )
        arr[i] = maxheap.extractMax();

}
~~~

#### 堆排序 Heap Sort

* 代码

  * ~~~c++
    #include <iostream>
    #include <algorithm>
    #include "MergeSort.h"
    #include "QuickSort.h"
    #include "QuickSort2Ways.h"
    #include "QuickSort3Ways.h"
    #include "HeapSort.h"
    #include "SortTestHelper.h"
    
    using namespace std;
    
    
    // 原始的shiftDown过程
    template<typename T>
    void __shiftDown(T arr[], int n, int k){
    
        while( 2*k+1 < n ){
            int j = 2*k+1;
            if( j+1 < n && arr[j+1] > arr[j] )
                j += 1;
    
            if( arr[k] >= arr[j] )break;
    
            swap( arr[k] , arr[j] );
            k = j;
        }
    }
    
    // 优化的shiftDown过程, 使用赋值的方式取代不断的swap,
    // 该优化思想和我们之前对插入排序进行优化的思路是一致的
    template<typename T>
    void __shiftDown2(T arr[], int n, int k){
    
        T e = arr[k];
        while( 2*k+1 < n ){
            int j = 2*k+1;
            if( j+1 < n && arr[j+1] > arr[j] )
                j += 1;
    
            if( e >= arr[j] ) break;
    
            arr[k] = arr[j];
            k = j;
        }
    
        arr[k] = e;
    }
    
    // 不使用一个额外的最大堆, 直接在原数组上进行原地的堆排序
    template<typename T>
    void heapSort3(T arr[], int n){
    
        // 注意，此时我们的堆是从0开始索引的
        // 从(最后一个元素的索引-1)/2开始
        // 最后一个元素的索引 = n-1
        for( int i = (n-1-1)/2 ; i >= 0 ; i -- )
            __shiftDown2(arr, n, i);
    
        for( int i = n-1; i > 0 ; i-- ){
            swap( arr[0] , arr[i] );
            __shiftDown2(arr, i, 0);
        }
    }
    ~~~

### 排序总结

* 稳定排序：
  * 对于相等的元素，在排序后，原来靠前的元素依然靠前。相等元素的相对位置没有发生改变。

<img src="%E7%AE%97%E6%B3%95bobo.assets/tmp2E2D.png" alt="tmp2E2D" style="zoom:50%;" />
