# NumPy, Plotting & Finance — Q&A (Set 23)

This set covers graph comparisons, compound interest, histograms, aspect ratio control, NumPy multiplication types, financial PMT, and handling strings in arrays.

## Q1. Ways to improve comparison of multiple series on one graph
Normalize/standardize data, use dual y-axes when units differ, apply log scale for wide ranges, add consistent colors/markers/linestyles with a legend, annotate key points, or use small multiples with shared axes to reduce clutter.

## Q2. Why compound interest beats higher non-compounding interest
Compounding earns interest on interest, making the effective annual rate exceed the nominal rate. Over time, a modest compounding rate outgrows a somewhat higher simple rate.

## Q3. What is a histogram? One NumPy method
A histogram shows frequency distribution of data into bins. NumPy method: np.histogram(data, bins=...). For plotting, use matplotlib.pyplot.hist.

## Q4. Change aspect ratio between X and Y
In Matplotlib:
ax.set_aspect('equal') → 1:1 units.
ax.set_aspect(2.0) → y twice x.
fig.set_size_inches(w,h) → control figure shape.
ax.set_box_aspect(r) → fixed ratio box.

## Q5. Array multiplication types
- Regular elementwise: A * B → broadcast elementwise.
- Dot/matrix multiply: A @ B or np.dot(A,B).
- Outer product: np.outer(a,b) → all pairwise products, shape (len(a),len(b)).

## Q6. Numpy function for monthly mortgage payment
numpy_financial.pmt(rate, nper, pv, fv=0, when='end'). Older NumPy had np.pmt; now in numpy_financial.

## Q7. Strings in NumPy arrays; restrictions
Yes. Use fixed-length dtype ('Sk' bytes or 'Uk' Unicode). Longer assignments are truncated. Vectorized string ops via np.char.*. For variable-length strings, use dtype=object (slower).

