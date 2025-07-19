# Go Worker Pool Pattern - Complete Reference

## Overview

This is a classic implementation of the Worker Pool pattern in Go. Use this pattern when you need to:

- Process many tasks concurrently with limited goroutines
- Control resource usage (CPU, memory, connections)
- Distribute work across multiple workers efficiently

## Pattern Architecture

```
Main Goroutine → inputCh → [Worker 1, Worker 2, ..., Worker N] → jobResults → Main Goroutine
```

### Key Components

1. **Fixed number of worker goroutines**
2. **Input channel** for job distribution
3. **Output channel** for result collection
4. **Controlled concurrency** (N workers, not M jobs goroutines)

## Code Implementation

```go
package main

import (
    "fmt"
    "time"
)

// jobResponse represents the result of processing a job
type jobResponse struct {
    WorkerID  int // Which worker processed this job
    ValueProc int // The job value that was processed
}

func main() {
    // Configuration
    numJobs := 100
    numWorkers := 10

    // Step 1: Create buffered channels
    inputCh := make(chan int, numJobs)
    jobResults := make(chan jobResponse, numJobs)

    // Step 2: Start worker pool
    for i := 0; i < numWorkers; i++ {
       go worker(i, inputCh, jobResults)
    }

    // Step 3: Send all jobs
    for j := 0; j < numJobs; j++ {
       inputCh <- j
    }

    // Step 4: Signal no more jobs
    close(inputCh)

    // Step 5: Collect results
    outputSlice := make(map[int][]int)
    for k := 0; k < numJobs; k++ {
       outputValue := <-jobResults
       if _, ok := outputSlice[outputValue.WorkerID]; ok {
          outputSlice[outputValue.WorkerID] = append(outputSlice[outputValue.WorkerID], outputValue.ValueProc)
       } else {
          outputSlice[outputValue.WorkerID] = []int{outputValue.ValueProc}
       }
    }

    // Step 6: Display results
    for workerID, processedJobs := range outputSlice {
       fmt.Printf("Worker %d processed jobs: %v\n", workerID, processedJobs)
    }
}

func worker(workerID int, inputCh <-chan int, outputCh chan<- jobResponse) {
    for jobValue := range inputCh {
       // Simulate work
       time.Sleep(2 * time.Second)
       
       // Send result
       outputCh <- jobResponse{
           WorkerID:  workerID,
           ValueProc: jobValue,
       }
    }
}
```

## Execution Flow

### Step-by-Step Breakdown

1. **Initialize**: Create channels and start 10 worker goroutines
2. **Queue Jobs**: Send 100 jobs (0-99) to `inputCh`
3. **Signal Complete**: Close `inputCh` to tell workers no more jobs coming
4. **Process**: Workers compete to grab jobs from `inputCh`
5. **Work**: Each worker processes jobs with 2-second simulated work
6. **Results**: Workers send completed work to `jobResults` channel
7. **Collect**: Main goroutine reads exactly 100 results
8. **Display**: Show which worker processed which jobs
9. **Exit**: All workers finish naturally, program terminates

### Timing Analysis

- **Total Jobs**: 100
- **Workers**: 10
- **Jobs per Worker**: ~10 (distributed automatically)
- **Work Time**: 2 seconds per job
- **Total Runtime**: ~20 seconds (parallel execution)

## Key Design Decisions

### Why Buffered Channels?

```go
inputCh := make(chan int, numJobs)          // Buffer = 100
jobResults := make(chan jobResponse, numJobs) // Buffer = 100
```

**Benefits:**
- Prevents blocking when sending jobs
- Improves performance by reducing synchronization overhead
- Allows main goroutine to queue all jobs quickly

### Why Close the Input Channel?

```go
close(inputCh)
```

**Purpose:**
- Signals workers that no more jobs are coming
- Causes `range inputCh` loops to exit naturally
- Enables clean worker shutdown without coordination

### Channel Directions

```go
func worker(workerID int, inputCh <-chan int, outputCh chan<- jobResponse)
```

**Type Safety:**
- `<-chan int`: Receive-only channel (worker can't send jobs)
- `chan<- jobResponse`: Send-only channel (worker can't read results)
- Prevents accidental misuse of channels

## Pattern Benefits

### Controlled Concurrency
- **Limited Resources**: Only 10 goroutines vs potentially 100
- **Predictable Memory**: Bounded channel sizes
- **System Stability**: Won't overwhelm system with unlimited goroutines

### Automatic Load Balancing
- Workers compete for jobs naturally
- Faster workers process more jobs
- No manual job assignment needed

### Clean Shutdown
- Workers exit automatically when input channel closes
- No need for complex synchronization
- Program terminates cleanly

### Scalability
- Easy to adjust `numWorkers` based on system capacity
- Can handle any number of jobs with fixed worker count
- Horizontal scaling by increasing worker count

## Common Variations

### With Error Handling

```go
type jobResponse struct {
    WorkerID int
    ValueProc int
    Error error
}
```

### With WaitGroup

```go
var wg sync.WaitGroup
for i := 0; i < numWorkers; i++ {
    wg.Add(1)
    go func(id int) {
        defer wg.Done()
        worker(id, inputCh, jobResults)
    }(i)
}

// Send jobs...
close(inputCh)
wg.Wait() // Wait for all workers to finish
close(jobResults) // Safe to close output channel
```

### With Context for Cancellation

```go
func worker(ctx context.Context, workerID int, inputCh <-chan int, outputCh chan<- jobResponse) {
    for {
        select {
        case jobValue, ok := <-inputCh:
            if !ok {
                return // Channel closed
            }
            // Process job...
        case <-ctx.Done():
            return // Context cancelled
        }
    }
}
```

## When to Use This Pattern

### Good Use Cases
- **CPU-intensive tasks**: Image processing, data transformation
- **I/O operations**: Database queries, API calls, file processing
- **Rate limiting**: Control concurrent connections to external services
- **Batch processing**: Large datasets that can be processed in chunks

### Consider Alternatives When
- **Single task**: No need for worker pool overhead
- **Real-time processing**: When job ordering matters
- **Dynamic scaling needed**: Worker pool has fixed size
- **Complex dependencies**: Jobs depend on other job results

## Performance Characteristics

### Memory Usage
- **Fixed**: O(numWorkers) goroutines regardless of job count
- **Bounded**: Channel buffers limit memory growth
- **Efficient**: No dynamic goroutine creation/destruction

### CPU Usage
- **Parallel**: Up to `numWorkers` tasks running simultaneously
- **Balanced**: Work distributed automatically among workers
- **Efficient**: No context switching overhead from excessive goroutines

### Throughput
- **Scalable**: Throughput increases with worker count (up to system limits)
- **Predictable**: Performance characteristics are consistent
- **Tunable**: Easy to adjust worker count based on profiling

## Debugging Tips

### Common Issues
1. **Deadlock**: Forgetting to close input channel
2. **Goroutine leaks**: Not reading all results from output channel
3. **Race conditions**: Sharing mutable state between workers

### Monitoring
```go
// Add metrics to track performance
processed := 0
for k := 0; k < numJobs; k++ {
    outputValue := <-jobResults
    processed++
    if processed%10 == 0 {
        fmt.Printf("Processed %d/%d jobs\n", processed, numJobs)
    }
}
```

## Summary

The Worker Pool pattern provides:
- **Bounded concurrency** with predictable resource usage
- **Automatic load balancing** across workers
- **Clean shutdown** through channel closing
- **High throughput** with controlled parallelism

This pattern is a fundamental building block for concurrent Go applications and is widely used in production systems for processing large workloads efficiently.
