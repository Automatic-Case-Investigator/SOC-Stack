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
