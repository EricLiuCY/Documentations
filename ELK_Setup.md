ELASTICSEARCH SETUP



STEP 1: Update system



Run the following commands in sequence



sudo apt-get update

sudo apt-get install default-jre



Checking the java version should return the following output or similar

openjdk version "1.8.0_151"
OpenJDK Runtime Environment (build 1.8.0_151-8u151-b12-0ubuntu0.16.04.2-b12)
OpenJDK 64-Bit Server VM (build 25.151-b12, mixed mode)



STEP 2: Installing elasticsearch nodes



First, get ElasticSearch's signing key

wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -



Then, install the necessary packages and add repo definition to system

sudo apt-get install apt-transport-https

echo "deb https://artifacts.elastic.co/packages/6.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-6.x.list



Lastly, install Elastic Search

sudo apt-get update 
sudo apt-get install elasticsearch



STEP 3: Edit config



By default, your instance of ElasticSearch only runs locally, and is not published. This means that you cannot access it using postman and what not for easier testing purposes. Thus, you need to edit some of the configurations. 



So run the command, vim config/elasticsearch.yml, and edit the files as follow



#give your cluster a name.
cluster.name: cloud-console

#give your nodes a name (change node number from node to node).
node.name: "node-1"

#enter the private IP and port of your node:
network.host: [Your VM IP HERE]
http.port: 9200

#detail the private IPs of your nodes:
discovery.zen.ping.unicast.hosts: [" [Your VM IP HERE]"]



STEP 4: Test that your server is live



To start ElasticSeach: run 

sudo service elasticsearch start



Then to test that the server is running, 

Run: curl https://[your_ip]:9200



You should receive something along the lines of:


{
"name" : "chenliu_tmp_dev",
"cluster_name" : "elasticsearch",
"cluster_uuid" : "lZGOC0CsRjyBy03pt22uCg",
"version" : {
"number" : "7.4.0",
"build_flavor" : "default",
"build_type" : "tar",
"build_hash" : "22e1767283e61a198cb4db791ea66e3f11ab9910",
"build_date" : "2019-09-27T08:36:48.569419Z",
"build_snapshot" : false,
"lucene_version" : "8.2.0",
"minimum_wire_compatibility_version" : "6.8.0",
"minimum_index_compatibility_version" : "6.0.0-beta1"
},
"tagline" : "You Know, for Search"
}





LOGSTASH SETUP:



Step 1: Checking Pre-req

Run: java-version.

Ensure that you have java installed, if not, install java.



Step 2: Install LogStash



Download and install the Public Signing Key:
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -

Add the repo definition
echo "deb https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-7.x.list
echo "deb https://artifacts.elastic.co/packages/oss-7.x/apt stable main" |sudo tee -a /etc/apt/sources.list.d/elastic-7.x.list


You may need to install the apt-transport-https package on Debian before proceeding:

sudo apt-get install apt-transport-https


Use the echo method described above to add the Logstash repository. Do not use add-apt-repository as it will add a deb-src entry as well, but we do not provide a source package. If you have added the deb-src entry, you will see an error like the following:

Unable to find expected entry 'main/source/Sources' in Release file (Wrong sources.list entry or malformed file)

Just delete the deb-src entry from the /etc/apt/sources.list file and the installation should work as expected.

Run sudo apt-get update and the repository is ready for use. You can install it with:

sudo apt-get update,  sudo apt-get install logstash



Step 3: Setting up conf files


A Typical logstash structure is like this
#/etc/logstash/conf.d/
- apache.conf
- haproxy.conf
- syslog.conf


in these conf files, you put the logstash configurations, such as 



input {
        file {
                path => "home/chenliu/cc2/cc_altus-platform/app/helpers/esUpdate/logstash/one/health/*"
                start_position => beginning
                sincedb_path => "/dev/null"
        }
}

filter {
        grok {
                match => { "message" => "\[%{HTTPDATE:timestamp}\] zone=>%{USERNAME:zone} cluster=>%{USERNAME:cluster} HOSTS_ON=>%{INT:HOSTS_ON:int} HOSTS_OFF=>%{INT:HOSTS_OFF:int} HOSTS_DISABLED=>%{INT:HOSTS_DISABLED:int} HOSTS_ERROR=>%{INT:HOSTS_ERROR:int} USED_CPU=>%{NUMBER:USED_CPU:float} FREE_CPU=>%{NUMBER:FREE_CPU:float} TOTAL_CPU=>%{NUMBER:TOTAL_CPU:float} MAX_CPU=>%{INT:MAX_CPU:int} CPU_USAGE=>%{INT:CPU_USAGE:int} USED_DISK=>%{INT:USED_DISK:int} FREE_DISK=>%{INT:FREE_DISK:int} MAX_DISK=>%{INT:MAX_DISK:int} DISK_USAGE=>%{INT:DISK_USAGE:int} USED_MEM=>%{INT:USED_MEM:int} FREE_MEM=>%{INT:FREE_MEM:int} TOTAL_MEM=>%{INT:TOTAL_MEM:int} MAX_MEM=>%{INT:MAX_MEM:int} MEM_USAGE=>%{INT:MEM_USAGE:int} RUNNING_VMS=>%{INT:RUNNING_VMS:int}"}
        }
        date {
                match => ["timestamp", 'dd/MMM/YYYY:HH:mm:ss Z']
                remove_field => ["timestamp"]
        }
}

output {
        #stdout { codec => rubydebug }
        elasticsearch {
                hosts => ["10.239.16.125:9200"]
                user => "kibana"
                password => "changeme"
                index => "one-clusters-%{+YYYY.MM}"
                template => "/etc/logstash/one-logstash.json"
                template_name => "one"
                template_overwrite => true
        }
}


The exact details of how to write these conf files can be better explained if you search on stackoverflow



Step 4: Running LogStash

 

To run logsash, simply type the following commands.

cd /usr/share/logstash

sudo systemctl start logstash



This should automatically run all the conf files stored in conf.d



If you want to monitor the logstash service, run:

sudo tail -f /var/log/logstash/logstash-plain.log




Downloading historical versions:



ElasticSearch 2.1.1

https://www.elastic.co/downloads/past-releases/elasticsearch-2-1-1



Logstash 2.3.4

https://www.elastic.co/downloads/past-releases/logstash-2-3-4



You can download historical java-versions through apt-get, and running

sudo update-alternatives --config java



Other Notes:

Some times when you want to explicitly run either logstash or elasticsearch, you should go to its respective folder located in /usr/share, and run it as ./bin/logstash or ./bin/elasticsearch















