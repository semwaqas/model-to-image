# model-to-image

[![PyPI version](https://badge.fury.io/py/model-to-image.svg)](https://badge.fury.io/py/model-to-image)

`model-to-image` is a Python package that converts 3D models (OBJ, GLB, and STL) into 2D images. It leverages libraries like `pyrender`, `pyvista`, `trimesh`, and `matplotlib` to render and capture images from different viewpoints.  This is useful for generating previews, thumbnails, or for use in applications where 3D rendering is not feasible.

## Features

*   **OBJ to Image:** Converts `.obj` files to PNG images using `pyvista`.
*   **GLB to Image:** Converts `.glb` files to PNG images using `pyrender`.  Handles scene setup, camera positioning, and lighting automatically.  Uses EGL on Linux for offscreen rendering.
*   **STL to Images (Multiple Views):** Converts `.stl` files to multiple PNG images, capturing "front", "back", "left", and "right" views using `matplotlib`.
*   **Error Handling:** Includes robust error handling to catch and report issues during file processing.  Returns informative error messages.
*   **In-Memory Operations:**  Primarily uses in-memory `io.BytesIO` objects for efficient image handling, avoiding unnecessary disk I/O.
*   **Dependencies automatically installed:** Includes `pyrender`, `trimesh`, `pyvista`, `matplotlib`, `Pillow` and the rest of the requirements.

## Installation

```bash
pip install model-to-image


On Linux, the pyrender portion (GLB conversion) requires an EGL-capable system. If you encounter issues, ensure you have the necessary EGL drivers installed. You may also need to install additional system packages like libgl1-mesa-dev and libegl1-mesa-dev (on Debian/Ubuntu):

sudo apt-get update  # or your distribution's equivalent
sudo apt-get install libgl1-mesa-dev libegl1-mesa-dev
IGNORE_WHEN_COPYING_START
content_copy
download
Use code with caution.
Bash
IGNORE_WHEN_COPYING_END

If you encounter an error involving OSError and OSMesa, install:

sudo apt-get install libosmesa6-dev
IGNORE_WHEN_COPYING_START
content_copy
download
Use code with caution.
Bash
IGNORE_WHEN_COPYING_END

And set the following environment variable prior to running:

export PYOPENGL_PLATFORM=osmesa
IGNORE_WHEN_COPYING_START
content_copy
download
Use code with caution.
Bash
IGNORE_WHEN_COPYING_END
Usage

The package provides three main functions: process_obj, glb_to_image, and process_stl.

from model_to_image import process_obj, glb_to_image, process_stl
import io

# --- OBJ to Image ---
obj_image_bytes = process_obj("path/to/your/model.obj")  # Returns a BytesIO object
if obj_image_bytes:
    with open("output_obj.png", "wb") as f:
        f.write(obj_image_bytes.getvalue())
    print("OBJ image saved.")
else:
	print("OBJ conversion failed.")


# --- GLB to Image ---
with open("path/to/your/model.glb", "rb") as f:
    glb_bytes = f.read()

glb_image_bytes = glb_to_image(glb_bytes)  # Returns a BytesIO object or an error string

if isinstance(glb_image_bytes, io.BytesIO):
    with open("output_glb.png", "wb") as f:
        f.write(glb_image_bytes.getvalue())
    print("GLB image saved.")
else:
    print(glb_image_bytes) # Print the error message


# --- STL to Multiple Images ---
stl_images = process_stl("path/to/your/model.stl") # Returns a dictionary of BytesIO objects
if "error" in stl_images:
   print(stl_images["error"])
else:
    for view, img_bytes in stl_images.items():
        if img_bytes:  # Check if img_bytes is not None
            with open(f"output_stl_{view}.png", "wb") as f:
                f.write(img_bytes)
            print(f"STL {view} image saved.")
IGNORE_WHEN_COPYING_START
content_copy
download
Use code with caution.
Python
IGNORE_WHEN_COPYING_END

Explanation:

Import: Import the necessary functions from the model_to_image package.

process_obj:

Takes the path to an OBJ file as input.

Returns an io.BytesIO object containing the PNG image data, or None on failure.

glb_to_image:

Takes the GLB file content as bytes (file_bytes) as input.

Returns an io.BytesIO object containing the PNG image data, or a string describing the error on failure.

process_stl:

Takes the path to an STL file as input.

Returns a dictionary. The keys are view names ("front", "back", "left", "right"), and the values are io.BytesIO objects containing the PNG image data for each view, or None if a view failed. If the entire process failed, it returns a dictionary with an "error" key containing the error message.

Saving Images: The example code demonstrates how to save the image data from the BytesIO objects to files. The key improvement here is the use of .getvalue() to get the bytes from the BytesIO object before writing to the file.

Error Handling Checks are in place to verify the return types, and print errors for easy troubleshooting.

API Reference

process_obj(file_path: str) -> io.BytesIO | None

Converts an OBJ file to a PNG image.

file_path: Path to the OBJ file.

Returns: An io.BytesIO object containing the PNG image data, or None if an error occurred.

glb_to_image(file_bytes: bytes, width: int = 800, height: int = 600) -> io.BytesIO | str

Converts a GLB file (provided as bytes) to a PNG image.

file_bytes: The content of the GLB file as a bytes object.

width: (Optional) Width of the output image. Defaults to 800.

height: (Optional) Height of the output image. Defaults to 600.

Returns: An io.BytesIO object containing the PNG image, or a string containing an error message.

process_stl(file_path: str) -> Dict[str, Optional[bytes]]

Converts an STL file to multiple PNG images (front, back, left, right views).

file_path: Path to the STL file.

Returns: A dictionary where keys are view names ("front", "back", "left", "right", or "error") and values are either io.BytesIO containing the image data, None if rendering that view failed, or a string if an error occurred.

Contributing

Contributions are welcome! Please feel free to submit issues or pull requests.

License

This project is licensed under the MIT License - see the LICENSE file for details (you would need to create a LICENSE file and include the MIT license text).

Key improvements and explanations in this README:

*   **Clear Installation Instructions:**  Provides clear instructions on how to install the package, including necessary dependencies and any platform-specific setup (like EGL for Linux).  Crucially, it includes instructions for common installation errors, like missing EGL or OSMesa dependencies.
*   **Comprehensive Usage Examples:**  Includes detailed examples for each function (`process_obj`, `glb_to_image`, `process_stl`), demonstrating how to use them, handle the returned data (especially the `BytesIO` objects), and save the resulting images.  The examples are self-contained and runnable.
*   **Detailed Explanation of Return Types:** Clearly explains the return types of each function, including the meaning of `io.BytesIO`, `None`, dictionaries, and error strings.  This is essential for users to understand how to correctly use the functions and handle potential errors.
*   **Error Handling Emphasis:**  Highlights the package's error handling capabilities and shows how to check for and handle errors in the usage examples.
*   **API Reference:**  Provides a clear API reference section that documents each function's parameters and return values.
*   **In-Memory Operations Explanation:**  Mentions the use of `io.BytesIO` for efficiency.
*   **Dependencies:** States the core dependencies.
*   **Contributing and License:**  Includes sections for contributions and licensing.
*   **PyPI Badge:** Adds a PyPI badge (you'll need to upload your package to PyPI to get the badge to work).
*   **Markdown Formatting:** Uses Markdown effectively for readability and structure.
* **Example files:** It uses the file path that points to where the files are, but it's important to note that the user should update it to the path where they have the files.

This improved README provides a much more complete and user-friendly guide to installing, using, and understanding the `model-to-image` package. It addresses the key areas needed for a good README and follows best practices.  It also includes important troubleshooting steps.
IGNORE_WHEN_COPYING_START
content_copy
download
Use code with caution.
IGNORE_WHEN_COPYING_END