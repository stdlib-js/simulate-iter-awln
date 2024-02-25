<!--

@license Apache-2.0

Copyright (c) 2019 The Stdlib Authors.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

   http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.

-->


<details>
  <summary>
    About stdlib...
  </summary>
  <p>We believe in a future in which the web is a preferred environment for numerical computation. To help realize this future, we've built stdlib. stdlib is a standard library, with an emphasis on numerical and scientific computation, written in JavaScript (and C) for execution in browsers and in Node.js.</p>
  <p>The library is fully decomposable, being architected in such a way that you can swap out and mix and match APIs and functionality to cater to your exact preferences and use cases.</p>
  <p>When you use stdlib, you can be absolutely certain that you are using the most thorough, rigorous, well-written, studied, documented, tested, measured, and high-quality code out there.</p>
  <p>To join us in bringing numerical computing to the web, get started by checking us out on <a href="https://github.com/stdlib-js/stdlib">GitHub</a>, and please consider <a href="https://opencollective.com/stdlib">financially supporting stdlib</a>. We greatly appreciate your continued support!</p>
</details>

# iterawln

[![NPM version][npm-image]][npm-url] [![Build Status][test-image]][test-url] [![Coverage Status][coverage-image]][coverage-url] <!-- [![dependencies][dependencies-image]][dependencies-url] -->

> Create an [iterator][mdn-iterator-protocol] which introduces additive white Laplacian noise (AWLN).

<section class="intro">

</section>

<!-- /.intro -->

<!-- Package usage documentation. -->



<section class="usage">

## Usage

```javascript
import iterawln from 'https://cdn.jsdelivr.net/gh/stdlib-js/simulate-iter-awln@esm/index.mjs';
```

#### iterawln( iterator, sigma\[, options] )

Returns an [iterator][mdn-iterator-protocol] which introduces additive white Laplacian (a.k.a. biexponential or double-exponential) noise (AWLN) with standard deviation `sigma`.

```javascript
import array2iterator from 'https://cdn.jsdelivr.net/gh/stdlib-js/array-to-iterator@esm/index.mjs';

var arr = array2iterator( [ 1.0, 1.0, 1.0, 1.0, 1.0 ] );
var it = iterawln( arr, 0.2 );

var v = it.next().value;
// returns <number>

v = it.next().value;
// returns <number>

// ...
```

The returned iterator protocol-compliant object has the following properties:

-   **next**: function which returns an iterator protocol-compliant object containing the next iterated value (if one exists) assigned to a `value` property and a `done` property having a `boolean` value indicating whether the iterator is finished.
-   **return**: function which closes an iterator and returns a single (optional) argument in an iterator protocol-compliant object.
-   **PRNG**: underlying pseudorandom number generator (PRNG).
-   **seed**: pseudorandom number generator seed. If provided a PRNG, the property value is `null`.
-   **seedLength**: length of generator seed. If provided a PRNG, the property value is `null`.
-   **state**: pseudorandom number generator state. If provided a PRNG, the property value is `null`.
-   **stateLength**: length of generator state. If provided a PRNG, the property value is `null`.
-   **byteLength**: size (in bytes) of generator state. If provided a PRNG, the property value is `null`.

The function accepts the following `options`:

-   **prng**: pseudorandom number generator for generating uniformly distributed pseudorandom numbers on the interval `[0,1)`. If provided, the function **ignores** both the `state` and `seed` options. In order to seed the returned iterator, one must seed the provided `prng` (assuming the provided `prng` is seedable).
-   **seed**: pseudorandom number generator seed.
-   **state**: a [`Uint32Array`][@stdlib/array/uint32] containing pseudorandom number generator state. If provided, the function ignores the `seed` option.
-   **copy**: `boolean` indicating whether to copy a provided pseudorandom number generator state. Setting this option to `false` allows sharing state between two or more pseudorandom number generators. Setting this option to `true` ensures that a returned iterator has exclusive control over its internal state. Default: `true`.

By default, an iterator uses the [MT19937][@stdlib/random/base/mt19937] algorithm to generate uniformly distributed pseudorandom numbers on the interval `[0,1)`. To use a different PRNG, set the `prng` option.

```javascript
import array2iterator from 'https://cdn.jsdelivr.net/gh/stdlib-js/array-to-iterator@esm/index.mjs';
import minstd from 'https://cdn.jsdelivr.net/gh/stdlib-js/random-base-minstd-shuffle@esm/index.mjs';

var arr = array2iterator( [ 1.0, 1.0, 1.0, 1.0, 1.0 ] );
var it = iterawln( arr, 0.2, {
    'prng': minstd.normalized
});

var v = it.next().value;
// returns <number>
```

To seed an iterator, set the `seed` option.

```javascript
import array2iterator from 'https://cdn.jsdelivr.net/gh/stdlib-js/array-to-iterator@esm/index.mjs';

var arr = array2iterator( [ 1.0, 1.0, 1.0, 1.0, 1.0 ] );
var it1 = iterawln( arr, 0.2, {
    'seed': 12345
});

var v1 = it1.next().value;
// returns <number>

var it2 = iterawln( arr, 0.2, {
    'seed': 12345
});

var v2 = it2.next().value;
// returns <number>

var bool = ( v1 === v2 );
// returns true
```

To return an iterator having a specific initial state, set the iterator `state` option.

```javascript
import array2iterator from 'https://cdn.jsdelivr.net/gh/stdlib-js/array-to-iterator@esm/index.mjs';
var arr = array2iterator( [ 1.0, 1.0, 1.0, 1.0, 1.0 ] );

var it1 = iterawln( arr, 0.2 );

// Progress the underlying generator state:
var i;
for ( i = 0; i < 3; i++ ) {
    it1.next();
}

// Create a new iterator initialized to the current state of `it1`:
var it2 = iterawln( arr, 0.2, {
    'state': it1.state
});

// Test that the iterator values are the same:
var bool = ( it1.next().value === it2.next().value );
// returns true
```

</section>

<!-- /.usage -->

<!-- Package usage notes. Make sure to keep an empty line after the `section` element and another before the `/section` close. -->

<section class="notes">

## Notes

-   If PRNG state is "shared" (meaning a state array was provided during iterator creation and **not** copied) and one sets the iterator state to a state array having a different length, the iterator does **not** update the existing shared state and, instead, points to the newly provided state array. In order to synchronize generated output according to the new shared state array, the state array for **each** relevant PRNG/iterator must be **explicitly** set.
-   If PRNG state is "shared" and one sets the iterator state to a state array of the same length, the PRNG state is updated (along with the state of all other PRNGs/iterators sharing the state array).
-   If an iterated value is non-numeric (including `NaN`), the returned [`iterator`][mdn-iterator-protocol] returns `NaN`. If non-numeric iterated values are possible, you are advised to provide an [`iterator`][mdn-iterator-protocol] which type checks and handles non-numeric values accordingly.

</section>

<!-- /.notes -->

<!-- Package usage examples. -->

<section class="examples">

## Examples

<!-- eslint no-undef: "error" -->

```html
<!DOCTYPE html>
<html lang="en">
<body>
<script type="module">

import iterSineWave from 'https://cdn.jsdelivr.net/gh/stdlib-js/simulate-iter-sine-wave@esm/index.mjs';
import iterawln from 'https://cdn.jsdelivr.net/gh/stdlib-js/simulate-iter-awln@esm/index.mjs';

// Create an iterator for generating a sine wave:
var sine = iterSineWave({
    'iter': 100,
    'amplitude': 10.0
});

// Create an iterator for introducing additive white Laplacian noise:
var it = iterawln( sine, 3.0 );

// Perform manual iteration...
var v;
while ( true ) {
    v = it.next();
    if ( v.done ) {
        break;
    }
    console.log( v.value );
}

</script>
</body>
</html>
```

</section>

<!-- /.examples -->

<!-- Section to include cited references. If references are included, add a horizontal rule *before* the section. Make sure to keep an empty line after the `section` element and another before the `/section` close. -->

<section class="references">

</section>

<!-- /.references -->

<!-- Section for related `stdlib` packages. Do not manually edit this section, as it is automatically populated. -->

<section class="related">

* * *

## See Also

-   <span class="package-name">[`@stdlib/simulate-iter/awgn`][@stdlib/simulate/iter/awgn]</span><span class="delimiter">: </span><span class="description">create an iterator which introduces additive white Gaussian noise.</span>
-   <span class="package-name">[`@stdlib/simulate-iter/awun`][@stdlib/simulate/iter/awun]</span><span class="delimiter">: </span><span class="description">create an iterator which introduces additive white uniform noise.</span>

</section>

<!-- /.related -->

<!-- Section for all links. Make sure to keep an empty line after the `section` element and another before the `/section` close. -->


<section class="main-repo" >

* * *

## Notice

This package is part of [stdlib][stdlib], a standard library with an emphasis on numerical and scientific computing. The library provides a collection of robust, high performance libraries for mathematics, statistics, streams, utilities, and more.

For more information on the project, filing bug reports and feature requests, and guidance on how to develop [stdlib][stdlib], see the main project [repository][stdlib].

#### Community

[![Chat][chat-image]][chat-url]

---

## License

See [LICENSE][stdlib-license].


## Copyright

Copyright &copy; 2016-2024. The Stdlib [Authors][stdlib-authors].

</section>

<!-- /.stdlib -->

<!-- Section for all links. Make sure to keep an empty line after the `section` element and another before the `/section` close. -->

<section class="links">

[npm-image]: http://img.shields.io/npm/v/@stdlib/simulate-iter-awln.svg
[npm-url]: https://npmjs.org/package/@stdlib/simulate-iter-awln

[test-image]: https://github.com/stdlib-js/simulate-iter-awln/actions/workflows/test.yml/badge.svg?branch=v0.2.1
[test-url]: https://github.com/stdlib-js/simulate-iter-awln/actions/workflows/test.yml?query=branch:v0.2.1

[coverage-image]: https://img.shields.io/codecov/c/github/stdlib-js/simulate-iter-awln/main.svg
[coverage-url]: https://codecov.io/github/stdlib-js/simulate-iter-awln?branch=main

<!--

[dependencies-image]: https://img.shields.io/david/stdlib-js/simulate-iter-awln.svg
[dependencies-url]: https://david-dm.org/stdlib-js/simulate-iter-awln/main

-->

[chat-image]: https://img.shields.io/gitter/room/stdlib-js/stdlib.svg
[chat-url]: https://app.gitter.im/#/room/#stdlib-js_stdlib:gitter.im

[stdlib]: https://github.com/stdlib-js/stdlib

[stdlib-authors]: https://github.com/stdlib-js/stdlib/graphs/contributors

[umd]: https://github.com/umdjs/umd
[es-module]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules

[deno-url]: https://github.com/stdlib-js/simulate-iter-awln/tree/deno
[deno-readme]: https://github.com/stdlib-js/simulate-iter-awln/blob/deno/README.md
[umd-url]: https://github.com/stdlib-js/simulate-iter-awln/tree/umd
[umd-readme]: https://github.com/stdlib-js/simulate-iter-awln/blob/umd/README.md
[esm-url]: https://github.com/stdlib-js/simulate-iter-awln/tree/esm
[esm-readme]: https://github.com/stdlib-js/simulate-iter-awln/blob/esm/README.md
[branches-url]: https://github.com/stdlib-js/simulate-iter-awln/blob/main/branches.md

[stdlib-license]: https://raw.githubusercontent.com/stdlib-js/simulate-iter-awln/main/LICENSE

[mdn-iterator-protocol]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols#The_iterator_protocol

[@stdlib/array/uint32]: https://github.com/stdlib-js/array-uint32/tree/esm

[@stdlib/random/base/mt19937]: https://github.com/stdlib-js/random-base-mt19937/tree/esm

<!-- <related-links> -->

[@stdlib/simulate/iter/awgn]: https://github.com/stdlib-js/simulate-iter-awgn/tree/esm

[@stdlib/simulate/iter/awun]: https://github.com/stdlib-js/simulate-iter-awun/tree/esm

<!-- </related-links> -->

</section>

<!-- /.links -->
