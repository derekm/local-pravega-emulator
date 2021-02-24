# local-pravega-emulator 0.9.0-rc2 test project

This project shows that `LocalPravegaEmulator` is broken out-of-the-box for 0.9.0 when using minimal dependencies.

The included unit test is taken from https://github.com/pravega/pravega/blob/r0.9/standalone/src/test/java/io/pravega/local/InProcPravegaClusterTest.java

Clone this project and run the test in your IDE or via `mvn test`, and you'll get the following exception:

```
testWriteAndReadEventWithValidClientConfig(io.pravega.local.InProcPravegaClusterTest)  Time elapsed: 6.047 sec  <<< ERROR!
java.lang.NoClassDefFoundError: com/google/protobuf/GeneratedMessageV3
        at java.base/java.lang.ClassLoader.defineClass1(Native Method)
        at java.base/java.lang.ClassLoader.defineClass(ClassLoader.java:1016)
        at java.base/java.security.SecureClassLoader.defineClass(SecureClassLoader.java:174)
        at java.base/jdk.internal.loader.BuiltinClassLoader.defineClass(BuiltinClassLoader.java:800)
        at java.base/jdk.internal.loader.BuiltinClassLoader.findClassOnClassPathOrNull(BuiltinClassLoader.java:698)
        at java.base/jdk.internal.loader.BuiltinClassLoader.loadClassOrNull(BuiltinClassLoader.java:621)
        at java.base/jdk.internal.loader.BuiltinClassLoader.loadClass(BuiltinClassLoader.java:579)
        at java.base/jdk.internal.loader.ClassLoaders$AppClassLoader.loadClass(ClassLoaders.java:178)
        at java.base/java.lang.ClassLoader.loadClass(ClassLoader.java:521)
        at io.pravega.client.control.impl.ControllerImpl.createScope(ControllerImpl.java:288)
        at io.pravega.client.admin.impl.StreamManagerImpl.createScope(StreamManagerImpl.java:125)
        at io.pravega.local.InProcPravegaClusterTest.testWriteAndReadEventWithValidClientConfig(InProcPravegaClusterTest.java:135)
...
```

0.9.0-rc2 uses `protobuf-java` version 3.13.0. Including this dependency to resolve the above error prevents `LocalPravegaEmulator` from starting properly, and the test ultimately fails trying to create its scope:

```
io.pravega.client.control.impl.ControllerFailureException: Failed to create scope: TestScope
	at io.pravega.client.control.impl.ControllerImpl.lambda$createScope$2(ControllerImpl.java:292)
	at java.base/java.util.concurrent.CompletableFuture$UniApply.tryFire(CompletableFuture.java:642)
	at java.base/java.util.concurrent.CompletableFuture.postComplete(CompletableFuture.java:506)
	at java.base/java.util.concurrent.CompletableFuture.complete(CompletableFuture.java:2073)
	at java.base/java.util.concurrent.CompletableFuture$UniAccept.tryFire(CompletableFuture.java:714)
	at java.base/java.util.concurrent.CompletableFuture.postComplete(CompletableFuture.java:506)
	at java.base/java.util.concurrent.CompletableFuture.complete(CompletableFuture.java:2073)
	at io.pravega.client.control.impl.ControllerImpl$RPCAsyncCallback.onCompleted(ControllerImpl.java:1720)
...
```

Please advise.
