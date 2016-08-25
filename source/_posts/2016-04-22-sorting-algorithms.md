---
layout: post
title: "排序算法"
date: 2016-04-22
tags:
  - 算法
---

排序算法是计算机领域最经典也是研究最广泛的算法，并且是许多算法的基础。

<!-- more -->

## 排序算法的应用领域

* **搜索**： 排过序的数据可以使用二分查找（折半查找）快速地搜索某个元素。
* **最邻近对**： 给定n个数的集合，如何找到相差最小的一对数？如果集合已经排过序，那么一次线性查找就可以完成任务。
* **元素唯一性** -- 给定n个数的集合，里面有重复的元素吗？这是最邻近对问题的一个特例。
* **频率分布**： 给定n个数的集合，哪个数出现的次数最多？（求众数）。如果集合是有序的，相同的元素势必会连在一起，一次线性循环即可搞定。如果要查找任意一个元素k出现了多少次，首先用二分查找找到k，然后再往左移动，直到出现不是k的元素位置，同理也往右移动，这样便可得到k出现的次数。
* **选择**： 一个数列里第k大的元素是哪个？如果元素已经是有序的，那么第k个元素就是我们要找的。
* **凸包**： 给定二维平面上的n个点，求最小的能包含所有点的多边形？如果点根据x坐标进行排序，就可以按顺序把点插入（详细算法以后会专门介绍）。
* **找到两个集合的交集**： 如果对两个集合分别排序，两个集合是否相交以及交集是多少就很容易求了。

## 排序的语义

排序有几个重要的语义，分述如下：

* 递增还是递减顺序？
* 使用某个键值排序还是用整个记录去排序？

上述两个歧义可以通过让客户端自己指定比较函数来解决。

* 遇到相同的元素怎么办？有时候即使多个记录的某个键值相同，但是这些记录间的相互顺序有可能是重要的，因为之前它们可能根据其它键值排过序。如果一个排序算法能够保持相同元素的相对顺序不变，那么称这个算法是稳定的。可惜的是比较快的算法几乎都不是稳定的。另外需要注意如果相同的元素特别多，一些系统默认的比较快的排序算法有可能会很慢！
* 遇到非数值型的数据怎么办？比如在排序一个整形数列时遇到string类型，排序一个string类型时遇到Beijing 和PeKing 或者是 北京师范大学和北师大？它们的相互顺序应该怎么确定？

## 几种排序方法的比较


排序方法 | 原地排序 | 稳定排序 | 最差 | 平均 | 最好 | 备注
--- | --- | --- | --- | --- | --- | ---
选择排序 | 是 | 否 | N^2/2 | N^2/2 | N^2/2 | 只需要N次交换
插入排序 | 是 | 是 | N^2/2 | N^2/4 | N | 通常在N很小或是数据以及部分排序的时候使用
shell排序 | 是 | 否 | 未知 | 未知 | N | 实现代码很少，通常用于嵌入式编程，时间复杂度未知但低于二次 |
快速排序 | 是 | 否 | N^2/2 | 2N\lg(N) | lg(N) | 概率上保证 2N\lg(N) 的时间复杂度，实际使用中最快的算法
3路快速排序 | 是 | 否 | N^2/2 | 2N\lg(N) | N | 提升快速排序在大量重复记录情况下的效率
归并排序 | 否 | 是 | N\lg(N) | N\lg(N) | N\lg(N) | 严格保证了lg(N)的时间复杂度，并且是稳定的
堆排序 | 是 | 否 | 2N\lg(N) | 2N\lg(N) | N\lg(N) | 严格保证了lg(N) 的时间复杂度，并且原地排序的（节省空间）
？？？ | 是 | 是 | N\lg(N) | N\lg(N) | N\lg(N) | 终极排序算法:)


## 选择排序

基本思想

1. 输入：包含N条记录的数列A
2. 从0 循环至 N-1
3. 在第i次循环中，找到剩下记录中最小纪录的索引值 min
4. 交换 A[i] 和 A[min]

实现代码

```c++
template<typename T>
void selection_sort(std::vector<T>& a, int lo, int hi) {
    for (int i = lo; i <= hi; ++i) {
        int min_index = i;

        for (int j = i + 1; j <= hi; ++j) {
            if (a[min_index] > a[j]) {
                min_index = j;
            }
        }

        // swap a[i] and a[min_index]
        exch(a[i], a[min_index]);
    }
}
```

选择排序的基本特点

* 运行时间和输入没有关系，即使输入已经是有序的，也需要二次的时间复杂度
* 选择排序的记录移动是所有算法中最小的，只有线性时间的交换

## 插入排序

基本思想

1. 输入：包含N条记录的数列A
2. 从0 循环至 N-1
3. 在第i次循环中，把A[i]和位于它左边并且比它大的记录交换

实现代码

```
    template<typename T>
    void insertion_sort(std::vector<T>& a, int lo, int hi) {
        for (int i = lo; i <= hi; ++i) {
            for (int j = i; j > lo; --j) {
                if (a[j] < a[j - 1]) {
                    exch(a[j], a[j - 1]);
                } else {
                    break;
                }
            }
        }
    }
```

基本特点

* 如果输入记录是部分排序的话，插入排序的运行时间是线性的


## shell 排序

shell 排序是插入排序的增强版：插入排序在往左比较大小时每次只后退一步，而shell排序每次会后退多步。假设后退h步，那么得到的序列就是一个以h为间隔排序好的序列。shell排序会进行多次h排序。h的取值则从大慢慢减为1，这样做的理由是：

* 当h很大时，以h为间隔的序列较小，排序可以很快完成
* 当h变为1时，由于之前已经进行了多次h间隔排序，序列已经部分排序，因此插入排序此时的运行时间是线性的，也可以很快完成。

在实际中，通常h的取值序列有多种，不同的序列会导致不一样的时间复杂度。比较容易计算的是使用 3x + 1 这个公式来产生序列

实现代码

```
    template<typename T>
    void shell_sort(std::vector<T>& a, int lo, int hi) {
        int N = hi - lo + 1;

        // decide decrease sequence
        int h = 1;
        while (h < N / 3) {
            h = 3 * h + 1;
        }

        while (h >= 1) {
            // h-sort the array
            for (int i = h + lo; i <= hi; ++i) {
                for (int j = i; j >= h + lo && a[j] < a[j - h]; j -= h) {
                    exch(a[j], a[j - h]);
                }
            }
            h = h / 3;
        }
    }
```

## 堆排序

堆排序体现了好的数据结构对算法的帮助。堆排序和选择排序的原理一致，都是从剩下的记录中不断选择最小的记录出来。但是选择排序需要线性的时间去查找最小记录。而从一个集合中选择最小的记录出来是一个经典的优先队列解决的问题，如果使用堆或者平衡二叉树来实现优先队列的话，就能让这个操作变成log(N)时间。从而，借助更好的优先队列实现，堆排序把选择排序从O(n^2)复杂度提升到了O(n\log(n))

## 堆

堆是实现优先队列插入和获取最小值操作的简单而高效的数据结构。他通过维持记录部分排序而非完全排序来工作，因此会比较高效。一个堆实际上可以用一个二叉树来表示（注意不是二叉搜索树！）。在一个最小堆中，一个节点的键值总是比它的子节点要小；在一个最大堆中，一个节点的键值总是比它的子节点要大。

堆使用数组来实现，不需要使用任何的指针！键值在堆中的位置充当了指针的作用。在这个数组中，我们把二叉树的根节点存储在数组的第一个位置（为了方便，数组索引从1开始），相应地把它的左右两个子节点放在第二和第三的位置。一般地，我们可以把完全二叉数第l层的2^l个键值从左到右放在2^{l-1}和2^l - 1之间。并且节点之间有以下关系：

* 位于位置k的结点的父结点的位置是 k / 2
* 位于位置k的结点的子节点的位置是 2k 和 2k + 1

## 如何构造一个堆
可以通过往数组末端不断插入记录来递增地构造一个堆。在插入新记录时，堆的顺序可能会不满足预定的条件：在最小堆中新记录可能小于它的父节点，或者是在最大堆中新纪录大于它的父节点。在这种情况下，需要交换这个记录和它的父节点的位置，这称作一次上游,对这个记录不断上浮直到不能继续上游为止，就维持了堆的既有顺序。下面的代码显示了最小堆的上游代码：

```
        void swim(int k) {
            // parent of node at k is k/2
            while (k > 1 && pq_[k / 2] > pq_[k]) {
                // if children's node is larger than parent, exchange
                exch(pq_[k], pq_[k / 2]);

                // swim up a level
                k /= 2;
            }
        }
```

对于一个有n个记录的堆来说，一次上浮最多只需要lg(n)次操作，因此，构造堆的时间复杂度为O(n\log(n))复杂度

## 如何从堆中取得最小值
从最小堆中取得最小的记录只需取数组的第一个元素即可，但是取完后二叉树会出现一个洞，需要把数组最后的一个记录填补到已经移除的第一个记录上；把最后一个记录移上来后可能会破坏堆的性质，如最小堆中根结点的记录可能会大于子结点，如果出现这种情况，需要将根结点和其较大的子结点交换，这称为一次下沉。下面是最小堆的下沉代码：

```
        void sink(int k) {
            // make sure k is not the bottom level
            while (2 * k <= N_) {
                // j is the left children
                int j = 2 * k;
                if (j < N_ && pq_[j] < pq_[j + 1]) {
                    // now, j is the bigger children
                    j++;
                }

                if (pq_[k] > pq_[j]) {
                    break;
                }

                // if parent node is smaller than the bigger children, exchange
                exch(pq_[k], pq_[j]);

                // sink down a level
                k = j;
            }

        }
```

对于一个有n个记录的堆来说，一次下沉最多只需要lg(n)次操作，因此，取得最小值的操作的时间复杂度为O(\log(n))

## 更快的构建堆的方法

一条一条地插入记录来构造堆的方法需要O(n\log(n))的时间复杂度，如果记录序列全部已知，我们可以采用一种自底向上的构造方法，基本思路是从底端不是叶子结点的记录开始，做下沉操作，这样只需处理n/2个结点，这个时间复杂度基本上是线性的。下面是最大堆的下沉操作和构造方式：

```
  template<typename T>
    void sink(std::vector<T>& a, int k, int N) {
        // NOTE: the value of node k is a[k - 1]

        // make sure k is not the bottom level
        while (2 * k < N) {
            // j is the left children
            int j = 2 * k;
            if (j < N && a[j - 1] < a[j]) {
                // now, j is the bigger children
                j++;
            }

            if (a[k - 1] > a[j - 1]) {
                break;
            }

            // if parent node is smaller than the bigger children, exchange
            exch(a[k - 1], a[j - 1]);

            // sink down a level
            k = j;
        }
    }
    // Heap construction
    for (int k = N / 2; k >= 1; --k) {
        // loop for every non leaf node
        sink(pq, k, N);
    }
```

堆排序实现(这里用到了上面的最大堆的下沉方法)

```
    template<typename T>
    void heap_sort(std::vector<T>& pq) {
        int N = pq.size();

        // Heap construction
        for (int k = N / 2; k >= 1; --k) {
            // loop for every non leaf node
            sink(pq, k, N);
        }

        // Sort down
        while(N > 1) {
            exch(pq[0], pq[N - 1]);
            sink(pq, 1, --N);
        }
    }
```

基本特点

* 最坏的情况下也能达到O(n\log(n))，这是排序算法的理论最优。
* 缺点在于内部循环较长，无法使用缓存，并且是不稳定的，在实际中并不是最快的

## 归并排序

归并排序体现了分治的策略。主要思想是把大问题分解成小问题，不断递归去求解。

代码实现

```
    // merge tow subarray
    template<typename T>
    void merge(std::vector<T>& a, std::vector<T>& aux, int lo, int mid, int hi) {
        for (int i = lo; i <= hi; ++i) {
            aux[i] = a[i];
        }

        int m = lo;
        int n = mid + 1;
        for (int i = lo; i <= hi; ++i) {
            if (m > mid) {
                a[i] = aux[n++];
            } else if (n > hi) {
                a[i] = aux[m++];
            } else if (aux[n] < aux[m]) {
                a[i] = aux[n++];
            } else {
                a[i] = aux[m++];
            }
        }
    }

    // resuive sort
    const int CUTOFF = 7;
    template<typename T>
    void merge_sort(std::vector<T>& a, std::vector<T>& aux, int lo, int hi) {
        if (hi <= lo) {
            return;
        }

        // use insertion sort for small subarrays
        if (hi <= lo + CUTOFF - 1) {
            insertion_sort(a, lo, hi);
            return;
        }

        int mid = lo + (hi - lo) / 2;
        merge_sort(a, aux, lo, mid);
        merge_sort(a, aux, mid + 1, hi);

        // do not merge if already sorted
        if (a[mid] < a[mid + 1]) {
            return;
        }

        merge(a, aux, lo, mid, hi);
    }
```

上述实现中借助了一个额外的aux数组来存储记录，并且在子问题规模很小时采用了插入排序。

基本特点

* 归并排序的平均时间复杂度为O(n\log(n))
* 归并排序不是原地排序，需要额外的存储空间


## 归并排序的非递归实现

基本思想

1. 遍历数组，首先归并排序大小为1的子数组
2. 继续遍历，不断归并大小为2，4，16的子数组

```
    template<typename T>
    void bottom_up_merge_sort(std::vector<T>& a, std::vector<T>& aux, int lo, int hi) {
        int N = hi - lo + 1;
        for (int sz = 1; sz < N; sz += sz) {
            for (int k = lo; k < lo + N - sz; k += (sz + sz)) {
                merge(a, aux, k, k + sz - 1, std::min(k + sz + sz - 1, N - 1));
            }
        }
    }
```

基本特点

* 如果有足够的空间的话，非递归的归并排序的稳定性是工业级别的

## 快速排序

快速排序和归并排序类似，都是递归的算法，通过把问题分解为子问题来解决。不同的是，归并排序每次都把问题分成相同大小的两个子问题，然后通过归并操作进行合并；而快速排序则通过拆分的方式来分解问题，即每次找一个中间元素，把记录分成小于该中间元素（在中间元素左边）和大于该中间元素（在中间元素右边）的这两部分，此时中间元素已经排好序，只需对左右两边递归继续采用相同方式拆分即可。

和归并排序的归并操作是线性的时间复杂度类似，快速排序的拆分操作也是线性的。归并排序和快速排序的递归分解都把问题变成了一个二叉树的结构，而归并排序的二叉树是完全二叉树，因此树高是lg(n)，而快速排序的树高则与中间元素的选取有很大的关系，为了达到了归并排序相似的树高，要求输入记录必须是无序的，研究表明，无序的二叉树插入的树高平均只比完全二叉树高36%，因此该种情况下的快速排序和归并排序的时间复杂度是相同的。当然由于快速排序加入了随机的因素，我们只能说平均情况下快速排序和归并排序的时间复杂度是相同的，也不排除很小的概率的情况下快速排序的时间复杂度为n^2

基本思想

* 随机打乱原始记录
* 针对索引为j的记录进行拆分，使得：
  * 记录a[j]位于最终已排序的位置
  * j左边的记录没有比a[j]大的
  * j右边的记录没有比a[j]小的
* 对拆分后的各个部分递归进行上述处理

代码实现

```
    template<typename T>
    int partition(std::vector<T>& a, int lo, int hi) {
        int i = lo;
        int j = hi + 1;

        while(true) {
            // process i pointer
            // find item on left to swap
            while(a[++i] < a[lo]) {
                if (i == hi) {
                    break;
                }
            }

            // process j pointer
            // find item on right to swap
            while(a[--j] > a[lo]) {
                if (j == lo) {
                    break;
                }
            }

            // find if pointers cross
            if (i >= j) {
                break;
            }

            // swap
            exch(a[i], a[j]);
        }

        // swap with partition item
        exch(a[lo], a[j]);

        // return index of item now known to be in place
        return j;
    }


    template<typename T>
    void quick_sort_sub(std::vector<T>& a, int lo, int hi) {
        if (hi <= lo + CUTOFF) {
            // improvement 1:  use insertion fort for small subarray
            insertion_sort(a, lo, hi);
            return;
        }

        // improvement 2: estimate partition item with median of three samples
        int m = median_of_three(a, lo, lo + (hi - lo)/ 2, hi);
        exch(a[lo], a[m]);

        int j = partition(a, lo, hi);
        quick_sort_sub(a, lo, j - 1);
        quick_sort_sub(a, j + 1, hi);
    }

    template<typename T>
    void quick_sort(std::vector<T>& a) {
        // shuffle is needed for performance guarantee
        shuffle(a);

        quick_sort_sub<T>(a, 0, static_cast<int>(a.size()) - 1);
    }
```

上面的代码使用了两个提升：和归并排序中一样，我们在记录序列很小时采用了插入排序；另外我们本来是采用随机打乱后的记录顺序来选取中间值，为了让得到的二叉树更加平衡，我们需要选择接近数列中位数的记录作为中间值，这里我们采用了抽样的方式来计算中值。

基本特点

虽然快速排序理论上只能在概率上趋近于nlg(n)的时间复杂度，但是由于它的内层循环较小，并且容易利用计算机缓存等原因，一个设计得很好的快速排序的效率是归并排序和堆排序的2-3倍！

在实际应用中，如果记录有许多重复的话，会发现快速排序接近于n^2的时间复杂度，这时候我们需要使用快速排序的改进版：3路快速排序

## 3路快速排序

基本思想是：

* 把记录序列查分成3部分（而不是之前的两部分）
* 在lt和gt中间的记录都等于中间元素
* lt左边的记录都不大于中间元素
* lt右边的记录都不小于中间元素

实现代码

```
    // * Let v be partitioning item a[lo].
    // * Scan i from left to right.
    //  - (a[i] < v): exchange a[lt] with a[i]; increment both lt and i
    //  - (a[i] > v): exchange a[gt] with a[i]; decrement gt;
    //  - (a[i] == v): increment i
    template<typename T>
    void quick_sort_3way_sub(std::vector<T>& a, int lo, int hi) {
        if (hi <= lo + CUTOFF) {
            // improvement 1:  use insertion fort for small subarray
            insertion_sort(a, lo, hi);
            return;
        }

        int lt = lo;
        int i = lo;
        int gt = hi;

        // improvement 2: estimate partition item with median of three samples
        int m = median_of_three(a, lo, lo + (hi - lo)/ 2, hi);
        exch(a[lo], a[m]);

        // partition item
        T v = a[lo];

        while(i <= gt) {
            if (a[i] < v) {
                exch(a[lt++], a[i++]);
            } else if (a[i] > v) {
                exch(a[i], a[gt--]);
            } else {
                i++;
            }
        }

        quick_sort_3way_sub(a, lo, lt - 1);
        quick_sort_3way_sub(a, gt + 1, hi);
    }

    template<typename T>
    void quick_sort_3way(std::vector<T>& a) {
        // shuffle is needed for performance guarantee
        shuffle(a);

        quick_sort_3way_sub<T>(a, 0, static_cast<int>(a.size()) - 1);
    }
```

至此，经典的排序方法已经介绍完毕。除了选择排序和插入排序需要二次的时间复杂度外，堆排序、归并排序以及快速排序都能达到nlg(n)的时间复杂度，而这也是证明了的排序算法时间复杂度的下界，即这已经是最优算法了。但是从之前的讨论可以看到，在实际情况中，受到各种因素的限制，时间复杂度相同的算法的实际效率并不同，并且有可能相差数倍，当然，这是大O方式来衡量时间复杂度的一个弊端：即它只能忽略影响算法效率的其它因素，单单从输入规模上来看算法运行时间随输入规模的变化。从这个角度来看，虽然堆排序、归并排序以及快速排序都是最优算法，但是还可能有更快的排序算法等待着我们去发掘。
