## Project – Numerical Solution of Heat Diffusion Equation in Python

This project aims to implement a numerical solution for the heat diffusion equation using Python.

### Content

-   **`main.py`** – Contains the full implementation of the issue discussed in the document `project_report.pdf`.
-   **`base.py`** – It is a base code, used as a starting point for developing a more complex version of the solution.

---

> For more theoretical details and explanations: `project_report.pdf`.

---

## Mathematical Model

This project implements a numerical solution for the 2D stationary heat diffusion equation with variable conductivity.

The script solves the following partial differential equation (PDE):
$$-\nabla\cdot(k(x,y)\nabla u(x,y))=f(x,y)$$

* **Domain:** A rectangle $\Omega = [0, a] \times [0, b]$, with $a=5$ and $b=2$.
* **Variable Conductivity:** $k(x,y)=1+0.5\cdot \sin(2\pi x)\cdot e^{-y}$.
* **Boundary Conditions:** Mixed conditions are applied:
    * **Dirichlet:** $u(x,y) = g_D(x,y)$ on $\Gamma_D$ (top and bottom boundaries).
    * **Neumann:** $k(x,y)\frac{\partial u}{\partial n} = g_N(x,y)$ on $\Gamma_N$ (left and right boundaries).
* **Analytical Solution:** To verify the accuracy of the numerical method, a known analytical solution is used: $u(x,y)=\sin(\frac{2\pi x}{3})\cos(\frac{\pi y}{2})$. The source term $f(x,y)$ and the boundary conditions are derived from this exact solution.

---

## Key Features & Implementation

The `main.py` script is a self-contained implementation of the entire numerical workflow, from discretization to analysis.

* [cite_start]**Finite Difference Method (FDM):** The PDE is discretized using a **second-order finite difference** scheme on a uniform grid[cite: 63, 178].
* [cite_start]**Sparse Matrix Assembly:** The resulting linear system ($A \cdot U = b$) [cite: 79] is constructed as a **sparse matrix**. [cite_start]The matrix is built in **LIL (List of Lists)** format for efficient element insertion [cite: 82] [cite_start]and then converted to **CSR (Compressed Sparse Row)** format, which is optimal for numerical operations[cite: 83]. This is visible in the code (`lil_matrix`, `csr_matrix`).
* **System Solvers:**
    * [cite_start]**`scipy.sparse.linalg.spsolve`**: The main, large linear system is solved using SciPy's high-performance sparse solver, which internally uses an efficient LU factorization[cite: 90, 91].
    * [cite_start]**Manual QR Solver (`rezolva_sistem_QR`)**: A custom QR decomposition solver, implemented manually using the Gram-Schmidt process [cite: 87][cite_start], is used to solve the smaller systems required for building the 1D quadratic spline interpolants[cite: 84].
* **Custom 2D Quadratic Spline:**
    * [cite_start]Since the FDM solution is only available at discrete grid points[cite: 94], a **continuous approximation** is built.
    * A **1D quadratic spline** (`spline_patratica_1d`) is implemented manually.
    * [cite_start]A **2D spline interpolant** (`spline_bi2d`) is constructed by first applying the 1D spline to each row (x-direction) [cite: 97][cite_start], and then applying it again to each column (y-direction) of the intermediate results[cite: 100].
* **Convergence Analysis:**
    * The script automatically runs the simulation for a range of grid sizes (e.g., N = 4, 8, 16, 32, 64, 128), as defined in the `n_vals` list.
    * [cite_start]It calculates the **maximum absolute error** between the interpolated numerical solution and the exact analytical solution[cite: 109, 110].
    * [cite_start]It generates 3D plots comparing the Numerical Solution, Analytical Solution, and Absolute Error for each `N` [cite: 107, 121-123].
    * [cite_start]Finally, it plots the maximum error vs. the step size ($h$) on a **log-log graph** to visually and numerically determine the method's convergence rate[cite: 111, 116].

---

## How to Run

### Dependencies

You will need the following Python libraries installed:
* `numpy`
* `matplotlib`
* `scipy`

You can install them using pip:
```bash
pip install numpy matplotlib scipy
