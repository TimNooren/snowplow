# snowplow


1. Download the repository as a ZIP
2. Extract and upload contents to S3
3. In AWS, Create a stack in Cloudformation
4. In "Specify an Amazon S3 template URL", enter the location of snowplow_template.json you just uploaded to S3
5. Click next and fill in the parameters. Most of them are pretty self-explanatory, except for:
	- BuildBucket/BuildBucketPath: the bucket/path in S3 you uploaded the contents of snowplow_cloudformation.zip to
	- SubnetsPrivate: do not have to private, but maybe they should be. All Elastic Beanstalk instances are launched in these subnets
	- SubnetsPublic: has to be public since the loadbalancer for the collector will be launched in these subnets
	- TargetBucket/TargetBucketPath: the bucket/path that the firehose stream will point at. Firehose provides 15 minute interval backups of the enriched stream
6. Click next (optionally specify tags that are propagated to all resources)
7. Click next and tick the box stating "I acknowledge that AWS CloudFormation might create IAM resources with custom names."
8. Click create stack

The stack will create 2 beanstalk environments (collector/enricher), 4 kinesis streams, a few IAM roles, a firehose stream, two lambda functions and a redis cluster.
One lambda function is responsible for pushing data to Redis. Right now it pushes URLs of page_view events based on user_id.
The enricher will create a DynamoDB by itself. If you delete the stack all resources will be deleted as well, except for the DynamoDB since it was created
outside of the Cloudformation process. You will have to delete the table manually.