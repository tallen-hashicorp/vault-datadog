# vault-datadog
Guide on setting up Datadog and Vault. In order to get aggregated data about the instance(s) of Vault, you can use Datadog. It is a mature monitoring solution that will report common resource metrics like CPU, memory and network metrics out of the box.

This tutorial was taken from [Monitoring Vault with Datadog](https://developer.hashicorp.com/vault/tutorials/monitoring/monitoring-vault-with-datadog) and covers set-up and configuration of Datadog Agent to monitor an instance of Vault Enterprise. Then you will look over the metrics available, and finally clean up the Datadog Agent and the local Vault installation.


## Prerequisites
To enable the Datadog Agent to gather metrics from Vault Enterprise, you will need to have:

* Free Tier Datadog account. Sign up for a free account at Datadog Pricing.
* A Vault Enterprise environment. Refer to the Getting Started tutorial to install Vault.
* Mac Workstation with MacOS 10.12 or higher, though much of this will be the same for other platforms.

## Set up the Datadog agent locally
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