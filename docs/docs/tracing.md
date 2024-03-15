# Tracing

Consuming and producing activities are recorded with tags as specified in the v1.7 of [OpenTelemetry Messaging Semantic Conventions](https://opentelemetry.io/docs/specs/semconv/messaging/kafka/).

The tracing context between consuming and producing activities propagates via `traceparent` header on the message envelope. This is with accordance to [W3C Trace Context spec](https://www.w3.org/TR/trace-context/#relationship-between-the-headers)j:

```json
{
    "messageId": "100bee5a-82af-4003-82a2-fa6e543de24of",
    // tracing context propagets via this header as {version}-{trace_id}-{span_id}-{trace_flags}
    "traceparent": "00-0af7651916cd43dd8448eb211c80319c-b7ad6b7169203331-01",
    "type": "some-message",
    "data": {
        "someId": "538b7db6-54c0-4115-ab6d-583d9714a289",
        "someData": "This is very important data"
    }
}
```

## Setup trace collection

Traces are being generated automaticaly.

However, your application must subscribe to **"Dafda" activity source explicitly** in order to collect them:
```csharp
using System;
using System.Diagnostics;
using OpenTelemetry;
using OpenTelemetry.Resources;
using OpenTelemetry.Trace;

class Program
{
    static void Main(string[] args)
    {
        using var traceProvider = Sdk.CreateTraceProviderBuilder()
            // link all traces from this service to "my-service"
            .SetResourceBuilder(ResourceBuilder.CreateDefault().AddService("my-service"))
            // collect traces generated by Dafda
            .AddSource("Dafda")
            .AddConsoleExporter()
            .Build();

        Consoe.WriteLine("Collecting Traces from Dafda!");
    }
}

```
