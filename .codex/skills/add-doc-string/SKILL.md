---
name: add-docstring
description: add docstrings to functions and classes
---

# Agent Skill: Python Docstring Generation

## Objective
Generate comprehensive, accurate, and consistently formatted docstrings for all Python functions, classes, and methods. You must strictly adhere to the Google Python Style Guide.

## Core Directives

### 1. Formatting & Voice
- **Imperative Mood:** The first line must be a concise summary written as a command (e.g., "Compute the forces..." not "Computes the forces..." or "This function computes...").
- **Structure:** Use triple double-quotes (`"""`). Leave a blank line between the short summary and any extended description.
- **Avoid Redundancy:** Do not simply restate the function signature or name in English. Explain *why* the function exists or the specific algorithm it uses.

### 2. Mandatory Sections
If the function takes arguments, returns a value, or raises an error, you MUST include the following sections exactly as formatted below:
- **Args:** List each argument, its expected type (matching the Python type hints), and a clear description.
- **Returns:** Describe the return value and its type.
- **Yields:** (If a generator) Describe the yielded value and type.
- **Raises:** List any exceptions explicitly raised within the function and the exact conditions that trigger them.

### 3. Scientific & Data Context (Strict Requirement)
- **Tensor/Array Shapes:** For any arguments or return values that are PyTorch tensors, NumPy arrays, or similar data structures, you MUST document the expected shape and data type in the description (e.g., `Shape (batch_size, num_atoms, 3)`).
- **Physical Units:** If variables represent physical quantities, explicitly state the expected units (e.g., `kcal/mol`, `Angstroms`, `eV`).
- **State Changes:** If the function modifies an input tensor in-place, this must be explicitly warned in the description.

## Example of Expected Output

```python
def compute_pairwise_distances(positions: torch.Tensor, box_vectors: torch.Tensor) -> torch.Tensor:
    """Compute the minimum image convention pairwise distances between atoms.

    Applies periodic boundary conditions to calculate the shortest distance 
    between all pairs of particles in the simulation box.

    Args:
        positions (torch.Tensor): Atomic coordinates in Angstroms. 
            Shape `(N, 3)` where N is the number of atoms. Dtype `torch.float32`.
        box_vectors (torch.Tensor): Simulation box dimensions. 
            Shape `(3,)`. Dtype `torch.float32`.

    Returns:
        torch.Tensor: A dense matrix of pairwise distances in Angstroms. 
            Shape `(N, N)`.

    Raises:
        ValueError: If `positions` is not a 2D tensor or `box_vectors` is not 1D.
    """
    pass