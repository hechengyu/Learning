# 5.HyperLogLog 结构

## 5.1 什么是 HyperLogLog？

**作用**：用于`基数`统计的算法。

**优点**：输入元素的数量或者体积非常非常大时，计算基数所需的空间总是固定 的、并且是很小的。每个 HyperLogLog 键只需要花费 12 KB 内存，就可以计算接近 2^64 个不同元素的基数。但是HyperLogLog 只会根据输入元素来计算基数，而不会储存输入元素本身，所以 HyperLogLog 不能像集合那样，返回输入的各个元素。

>  **什么是基数？**
>
>  比如数据集 {1, 3, 5, 7, 5, 7, 8}， 那么这个数据集的基数集为 {1, 3, 5 ,7, 8}, 基数(不重复元素)为5。 基数估计就是在误差可接受的范围内，快速计算基数。

## 5.2 相关命令

| 命令                                      | 描述                                      |
| ----------------------------------------- | ----------------------------------------- |
| PFADD key element [element ...]           | 添加元素到 HyperLogLog 中                 |
| PFCOUNT key [key]                         | 返回给定 HyperLogLog 的基数估算值         |
| PFMERGE destkey sourcekey [sourcekey ...] | 将多个 HyperLogLog 合并为一个 HyperLogLog |

测试：

```shell
pfadd hL1 a b c d
pfcount hL1
# (integer) 4

pfadd hL2 a b e f
pfcount hL2
# (integer) 4

pfcount hL1 hL2
# (integer) 6

pfmerge hL3 hL1 hL2
pfcount hL3
# (integer) 6
```