2012/oktobar/SI Kolokvijum 1 - Oktobar 2012 - Resenja.pdf
--------------------------------------------------------------------------------
schedule
LP, MP, MP, HP, MP, HP, MP 

--------------------------------------------------------------------------------
sharedobj
```ada
monitor server; 
export acquireToken, returnToken; 
 
var numOfTokens : integer; 
 
procedure acquireToken (); 
begin 
  while numOfTokens<=0 do wait(); 
  numOfTokens := numOfTokens - 1; 
end; 
 
procedure returnToken (); 
begin 
  numOfTokens := numOfTokens + 1; 
  notifyAll(); 
end; 
 
begin 
  numOfTokens := N; 
end; (* server *) 
 
 
task type client; 
begin 
  loop 
    server.acquireToken; 
    do_some_activity; 
    server.returnToken; 
  end; 
end; (* client *)  
```

--------------------------------------------------------------------------------
network
```java
public class NetBuffer extends Usluga { 
 public NetBuffer(String host, int port) { 
  super(host, port); 
  getK(); 
 } 
 private int K; 
 public void put(int d[]) { 
  String message = "#put#"; 
  for (int i = 0; i < K; i++) { 
   message += d[i] + "#"; 
  } 
  sendMessage(message); 
  receiveMessage(); 
 } 
 public int get() { 
  String message = "#get#"; 
  sendMessage(message); 
  return receiveIntMessage(); 
 } 
public int getK() { 
  String message = "#getK#"; 
  sendMessage(message); 
  return K = receiveIntMessage(); 
 } 
} 
```
Na serverskoj strani u klasi `Server` treba da se dodaju sledeći atributi: 
```java
public BoundedBuffer buff;  
public final int K; 
public Server(int port, int N, int K) { 
  buff = new buffer(N, K); 
  this.K=K; 
  ... 
//poziv konstruktora new RequestHandler(clientSocket,buff,K); 
```
`RequestHandler` treba izmeniti na sledeći način: 
```java
public class RequestHandler extends Thread { 
... 
 buffer buff; 
  int K; 
...  
public RequestHandler(Socket clientSocket, buffer buff, int K) { 
  this.sock = clientSocket; 
  this.buff = buff; 
  this.K = K; 
  ... 
}
protected void processRequest(String request) { 
  StringTokenizer st = new StringTokenizer(request, "#"); 
  String functionName = st.nextToken(); 
  if (functionName.equals("put")) { 
   int d[] = new int[K]; 
   for (int i = 0; i < K; i++) 
    d[i] = Integer.parseInt(st.nextToken()); 
   buff.put(d); 
   sendMessage("done"); 
  } else if (functionName.equals("get"))  
    sendMessage("" + buff.get()); 
   else if (functionName.equals("getK"))  
    sendMessage("" + K); 
 } 
```
