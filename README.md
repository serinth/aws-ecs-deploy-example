# AWS ECS Devops

> Amazon EC2 Container Service (Amazon ECS) is a highly scalable, fast, container management service that makes it easy to run, stop, and manage Docker containers on a cluster of Amazon Elastic Compute Cloud (Amazon EC2) instances.
> - [Amazon ECS](http://docs.aws.amazon.com/AmazonECS/latest/developerguide/Welcome.html)

# Core Concepts

## Task definition

Task definition describes how the docker containers are run. How many containers, how they're linked, ports and IAM roles etc.
See [the task definition documentation](http://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_defintions.html) for more info.

Containers in tasks need to manage port conflicts. Persistent data on EBS volumes is not recommended as they do not travel across nodes. Containers should be dumb and very simple in the tasks they do. Calls to other services should be used that need to persist data.

## Service definition

Service definition exposes the task or set of tasks as a service. Usually this is done behind a load balancer which distributes incoming traffic across the tasks.

This implies that tasks are homogeneous and are deployed per node. 

For example:
  - a task has 2 containers A and B
  - a service autoscales with a minimum of 2 nodes and a max of 4 nodes
  - each node looks like this node[A, B] node[A, B] -- this is the minimum set
  - services round robin requests to each node


# Service Discovery
ECS does not have service discovery built in. The use of an external service discovery tool is needed such as Consul or Etcd.
We can achieve a static endpoint if we use Elastic Load balancers with known domain names via Route53.

# Deploying via Stackup

```
docker run --rm -v $PWD/ops:/app/ops -e AWS_REGION=ap-southeast-2 -e AWS_ACCESS_KEY_ID=<ACCESS KEY> -e AWS_SECRET_ACCESS_KEY='<SECRET ACCESS KEY>' realestate/stackup simple-app up -t /app/ops/ecs-template.yml

```


# References
[Build Pipeline With Jenkins](https://aws.amazon.com/blogs/devops/set-up-a-build-pipeline-with-jenkins-and-amazon-ecs/)
[Cloudformation Template](http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/quickref-ecs.html#quickref-ecs-example-1.yaml)
