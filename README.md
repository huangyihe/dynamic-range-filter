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

_min<sub>i=1...k</sub>(h<sub>i</sub>(y) - h<sub>i</sub>(x)) = O(log(y-x))_

If the conjecture is true, then approximate range query can be performed by checking _O(log(y-x))_ bits in the Bloom filter.

The asymptotic time complexity will be the same as a full-blown ordered data structures like balanced search trees,
but the sapce required can be much smaller.
