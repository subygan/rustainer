# rustainer

A container implementation using User Namespaces, CGroups, and [slirp4netns](https://github.com/rootless-containers/slirp4netns) in Rust.

## Implementation Status

- [x] User Namespaces
- [x] Networking (with `slirp4netns`)
    - [x] Outgoing Connections
    - [x] Port Forwarding to expose container
- [ ] CGroups (needs integration for delegation mechanisms)
- [x] Clear Environment Variables
- [x] Handling `setgroups()` inside the container
- [ ] Proper PTY handling (use `tmux` inside the container for now)
- [x] Ability to fetch images from Docker Registry
- [x] Image storage
- [ ] Handling whiteout files in layers

## Dependencies

- [`slirp4netns`](https://github.com/rootless-containers/slirp4netns)
- `new{uid,gid}map`: Likely provided by the `shadow` or `uidmap` package, depending on distribution

## Usage

```sh
cargo run -- <image_name> <tag>
```

Example:

```sh
cargo run -- alpine latest
```

This fetches the Alpine Linux image, sets up the container environment, and drops you into a shell inside the container.

## Inside the Container

Once inside the container, you can verify the isolation:

```sh
/ # whoami
root
/ # ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
...
4: tap0: <BROADCAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UNKNOWN qlen 1000
    link/ether fe:7c:24:98:65:d3 brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.100/24 brd 10.0.2.255 scope global tap0
       valid_lft forever preferred_lft forever
...
```

You can install packages and access the internet:

```sh
/ # apk add curl
...
/ # curl https://httpbin.org/get
{
  "args": {}, 
  "headers": {
    "Accept": "*/*", 
    "Host": "httpbin.org", 
    "User-Agent": "curl/8.3.0", 
  }, 
  "origin": "XX.XXX.XXX.XX",
  "url": "https://httpbin.org/get"
}
```

## Project Goals

This project aims to provide a lightweight containerization solution with a focus on security and efficiency. It's been a great way to deepen my understanding of container technologies and Linux systems programming.

## Future Work

- Improve CGroup integration
- Enhance PTY handling
- Implement handling of whiteout files in layers

Contributions and feedback are welcome!