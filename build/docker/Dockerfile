FROM docker.elastic.co/elasticsearch/elasticsearch:7.14.0

RUN bin/elasticsearch-plugin install --batch repository-s3

RUN echo "adminminio" | bin/elasticsearch-keystore add --stdin --force s3.client.default.access_key
RUN echo "adminminio" | bin/elasticsearch-keystore add --stdin --force s3.client.default.secret_key
