# OpenSearch in Docker Compose

![OpenSearch version](https://img.shields.io/badge/OpenSearch%20version-2.6.0-blue)

Dockerized cluster architecture for OpenSearch with compose.

## Key concepts

- OpenSearch is [the successor of OpenDistro](https://opendistro.github.io/for-elasticsearch/blog/2021/06/forward-to-opensearch/)
- OpenSearch = Elasticsearch
- OpenSearch Dashboards = Kibana

## Cluster setup

Raise your host's ulimits for ElasticSearch to handle high I/O :

```bash
sudo sysctl -w vm.max_map_count=512000
# Persist this setting in `/etc/sysctl.conf` and execute `sysctl -p`
```

Now, we will generate the certificates for the cluster :

```bash
# You may want to edit the OPENDISTRO_DN variable first
bash generate-certs.sh
chown -R 1000.1000 certs
```

Start the cluster :

```bash
docker-compose up -d
```

Wait about 30 seconds and run `securityadmin` to initialize the security plugin :

```bash
docker-compose exec os01 bash -c "chmod +x plugins/opensearch-security/tools/securityadmin.sh && bash plugins/opensearch-security/tools/securityadmin.sh -cd /usr/share/opensearch/config/opensearch-security/ -icl -nhnv -cacert config/certificates/ca/ca.pem -cert config/certificates/ca/admin.pem -key config/certificates/ca/admin.key -h localhost"
```

> Find all the configuration files in the container's `/usr/share/opensearch/plugins/opensearch-security/securityconfig` directory. You might want to [mount them as volumes](https://opendistro.github.io/for-elasticsearch-docs/docs/install/docker-security/).

Access OpenSearch Dashboards through [https://localhost:5601](https://localhost:5601)

Default username is `admin` and password is `admin`

> Take a look at [OpenSearch's internal users documentation](https://opensearch.org/docs/security-plugin/configuration/yaml/) to add, remove or update a user.


## Why OpenSearch

- Fully open source (including plugins)
- Fully under Apache 2.0 license
- Advanced security plugin (free)
- Alerting plugin (free)
- Allows you to [perform SQL queries against ElasticSearch](https://opendistro.github.io/for-elasticsearch-docs/docs/sql/)
- Maintained by AWS and used for its cloud services
