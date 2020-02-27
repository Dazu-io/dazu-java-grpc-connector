# Dazu Java GRPC Connector

This component can be used to create components to [Dazu](https://github.com/dazu-io/dazu) in Java!

## Import with Maven

```xml
<dependency>
    <groupId>io.dazu</groupId>
    <artifactId>dazu-grpc-connector</artifactId>
    <version>1.0-SNAPSHOT</version>
</dependency>
```


## Sample of usage

```java
import io.dazu.Component.Message;
import io.dazu.ComponentServiceGrpc.ComponentServiceImplBase;
import io.grpc.Server;
import io.grpc.ServerBuilder;
import io.grpc.stub.StreamObserver;

public class Main {

	public static void main(String[] args) {
		try {
			Server server = ServerBuilder.forPort(9898).addService(new ComponentImpl()).build().start();
			server.awaitTermination();
		} catch (Exception e) {
			e.printStackTrace();
		}
	}

	static class ComponentImpl extends ComponentServiceImplBase {

		@Override
		public void process(Message request, StreamObserver<Message> responseObserver) {
			Message response = Message.newBuilder(request).setOutput(request.getInput() + "!!!!!").build();
			responseObserver.onNext(response);
		}
	}
}
```   