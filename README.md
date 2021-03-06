# A E Q V I P E D I S

`AEQVIPEDIS` converts images to low-resolution triangle-meshes stored in an efficiently packed bitstream. A complementary JavaScript function dynamically generates SVGs from the binary data. With this technique short chunks of text (i.e., the Base64 encoded bitstream) can be used as a placeholder for high-resolution graphics on the web.

This project was inspired by José M. Pérez's [blog post on SVG placeholders](https://jmperezperez.com/svg-placeholders/).

## How to use

The library (`aequipedis.cpp`, `aequipedis.hpp`) that generates the triangle representations is written in C++ and only depends on the standard library. This repository includes a standalone demo program that uses the `stb_image` library to load images. `stb_image` will automatically be downloaded by the provided makefile. Compilation was tested with GCC 7.3.1. 

From the command line run
```bash
git clone https://github.com/astoeckel/aequipedis && cd aequipedis && make
```
You can now run the generated `./aequipedis` binary.

### Command line arguments

* **Resolution** `-r`, `--res`. The resolution determines the density of initial feature points that are extracted from the image. Minimum value is `3`, maximum value is `254`. Default value is 16.
* **Maximum feature count** `-m`, `-max_count`. This parameter determines the number of feature points that are actually used, i.e. if `m` is smaller than `r * r` the program removes feature points that are likely to be less relevant for the image. If `-m` is zero, all feature points will be used. Default value is zero.
* **Relative feature threshold** `-t`, `--threshold`. Value between 0.0 and 1.0 that determines which features are discarded. If `0.0` all features are used, independent of the associated edge strength. If `0.5` only features stronger than `0.5 * strongest feature strength` are used. If `1.0` only the strongest feature is used. This does not affect border points. Default value is `0.0`.

### Use as a library

To use the code as a library add the provided `aequipedis.cpp` and `aequipedis.hpp` to your C++ project. In conjunction with the CLI program, the header file `aequipedis.hpp` should contain all necessary information on how to use the library.

## Example

Consider the following image

![Source image](demo/src/david-clode-363878.small.jpg)

Running
```bash
./aequipedis -r 8 -m 32 image.png
```
produces the following `base64` encoded bitstream
```
IC4IAAAAJABsAJAA2QD/GRIifiKAKM48uEj/bABfJHt9dMhs/5AAgR+TVJtdgYSM5tkAzTnrWsrQ/wD/
SP9s/5D//8ACNzZzoPLtxYyVZxHc2+QAPDYTkOOBCyNCXaT53RxOhbT/1wlTXob+0RwTK0JPqs+y6KOp
o6KRmVtef2WSm2xhbHLQ8tey17DNkFcvvrTPtdDvx7DPj11otq/Ftdny16qziyUOPyqtqYipe1uEjTYl
cHarLbWsvqijmnZHMGpzNZAqs6miqKufdVA9I1EAAA==
```
Now, in your JavaScript code (requires ES6), you can dynamically generate an SVG from that data
```js
let svg = aequipedis.from_base64('...');
```
![Example image generated from the above Base64 data](demo/out/david-clode-363878.small.jpg_r8_m32.jpg)

You can also use the `demo.html` included in the `demo` folder to view the images.

## Gallery

The following table shows results produced by running `aequipedis` with varying parameters for `-r` and `-m`. The threshold `-t` was set to `0.05`.

| Original |  *r* = 32 |  *r* = 16 |  *r* = 8 |       |
| :------- |   ---: |   ---: |   ---: |  :--- |
| ![](demo/src/david-clode-363878.small.jpg) | ![6.4k (4.8k)](demo/out/david-clode-363878.small.jpg_r32_m1024.jpg) | ![1.9k (1.4k)](demo/out/david-clode-363878.small.jpg_r16_m256.jpg) | ![0.5k (0.4k)](demo/out/david-clode-363878.small.jpg_r8_m64.jpg) | all points 
|  | 6.4k (4.8k) | 1.9k (1.4k) | 0.5k (0.4k) |
| | ![3.0k (2.3k)](demo/out/david-clode-363878.small.jpg_r32_m256.jpg) | ![1.9k (1.4k)](demo/out/david-clode-363878.small.jpg_r16_m256.jpg) | ![0.6k (0.5k)](demo/out/david-clode-363878.small.jpg_r8_m256.jpg) | *n* ≤ 256
| | 3.0k (2.3k) | 1.9k (1.4k) | 0.6k (0.5k) |
| | ![1.4k (1.1k)](demo/out/david-clode-363878.small.jpg_r32_m128.jpg) | ![1.4k (1.1k)](demo/out/david-clode-363878.small.jpg_r16_m128.jpg) | ![0.5k (0.4k)](demo/out/david-clode-363878.small.jpg_r8_m128.jpg) | *n* ≤ 128
| | 1.4k (1.1k) | 1.4k (1.1k) | 0.5k (0.4k) |
| | ![0.7k (0.5k)](demo/out/david-clode-363878.small.jpg_r32_m64.jpg) | ![0.7k (0.5k)](demo/out/david-clode-363878.small.jpg_r16_m64.jpg) | ![0.5k (0.4k)](demo/out/david-clode-363878.small.jpg_r8_m64.jpg) | *n* ≤ 64
| | 0.7k (0.5k) | 0.7k (0.5k) | 0.5k (0.4k) |
| | ![0.3k (0.3k)](demo/out/david-clode-363878.small.jpg_r32_m32.jpg) | ![0.3k (0.3k)](demo/out/david-clode-363878.small.jpg_r16_m32.jpg) | ![0.3k (0.3k)](demo/out/david-clode-363878.small.jpg_r8_m32.jpg) | *n* ≤ 32
|  | 0.3k (0.3k) | 0.3k (0.3k) | 0.3k (0.3k) |
| ![](demo/src/guy-bowden-95257.small.jpg) | ![8.5k (6.2k)](demo/out/guy-bowden-95257.small.jpg_r32_m1024.jpg) | ![2.2k (1.7k)](demo/out/guy-bowden-95257.small.jpg_r16_m256.jpg) | ![0.5k (0.4k)](demo/out/guy-bowden-95257.small.jpg_r8_m64.jpg) | all points 
|  | 8.5k (6.2k) | 2.2k (1.7k) | 0.5k (0.4k) |
| | ![3.0k (2.3k)](demo/out/guy-bowden-95257.small.jpg_r32_m256.jpg) | ![2.2k (1.7k)](demo/out/guy-bowden-95257.small.jpg_r16_m256.jpg) | ![0.6k (0.5k)](demo/out/guy-bowden-95257.small.jpg_r8_m256.jpg) | *n* ≤ 256
| | 3.0k (2.3k) | 2.2k (1.7k) | 0.6k (0.5k) |
| | ![1.4k (1.1k)](demo/out/guy-bowden-95257.small.jpg_r32_m128.jpg) | ![1.4k (1.1k)](demo/out/guy-bowden-95257.small.jpg_r16_m128.jpg) | ![0.5k (0.4k)](demo/out/guy-bowden-95257.small.jpg_r8_m128.jpg) | *n* ≤ 128
| | 1.4k (1.1k) | 1.4k (1.1k) | 0.5k (0.4k) |
| | ![0.7k (0.5k)](demo/out/guy-bowden-95257.small.jpg_r32_m64.jpg) | ![0.7k (0.6k)](demo/out/guy-bowden-95257.small.jpg_r16_m64.jpg) | ![0.5k (0.4k)](demo/out/guy-bowden-95257.small.jpg_r8_m64.jpg) | *n* ≤ 64
| | 0.7k (0.5k) | 0.7k (0.6k) | 0.5k (0.4k) |
| | ![0.3k (0.3k)](demo/out/guy-bowden-95257.small.jpg_r32_m32.jpg) | ![0.3k (0.3k)](demo/out/guy-bowden-95257.small.jpg_r16_m32.jpg) | ![0.3k (0.3k)](demo/out/guy-bowden-95257.small.jpg_r8_m32.jpg) | *n* ≤ 32
|  | 0.3k (0.3k) | 0.3k (0.3k) | 0.3k (0.3k) |
| ![](demo/src/steinar-engeland-111914.small.jpg) | ![9.6k (7.1k)](demo/out/steinar-engeland-111914.small.jpg_r32_m1024.jpg) | ![2.3k (1.8k)](demo/out/steinar-engeland-111914.small.jpg_r16_m256.jpg) | ![0.5k (0.4k)](demo/out/steinar-engeland-111914.small.jpg_r8_m64.jpg) | all points 
|  | 9.6k (7.1k) | 2.3k (1.8k) | 0.5k (0.4k) |
| | ![3.0k (2.3k)](demo/out/steinar-engeland-111914.small.jpg_r32_m256.jpg) | ![2.3k (1.8k)](demo/out/steinar-engeland-111914.small.jpg_r16_m256.jpg) | ![0.6k (0.5k)](demo/out/steinar-engeland-111914.small.jpg_r8_m256.jpg) | *n* ≤ 256
| | 3.0k (2.3k) | 2.3k (1.8k) | 0.6k (0.5k) |
| | ![1.4k (1.1k)](demo/out/steinar-engeland-111914.small.jpg_r32_m128.jpg) | ![1.4k (1.1k)](demo/out/steinar-engeland-111914.small.jpg_r16_m128.jpg) | ![0.5k (0.4k)](demo/out/steinar-engeland-111914.small.jpg_r8_m128.jpg) | *n* ≤ 128
| | 1.4k (1.1k) | 1.4k (1.1k) | 0.5k (0.4k) |
| | ![0.7k (0.5k)](demo/out/steinar-engeland-111914.small.jpg_r32_m64.jpg) | ![0.7k (0.6k)](demo/out/steinar-engeland-111914.small.jpg_r16_m64.jpg) | ![0.5k (0.4k)](demo/out/steinar-engeland-111914.small.jpg_r8_m64.jpg) | *n* ≤ 64
| | 0.7k (0.5k) | 0.7k (0.6k) | 0.5k (0.4k) |
| | ![0.3k (0.3k)](demo/out/steinar-engeland-111914.small.jpg_r32_m32.jpg) | ![0.3k (0.3k)](demo/out/steinar-engeland-111914.small.jpg_r16_m32.jpg) | ![0.3k (0.3k)](demo/out/steinar-engeland-111914.small.jpg_r8_m32.jpg) | *n* ≤ 32
|  | 0.3k (0.3k) | 0.3k (0.3k) | 0.3k (0.3k) |
| ![](demo/src/tina-rolf-269345.small.jpg) | ![8.5k (6.3k)](demo/out/tina-rolf-269345.small.jpg_r32_m1024.jpg) | ![2.2k (1.7k)](demo/out/tina-rolf-269345.small.jpg_r16_m256.jpg) | ![0.5k (0.4k)](demo/out/tina-rolf-269345.small.jpg_r8_m64.jpg) | all points 
|  | 8.5k (6.3k) | 2.2k (1.7k) | 0.5k (0.4k) |
| | ![3.0k (2.3k)](demo/out/tina-rolf-269345.small.jpg_r32_m256.jpg) | ![2.2k (1.7k)](demo/out/tina-rolf-269345.small.jpg_r16_m256.jpg) | ![0.6k (0.5k)](demo/out/tina-rolf-269345.small.jpg_r8_m256.jpg) | *n* ≤ 256
| | 3.0k (2.3k) | 2.2k (1.7k) | 0.6k (0.5k) |
| | ![1.4k (1.1k)](demo/out/tina-rolf-269345.small.jpg_r32_m128.jpg) | ![1.4k (1.1k)](demo/out/tina-rolf-269345.small.jpg_r16_m128.jpg) | ![0.5k (0.4k)](demo/out/tina-rolf-269345.small.jpg_r8_m128.jpg) | *n* ≤ 128
| | 1.4k (1.1k) | 1.4k (1.1k) | 0.5k (0.4k) |
| | ![0.7k (0.5k)](demo/out/tina-rolf-269345.small.jpg_r32_m64.jpg) | ![0.7k (0.6k)](demo/out/tina-rolf-269345.small.jpg_r16_m64.jpg) | ![0.5k (0.4k)](demo/out/tina-rolf-269345.small.jpg_r8_m64.jpg) | *n* ≤ 64
| | 0.7k (0.5k) | 0.7k (0.6k) | 0.5k (0.4k) |
| | ![0.3k (0.3k)](demo/out/tina-rolf-269345.small.jpg_r32_m32.jpg) | ![0.3k (0.3k)](demo/out/tina-rolf-269345.small.jpg_r16_m32.jpg) | ![0.3k (0.3k)](demo/out/tina-rolf-269345.small.jpg_r8_m32.jpg) | *n* ≤ 32
|  | 0.3k (0.3k) | 0.3k (0.3k) | 0.3k (0.3k) |

All sizes in `kb` (thousand bytes) of the `base64` encoded output. Size in parentheses are the gzipped size. Images courtesy of [Unsplash](https://unsplash.com/) photographers David Clode, Guy Bowden, Steinar Engeland, and Tina Rolf.

## FAQ

**How does it work?**

The code extracts a set of feature points from the image using a Sobel edge detector. Feature points are filtered according to spatial constraints and the detected edge strength. The feature points are then converted into a Delaunay triangle mesh. This conversion uses the Bowyer-Watson algorithm. So, nothing fancy, all pretty basic stuff.

**Should I use this code in production?**

Probably not. While this code is extracted from another project of mine that actually has unit tests, I would consider it still quite experimental. There is plenty of pointer magic involved in reading the image data that is only correct if the computational geometry code does not screw up (which it will probably do). Also, this code has been optimised for small images and may be quite inefficient when used with large photos. So be warned.

**What's with the name?**

`AEQVIPEDIS` (or `aequipedis` if you anachronistically allow “u”s in your Latin inscriptions) roughly translates to “of an equal-footed triangle”. Before you ask, no, the triangles generated by this code are not isosceles triangles. But, hey, they *are* triangles after all. So, while the name doesn't make sense, is annoying to type, and hard to pronounce, there was no repository of this name on GitHub; and names with this property are increasingly hard to come by. So here you go!

## Licence

The C++ library and the JavaScript code are made available under two different licences. The C++ code is licensed under the more restrictive [AGPLv3 licence](https://www.gnu.org/licenses/agpl-3.0.en.html), whereas the JavaScript code is licensed under the permissive [MIT licence](https://opensource.org/licenses/MIT).

In a nutshell, this means that you cannot embed the C++ library into a server-side program that interfaces with a user (e.g. a SaaS providing image placeholders) without making your server program available under the GPLv3/AGPLv3 as well. Note that these restrictions do not apply if you just use the output of the provided program as part of a batch process that is not directly involved in user-interaction, e.g. as part of a static site generator that calls the executable.
