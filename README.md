# SSV Stack 
DISCLAIMER: SSV stack is an example repository how you can run SSV node with monitoring stack. It is not recommended to use it in production.

It includes Prometheus, which scrapes the SSV node and Grafana which has dashboard for the SSV node.

## How to run

### Prerequisites
* `docker`
* `docker compose` version >= 2.0
* `ssv-node` version >= `v2.1.0`

### Clone the repository

```bash
git clone https://github.com/ssvlabs/ssv-stack.git
cd ssv-stack
```
### Adjust `env` file
```bash
cp ssv.example.env ssv.env
```
Edit the `ssv.env` file and adjust the settings to your needs.
The minimum you need to change is:
* `BEACON_NODE_ADDR` - the address of the beacon node
* `ETH_1_ADDR` - the address of the eth1 node
* `NETWORK` - the network you are running on (`mainnet`, `holesky`, etc)

Check the `ssv.example.env` file for details for all the others settings you can adjust.

### Alerting rules
You can adjust the alerting rules in the `./prometheus/alert-rules.yml` file.

For this to work, you can configure alertmanager with your telegram bot token and chat id. You can do this by editing the `./alertmanager/alertmanager.yml` file. Put the raw token in the `alertmanager/telegram_bot_token.txt`.

### SSV node private key
#### Generate New Key
This example generates a new ssv node private key which is encrypted by a newly generated random password. Both files will be in the `./ssv-node-data` directory. 
#### Use Existing Key
If you want to use your own private key, you can go to the `./ssv-node-data`folder and put in your private_key and password files such as: `./ssv-node-data/private_key` and `./ssv-node-data/password`. You might need to create the `./ssv-node-data` directory if it does not exist.

### Run the stack
Run in foreground
```bash
docker compose up
```
Run in background
```bash
docker compose up -d
```

### Run with DKG node
You can also run the stack with DKG, simplifying the setup process.
First, you need to edit the config in `./dkg-data/operator.yaml`.
The minimum you need to change is:
* `operatorID` - the ID of your operator
* `ethEndpointURL` - HTTP Address of your Execution node endpoint

Also, you'll need to expose port `3030` on your firewall for the DKG endpoint to be available.

Make sure you're in `ssv-stack` directory, and then run the command:
```bash
docker compose --profile dkg up
```
Or run in background
```bash
docker compose --profile dkg up -d
```

## Access Grafana
Open your browser and go to [http://localhost:3000](http://localhost:3000) and login with `admin`/`admin`. You can change the password later.

In the `Dashboards` section you should be able to find the `SSV Operational` dashboard. Unless the node is registered with the SSV network and has validators, some of the dashboard's columns might be empty.

**WARNING**: Grafana and Prometheus is running only on the `localhost` by default. You should change the password and/or restrict access to it. If you expose Grafana and Prometheus to the internet, you should be extra careful and make sure they are secured.

## Benchmarking tool
The Stack comes with a benchmarking tool which is [ssv-pulse](https://github.com/ssvlabs/ssv-pulse).
It helps with troubleshooting and the results can be interpreted with our [Troubleshooting page](https://docs.ssv.network/operators/operator-node/maintenance/troubleshooting/#checklists-for-troubleshooting).
To run the tool:
* Edit `docker-compose.yaml`, at the very end of the file
* Change `--consensus-addr` to your Consensus HTTP and `--execution-addr` to Execution HTTP
* If running on Hoodi - uncomment `--network=hoodi`
* Run `docker compose run ssv-pulse`
* The tool will be running for 60 minutes and produce a table with results