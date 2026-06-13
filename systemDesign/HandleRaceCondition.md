# How to handle race condition when modifying data

## On a single DB
### Use Locks

- Pessimistic lock: require lock(smallest scope, like row lock) when updating the data.

    - Should be used for frequent contention (race condition) is expected

    - Downgrade performance. Should require the lock of least scope (row lock is better than table lock)

- Optimistic locking: provide a version and check if version match at the end of the update. If mismatch, rollback previous steps

    - Should be used for rare contention scenarios: race condition is unlikely to happen. Otherwise it triggers too many retries and is worse than the pessimistic lock

    - Better performance for Pessimistic lock when race condition is unlikely.

## Between different nodes/DB
### Two-Phase Commit (2PC)