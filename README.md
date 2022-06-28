# vSwarm-proto

vSwarm-proto is a utility for [vSwarm](https://github.com/ease-lab/vSwarm/) containing all the proto files and client applications necessary to run benchmarks using [relay](https://github.com/ease-lab/vSwarm/tree/main/tools/relay).

## Adding clients

After you have added your benchmark in [vSwarm](https://github.com/ease-lab/vSwarm/) and the necessary docker compose and knative yaml files with [the relay snippet](https://github.com/ease-lab/vSwarm/blob/main/tools/relay/README.md), the next step is to add a client to generate the necessary input for your benchmark.

A gRPC client would consist of a `GeneratorBase` structure (and a `Generator` interface for accessing its data fields) and `ClientBase` structure with connection information and `GrpcClient` interface with methods to establish connection, send requests, and close the connection.

The initial connection will be established similarly for most benchmarks, with a connection happening at the required ip address and port.

As mentioned in the relay README, the actual invokation to the benchmark happens in the `Request` method, which has to be defined for each benchmark and will change based on what gRPC methods the benchmark provides to be called. 

> For instance in `fibonacci`, this is simply the `SayHello` function that expects a context and `HelloRequest` object with the `Name` field as the number. The `Request()` method would then have to call this function with appropriate parameters, and return the response message from the benchmark as a string.

There is a function `Next()` which was introduced to be used in conjunction with different generator options such as Unique, Linear, Random. The relay will retrieve the request message value from the `Next()` function, so its important to implement this. 

> In `fibonacci` if you wanted a linearly varying stream of inputs, you would set the packet value as the next value, here implemented in `Increment()` as increasing by 1 until `upperBound` hits.