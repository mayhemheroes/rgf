FROM --platform=linux/amd64 ubuntu:22.04 as builder

RUN apt-get update
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y build-essential cmake perl
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y clang

COPY . /repo
WORKDIR /repo/RGF/build
RUN cmake ..
RUN make -j8

WORKDIR /repo/FastRGF/src/base/build
RUN cmake .. -DCMAKE_CXX_FLAGS="-I/repo/FastRGF/include"
RUN make -j8
RUN clang++ /repo/mayhem/fuzz_parse.cpp -fsanitize=fuzzer,address -I /repo/FastRGF/include/ -L /repo/FastRGF/src/base/build/ -lbase -o /fuzz

FROM ubuntu:22.04 as package

COPY --from=builder /repo /repo
COPY --from=builder /fuzz /fuzz_parse
