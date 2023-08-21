Windows users should use docker managed volume for remote integration logs. 
Create docker volume (for ex. `tb-tcp-udp-integration-logs`) before executing docker run command:
Open "Docker Quickstart Terminal". Execute the following command to create docker volume:

``` 
docker volume create tb-pe-tcp-udp-integration-logs
```
{: .copy-code}

Execute the following command to run this docker directly:

```bash
docker run -it -p 10560:10560 -v tb-pe-tcp-udp-integration-logs:/var/log/tb-tcp-udp-integration `
-e "RPC_HOST=EDGE_IP_OR_HOST_ADDRESS" -e "RPC_PORT=9090" `
-e "INTEGRATION_ROUTING_KEY=YOUR_ROUTING_KEY"  -e "INTEGRATION_SECRET=YOUR_SECRET" `
--name my-tb-pe-tcp-udp-integration --restart always thingsboard/tb-pe-tcp-udp-integration:{{ site.release.pe_full_ver }}
```
{: .copy-code}

Where: 
    
- `EDGE_IP_OR_HOST_ADDRESS` - is the host name or IP address of your ThingsBoard PE Edge;
- `9090` - is the integrations port of your ThingsBoard PE Edge. It is configured in tb-edge.yml using INTEGRATIONS_RPC_PORT env variable;   
- `YOUR_ROUTING_KEY` - placeholder for your integration routing key obtained on [Step 3](/docs/pe/edge/user-guide/integrations/remote-integrations/#step-3-save-remote-integration-credentials);
- `YOUR_SECRET` - placeholder for your integration secret obtained on [Step 3](/docs/pe/edge/user-guide/integrations/remote-integrations/#step-3-save-remote-integration-credentials);
- `docker run`              - run this container;
- `-it`                     - attach a terminal session with current ThingsBoard remote integration process output;
- `-p 10560:10560` - connect local port 10560 to exposed internal 10560 port for the integration
- `-v tb-pe-tcp-udp-integration-logs:/var/log/tb-tcp-udp-integration`   - mounts the host's dir `~/.tb-pe-tcp-udp-integration-logs` to ThingsBoard remote integration logs directory;
- `--name tb-pe-tcp-udp-integration`             - friendly local name of this machine;
- `--restart always`        - automatically start ThingsBoard Integration in case of system reboot and restart in case of failure.;
- `thingsboard/tb-pe-tcp-udp-integration:{{ site.release.pe_full_ver }}`          - docker image.

After executing this command you can open logs which are located here `~/.tb-pe-tcp-udp-integration-logs`. 
You should see some INFO log messages with your latest Integration configuration that arrived from the server.

<br/>

You can detach from session terminal with **`Ctrl-p`**+**`Ctrl-q`** - the container will keep running in the background.

<br/>

- **Reattaching, stop and start commands**

To reattach to the terminal (to see ThingsBoard remote integration logs) run:

```
docker attach tb-pe-tcp-udp-integration
```
{: .copy-code}

To stop the container:

```
docker stop tb-pe-tcp-udp-integration
```
{: .copy-code}

To start the container:

```
docker start tb-pe-tcp-udp-integration
```
{: .copy-code}