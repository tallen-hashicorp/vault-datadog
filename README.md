# vault-datadog
Guide on setting up Datadog and Vault. In order to get aggregated data about the instance(s) of Vault, you can use Datadog. It is a mature monitoring solution that will report common resource metrics like CPU, memory and network metrics out of the box.

This tutorial was taken from [Monitoring Vault with Datadog](https://developer.hashicorp.com/vault/tutorials/monitoring/monitoring-vault-with-datadog) and covers set-up and configuration of Datadog Agent to monitor an instance of Vault Enterprise. Then you will look over the metrics available, and finally clean up the Datadog Agent and the local Vault installation.


## Prerequisites
To enable the Datadog Agent to gather metrics from Vault Enterprise, you will need to have:

* Free Tier Datadog account. Sign up for a free account at Datadog Pricing.
* A Vault Enterprise environment. Refer to the Getting Started tutorial to install Vault.
* Mac Workstation with MacOS 10.12 or higher, though much of this will be the same for other platforms.

## Set up the Datadog agent
Install the datadog agent on your Vault servers with the following steps
1. You will need an API key from Datadog. From the Datadog dashboard, select your user name at the bottom of the left navigation.
![Datadog](./docs/datadog.avif)
2. Select **Organizational Settings**, and then API Keys which lists existing API keys.
3. Select an API key created for your username.
![Datadog2](./docs/datadog2.avif)
4. Copy the API Key value, and then store it as a `DATADOG_API_KEY` environment variable.
5. Open a terminal and run the following command to install Datadog Agent.
```bash
DD_AGENT_MAJOR_VERSION=7 DD_API_KEY=$DATADOG_API_KEY \
    DD_SITE="datadoghq.com" \
    bash -c "$(curl -L https://s3.amazonaws.com/dd-agent/scripts/install_mac_os.sh)"
```
6. Verify that the agent is running.
```bash
datadog-agent status
```
If you encountered an error, try executing `datadog-agent stop` and then `datadog-agent run` again.

## Set up the Vault integration
Now we have the agent configured we can configure to send metrics, you need to set up access for the agent to connect to Datadog.
1. Navigate to `/opt/datadog-agent/etc/conf.d/vault.d.`
```bash
cd /opt/datadog-agent/etc/conf.d/vault.d
```
2. In the `/vault.d` directory, make a copy of `conf.yaml.example.`
```bash
cp conf.yaml.example conf.yaml
```
3. Open the `conf.yaml` file with an editor of your choice. Find the `instances`: section, and notice that the `api_url` parameter points to the Vault address to pull metrics from which is set to the locally running Vault (http://localhost:8200/v1).

Locate the no_token parameter and set it to true for the convenience of this tutorial.
```yaml
...snip...

init_config:

instances:
   ## @param api_url - string - required
   ## URL of the Vault to query.
 - api_url: http://localhost:8200/v1

   ## @param no_token - boolean - optional - default: false
   ## Attempt metric collection without a token.
   no_token: true
```
4. Restart the Datadog Agent to apply the configuration changes.
```bash
datadog-agent stop
datadog-agent run
```

## Monitor the Vault metrics
Now that the agent is installed and running, you need to validate that the agent is correctly sending data to Datadog. Metrics about both your workstation and your Vault Enterprise Instance should be streaming to Datadog.
1. In your Datadog dashboard, select **Metrics > Explorer.**
![datadog3](./docs/datadog3.avif)
2. The **Explorer** page shows the default metrics of `system.cpu.user`.
![datadog3](./docs/datadog4.avif)
3. With **Metrics** selected, start typing in `vault`. and you will see a number of available Vault metrics.
![datadog4](./docs/datadog4.avif)
Explorer the available metrics collected by the Datadog.