# ElasticSearch AWS

### Servers

**AWS Console:** https://eu-west-1.console.aws.amazon.com/es/home?region=eu-west-1

**elasticsearch-aws-71**
- Console: https://eu-west-1.console.aws.amazon.com/es/home?region=eu-west-1#domain:resource=elasticsearch-aws-71;action=dashboard;tab=undefined
- CloudWatch: https://eu-west-1.console.aws.amazon.com/cloudwatch/home?region=eu-west-1#dashboards:name=Datanode_JsdqtWtmQOiHYuv-susxSw_elasticsearch-aws-71

**elasticsearch-aws-67-public**
- user: root | pass: Demo!234
- Console: https://eu-west-1.console.aws.amazon.com/es/home?region=eu-west-1#domain:resource=elasticsearch-aws-67-public;action=dashboard;tab=undefined
- Kibana: https://search-elasticsearch-aws-67-public-xjylk6lmk4pbd6zex5oa3uiauu.eu-west-1.es.amazonaws.com/_plugin/kibana/app/kibana#/management/kibana/index?_g=()


### Tutorials and Links

- Setup Guide
  - https://docs.aws.amazon.com/elasticsearch-service/latest/developerguide/es-gsg-create-domain.html
- Best practices for configuring your Amazon Elasticsearch Service domain
    - https://aws.amazon.com/blogs/big-data/best-practices-for-configuring-your-amazon-elasticsearch-service-domain/
- Best practices for Amazon Elasticsearch Service
    - https://docs.aws.amazon.com/elasticsearch-service/latest/developerguide/aes-bp.html
- Creating index snapshots in Amazon Elasticsearch Service
    - https://docs.aws.amazon.com/elasticsearch-service/latest/developerguide/es-managedomains-snapshots.html
- Amazon Elasticsearch Service now supports VPC
  - https://aws.amazon.com/blogs/aws/amazon-elasticsearch-service-now-supports-vpc/
- Living Maps Solution Writeup
  - https://www.evernote.com/shard/s206/sh/47c18ff9-a6f0-41b7-8456-c00041f1a762/aeb75ce08d8f37b9f4c4d0f010266152

- Reindex before upgrading
  - https://www.elastic.co/guide/en/elasticsearch/reference/current/reindex-upgrade-remote.html
  - https://www.elastic.co/guide/en/elasticsearch/reference/current/reindex-upgrade.html


### Upgrade Process

I've previously gone through the process for both v2 -> v5 (more complex) and v5 -> v6 (less issues). However, I had the luxury of being familiar with my own codebase and servers whilst performing the upgrade.

There are three things that need to be looked at:
- ElasticSearch server configuration
- ElasticSearch schema files
- ElasticSearch queries in the codebase

The complexity of the process really depends on how many items in the breaking changes documentation affect you.

The major breaking change in v6 is the removal of having multiple mapping types per index. If your existing code used this feature, then this might require a bit of an architectural rethink about your design.

The basic process is to be systematic, read through all the breaking change documentation, line by line, going from your current v5.2 -> v6.x -> v7.11. For each item, check to see if it's a feature you have been using. Using grep or find-in-all-files will sometimes give you a quick answer.

The other useful approach is to attempt the v5->v6 then v6->v7 upgrade on a development server, and let ElasticSeach tell you if it sees any errors. You will want to rerun any unit tests you have, and possibly also have a round of manual testing.

- https://www.elastic.co/guide/en/elasticsearch/reference/5.0/breaking-changes-5.0.html
- https://www.elastic.co/guide/en/elasticsearch/reference/6.0/breaking-changes-6.0.html
- https://www.elastic.co/guide/en/elasticsearch/reference/7.0/breaking-changes-7.0.html

If you have persistent data, you will need to upgrade from v5 -> v6 and then v6 -> v7. Each upgrade will require a full cluster restart and downtime

for reindexing. If you have a data pipeline that can simply reload everything from an external data source into an empty ElasticSearch index, then you might be able to jump directly to v7.

I would also recommend testing this process in development first, with a branch of your codebase, before touching your production servers. Also ensure you have a working backup of your data.

A few questions:
- What is your current usecase for ElasticSearch?
- How large is your data and cluster?
- How sensitive are you to downtime?
- Do you have the ability to recreate everything in ElasticSearch from an external data-source/pipeline? Or are you storing user/application information not available elsewhere?
- Do you have an existing development team working on your project?


### TODO

- Create example schema for v2, v5, v6, v7
