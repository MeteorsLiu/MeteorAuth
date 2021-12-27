# MeteorAuth
A bidirectional authentication solution


# Idea

## First Step C/S Handshake

### Handshake Goal

1. Ensure the Client/Server is reliable

2. Verify the identity of the specific client and the server

### Handshake Result

Generate a pair of temporary Tokens

E.g Sever Token is used in the situation that client requests the data of the server

Client Token is for sever CMDs.

### How to Handshake

1. initialize

Two pairs of Sign Key MUST BE SET UP for the first time

E.g Client Private Key stores in the clinet side, whose public key stores in the server side

Server Private Key stores in the server side, whose public key stores in the client side


2. Algorithm

**Sign Algo: Ed25519**

**Verify Algo: X25519 + HMAC-SHA256**

3. Params

**Server Side**

```
Client Public Sign Key

Server Private Sign Key

Server Private Key FOR X25519

Client Public Key FOR X25519

```

**Client Side**

```
Server Public Sign Key

Client Private Sign Key

Client Private Key FOR X25519

Server Public Key FOR X25519

```


4. Process

1. One side request the handshake action

2. Two sides both calculate the X25519 Shared Key

**Client**

```
Shared Key = X25519(Client Private Key, Server Public Key)
```


**Server**

```
Shared Key = X25519(Server Private Key, Client Public Key)
```

3. Then generate the Hash

First the requested side generate 32 Bytes long Random Char as token

Then 

```
hash = HMAC-SHA256(Shared Key, Random Char)

token = Random Char
```

4. one side use the Ed25519 Algo Generate the sign


```
Ed25519(One Side Private Key, Hash)
```

5. Send the Handshake packet

Handshake packet is in TLS1.3 with gRPC

its proto3 like

```
message Key {

    string sessionid = 1;
    
    string token = 2;
    
    string hash = 3;
    
    string sign = 4;
    
}

```

6. The other side verify after received

```
1.Verify Sign

if Sign are correct

2. Verify Hash

if Hash are correct

3. Repeat the handshake again

generate the token and send it to the other side
```




