# Benchmarks of Go serialization methods

This is a test suite for benchmarking various Go serialization methods.

## Tested serialization methods

- [encoding/gob](http://golang.org/pkg/encoding/gob/)
- [encoding/json](http://golang.org/pkg/encoding/json/)
- [github.com/json-iterator/go](https://github.com/json-iterator/go)
- [github.com/ugorji/go/codec](https://github.com/ugorji/go/tree/master/codec)
- [gopkg.in/vmihailenco/msgpack.v2](https://github.com/vmihailenco/msgpack)
- [labix.org/v2/mgo/bson](https://labix.org/v2/mgo/bson)
- [github.com/tinylib/msgp](https://github.com/tinylib/msgp) *(code generator for msgpack)*
- [github.com/golang/protobuf](https://github.com/golang/protobuf) (generated code)
- [github.com/gogo/protobuf](https://github.com/gogo/protobuf) (generated code, optimized version of `goprotobuf`)
- [github.com/DeDiS/protobuf](https://github.com/DeDiS/protobuf) (reflection based)
- [github.com/google/flatbuffers](https://github.com/google/flatbuffers)
- [github.com/andyleap/gencode](https://github.com/andyleap/gencode)
- [github.com/pascaldekloe/colfer](https://github.com/pascaldekloe/colfer)
- [github.com/ikkerens/ikeapack](https://github.com/ikkerens/ikeapack)
- [github.com/niubaoshu/gotiny](https://github.com/niubaoshu/gotiny)

## Excluded Serializers

Given existed [benchmark](https://github.com/alecthomas/go_serialization_benchmarks) by alecthomas，the below serializers are excluded from this test because of their poor performance.

- [github.com/Sereal/Sereal/Go/sereal](https://github.com/Sereal/Sereal)
- [github.com/davecgh/go-xdr/xdr](https://github.com/davecgh/go-xdr)
- [zombiezen.com/go/capnproto2](https://godoc.org/zombiezen.com/go/capnproto2)
- [github.com/alecthomas/binary](https://github.com/alecthomas/binary)
- [github.com/glycerine/go-capnproto](https://github.com/glycerine/go-capnproto)
- [github.com/hprose/hprose-go/io](https://github.com/hprose/hprose-go)
- [github.com/linkedin/goavro](https://github.com/linkedin/goavro)

## Running the benchmarks

```bash
go get -u -t
go test -bench='.*' ./
```

Shameless plug: I use [pawk](https://github.com/alecthomas/pawk) to format the table:

```bash
go test -bench='.*' ./ | pawk -F'\t' '"%-40s %10s %10s %s %s" % f'
```

## Recommendation

If performance, correctness and interoperability are the most
important factors, [gogoprotobuf](https://gogo.github.io/) is
currently the best choice. It does require a pre-processing step (eg.
via Go 1.4's "go generate" command).

But as always, make your own choice based on your requirements.

## Data

The data being serialized is the following structure with randomly generated values:

```go
type A struct {
    Name     string
    BirthDay time.Time
    Phone    string
    Siblings int
    Spouse   bool
    Money    float64
}
```


## Results

2019-02-27 Results with Go 1.11 on a Thinkpad T410:

```
benchmark                                      iter    time/iter   bytes/op     allocs/op  tt.time   tt.bytes
---------                                      ----    ---------   --------     ---------  -------   --------
BenchmarkGotinyMarshal-4                   10000000    184 ns/op     0 B/op   0 allocs/op   1.84 s       0 KB
BenchmarkGotinyUnmarshal-4                  3000000    497 ns/op   112 B/op   3 allocs/op   1.49 s   33600 KB
BenchmarkGotinyNoTimeMarshal-4             10000000    183 ns/op     0 B/op   0 allocs/op   1.83 s       0 KB
BenchmarkGotinyNoTimeUnmarshal-4            3000000    465 ns/op    96 B/op   3 allocs/op   1.40 s   28800 KB
BenchmarkMsgpMarshal-4                      5000000    363 ns/op   128 B/op   1 allocs/op   1.81 s   64000 KB
BenchmarkMsgpUnmarshal-4                    2000000    591 ns/op   112 B/op   3 allocs/op   1.18 s   22400 KB
BenchmarkVmihailencoMsgpackMarshal-4         500000   3251 ns/op   368 B/op   6 allocs/op   1.63 s   18400 KB
BenchmarkVmihailencoMsgpackUnmarshal-4       500000   3768 ns/op   384 B/op  13 allocs/op   1.88 s   19200 KB
BenchmarkJsonMarshal-4                       500000   3606 ns/op   304 B/op   4 allocs/op   1.80 s   15200 KB
BenchmarkJsonUnmarshal-4                     200000   7351 ns/op   359 B/op   7 allocs/op   1.47 s    7180 KB
BenchmarkJsonIterMarshal-4                   500000   3455 ns/op   312 B/op   5 allocs/op   1.73 s   15600 KB
BenchmarkJsonIterUnmarshal-4                 500000   3142 ns/op   248 B/op   7 allocs/op   1.57 s   12400 KB
BenchmarkEasyJsonMarshal-4                   500000   3129 ns/op   784 B/op   5 allocs/op   1.56 s   39200 KB
BenchmarkEasyJsonUnmarshal-4                1000000   2349 ns/op   160 B/op   4 allocs/op   2.35 s   16000 KB
BenchmarkBsonMarshal-4                       500000   2264 ns/op   392 B/op  10 allocs/op   1.13 s   19600 KB
BenchmarkBsonUnmarshal-4                     500000   3081 ns/op   244 B/op  19 allocs/op   1.54 s   12200 KB
BenchmarkGobMarshal-4                       1000000   1383 ns/op    48 B/op   2 allocs/op   1.38 s    4800 KB
BenchmarkGobUnmarshal-4                     1000000   1482 ns/op   112 B/op   3 allocs/op   1.48 s   11200 KB
BenchmarkUgorjiCodecMsgpackMarshal-4         500000   3097 ns/op  1280 B/op   4 allocs/op   1.55 s   64000 KB
BenchmarkUgorjiCodecMsgpackUnmarshal-4      1000000   2685 ns/op   464 B/op   5 allocs/op   2.69 s   46400 KB
BenchmarkUgorjiCodecBincMarshal-4            500000   3314 ns/op  1328 B/op   5 allocs/op   1.66 s   66400 KB
BenchmarkUgorjiCodecBincUnmarshal-4          500000   3419 ns/op   704 B/op   8 allocs/op   1.71 s   35200 KB
BenchmarkFlatBuffersMarshal-4               3000000    521 ns/op     0 B/op   0 allocs/op   1.56 s       0 KB
BenchmarkFlatBuffersUnmarshal-4             3000000    508 ns/op   112 B/op   3 allocs/op   1.52 s   33600 KB
BenchmarkProtobufMarshal-4                  1000000   1494 ns/op   200 B/op   7 allocs/op   1.49 s   20000 KB
BenchmarkProtobufUnmarshal-4                1000000   1377 ns/op   192 B/op  10 allocs/op   1.38 s   19200 KB
BenchmarkGoprotobufMarshal-4                2000000    699 ns/op    96 B/op   2 allocs/op   1.40 s   19200 KB
BenchmarkGoprotobufUnmarshal-4              1000000   1173 ns/op   200 B/op  10 allocs/op   1.17 s   20000 KB
BenchmarkGogoprotobufMarshal-4              5000000    314 ns/op    64 B/op   1 allocs/op   1.57 s   32000 KB
BenchmarkGogoprotobufUnmarshal-4            3000000    448 ns/op    96 B/op   3 allocs/op   1.34 s   28800 KB
BenchmarkColferMarshal-4                    5000000    259 ns/op    64 B/op   1 allocs/op   1.29 s   32000 KB
BenchmarkColferUnmarshal-4                  3000000    405 ns/op   112 B/op   3 allocs/op   1.22 s   33600 KB
BenchmarkGencodeMarshal-4                   5000000    337 ns/op    80 B/op   2 allocs/op   1.69 s   40000 KB
BenchmarkGencodeUnmarshal-4                 3000000    398 ns/op   112 B/op   3 allocs/op   1.19 s   33600 KB
BenchmarkGencodeUnsafeMarshal-4            10000000    201 ns/op    48 B/op   1 allocs/op   2.01 s   48000 KB
BenchmarkGencodeUnsafeUnmarshal-4           5000000    344 ns/op    96 B/op   3 allocs/op   1.72 s   48000 KB
BenchmarkXDR2Marshal-4                      5000000    331 ns/op    64 B/op   1 allocs/op   1.66 s   32000 KB
BenchmarkXDR2Unmarshal-4                   10000000    246 ns/op    32 B/op   2 allocs/op   2.46 s   32000 KB
BenchmarkIkeaMarshal-4                      1000000   1066 ns/op    72 B/op   8 allocs/op   1.07 s    7200 KB
BenchmarkIkeaUnmarshal-4                    1000000   1464 ns/op   160 B/op  11 allocs/op   1.46 s   16000 KB
BenchmarkShamatonMapMsgpackMarshal-4        1000000   1338 ns/op   208 B/op   4 allocs/op   1.34 s   20800 KB
BenchmarkShamatonMapMsgpackUnmarshal-4      1000000   1188 ns/op   144 B/op   3 allocs/op   1.19 s   14400 KB
BenchmarkShamatonArrayMsgpackMarshal-4      1000000   1185 ns/op   176 B/op   4 allocs/op   1.19 s   17600 KB
BenchmarkShamatonArrayMsgpackUnmarshal-4    2000000    873 ns/op   144 B/op   3 allocs/op   1.75 s   28800 KB
---
totals:
BenchmarkGencodeUnsafe-4                   15000000    545 ns/op   144 B/op   4 allocs/op   8.18 s  216000 KB  136.25 ns/alloc
BenchmarkXDR2-4                            15000000    577 ns/op    96 B/op   3 allocs/op   8.65 s  144000 KB  192.33 ns/alloc
BenchmarkGotinyNoTime-4                    13000000    648 ns/op    96 B/op   3 allocs/op   8.42 s  124800 KB  216.00 ns/alloc
BenchmarkColfer-4                           8000000    664 ns/op   176 B/op   4 allocs/op   5.31 s  140800 KB  166.00 ns/alloc
BenchmarkGotiny-4                          13000000    681 ns/op   112 B/op   3 allocs/op   8.85 s  145600 KB  227.00 ns/alloc
BenchmarkGencode-4                          8000000    735 ns/op   192 B/op   5 allocs/op   5.88 s  153600 KB  147.00 ns/alloc
BenchmarkGogoprotobuf-4                     8000000    762 ns/op   160 B/op   4 allocs/op   6.10 s  128000 KB  190.50 ns/alloc
BenchmarkMsgp-4                             7000000    954 ns/op   240 B/op   4 allocs/op   6.68 s  168000 KB  238.50 ns/alloc
BenchmarkFlatBuffers-4                      6000000   1029 ns/op   112 B/op   3 allocs/op   6.17 s   67200 KB  343.00 ns/alloc
BenchmarkGoprotobuf-4                       3000000   1872 ns/op   296 B/op  12 allocs/op   5.62 s   88800 KB  156.00 ns/alloc
BenchmarkShamatonArrayMsgpack-4             3000000   2058 ns/op   320 B/op   7 allocs/op   6.17 s   96000 KB  294.00 ns/alloc
BenchmarkShamatonMapMsgpack-4               2000000   2526 ns/op   352 B/op   7 allocs/op   5.05 s   70400 KB  360.86 ns/alloc
BenchmarkIkea-4                             2000000   2530 ns/op   232 B/op  19 allocs/op   5.06 s   46400 KB  133.16 ns/alloc
BenchmarkGob-4                              2000000   2865 ns/op   160 B/op   5 allocs/op   5.73 s   32000 KB  573.00 ns/alloc
BenchmarkProtobuf-4                         2000000   2871 ns/op   392 B/op  17 allocs/op   5.74 s   78400 KB  168.88 ns/alloc
BenchmarkBson-4                             1000000   5345 ns/op   636 B/op  29 allocs/op   5.34 s   63600 KB  184.31 ns/alloc
BenchmarkEasyJson-4                         1500000   5478 ns/op   944 B/op   9 allocs/op   8.22 s  141600 KB  608.67 ns/alloc
BenchmarkUgorjiCodecMsgpack-4               1500000   5782 ns/op  1744 B/op   9 allocs/op   8.67 s  261600 KB  642.44 ns/alloc
BenchmarkJsonIter-4                         1000000   6597 ns/op   560 B/op  12 allocs/op   6.60 s   56000 KB  549.75 ns/alloc
BenchmarkUgorjiCodecBinc-4                  1000000   6733 ns/op  2032 B/op  13 allocs/op   6.73 s  203200 KB  517.92 ns/alloc
BenchmarkVmihailencoMsgpack-4               1000000   7019 ns/op   752 B/op  19 allocs/op   7.02 s   75200 KB  369.42 ns/alloc
BenchmarkJson-4                              700000  10957 ns/op   663 B/op  11 allocs/op   7.67 s   46410 KB  996.09 ns/alloc
```
