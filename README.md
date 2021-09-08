# Elasticsearch Snapshot Using MinIO

## MinIO Configuration

Create a bucket for the Elasticsearch repository using [MinIO client](https://docs.min.io/docs/minio-client-quickstart-guide.html)

```bash
mc alias set your_remote_minio_alias [your_minio_host] [your_minio_access_key] [your_minio_secret_key]
mc mb your_remote_minio_alias/your_elasticsearch_bucket
```

## Elasticsearch Configuration

To make Elasticsearch works with MinIO, we need to install [the repo plugin](https://www.elastic.co/guide/en/elasticsearch/plugins/7.14/repository-s3.html) and add various keystores. To achieve this, we simply put these inside the `Dockerfile`

```bash
FROM docker.elastic.co/elasticsearch/elasticsearch:7.14.0

RUN bin/elasticsearch-plugin install --batch repository-s3

RUN echo "your_minio_access_key" | bin/elasticsearch-keystore add --stdin --force s3.client.default.access_key
RUN echo "your_minio_secret_key" | bin/elasticsearch-keystore add --stdin --force s3.client.default.secret_key
```

Make sure the `access_key` and the `secret_key` are the same as the earlier step.

Now we have our `Dockerfile`, then just build the image.

After the image built, we need to configure our `elasticsearch.yml`

```yml
cluster.name: "docker-cluster"
network.host: 0.0.0.0

s3.client.default.endpoint: "your_minio_host"
s3.client.default.protocol: "http"
```

Finally, run your Elasticsearch container.

## Main Configuration

Register our Elasticsearch snapshot repo:

```curl
curl --location --request PUT 'http://[your_es_host]:[your_es_port]/_snapshot/[your_elasticsearch_repo]' \
--header 'Content-Type: application/json' \
--data-raw '{
    "type": "s3",
    "settings": {
        "bucket": "your_elasticsearch_bucket"
    }
}'
```
