Certainly! Here’s the revised version with the comments before each `bash` command and no `#` in the commands themselves:

### Instructions for Retrieving and Modifying Ports for 0gchain Setup

To fill out the form, you need to determine the ports used by your setup. The commands below will help you identify these ports.

Retrieve the TCP or UNIX socket address for the RPC server to listen on:
```bash
sed -n '/# TCP or UNIX socket address for the RPC server to listen on/,+1p' /root/.0gchain/config/config.toml
```

Retrieve the node address:
```bash
sed -n 's/^node = ./&/p' /root/.0gchain/config/client.toml
```

Retrieve the JSON-RPC address:
```bash
sed -n '/\[json-rpc\]/,/\[.*\]/ { /^\s*address\s*=\s*".*"/p }' $HOME/.0gchain/config/app.toml
```

If the output shows `127.0.0.1`, you need to change it to `0.0.0.0`. Use the following commands:

Change the EVM RPC HTTP server address to `0.0.0.0`:
```bash
sed -i '/# Address defines the EVM RPC HTTP server address to bind to./!b;n;c\address = "0.0.0.0:26145"' /root/.0gchain/config/app.toml
```

Change the RPC server listening address to `0.0.0.0`:
```bash
sed -i '/# TCP or UNIX socket address for the RPC server to listen on/!b;n;c\laddr = "tcp://0.0.0.0:27657"' /root/.0gchain/config/config.toml
```

### RPC Port Information
- **Validator**: `YOUR_IP:26145`, `YOUR_IP:27657` (**Default Port**: 8545 if you didn't change it.)
- **Storage Node**: `YOUR_IP:5678` (default port)
- **Storage KV**: `YOUR_IP:6789` (default port)
- **DA Node**: `YOUR_IP:34000` (default port)
- **DA Client**: `YOUR_IP:51001` (default port)

If you want to use your own RPC when setting up a Storage Node, you will use the output of this command:

```bash
sed -n '/\[json-rpc\]/,/\[.*\]/ { /^\s*address\s*=\s*".*"/p }' $HOME/.0gchain/config/app.toml
```

**Please note: Your ports might be different.**

#### EVM RPC URL
Access your EVM RPC at:

```bash
http://your_ip:YOUR_PORT/
```

[Form Link](https://docs.google.com/forms/d/e/1FAIpQLScsa1lpn43F7XAydVlKK_ItLGOkuz2fBmQaZjecDn76kysQsw/viewform?ts=6617a343)

**Important:** Ensure to replace `YOUR_IP` with your actual IP address and `YOUR_PORT` with the correct port number when filling out the form.
