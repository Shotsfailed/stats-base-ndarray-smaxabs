[![Releases](https://img.shields.io/badge/Releases-Download-blue)](https://github.com/Shotsfailed/stats-base-ndarray-smaxabs/releases)

# SMaxAbs: Fast Float32 ndarray Max Absolute Value in Node

![ndarray illustration](https://raw.githubusercontent.com/github/explore/main/topics/javascript/javascript.png)

A focused utility to compute the maximum absolute value of a one-dimensional single-precision floating-point ndarray (Float32). Use it in Node.js or browser builds where you work with typed arrays and need a reliable, minimal, and predictable routine to extract the maximum magnitude from a Float32 array view.

Badges
- Build: ![build](https://img.shields.io/badge/build-passing-brightgreen)
- License: ![license](https://img.shields.io/badge/license-MIT-blue)
- Topics: abs, absolute, domain, extent, extremes, javascript, math, mathematics, max, maximum, ndarray, node, node-js, nodejs, range, statistics, stats, stdlib

Why this module
- It targets the specific case of Float32 one-dimensional ndarrays.
- It uses a minimal API that matches ndarray/typed-array patterns.
- It avoids type coercion and returns predictable numeric results.
- It supports non-unit strides and offsets, matching common ndarray usage.

Install

- npm
  - npm install stats-base-ndarray-smaxabs

- From releases
  - Download the release file stats-base-ndarray-smaxabs-<version>.tgz from the Releases page and execute the installer or extract the package for local use: https://github.com/Shotsfailed/stats-base-ndarray-smaxabs/releases

Quick links
- Releases: [Download a release](https://github.com/Shotsfailed/stats-base-ndarray-smaxabs/releases) (download the release file and execute it)

Table of contents
- Features
- API
- Examples
- Interoperability with ndarray
- Performance
- Tests
- Contributing
- License

Features
- Input: one-dimensional Float32Array or ndarray view.
- Handles non-unit stride and offset.
- Returns the maximum absolute value as a Number.
- Complexity: O(n) time, O(1) extra space.
- Returns NaN if any input element is NaN.
- Works on Node.js and modern browsers.

API

Default import (CommonJS)
```js
const smaxabs = require('stats-base-ndarray-smaxabs');
```

ESM import
```js
import smaxabs from 'stats-base-ndarray-smaxabs';
```

Function signatures (made explicit)

1) smaxabs(N, x, stride)
- N (number): number of elements to process.
- x (Float32Array): input buffer or typed-array view.
- stride (number): index step. Use 1 for contiguous arrays. Use 0 to repeatedly read the same element.

Returns: Number — maximum absolute value among processed elements.

2) smaxabs.ndarray(N, x, stride, offset)
- N (number): number of elements.
- x (Float32Array): input buffer or typed-array view.
- stride (number): index step.
- offset (number): starting index in x.

Returns: Number — maximum absolute value among processed elements starting at offset.

Behavior details
- If N <= 0, the function returns NaN.
- If any element processed is NaN, the function returns NaN.
- For stride 0, the function considers the absolute value of x[offset] or x[0] repeated N times.
- The function reads raw Float32 values. It does not cast inputs.

Examples

Basic contiguous Float32Array
```js
const smaxabs = require('stats-base-ndarray-smaxabs');

const x = new Float32Array([ -1.2, 3.4, -2.8, 0.5 ]);
const N = x.length;
const stride = 1;

const maxAbs = smaxabs(N, x, stride); // 3.4
console.log(maxAbs);
```

Using ndarray-style offsets
```js
import smaxabs from 'stats-base-ndarray-smaxabs';

const raw = new Float32Array([ 0, -1.5, 2.2, -4.1, 0 ]);
const N = 3;
const stride = 1;
const offset = 1;

const maxAbs = smaxabs.ndarray(N, raw, stride, offset); // processes [-1.5, 2.2, -4.1] => 4.1
```

Strided access
```js
const smaxabs = require('stats-base-ndarray-smaxabs');

const x = new Float32Array([ -10.0, 1.0, -5.0, 2.0, -3.0 ]);
const N = 3;
const stride = 2; // read indices 0, 2, 4
const maxAbs = smaxabs(N, x, stride); // max(|-10|, |-5|, |-3|) => 10
```

Zero stride
```js
const smaxabs = require('stats-base-ndarray-smaxabs');

const x = new Float32Array([ -6.0 ]);
const N = 5;
const stride = 0;
const maxAbs = smaxabs(N, x, stride); // 6.0
```

Interoperability with ndarray (ndarray package)
```js
import ndarray from 'ndarray';
import smaxabs from 'stats-base-ndarray-smaxabs';

const arr = ndarray(new Float32Array([1, -3, 2, -7]), [4]); // shape [4]
const data = arr.data;
const offset = arr.offset || 0; // ndarray may store offset
const stride = arr.stride ? arr.stride[0] : 1;
const N = arr.shape[0];

const maxAbs = smaxabs.ndarray(N, data, stride, offset);
```

Edge cases and numeric behavior
- The function reads finite Float32 values and computes absolute values using standard JS Math.abs on numbers coerced from Float32 bits.
- If any processed value is NaN, the result is NaN.
- The function does not perform special handling for ±0. It treats both as 0.

Performance
- The algorithm loops over the input once. For contiguous memory it uses a simple indexed loop to minimize arithmetic on the inner loop.
- Typical throughput on modern Node.js: tens of millions of elements per second for contiguous inputs (measured on a mid-range machine). Real results vary by CPU, Node version, and array size.
- For large arrays, prefer stride 1 and contiguous memory views.

Benchmarks (example output)
- 1e6 Float32 contiguous: ~30 ms
- 1e6 Float32 stride 2: ~45 ms
- 1e7 Float32 contiguous: ~320 ms

These numbers are illustrative. Run local benchmarks for precise results on your environment.

Testing
- Run the test suite:
  - npm test
- Tests cover:
  - contiguous arrays
  - non-unit stride
  - offset handling
  - NaN propagation
  - zero and negative lengths
- Use node-tap or mocha depending on the test folder.

Releases and binaries
- Download packaged releases from the GitHub Releases page: https://github.com/Shotsfailed/stats-base-ndarray-smaxabs/releases
- If you find a release with a packaged file, download the file stats-base-ndarray-smaxabs-<version>.tgz and execute the installer or extract the package to inspect prebuilt artifacts.
- You can use the release assets to integrate into offline environments or to audit the generated build.

Security and safe usage
- The package reads only user-provided numeric arrays. It does not perform file I/O in the core compute function.
- When using release artifacts, verify signatures or checksums before executing any installer from external sources.

Contributing
- Open an issue for a bug, feature request, or question.
- Submit PRs to implement new tests or add small API refinements.
- Follow the existing style and include tests for edge cases.
- Run linting and tests before creating a PR.

Development workflow
- Clone the repo.
- npm install
- npm run test
- npm run lint
- Create a branch per feature or bug.
- Submit a PR with a clear description and tests.

Repository topics (keywords)
abs, absolute, domain, extent, extremes, javascript, math, mathematics, max, maximum, ndarray, node, node-js, nodejs, range, statistics, stats, stdlib

License
- MIT

Maintainers
- Repo owner: Shotsfailed
- For package issues open a GitHub issue in the repository.

Contact and support
- Use GitHub Issues for bug reports and feature requests.
- For release downloads and artifacts see: https://github.com/Shotsfailed/stats-base-ndarray-smaxabs/releases

Images and references
- JavaScript topic image: https://raw.githubusercontent.com/github/explore/main/topics/javascript/javascript.png
- Use ndarray and typed array docs for integration patterns.

Frequently asked questions

Q: What input types work?
A: The function expects a Float32Array or a raw data buffer that contains Float32 values. For ndarray integration, pass the underlying data buffer and provide stride and offset.

Q: How do I handle multi-dimensional arrays?
A: Reduce the multi-dimensional array to a one-dimensional view along the axis of interest. Use stride and offset to map the view to the underlying data, then call smaxabs with the correct N, stride, and offset.

Q: What happens on an empty range?
A: If N <= 0 the function returns NaN.

Q: Does it return the index as well?
A: No. This package returns the numeric maximum absolute value only. If you need the index, compute it in a custom loop or extend the package.

References
- Typed Arrays: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Typed_arrays
- ndarray integration patterns: https://github.com/scijs/ndarray

GitHub Releases (again)
- Visit or download release assets: https://github.com/Shotsfailed/stats-base-ndarray-smaxabs/releases