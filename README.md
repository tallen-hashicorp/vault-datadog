# Vault-Datadog Setup Guide

This guide provides step-by-step instructions for setting up Datadog to monitor instances of HashiCorp Vault. Datadog, a mature monitoring solution, offers out-of-the-box reporting on common resource metrics such as CPU, memory, and network metrics.

This tutorial is adapted from [Monitoring Vault with Datadog](https://developer.hashicorp.com/vault/tutorials/monitoring/monitoring-vault-with-datadog). It covers the setup and configuration of the Datadog Agent to monitor a Vault Enterprise instance. You will explore available metrics and learn how to clean up the Datadog Agent and the local Vault installation.

## Prerequisites

Before you begin, ensure you have the following prerequisites:

- Free Tier Datadog account. Sign up for a free account at [Datadog Pricing](https://www.datadoghq.com/pricing).
- Vault Enterprise environment. Follow the [Getting Started tutorial](https://www.hashicorp.com/resources/getting-started-vault) to install Vault.
- Mac Workstation with MacOS 10.12 or higher (though instructions are similar for other platforms).

## Set up the Datadog Agent

1. Obtain an API key from Datadog. In the Datadog dashboard, select your username at the bottom of the left navigation.

    ![Datadog](./docs/datadog.png)

2. Navigate to **Organizational Settings > API Keys** to find existing API keys.

    ![Datadog2](./docs/datadog2.png)

3. Copy the API Key value and store it as a `DATADOG_API_KEY` environment variable.

4. Open a terminal and run the following command to install the Datadog Agent.

    ```bash
    DD_AGENT_MAJOR_VERSION=7 DD_API_KEY=$DATADOG_API_KEY \
        DD_SITE="datadoghq.com" \
        bash -c "$(curl -L https://s3.amazonaws.com/dd-agent/scripts/install_mac_os.sh)"
    ```

5. Verify that the agent is running.

    ```bash
    datadog-agent status
    ```

    If you encounter an error, try executing `datadog-agent stop` and then `datadog-agent run` again.

## Set up the Vault integration

Now that the agent is configured, set up access for the agent to connect to Datadog.

1. Navigate to `/opt/datadog-agent/etc/conf.d/vault.d`.

    ```bash
    cd /opt/datadog-agent/etc/conf.d/vault.d
    ```

2. In the `/vault.d` directory, make a copy of `conf.yaml.example`.

    ```bash
    cp conf.yaml.example conf.yaml
    ```

3. Open the `conf.yaml` file with an editor of your choice. Locate the `instances` section and find the `api_url` parameter, which points to the Vault address. Set the `no_token` parameter to true.

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

Now that the agent is installed and running, validate that it is correctly sending data to Datadog. Metrics for both your workstation and your Vault Enterprise instance should be streaming to Datadog.

1. In your Datadog dashboard, select **Metrics > Explorer**.

    ![datadog3](./docs/datadog3.png)

2. The **Explorer** page shows default metrics such as `system.cpu.user`.

    ![datadog3](./docs/datadog4.png)

3. With **Metrics** selected, type `vault` to see a list of available Vault metrics.

    ![datadog4](./docs/datadog4.png)

Explore the metrics collected by Datadog to gain insights into your Vault instances.