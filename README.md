# Range filters

## Idea

The idea is to have _k_ uniform-randomly sampled order-preserving hash functions,
_h<sub>1</sub>_, _h<sub>2</sub>_, ..., _h<sub>k</sub>_.

The hash functions map from the _key space we want to do range query on_ to _the Bloom filter bit index space_
(whose cardinality == number of bits in the Bloom filter), and preserve order.
So if _x_, _y_ in key space satisfy _x ≤ y_, then _h<sub>i</sub>(x) ≤ h<sub>i</sub>(y)_.

Like in a Bloom filter, the output _n_ of each hash function sets the _n_-th bit of the Bloom filter.

**The conjecture** is that on average, if _k = O(log N)_ (N is cardinality of key/input space),
given _x_, _y_ where _x < y_, then:

E<sub>all x, y</sub>\[_min<sub>i=1...k</sub>(h<sub>i</sub>(y) - h<sub>i</sub>(x))_\] = _O(log(y-x))_

If the conjecture is true, then approximate range query can be performed by checking _O(log(y-x))_ bits in the Bloom filter.

The asymptotic time complexity will be the same as a full-blown ordered data structures like balanced search trees,
but the sapce required can be much smaller.

## Constructing order-preserving hash functions

Assume the hash function is a map {0,1}<sup>N</sup> → {0,1}<sup>n</sup>. Typical choice of N may be 64 (64-bit keys), and n can be 30 (for a 64MB Bloom filter).

The hash function can be constructed by:

1. Choose a "lead-in" offset, denoted by _o_ uniformly from \[0,2<sup>N-1</sup>\), which is the hash output for the smallest possible key in the key space.

2. The hash function is essentially defined by (2<sup>n</sup> - 1) "separators" inserted among all values \[_o_, 2<sup>N</sup>\).

3. The separator locations can be sampled using resorvior sampling. This operation will take O(2<sup>n</sup>) time assuming that N is sufficiently larger than n.

4. Construct interval map out of separator locations. The interval map maps the interval ends with a particular separators to the order of the separator in sorted order.

5. The hash function is now just the interval map lookup function. Time complexity is O(n), space complexity O(2<sup>n</sup>).
