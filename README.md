# Motivation

I want to run marathon-mesos-deimos setup (http://beingasysadmin.wordpress.com/2014/06/27/managing-docker-clusters-using-mesos-and-marathon/) on CoreOS.

I was ispired by Ebay's building from docker using hosts docker setup (http://www.ebaytechblog.com/2014/05/12/delivering-ebays-ci-solution-with-apache-mesos-part-ii/#.U85a4nV53Ua).

I want to enjoy both CoreOS and mesos resource management, in future hopefully kubernetes (https://github.com/mesosphere/kubernetes-mesos).

# Install

 - Start CoreOS cluster
  - for testing I am using vagrant - http://coreos.com/docs/running-coreos/platforms/vagrant/
 - Logon to Cluster and add unit files
 - run units in following order (might work anyway if you change order, I just didn't try it yet):
  - zookeeper
  - mesos-master
  - marathon
  - mesos-slave
 - Test marathon API
  - curl -v -X POST -H "Content-Type: application/json" <marathon-IP>:8080/v2/apps -d@test.json
  - test.json:
``	{
	"id": "test",
	"container": {"image": "docker:///debian:jessie", "options" : []},
	"cmd": "while sleep 10; do date -u +%T; done",
	"cpus": "0.5",
	"mem": "268.0",
		"uris": [ ],
		"instances": "1"
	}``

# Architecture

 - Host OS
  - CoreOS - https://coreos.com/
   - Native Technology:
    - ETCD - key/value store 
    - Fleet - cluster level systemd
     - Used for triggering cluster apps - zookeeper, mesos-master/slave, marathon
    - Omaha update protocol - https://github.com/coreos/go-omaha
  - Mesos - Resource manager gathers resources
  - Marathon - Mesos framework for running docker images
  - Deimos - python Docker bindings for mesos-slave

# Features

 - Deploy app simply by specifying:
  - image name
  - cpu, ram
  - No. of instances
 - Fault tolerance - Environment will ensure that apps are running
 - Speed
  - Dev/Test
   - Create stack faster on demand (Ebay has minimal stack up in 10 minutes)
  - Prod
   - Faster Scaling - simply run more instances of app
 - Better resource utilization
  - Scale apps up/down on demand
   - http://blog.docker.com/2014/06/dockercon-video-cluster-management-and-containerization-at-twitter/

# TODO

 - [x] Crate docker images for
  - Zookeeper
  - Mesos-master
  - Mesos-slave
  - Marathon (mesos framework)
 - [ ] Run apps in HA 
  - [ ] Create pattern Unit files
   - [ ] Idealy using some tool (ansible...)
  - [ ] Set dynamic communication between services
   - probably using etcd


