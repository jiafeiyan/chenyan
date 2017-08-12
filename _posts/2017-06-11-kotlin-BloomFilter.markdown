---
layout:     post
title:      "Kotlin入门学习————BloomFilter算法编写"
subtitle:   "内存消耗较少的去重策略，提供稳定去重服务"
date:       2017-06-11
author:     "Chenyan"
header-img: "img/post-bg-bloomfilter.jpg"
catalog:    false
tags:
    - Kotlin
---
> Bloom Filter 是由 Howard Bloom在1970年提出的二进制向量数据结构，它具有很好的空间和时间效率，被用来检测一个元素是不是集合中的一个成员。如果检测结果为是，该元素不一定在集合中的；但如果检测结果为否，该元素一定不在集合中。因此Bloom Filter具有100%的召回率，同时也不适合哪些“零错误”的应用场景。这样每个检测请求返回有”在集合内（可能错误）“和”不在集合内（绝对不在集合内）“两种情况，可见Bloom Filter是牺牲了正确率和时间以节省空间。

#### 程序例子代码

```
package spider.bloomFilter

import java.util.*

/**
 * Created by chenyan on 2017/5/25.
 * BloomFilter算法，去重策略，提供稳定的去重服务，内存消耗较少
 */

class BloomFilter{

    /* BitSet初始分配2^24个bit */
    private val DEFAULT_SIZE: Int get() = 1 shl 25
    private val bits: BitSet = BitSet(DEFAULT_SIZE)

    /* 哈希函数的种子，一般应取质数 */
    private val SEEDS: IntArray get() = intArrayOf(5,7,11,13,31,37,61)
    /* 哈希函数对象 */
    private val func: Array<SimpleHash?> get() = Array(size = SEEDS.size,init = {
        index -> SimpleHash(DEFAULT_SIZE , SEEDS[index])
    })

    /* 将字符串标记到bits中 */
    @Synchronized fun add(value: String): Unit {
        func.forEach { simpleHash ->
            bits.set(simpleHash!!.hash(value),true)
        }
    }

    /* 判断字符串是否已经被bits标记 */
    fun contains(value: String): Boolean {
        if (value == null) return false
        var ret: Boolean = true
        func.forEach { simpleHash ->
            ret = ret && bits.get(simpleHash!!.hash(value))
        }
        return ret
    }

    /* 哈希函数类 */
    private class SimpleHash( val cap: Int , val seed: Int ){
        /* hash函数，采用简单的加权和hash */
        fun hash(value: String): Int {
            var result: Int = 0
            for (i in value.indices){
                result = seed * result + value[i].toInt()
            }
            return (cap - 1) and result
        }
    }
}
```
