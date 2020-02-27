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

import com.google.protobuf.InvalidProtocolBufferException;
import com.google.protobuf.Struct;
import com.google.protobuf.util.JsonFormat;

import io.dazu.Component.DazuMessage;
import io.dazu.ComponentServiceGrpc.ComponentServiceImplBase;
import io.grpc.Server;
import io.grpc.ServerBuilder;
import io.grpc.stub.StreamObserver;

public class Main {

	public static void main(String[] args) throws Exception {
			final int port = 9898;
			Server server = ServerBuilder.forPort(port).addService(new ComponentImpl()).build().start();
			System.out.println("Listening on port "+ port);
			server.awaitTermination();	
	}

	static class ComponentImpl extends ComponentServiceImplBase {

		@Override
		public void process(DazuMessage request, StreamObserver<DazuMessage> responseObserver) {
			DazuMessage.Builder builder = DazuMessage.newBuilder().mergeFrom(request);

			String inputText = request.getInput().getFieldsMap().get("text").getStringValue();

			Struct.Builder structBuilder = Struct.newBuilder();
			try {
				JsonFormat.parser().merge("{'text': ['My answer for: "+inputText+"']}", structBuilder);
			} catch (InvalidProtocolBufferException e) {
				e.printStackTrace();
			}
			builder.setOutput(structBuilder);
			responseObserver.onNext(builder.build());
			responseObserver.onCompleted();
		}
	}

}
```   