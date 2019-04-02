# Cryptoz Setup Steps

### Prerequisites:
#### Download binary zip file for Cassandra from :
```
http://www.apache.org/dyn/closer.lua/cassandra/3.11.4/apache-cassandra-3.11.4-bin.tar.gz
```
(Minimum version needed 3.11.x)

#### To configure Cassandra execute following commands :
```
mkdir -p ~/opt/packages && cd $_
curl -O http://mirrors.koehn.com/apache/cassandra/3.7/apache-cassandra-3.7-bin.tar.gz
gzip -dc apache-cassandra-3.7-bin.tar.gz | tar xf -
ln -s ~/opt/packages/apache-cassandra-3.7 ~/opt/cassandra
mkdir -p ~/opt/cassandra/data/data
mkdir -p ~/opt/cassandra/data/commitlog
mkdir -p ~/opt/cassandra/data/saved_caches
mkdir -p ~/opt/cassandra/logs

```
#### Start cassandra once configured successfully using:
```
sudo cassandra -R
```
#### In cassandra.yaml, change the field `authenticator` value to `PasswordAuthenticator`.

#### Use cqlsh to create a superuser with username `ecurrencyindex` and password as `App4ever#`
```
$ cqlsh -u cassandra -p cassandra
cassandra@cqlsh> create user ecurrencyindex with password 'App4ever#' SUPERUSER;
```

#### 4 repositories should be cloned. Create a folder and `cd` to it. Then execute following commands:
  ```
  git clone https://ap-qio:admin#2017@github.com/ap-qio/cryptoz-collection.git
  git clone https://ap-qio:admin#2017@github.com/ap-qio/casandradatalayer.git
  git clone https://ap-qio:admin#2017@github.com/ap-qio/cryptoz-defaultproperties.git
  git clone https://ap-qio:admin#2017@github.com/ap-qio/cryptoz-uiserver.git
  ```

#### Create tables in the database using `ecurrencyindex.cql` as source file (present inside cryptoz-defaultproperties), with fully specified path:
```
SOURCE 'full/path/to/eCurrencyIndex.cql'; 
```

#### cluster name in cassandra.properties is set as `ecurrencyindex`. Set it as the name of the cluster in cassandra:
```
UPDATE system.local SET cluster_name = ‘ecurrencyindex’ where key='local';
```

#### Default entries should be entered in tables (cryptoz_indexes_one_minute & update_time_stamp). Execute these queries in cqlsh with newly created user credentials.
```
insert into update_time_stamp (type, flag, time_stamp)
values
('              Cryptoz-EW-25' , 'last_success' , '2019-04-01 05:45:00.000+0000');
```

#### To setup project with required jars, use maven as:
```
mvn clean install
```

#### Execution begins from main.java. Set up the command line arguments and VMarguments:
Commandline argument contains fully qualified path name of default.properties 
VMarguments contains setting time zone as GMT `-Duser.timezone=GMT`
