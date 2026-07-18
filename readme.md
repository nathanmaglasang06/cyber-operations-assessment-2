# Assesment 2: CarePlus Elastic/Kibana

## Docker Setup

This will work on a standard Ubuntu VM, I would reccomend 12gb ram, and 6 cores if possible, where on my arch system it takes around 10 gb ram to run including base OS

When you want to stop working there is no need to do docker down or anything, just suspend the ubuntu vm that will basically pause it in its current state, so you can start up again at the same spot

Hey all, to replicate my docker setup you will need the following structure, this repo is formatted as such so you will just need to clone it, instructions below

    .
    ├── docker-compose.yml
    ├── elasticsearch
    │   └── elasticsearch.yml
    ├── kibana
    │   └── kibana.yml
    ├── logs
    │   ├── careplus_elk_security_logs.jsonl
    │   └── careplus_logstash_pipeline.conf
    └── logstash
        ├── careplus_elk_security_logs.jsonl
        ├── careplus_logstash_pipeline.conf
        ├── config
        │   └── logstash.yml
        └── pipeline
            └── logstash.conf

## Update System

The first step is to update system:

> sudo apt update

> sudo apt upgrade

This might take like 1-2 mins depending on internet speeds

### Install Dependecies

> sudo apt install docker.io docker-compose-v2 curl git -y

### Enable Docker

> sudo systemctl enable docker

> sudo systemctl start docker

### Verify docker and docker compose

> sudo docker --version

> sudo docker compose version

Should return some version numbers

### Test docker

> sudo docker ps

this command will show all running containers, similar to regular ps in the terminal, ps stands for process

Now to actuall get all the files i mentioned above, you will need to clone this github repository, which will basically create a local copy on your machine
To do this first verify git is installed

> git --version

then clone the repo

> git clone https://github.com/nathanmaglasang06/cyber-operations-assessment-2.git

Navigate into the cloned repo: 

> cd cyber-operations-assessment-2

then run the following command to start all the docker containers

> sudo docker compose up -d

This might take upwards of 5 minutes depending on your computer, if it takes any longer than say 20 min something is failing

Once that compose command has completed run a docker ps, to verify

- There are 3 containers
- They all say healthy

> docker ps

Example output from my system: 

    CONTAINER ID   IMAGE                                                  COMMAND                  CREATED       STATUS                 PORTS                                                                                                                                                                                NAMES
    100fcb10840d   docker.elastic.co/kibana/kibana:8.17.4                 "/bin/tini -- /usr/l…"   7 hours ago   Up 7 hours (healthy)   0.0.0.0:5601->5601/tcp, [::]:5601->5601/tcp                                                                                                                                          careplus-kibana
    180069b80ced   docker.elastic.co/logstash/logstash:8.17.4             "/usr/local/bin/dock…"   7 hours ago   Up 7 hours (healthy)   0.0.0.0:5000->5000/tcp, [::]:5000->5000/tcp, 0.0.0.0:5044->5044/tcp, [::]:5044->5044/tcp, 0.0.0.0:9600->9600/tcp, 0.0.0.0:5000->5000/udp, [::]:9600->9600/tcp, [::]:5000->5000/udp   careplus-logstash
    0de810bfdd4e   docker.elastic.co/elasticsearch/elasticsearch:8.17.4   "/bin/tini -- /usr/l…"   7 hours ago   Up 7 hours (healthy)   0.0.0.0:9200->9200/tcp, [::]:9200->9200/tcp, 0.0.0.0:9300->9300/tcp, [::]:9300->9300/tcp                                                                                             careplus-elasticsearch

## Elastic Search

Now in your browser (inside the VM) go to: 

> http://localhost:9200

You should see some JSON with the first line being name: careplus-es-node-01, with the tagline at the bottom saying "You Know, For Search", this means that the Elastic Search backend is setup 

Now we need to verify that elastic search can see the log files provided by the assessment, to do this run:

> curl "http://localhost:9200/careplus-security-logs/_count?pretty"

you should again get some JSON, here is what I get, where the count means that there have been 615 events recorded from the log files


     {
      "count" : 615,
       "_shards" : {
         "total" : 1,
         "successful" : 1,
         "skipped" : 0,
         "failed" : 0
       }
     }

## Kibana

Now that elastic search is verified we can open kibana
Again in your browser go to: 

> http://localhost:5601

It may take a minute or two on first attempt

## Creating Data View 

To create the Data View, on the kibana home page click the hamburger (3 horizontal lines) on the top left, scroll down to the bottomo and navigate to stack management
On the left hand side under the heading kibana click on Data Views
Create a Data View using the button on the top right using the following info

> Name: careplus-security-logs

> Index Pattern: careplus-security-logs

> Timestamp: @timestamp

> (For timestamp, you should jsut be able to open the dropdown and select it)

Now select save data view to Kibana, the blue button at the bottom

## Verify Data View

Click the hamburger on the top left again and scroll to the top
Under analytics you should see discover, click that then you should be able to see all the logs available, along with a little graph at the top

Thats it you should have it all setup now.


