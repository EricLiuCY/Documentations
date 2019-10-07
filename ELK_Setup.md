ELASTICSEARCH SETUP



STEP 1: Download Elastic Search

 

Run the following commands in sequence

 

wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.4.0-linux-x86_64.tar.gz

wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.4.0-linux-x86_64.tar.gz.sha512

shasum -a 512 -c elasticsearch-7.4.0-linux-x86_64.tar.gz.sha512

 

These commands downloads ElasticSearch,  It then verifies that the file downloaded isn't corrupted by comparing the SHA of the downloaded .tar.gz archive and the published checksum, which should output elasticsearch-{version}-linux-x86_64.tar.gz: OK.

 

Once the verification is complete and return Okay, run the following commands to unzip the tar file.

 

tar -xzf elasticsearch-7.4.0-linux-x86_64.tar.gz

cd elasticsearch-7.4.0/ 

 

The directory elasticsearch-7.4.0 is known as $ES_HOME

 

STEP 2: Edit config

 

By default, your instance of ElasticSearch only runs locally, and is not published. This means that you cannot access it using postman and what not for easier testing purposes. Thus, you need to edit some of the configurations. 

 

Run the following steps,

 

vim config/elasticsearch.yml
Find "Set the bind address to a specific IP (IPv4 or IPv6) : ", and set network.host to the IP address of your IPaddress.
Find "Bootstrap the cluster using an initial set of master-eligible nodes:" and set cluster.initial_master_nodes: ["node-1"]

STEP 3: Test that your server is live

 

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

 

Step 1: Checking Pre-reqa

Run: java-version.

Ensure that you have java installed, if not, install java.

 

Step 2: Install LogStash

 

Download and install the Public Signing Key:
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -

You may need to install the apt-transport-https package on Debian before proceeding:

sudo apt-get install apt-transport-https

Save the repository definition to /etc/apt/sources.list.d/elastic-6.x.list:

echo "deb https://artifacts.elastic.co/packages/6.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-6.x.lis


Use the echo method described above to add the Logstash repository. Do not use add-apt-repository as it will add a deb-src entry as well, but we do not provide a source package. If you have added the deb-src entry, you will see an error like the following:

Unable to find expected entry 'main/source/Sources' in Release file (Wrong sources.list entry or malformed file)

Just delete the deb-src entry from the /etc/apt/sources.list file and the installation should work as expected.

Run sudo apt-get update and the repository is ready for use. You can install it with:

sudo apt-get update && sudo apt-get install logstash



Step 3: Running LogStash

 

To run logsash, simply type the following commands.

sudo systemctl start logstash.service
