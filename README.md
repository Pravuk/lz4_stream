lz4_stream - A C++ stream using LZ4 (de)compression
===================================================

lz4_stream is a simple wrapper that uses C++ streams for compressing and decompressing data using the [LZ4 compression library]

Usage
-----

Look at lz4\_compress.cpp and lz4\_decompress.cpp for example command line programs that can compress and decompress using this stream library.

Building
--------

```
mkdir build
cd build
cmake ..
make
```

License
-------

Standard BSD 3-Clause License as used by the LZ4 library.
[laudrup/lz4_stream](https://github.com/laudrup/lz4_stream)
[LZ4 compression library]: https://github.com/lz4/lz4
[cmake]: http://cmake.org
[Google Test Framework]: https://github.com/google/googletest
