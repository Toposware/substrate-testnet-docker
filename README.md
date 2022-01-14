# substrate-testnet-docker
`substrate-testnet-docker` is a docker-compose setup to launch a Substrate testnet network along with a load balancer that distributes access to the network. Clients, e.g. wallets, connect to a static url which load balances (round robin) the connection to one of the network nodes.

## Why?
Running a Substrate blockchain network is highly documented (see one of the very first tutorials by Parity [here](https://docs.substrate.io/tutorials/v3/private-network/)). 

Opening a distributed access to the network is critical in web3 for it removes the need for static client<->server connections as well as API middleman services that are commonly used today by major wallets (read more [here](https://twitter.com/moxie/status/1479567493215637506)).

## Setup

- Substrate: `node-template` binary
  - Validators: 2 (Alice & Bob)
  - Non validators: *n* replicas (see [Config](#config))
- Load balancer: [Traefik](https://traefik.io)
  - 1 endpoint/route: (see [Config](#config))
  - Routed services: the *n* non validators nodes
- TLS certs: LetsEncrypt 
  - (staging by default => comment related line in `docker-compose.yml`)

## Config
- ACME_EMAIL: email for TLS certs provisioned by LetsEncrypt
- LOAD_BALANCER_ENDPOINT: DNS name that points to the server where you run the docker setup
- REPLICAS_NON_VALIDATORS: number of non validator nodes you want to run

## Getting Started

- Run a server
- Install `docker` and `docker-compose`
- Set a DNS A record pointing to the server's IP address
- Set your DNS record name in `.env`
- Set the number of replicas you want to run in `.env`
- Set your email for TLS certs in `.env`
- Run `docker-compose up -d`

Then, using your favorite Substrate client, e.g. [polkadot-js/apps](https://polkadot.js.org/apps/) and setting your DNS record name as the custom endpoint (see screenshot below), enjoy a load balanced access to your network! (refreshing the page will connect you to another of your *n* network nodes!).

<img width="887" alt="Screen Shot 2022-01-14 at 16 52 18" src="https://user-images.githubusercontent.com/12198372/149471114-60ef4d1a-c6fb-4951-a63e-304e64f01c6a.png">

## Where to go from there?
This setup is clearly more of a playground than a production ready docker-compose file.

Validators are hardly coded because it doesn't seem easy to start a dynamic number of validators and handle their session keys in a docker-compose file (sessions keys must be injected on running nodes which then must be restarted => not a common workflow in docker). Also, `peerId` of the bootnode must be known in advance when specifying the `--bootnode` flag when running subsequent nodes.