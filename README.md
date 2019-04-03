# Cryptoz 

To setup cryptoz on your local machine, follow the steps in sequential order.

- Prerequisites
- Setting up Cassandra
- Cloning Repositories
- Execution Steps



### Prerequisites

1. Download Cassandra binary zip file from following link : [Download Link](http://www.google.com)

   > **Note ** : For Cassandra minimum version required is 3.10.x+

2. After the download completes, unzip the contents using:

   ```shell
   $> tar -xvf apache-cassandra-3.11.x.bin.tar.gz
   ```

3. Move the extracted folder to `~/opt/packages` and create a symbolic link as follows:

   ```shell
   $> mkdir -p ~/opt/packages
   $> mv apache-cassandra-3.11.x.bin.tar.gz ~/opt/packages
   $> ln -s ~/opt/packages/apache-cassandra-3.11.x ~/opt/cassandra
   ```

4. Create following folders, to store cassandra session related data

   ```shell
   $> mkdir -p ~/opt/cassandra/data/data
   $> mkdir -p ~/opt/cassandra/data/commitlog
   $> mkdir -p ~/opt/cassandra/data/saved_caches
   $> mkdir -p ~/opt/cassandra/logs
   ```

5. Add Cassandra to your path by adding the following to `~/.bash_profile`.

   ```shell
   # include locally installed Cassandra in PATH
   if [ -d "$HOME/opt" ]; then
       PATH="$PATH:$HOME/opt/cassandra/bin"
   fi
   ```

6. Open `cassandra.yaml` file

   ```shell
   $> nano ~/opt/cassandra/conf/cassandra.yaml
   ```

7. Locate the following code snippet in the file and change the field value for ``authenticator`` as ``PasswordAuthenticator``. 

   ```yaml
   - AllowAllAuthenticator performs no checks - set it to disable authentication.
   # - PasswordAuthenticator relies on username/password pairs to authenticate
   #   users. It keeps usernames and hashed passwords in system_auth.roles table.
   #   Please increase system_auth keyspace replication factor if you use this authenticator.
   #   If using PasswordAuthenticator, CassandraRoleManager must also be used (see below)
   authenticator: PasswordAuthenticator
   ```

8. Set the cluster name as `ecurrencyindex` in `cassandra.yaml` file.

   ```yaml
   # The name of the cluster. This is mainly used to prevent machines in
   # one logical cluster from joining another.
   cluster_name: 'ecurrencyindex'
   ```

   

### Setting up Cassandra

1. Start cassandra 

   ```shell
   $> sudo cassandra -R
   ```

2. After it starts, a prompt will be returned. Start `cqlsh` to verify that `cassandra` is running properly.

   ```shell
   $> cqlsh -u cassandra -p cassandra
   ```

   > **Note** : Default username and password is `cassandra`

3. If everything works fine, prompt will change to something like

   ```shell
   cqlsh> 
   ```

4. Create a new user with username and password.

   ```shell
   cqlsh> create user username with password 'password';
   ```

   > **Note**: Remember the username and password. They will be required in later steps.

5. Exit the current session and start `cqlsh`, with newly created user fields.

   ```shell
   $> cqlsh -u username -p password
   ```

6. Update the cluster name to the `ecurrenyindex` from the command-line.

   ```shell
   cqlsh> UPDATE system.local SET cluster_name = ‘ecurrencyindex’ where key='local';
   ```



### Cloning Repositories

1. Clone following packages from github

   ```shell
   $> git clone https://ap-qio:admin#2017@github.com/ap-qio/cryptoz-collection.git
   $> git clone https://ap-qio:admin#2017@github.com/ap-qio/casandradatalayer.git
   $> git clone https://ap-qio:admin#2017@github.com/ap-qio/cryptoz-defaultproperties.git
   ```

2. In **cassandradatalayer **, locate cassandra.properties file and open it.

   ```shell
   $> nano /casandradatalayer/src/main/resources/cassandra.properties
   ```

   > **Note**: This file contains confiugration details for cassandra. Hence, following fields will be set.

   ```properties
   cassandra.cluster.name=ecurrencyindex   # cluster name specified in Prerequisites step 8
   cassandra.cluster.username=username     # username of new user
   cassandra.cluster.password=password     # password corresponding to that user.
   cassandra.keyspace.name=keyspace_name   # specify the keyspace_name to be used.
   ```

3. Inside the keyspace `keyspace_name`, execute `ecurrencyindex.cql` script to create schemas.

   ```shell
   $> cqlsh -u username -p password
   ecurrencyindex@cqlsh> use keyspace_name;
   ecurrencyindex@cqlsh:collection_1> 
   ```

4. Execute `ecurrencyindex.cql` script to create schemas.

   ```shell
   ecurrencyindex@cqlsh:collection_1> source SOURCE '~/casandradatalayer/CassandraSchema/eCurrencyIndex.cql'
   ```

5. After loading all the required schemas, starting point values need to be added to the tables. Execute `init.cql`.

   ```shell
   ecurrencyindex@cqlsh:collection_1> source '~/casandradatalayer/CassandraSchema/init.cql'
   ```



#### Execution Step

1. To remove already compiled binaries, and to install required binaries, use maven for each package:

   ```shell
   $> mvn clean
   ```

2. Execution of code begins from main.java. Command-line arguments and VMarguments are to be passed as arguments.

   ```shell
   $> cd ~/cryptoz-defaultproperties
   $> mvn clean install
   $> mvn compile
   $> mvn exec:java -Dexec.mainClass="src.main.java.com.cryptoz.main.Main" -Dexec.args="~/cryptozdefaults/src/main/resources/default.properties" -Duser.timezone=GMT
   ```
