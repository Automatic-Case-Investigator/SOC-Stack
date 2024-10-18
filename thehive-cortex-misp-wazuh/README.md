# SOC Technology Stack

| Technology    | Category            | Publicly Facing Network Ports |
|---------------|---------------------|-------------------------------|
| Wazuh         | SIEM                | 5601                          |
| MISP          | Threat Sharing      | 80/443                        |
| Cortex        | Threat Intelligence | 9001                          |
| The Hive      | SOAR                | 9000                          |
| Cassandra     | Database Management | 9042                          |
| ElasticSearch | Search Engine       | 9200                          |
| Minio         | Object Storage      | 9090                          |
| Redis         | Non relational DB   | Default                       |
| MySQL         | Relational DB       | Default                       |
| SMTP          | Mail                | Default                       |

## Setup guide
### Wazuh
Run the following to generate keys for wazuh:
```bash
docker compose -f generate-indexer-certs.yml run --rm generator
```

Run the SOC stack with the following command:
```bash
docker compose up -d
```

In case if Wazuh indexer cannot cannot find the template of the index pattern [wazuh-alerts-*], run the following to update the template in Wazuh indexer:
```bash
cat wazuh-index-template.json | curl -X PUT "https://localhost:9201/_template/wazuh" -H 'Content-Type: application/json' -d @- -u admin:SecretPassword -k
```

#### Wazuh The Hive integration

Go to the Wazuh manager's docker container and run:
```bash
/var/ossec/framework/python/bin/pip3 install thehive4py==1.8.1
```

Copy the files in ```wazuh_integrations``` to Wazuh manager's docker container and run the following:
```bash
chmod 755 /var/ossec/integrations/custom-w2thive.py
chmod 755 /var/ossec/integrations/custom-w2thive
chown root:ossec /var/ossec/integrations/custom-w2thive.py
chown root:ossec /var/ossec/integrations/custom-w2thive
```

Add the following to the Wazuh manager's ossec.conf:
```bash
<ossec_config>
    <integration>
        <name>custom-w2thive</name>
        <hook_url>http://thehive:9000</hook_url>
        <api_key>YOUR_THEHIVE_API_KEY</api_key>
        <alert_format>json</alert_format>
    </integration>
</ossec_config>
```

Restart the Wazuh manager to apply the changes. If not working as intended, check ```/var/ossec/logs/integrations.log``` for more information.

### Cortex
1. Create an admin user
2. Create a new organization
3. In that new organization, create a new account with org admin privileges and set up an API key for it

### MISP
1. Log in with admin@admin.test : admin
2. In account settings, create a new API key

### The hive
1. Login with admin@thehive.local : secret
2. In platform settings, add the API keys for cortex and misp to connect these two services
