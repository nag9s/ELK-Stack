
1. 4 node cluster with 3 master nodes ( also data nodes) , 1 data node
   Set sudo sysctl -w vm.max_map_count=262144 to avoid the error "max virtual memory areas". See Known Issues.
2. cd playArea
3. docker-compose up
4. open dev tools
   GET _cat/nodes?v
5. shutdown active master, after sometime other master elgible node will become master
	 docker-compose stop elasticsearch2  (elasticsearch 2 is the current master node )
	 http://167.179.101.121:9200/_cat/nodes?v
	 

   
   docker-compose stop elasticsearch3

need to fix logsstah instance
	- with volume exposed
	- able to run the logstash instance with the config file modified, source file
	
	logstash:
    image: 'docker.elastic.co/logstash/logstash:6.8.12'
    container_name: logstash
    environment:
      elasticsearch.hosts: http://elasticsearch:9200,http://elasticsearch2:9200
    ports:
      - '5601:5601'
    networks:
      - esnet
    depends_on:
      - elasticsearch
      - elasticsearch2
      - elasticsearch3
      - elasticsearch4
      
Known Issues
1. max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]
	https://stackoverflow.com/questions/57998092/docker-compose-error-bootstrap-checks-failed-max-virtual-memory-areas-vm-ma

Troubleshooting
1. Variables ingested - you can see that elasticsearch.hosts is ingested.
 training@training-virtualbox:~/playArea$ docker container exec -it kibana sh
sh-4.2$ ps -aef | cat
UID          PID    PPID  C STIME TTY          TIME CMD
kibana         1       0  6 06:59 ?        00:00:13 /usr/share/kibana/bin/../node/bin/node --no-warnings --max-http-header-size=65536 /usr/share/kibana/bin/../src/cli --cpu.cgroup.path.override=/ --cpuacct.cgroup.path.override=/ --elasticsearch.hosts=["http://elasticsearch1:9200","http://elasticsearch2:9200"]
kibana       452       0  0 07:02 pts/0    00:00:00 sh
kibana       457     452  0 07:03 pts/0    00:00:00 ps -aef
kibana       458     452  0 07:03 pts/0    00:00:00 cat
