# OS-Lab Project — Adaptive Readahead Optimization

+ Final Project for **Operating Systems Lab**  
+ Instructor: *Dr. Nabavi*
+ University: Shahid Beheshti University (SBU)

## Project Overview

This project focuses on improving **Readahead** mechanisms in operating systems using data-driven modeling and machine learning.

**Readahead** is a technique used by the OS to prefetch pages into the **page cache** before a process actually requests them, in order to reduce I/O latency.  
While this improves performance for sequential access patterns, static or poorly tuned readahead parameters can also cause **cache pollution** and **inefficient I/O** when the workload behavior changes.

The goal of this project is to:

- Analyze different I/O workloads and their effect on readahead performance  
- Extract and learn workload-specific features from system traces  
- Train models to dynamically determine optimal readahead sizes  
- Implement and integrate an adaptive readahead system call into a teaching OS (e.g. Pintos)

## Methodology

### Data Collection
- Collect low-level kernel trace data using **LTTng** while running **RocksDB** benchmarks.  
- Tracepoints such as:
  - `add_to_page_cache`
  - `writeback_dirty_page`
  - Other page-cache-related functions  

- Run at least **10 benchmarks**, ensuring steady-state performance.

### Feature Extraction
From collected traces, compute per-interval or per-transaction metrics such as:
- Average and variance of page faults per second  
- Number of read/write transactions  
- Differences between consecutive page offsets  
- Moving averages and standard deviations  

Use the trace information (e.g., `struct page`, `inode`, etc.) to design **hooks** for data collection.

### Dataset Processing
- Normalize data using **Z-Score normalization**
- Reduce dimensionality or visualize using **t-SNE**
- Label workloads as one of four classes:
  - `readseq`
  - `readreverse`
  - `readrandom`
  - `writerandom`

### Model Training
- Train a **neural network classifier** to distinguish between workloads based on extracted features.  
- Use:
  - **10-fold cross-validation**
  - **Cross-Entropy loss**
  - Hyperparameter tuning for optimal accuracy  
- Evaluate the model using **Accuracy** and confusion matrix.

### Decision Tree Model
- Train a **Decision Tree** model for interpretability.
- Visualize and compare its structure and classification performance with the neural network.

### Implementation in Pintos
- Add a new **system call** to Pintos, e.g.:
  ```c
  sys_readahead_tune(...)
