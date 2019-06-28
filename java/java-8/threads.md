# New classes related to thread in java 8

## Adder & LongAccumators

LongAdder and LongAccumators which are recommended instead of the Atomic classes when multiple threads update frequently and less read frequently. During high contention, they were designed in such a way they can grow dynamically.


