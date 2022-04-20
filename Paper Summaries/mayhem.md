offline SE = "concolic" SE
online SE = "static"/"classic" SE

MAYHEM introduces hybrid SE
  - does an hybrid between concolic and concrete (concrete executes concrete blocks concretely so it has concrete state)
  - when memory is low a states' concrete state is discarded, symbolic is saved to disk
  - when checkpoints are fetched from disk concrete input is generated based on
    the path condition and it is concretely executed to the checkpoint,
    at that point hybrid execution can continue

MAYHEM proposes techniques for symbolic memory indexing
  Annex V

Path Selection uses heuristics to classify states
  - **explore paths with symbolic instruction pointers**
    overwriting the return address would result in this => top priority
  - explore new code
  - explore memory accesses with symbolic indices

Optimizations:
  - split path predicate into independent formulas (Separate formulas without common variables)
  - algebraic simplifications
  - taint analysis (to identify non-symbolic basic blocks)

Memory Objects (not allocating for not accessed indices saves a lot of memory)
  * maps 32 bit index to (symbolic) expressions
  * MOs are immutable
  * symbolically indexed reads result in generating a new MO that contains
      mappings for all values the index could take. To know which values
      the index can take we do binary search to find the upper and lower bound
    - still a lot of queries
    - may not be continuous
    - there may be structure to the data in memory that we are ignoring
    - might be able to access everything
    + Value Set Analysis, use a stride
      example array of 128 bit structs, first 32 bits of each struct are accessed, ignore the other 96
    + Refinement Cache (**Not sure**, map large intervals to refinements as cache?)
    + Lemma Cache, canonicalize formulas (F -> Q) and cache the results (res(Q))
  _requires_ split path predicate optimization
    + Index Search Trees, symbolic index is the key, leaf nodes are the entries
  of the MO
    + Bucketization, y = A * x + B, reduces number of leaf nodes in the IST

WASP:
  - formula normalization and Lemma Cache
  - symbolic writes -> concretize idx, careful for sequential accesses (cache the expression's results)
    + add concrete idx to pc so that different indexes are considered
  - symbolic reads -> IST, if range is too big then concretize with focus on unmapped and symbolic data regions
