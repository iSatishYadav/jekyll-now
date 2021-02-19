---
layout: post
title: Creating a gRPC Microservice and Client in .NET Core
---

Hi, in this tutorial we're going to create a`gRPC` Service and Client using `.NET Core`. I'm assuming you've already heard about `gRPC` and want to see some code. In case you haven't, here's an excerpt from [Microsoft Docs](https://docs.microsoft.com/en-us/dotnet/architecture/cloud-native/grpc#what-is-grpc):
## What is `gRPC`? 
> gRPC is a modern, high-performance framework that evolves the age-old remote procedure call (RPC) protocol. At the application level, gRPC streamlines messaging between clients and back-end services. Originating from Google, gRPC is open source and part of the Cloud Native Computing Foundation (CNCF) ecosystem of cloud-native offerings.

[Wikipedia](https://en.wikipedia.org/wiki/GRPC) defines `gRPC` as:
> gRPC (gRPC Remote Procedure Calls) is an open source remote procedure call (RPC) system initially developed at Google in 2015. It uses HTTP/2 for transport, Protocol Buffers as the interface description language, and provides features such as authentication, bidirectional streaming and flow control, blocking or nonblocking bindings, and cancellation and timeouts. It generates cross-platform client and server bindings for many languages. Most common usage scenarios include connecting services in microservices style architecture and connect mobile devices, browser clients to backend services.

Enough with the theory, let's get down with the code:
 
## How to create `gRPC` (Micro?) Service (and Clinet) using .NET?
FYI: I'm using Visual Studio 2019 in a Windows 10 Machine.

* Create a new Project using `gRPC` template.

Don't worry, we'll create a new `gRPC` from the scratch.

![Create New Project](https://raw.githubusercontent.com/iSatishYadav/HashGrpc/master/assets/images/create-new-project.png)

* I'm using ASP.NET Core 3.1, but you may use .NET 5 as well

	![Create New Project Framework Selection](https://raw.githubusercontent.com/iSatishYadav/HashGrpc/master/assets/images/create-new-project-framework-selection.png)

* The solution looks like this:

	![Initial Project Structure](https://raw.githubusercontent.com/iSatishYadav/HashGrpc/master/assets/images/initial-project-structure.png)

* Let's have a look at the existing `proto` file:

````protobuf
syntax = "proto3";

option csharp_namespace = "HashGrpc";

package greet;

// The greeting service definition.
service Greeter {
  // Sends a greeting
  rpc SayHello (HelloRequest) returns (HelloReply);
}

// The request message containing the user's name.
message HelloRequest {
  string name = 1;
}

// The response message containing the greetings.
message HelloReply {
  string message = 1;
}

````

## Creating new `gRPC` for creating SHA256 Hash of a given input `string`

Let's build a Hashing `gRPC` Service which takes a `string` input and returns `SHA256` hash of the input.

### Create the `proto` contract

* Add a new file under _Protos_ directory:

![New Proto Hash](https://raw.githubusercontent.com/iSatishYadav/HashGrpc/master/assets/images/new-proto-hash.png)
![New Proto Hash Created](https://raw.githubusercontent.com/iSatishYadav/HashGrpc/master/assets/images/new-proto-hash-created.png)

* Copy this code to newly created `hash.proto`.

````protobuf
syntax = "proto3";

option csharp_namespace = "HashGrpc.Protos";

package hash;

service Hash {
	rpc GetHash(HashRequest) returns (HashResponse);
}

message HashRequest {
	string input = 1;
}

message HashResponse {
	string hash = 1;
}

````

Let's break it down.

1. `syntax` - Tells the version of `Protobuf`. In our case it's `proto3`.
2. `option csharp_namespace` - While generating `C#` code, the `namespace` used would be "HashGrpc.Protos".
3. `package` - package name for the service in `proto` file
4. `service` - Service definition. Currently our contract has only 1  service.
5. `message` - Defines the types used in Service. The numbers are unique `tag` for the fields.

### Generate Server stub
* Edit the `.csproj` file of your project:
    
    ![Edit Csproj](https://raw.githubusercontent.com/iSatishYadav/HashGrpc/master/assets/images/edit-csproj.png)

Add these lines to the `csproj` of your project. This lets Visual Studio known that we want to generate Server code for our `gRPC` service.
````xml
  <ItemGroup>    
    <Protobuf Include="Protos\hash.proto" GrpcServices="Server" />
  </ItemGroup>
````

### Implement the Service

* Under Services folder, create a new `Class` named `HashService`.
 
    ![New Service Hash](https://raw.githubusercontent.com/iSatishYadav/HashGrpc/master/assets/images/new-service-hash.png)
* Copy this code to the newly created Service.

````csharp
using Grpc.Core;
using HashGrpc.Protos;
using Microsoft.Extensions.Logging;
using System.Security.Cryptography;
using System.Text;
using System.Threading.Tasks;

namespace HashGrpc.Services
{
    public class HashService : Hash.HashBase
    {
        private readonly ILogger<HashService> _logger;

        public HashService(ILogger<HashService> logger)
        {
            _logger = logger;
        }
        public override Task<HashResponse> GetHash(HashRequest request, ServerCallContext context)
        {
            _logger.LogDebug("Getting hash for {request}", request);
            var hashResponse = new HashResponse
            {
                Hash = GetSha256Hash(request.Input)
            };
            _logger.LogDebug("Hash generated for {request} is {response}", request, hashResponse);
            return Task.FromResult(hashResponse);
        }

        private static string GetSha256Hash(string plainText)
        {
            // Create a SHA256 hash from string   
            using SHA256 sha256Hash = SHA256.Create();
            // Computing Hash - returns here byte array
            byte[] bytes = sha256Hash.ComputeHash(Encoding.UTF8.GetBytes(plainText));

            // now convert byte array to a string   
            StringBuilder stringbuilder = new StringBuilder();
            for (int i = 0; i < bytes.Length; i++)
            {
                stringbuilder.Append(bytes[i].ToString("x2"));
            }
            return stringbuilder.ToString();
        }
    }
}


````

Let's break it down.

* `Hash.HashBase` - This is the `base` class generated by `Visual Studio` which includes plumbing required to communicate. DO NOT EDIT IT.
* `HashService` - Just to emphasize that this is just a plain old class, we've added a constructor which takes an `ILogger<HashService>`, which can be injected by .NET Core's Dependency Injection system.
* `GetHash` method - Remember our `protobuf` had a method named `GetHash`? You can now implement this method. In our case we've calculated the SHA 256 hash of given input.
 And that's it. You've just created a fully functional `gRPC` Service. Now let's create a Client to call this Service.

## Consuming the `gRPC` Service

### Creating the Client
Let's create a new `Console App` Project to test our `gRPC` Service.

![Client New Project](https://raw.githubusercontent.com/iSatishYadav/HashGrpc/master/assets/images/client-new-project.png)

#### Adding `Nuget` packages
Install following nuget packages:

1. [Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf)
2. [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client)
3. [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools)

![Client Nuget](assets/images/client-nuget.png)

#### Adding reference to the `.proto`
* Add our `hash.proto` to a new Folder named `Protos`.

    ![Client New Proto](https://raw.githubusercontent.com/iSatishYadav/HashGrpc/master/assets/images/client-new-proto.png)

* Edit the `.csproj` of the project and following lines:

````xml
  <ItemGroup>   
    <Protobuf Include="Protos\hash.proto" GrpcServices="Client" />
  </ItemGroup>
````

This let's Visual Studio know that we want to create Client code for the 

#### Creating a channel
* Change the content of `Program.cs` as follows:

````csharp
using Grpc.Net.Client;
using HashGrpc.Protos;
using System;
using System.Threading.Tasks;
using static HashGrpc.Protos.Hash;

namespace HashGrpc.Client
{
    class Program
    {
        static async Task Main(string[] args)
        {
            Console.WriteLine("Hello World!");
            using var channel = GrpcChannel.ForAddress("https://localhost:5001");
            var client = new HashClient(channel);
            var response = await client.GetHashAsync(new HashRequest { Input = "Test" });
            Console.WriteLine($"Hash: {response.Hash}");
        }
    }
}

````

Let's break it down:

* First we need to create a `gRPC` Channel by giving the address where our service is running.
* Then we create a client by passing the channel.

* Now we can call the method by passing the `HashRequest` for which we want to get the `SHA 256` hash.

> Notice the Service had method `GetHash` but the client has 2 methods `GetHash` and `GetHashAsync`. That's thanks to the tooling which generated both Synchronous and Asynchronous versions of our `GetHash` method.
> 
>![Client Sync And Async Get Hash](https://raw.githubusercontent.com/iSatishYadav/HashGrpc/master/assets/images/client-sync-and-async-get-hash.png)

Run both Client and Service and you'll see the output something like this:

* Client:

    ![Client Output](https://raw.githubusercontent.com/iSatishYadav/HashGrpc/master/assets/images/client-output.png)
* Service

    ![Client Log](https://raw.githubusercontent.com/iSatishYadav/HashGrpc/master/assets/images/client-log.png)

> `gRPC` needs HTTP2 under the hood for communication. HTTP2 may not be supported everywhere yet (unfortunately).

Congratulations! You've just created a new `gRPC` Service and Client.

## Source Code

Source code is available on [GitHub here](https://github.com/iSatishYadav/HashGrpc).

## Next Steps

Read more about:
* [Protocol Buffers](https://developers.google.com/protocol-buffers)
* [gRPC](https://grpc.io/)
* [gRPC on ASP.NET Core](https://docs.microsoft.com/en-us/aspnet/core/grpc/?view=aspnetcore-3.1)
* [HTTP 2](https://developers.google.com/web/fundamentals/performance/http2/)
