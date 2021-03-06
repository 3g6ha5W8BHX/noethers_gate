# Noether's Gate  
Noether's Gate - cryptocurrency transactions over Bitmessage network  

## WTF
Basically Bitmessage is P2P trustless, metadata free network where any message sent to the network is broadcasted to every node - it uses code from bitcoin. Every node will try to decrypt every passing message with all private keys, if sucessful, the message is for you.  

What I have done is, that I created a listening script on a BM address which will decrypt a message and send it to multiple remote Monero nodes. It is basically transaction proxy on a steroids as the BM network is metadata free where hundreds of nodes are just reposting messages.  

`script.py` is what I run on a server, nothing that should be used when you want to just send the transaction.  

## Steps  
1. Start your local `monero-wallet-cli`:  
```
./monero-wallet-rpc --rpc-bind-port 16969 --rpc-bind-ip 127.0.0.1 --rpc-login username:password --wallet-file desktop_wallet --prompt-for-password
```

2. Create raw transaction WITHOUT broadcasting it to a network.  
Wallet API could change in the future, what is really IMPORTANT is to set `do_not_relay` and `get_tx_hex` flags, otherwise your node will broadcast transaction to a network.  

```
curl -u username:password --digest -s -S -X POST http://127.0.0.1:16969/json_rpc \
-H 'Content-Type: application/json' \
-d \
'{
  "jsonrpc": "2.0",
  "id": "0",
  "method": "transfer",
  "params": {
    "destinations": [{
      "amount": 10000000000,
      "address": "45dZruKBeAFcBkoFckTTonJPDUDFcPkUMdSebg2YTE8mTaBbhnv9DWwR9DYvfqKXjn8M49XpTcLw8c5mZ457etvuP8e9DDC"
    }],
    "priority": 0,
    "get_tx_key": true,
    "do_not_relay": true,
    "get_tx_hex": true
  }
}'
```

2. Send raw transaction hex string to a Bitmessage relay address.  
Just pass the raw `tx_blob` value string from the previous step to the Bitmessage message body and send it to the BM address:  
```
BM-2cVrx5dDYQJR5hDmnEKiA25pqJtQmAwxzg
```


## How it works  
Bitmessage daemon will trigger transaction relay script defined in the [apinotifypath](https://bitmessage.org/wiki/API_Reference) configuration option every time new message is received.  
Script will iterate through the new messages and if there is a raw transaction, it will try to forward it to the appropriate currency network.  

Only XMR transactions are currently supported, but adding another currencies is trivial.  

## Donate
Want to try Noether's Gate? Send me some donation, so I can pay for the server costs.  
You can test the Gate by sending XMR to a donation address:
```
address:  
45dZruKBeAFcBkoFckTTonJPDUDFcPkUMdSebg2YTE8mTaBbhnv9DWwR9DYvfqKXjn8M49XpTcLw8c5mZ457etvuP8e9DDC  

viewkey:  
9c68f51ea01b3446a31013a15f826d982d44f25d65bcc6b843eba68246eb5708
```  

I will probably use my own Monero node in the future.  

