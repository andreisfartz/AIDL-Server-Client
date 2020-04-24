# AIDL-Server-Client

##### Source and clarifications: https://developer.android.com/guide/components/aidl

Scenario: There are 2 applications, the server app and the client app. We'll call them AidlServer and AidlClient. The server app contains a service which can do Math operations, called MathService (or MyService). The client app wants to use the complicated math methods that are implemented in this Service, which is located on the server. This is what we are going to try to do.


___

We'll start with the AidlServer.
## 1.  The aidl file

In the _project_ structure of your current project, go to **app/src/main**, Right Click on **main** > New > AIDL > AIDL File.  Create the IMathManager.aidl file and its methods. The aidl file will generate the required interface that both the client application and the server will be using.
```java
interface IMathManager {
    int add(in int x, in int y);
    int substract(in int x, in int y);
}
```

Now, after rebuilding the project, you should have in the Android project structure, in the **java *(generated)*** folder the IMathManager interface. Since it has been generated by the Android framework, we won't be modifying anything here. Notice however the Stub abstract class inside it. That is what we'll be using to communicate between processes.

## 2.  The Service

Create a new class extending Service, we'll call it MathService. It will have to override the public IBinder ```onBind(Intent intent)``` method, which returns an IBinder object, through which clients can call on to the service.

We'll have to declare the IBinder object however, before returning it in the onBind method.
```java
private MathManagerImpl mathManagerImpl = new MathManagerImpl();

private class MathManagerImpl extends IMathManager.Stub {

    @Override
    public int add(int x, int y) throws RemoteException {
        return x + y;
    }

    @Override
    public int substract(int x, int y) throws RemoteException {
        return x - y;
    }
}
```
##### Note: creating a class that extends IMathManager.Stub is optional, you could just create an annonymous implementation like this:
_``` IMathManager mathManager = new IMathManager.Stub() { ... } ```_


The IMathManager.Stub class extends the Binder object and implements the IMathManager interface, which means that this is where we are going to define our methods, and that this is what the client is going to receive from the onBind method.

All that is left now is to return our ```mathManagerImpl``` object in the onBind method:
```java
    @Override
    public IBinder onBind(Intent intent) {
        return mathManagerImpl;
    }
```

