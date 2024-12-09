import tkinter as tk
from tkinter import messagebox

def solve_matrix():
    try:
        # Read matrix size
        size = int(entry_size.get())
        if size <= 0:
            raise ValueError("Matrix size must be a positive integer.")

        # Read coefficients
        matrix = []
        for i in range(size):
            row = [float(entry_matrix[i][j].get()) for j in range(size)]
            matrix.append(row)
        
        # Read constants
        constants = [float(entry_constants[i].get()) for i in range(size)]

        # Solve the system using Gaussian elimination
        solution = gaussian_elimination(matrix, constants)
        output_solution(solution)
    except Exception as e:
        messagebox.showerror("Error", str(e))

def gaussian_elimination(matrix, constants):
    n = len(matrix)
    
    # Forward elimination
    for i in range(n):
        # Make the diagonal element 1 and eliminate below
        pivot = matrix[i][i]
        if pivot == 0:
            raise ValueError("Matrix is singular or nearly singular.")
        for j in range(i, n):
            matrix[i][j] /= pivot
        constants[i] /= pivot
        
        for k in range(i + 1, n):
            factor = matrix[k][i]
            for j in range(i, n):
                matrix[k][j] -= factor * matrix[i][j]
            constants[k] -= factor * constants[i]

    # Back substitution
    solution = [0] * n
    for i in range(n - 1, -1, -1):
        solution[i] = constants[i] - sum(matrix[i][j] * solution[j] for j in range(i + 1, n))
    
    return solution

def output_solution(solution):
    solution_str = "\n".join(f"x{i + 1} = {val:.2f}" for i, val in enumerate(solution))
    messagebox.showinfo("Solution", solution_str)

# GUI Setup
root = tk.Tk()
root.title("Matrix Solver")

# Input Matrix Size
frame_size = tk.Frame(root)
frame_size.pack(pady=10)
tk.Label(frame_size, text="Matrix Size (n x n):").pack(side=tk.LEFT)
entry_size = tk.Entry(frame_size, width=5)
entry_size.pack(side=tk.LEFT)

# Input Matrix Coefficients
frame_matrix = tk.Frame(root)
frame_matrix.pack(pady=10)
tk.Label(frame_matrix, text="Coefficients Matrix (A):").pack()
entry_matrix = []

# Constants Vector
frame_constants = tk.Frame(root)
frame_constants.pack(pady=10)
tk.Label(frame_constants, text="Constants Vector (b):").pack()
entry_constants = []

def create_input_fields():
    try:
        # Clear previous entries
        for widget in frame_matrix.winfo_children():
            widget.destroy()
        for widget in frame_constants.winfo_children():
            widget.destroy()
        entry_matrix.clear()
        entry_constants.clear()

        # Get matrix size
        size = int(entry_size.get())
        if size <= 0:
            raise ValueError("Matrix size must be a positive integer.")

        # Create input fields for matrix
        for i in range(size):
            row_entries = []
            for j in range(size):
                entry = tk.Entry(frame_matrix, width=5)
                entry.grid(row=i, column=j, padx=5, pady=5)
                row_entries.append(entry)
            entry_matrix.append(row_entries)

        # Create input fields for constants
        for i in range(size):
            entry = tk.Entry(frame_constants, width=5)
            entry.grid(row=i, column=0, padx=5, pady=5)
            entry_constants.append(entry)
    except ValueError as e:
        messagebox.showerror("Error", str(e))

# Generate Input Fields Button
btn_generate = tk.Button(root, text="Generate Input Fields", command=create_input_fields)
btn_generate.pack(pady=10)

# Solve Button
btn_solve = tk.Button(root, text="Solve", command=solve_matrix)
btn_solve.pack(pady=10)

root.mainloop()
