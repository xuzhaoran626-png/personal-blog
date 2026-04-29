# Remote Development Project Report

**Student Name**: 徐兆冉  
**Student ID**: ZY2557210  

## System Configuration

| Item | Information |
|------|-------------|
| CPU Model | AMD Ryzen 7 H 255 w/ Radeon 780M Graphics, 16 cores, x86_64 |
| Memory Size | 15 Gi total, 620 Mi used, 14 Gi free |
| Operating System Version | Linux localhost 6.6.87.2-microsoft-standard-WSL2 |
| Compiler Version |gcc (Ubuntu 13.3.0-6ubuntu2~24.04.1) 13.3.0 |
| Python Version |Python 3.13.12 |
## Implementation Details

### Python Language Implementation

#### Source Code
def matrix_multiply(A, B):
    # 维度检查
    if len(A[0]) != len(B):
        raise ValueError("矩阵维度不匹配")
    # 初始化结果矩阵
    result = [[0.0] * len(B[0]) for _ in range(len(A))]
    # 三重循环
    for i in range(len(A)):
        for j in range(len(B[0])):
            for k in range(len(B)):
                result[i][j] += A[i][k] * B[k][j]
    return result

if __name__ == "__main__":
    # 示例矩阵 A (2x3)
    A = [
        [1, 2],
        [3, 4]
    ]
    # 示例矩阵 B (3x2)
    B = [
        [5, 6],
        [7, 8]
    ]
    C = matrix_multiply(A, B)
    print("矩阵 A:")
    for row in A:
        print(' '.join(map(str, row)))
    print("\n矩阵 B:")
    for row in B:
        print(' '.join(map(str, row)))
    print("\n矩阵 A * B:")
    for row in C:
        print(' '.join(map(str, row)))

#### Execution Command
```bash
python assignment.py
## Algorithm Verification

### Correctness

To verify the correctness of the matrix multiplication algorithm, a small 2×2 test case was used.

The input matrices were:

\[
A =
\begin{bmatrix}
1 & 2 \\
3 & 4
\end{bmatrix}
\]

\[
B =
\begin{bmatrix}
5 & 6 \\
7 & 8
\end{bmatrix}
\]

The expected result by manual calculation is:

\[
A \times B =
\begin{bmatrix}
19 & 22 \\
43 & 50
\end{bmatrix}
\]

## Conclusion

In this project, I learned how to use Unix/Linux command line tools to collect system information and manage files. I also practiced writing technical documentation in Markdown format and implemented matrix multiplication in Python. By testing the program with a small example and comparing the output with manual calculation, I verified the correctness of the algorithm. This project helped me better understand remote development workflows and the differences between documentation and implementation tasks.
The output of the Python program matched the manually calculated result exactly. Therefore, the algorithm was verified to be correct for this test case.

## References

1. Python Official Documentation  
2. GCC Official Documentation  
3. Linux manual pages  
4. Markdown Guide
