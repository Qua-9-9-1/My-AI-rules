# OpenCV (Python/C++) : Best Practices & Architecture

## 1. Color Space Awareness
- **The BGR Trap:** OpenCV natively reads and decodes images in BGR format, whereas most modern tools (Matplotlib, PIL, Web Browsers) expect RGB. 
- **Rule:** Always explicitly convert color spaces at the system boundaries. Convert to RGB immediately upon reading if passing to another library (`cv2.cvtColor(img, cv2.COLOR_BGR2RGB)`).

## 2. Memory & Array Manipulation (Numpy)
- **Zero-Copy Operations:** In Python, OpenCV uses Numpy arrays. Prefer slicing and Numpy vectorized operations over iterating through pixels with nested `for` loops, which is drastically slower.
- **Contiguity:** When altering image shapes or passing arrays to C++ extensions, ensure the memory block is contiguous using `np.ascontiguousarray()` to prevent segmentation faults.

## 3. Resource Management
- **Hardware Locks:** Video captures (`cv2.VideoCapture`) place hardware locks on cameras. Always use `try/finally` blocks or context managers to guarantee that `cap.release()` and `cv2.destroyAllWindows()` are executed, even if the processing loop crashes.

## 4. Performance Optimization
- **Pre-allocation:** Do not allocate new memory matrices inside a high-speed video processing loop. Pre-allocate your output arrays (`np.zeros_like(frame)`) outside the loop and overwrite them using the `dst` parameter in OpenCV functions (e.g., `cv2.resize(src, dsize, dst=my_buffer)`).