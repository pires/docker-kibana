# This project is no longer maintained

As of November 7th, 2018, I've decided to end my commitment to maintaining this repo and related.

It's been 3 years since I last used Elasticsearch, so I no longer have the motivation it takes to maintain and evolve this project. Also, other projects need all the attention I can give.

It was a great run, **thank you all**.

# docker-kibana

This is [Kibana 6.3.1](https://github.com/elastic/kibana) in a minimal 184MB Docker image. Images are tagged by Kibana versions.

[![Docker Repository on Quay.io](https://quay.io/repository/pires/docker-kibana/status "Docker Repository on Quay.io")](https://quay.io/repository/pires/docker-kibana)

## Running

```
docker run --name kibana \
           --detach \
           --publish 5601:5601 \
           -e KIBANA_ES_URL=<elasticsearch url> \
           quay.io/pires/docker-kibana:6.3.1
```

You could set `KIBANA_INDEX` env variable to set an index for Kibana's data.

## Changing the [Base Path](https://www.elastic.co/guide/en/kibana/current/settings.html)
When changing the base path, kibana will re-optimize its resources during startup. This easily takes a few minutes and requires more memory than usual. To avoid this, build your own image with the follwing dockerfile: (note the `/kibana` base URL )

    FROM quay.io/pires/docker-kibana:6.3.1
    RUN sed -i "s;.*server\.basePath:.*;server\.basePath: /kibana;" /opt/kibana-${KIBANA_VERSION}/config/kibana.yml
    RUN  /opt/kibana-${KIBANA_VERSION}/bin/kibana 2>&1 | grep -m 1 "Optimization of .* complete in .* seconds"

Note that you can only access the resulting kibana via a reverse proxy. Config snippet for NGINX:

    <Location "/kibana/">
        ProxyPass http://kibana.ops:5601/ ttl=60 disablereuse=On retry=0
    </Location>

Config snippet for the `package.json` of a [react-scripts](https://www.npmjs.com/package/react-scripts) project:

    "proxy": {
      "/kibana/": {
        "target": "http://localhost:5601/",
        "ws": true,
        "pathRewrite": {
          "^/kibana": ""
        }
      }
    }
