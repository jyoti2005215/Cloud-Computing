# Experiment 9: Develop MapReduce Application Using Hadoop Cluster Setup

**Course:** Cloud Computing Lab  
**Experiment:** 9  
**Student:** Suvankar Pramanik  
**Programme:** B.Tech – Computer Science and Engineering  
**Institution:** Adamas University

---

## 1. Aim

To set up a Hadoop cluster in a Linux environment and develop, execute, and verify a MapReduce application using the Hadoop Distributed File System (HDFS) and Hadoop MapReduce framework.

## 2. Introduction

Apache Hadoop is an open-source framework for distributed storage and processing of large datasets.

Its main components are:

- **HDFS:** Distributed file storage.
- **YARN:** Cluster resource management and application scheduling.
- **MapReduce:** Distributed data processing.
- **Hadoop Common:** Shared libraries and utilities.

This experiment uses a **single-node pseudo-distributed cluster**, with Hadoop daemons running as separate processes on one Ubuntu virtual machine.

## 3. Software and Environment

| Component | Installed/configured value |
|---|---|
| Operating system | Ubuntu 26.04 LTS |
| Architecture | x86_64 |
| Java | OpenJDK 17.0.21 |
| Hadoop | Apache Hadoop 3.5.0 |
| Cluster mode | Single-node pseudo-distributed |
| Hadoop directory | `/usr/local/hadoop` |
| Working directory | `~/cloud-lab9` |
| Default HDFS URI | `hdfs://localhost:9000` |
| HDFS replication factor | `1` |

## 4. Task 1 – Hadoop Cluster Setup

### 4.1 Java verification

Java and the Java compiler were checked:

```bash
java -version
javac -version
```

The installed version was OpenJDK 17.0.21.

### 4.2 Hadoop installation

The Hadoop 3.5.0 archive was downloaded, extracted, and moved to `/usr/local/hadoop`:

```bash
mkdir -p ~/hadoop-download
cd ~/hadoop-download
wget https://dlcdn.apache.org/hadoop/common/hadoop-3.5.0/hadoop-3.5.0.tar.gz
tar -xzf hadoop-3.5.0.tar.gz
sudo mv hadoop-3.5.0 /usr/local/hadoop
sudo chown -R $USER:$USER /usr/local/hadoop
```

Installation was verified using:

```bash
/usr/local/hadoop/bin/hadoop version
```

The terminal reported **Hadoop 3.5.0**.

### 4.3 Environment variables

The following variables were added to `~/.bashrc`:

```bash
export JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64
export HADOOP_HOME=/usr/local/hadoop
export HADOOP_HDFS_HOME=$HADOOP_HOME
export HADOOP_MAPRED_HOME=$HADOOP_HOME
export HADOOP_YARN_HOME=$HADOOP_HOME
export HADOOP_COMMON_HOME=$HADOOP_HOME
export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
```

The changes were loaded and checked:

```bash
source ~/.bashrc
echo $JAVA_HOME
echo $HADOOP_HOME
which hadoop
hadoop version
```

The Java path was also configured in `$HADOOP_HOME/etc/hadoop/hadoop-env.sh`:

```bash
export JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64
```

### 4.4 Hadoop configuration files

The following configuration was applied.

**`core-site.xml`**

```xml
<configuration>
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://localhost:9000</value>
    </property>
</configuration>
```

This sets the default HDFS filesystem.

**`hdfs-site.xml`**

```xml
<configuration>
    <property>
        <name>dfs.replication</name>
        <value>1</value>
    </property>
    <property>
        <name>dfs.namenode.name.dir</name>
        <value>file:///usr/local/hadoop/data/namenode</value>
    </property>
    <property>
        <name>dfs.datanode.data.dir</name>
        <value>file:///usr/local/hadoop/data/datanode</value>
    </property>
</configuration>
```

This configures the replication factor and the NameNode and DataNode storage directories. The directories were created and assigned to the current user:

```bash
mkdir -p /usr/local/hadoop/data/namenode
mkdir -p /usr/local/hadoop/data/datanode
sudo chown -R $USER:$USER /usr/local/hadoop/data
```

**`mapred-site.xml`**

```xml
<configuration>
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>
    <property>
        <name>mapreduce.application.classpath</name>
        <value>$HADOOP_MAPRED_HOME/share/hadoop/mapreduce/*:$HADOOP_MAPRED_HOME/share/hadoop/mapreduce/lib/*</value>
    </property>
    <property>
        <name>yarn.app.mapreduce.am.env</name>
        <value>HADOOP_MAPRED_HOME=/usr/local/hadoop</value>
    </property>
    <property>
        <name>mapreduce.map.env</name>
        <value>HADOOP_MAPRED_HOME=/usr/local/hadoop</value>
    </property>
    <property>
        <name>mapreduce.reduce.env</name>
        <value>HADOOP_MAPRED_HOME=/usr/local/hadoop</value>
    </property>
</configuration>
```

This configures MapReduce to use YARN and provides the MapReduce runtime classpath and environment to the ApplicationMaster, Mapper, and Reducer containers.

**`yarn-site.xml`**

```xml
<configuration>
    <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
    </property>
    <property>
        <name>yarn.nodemanager.aux-services.mapreduce_shuffle.class</name>
        <value>org.apache.hadoop.mapred.ShuffleHandler</value>
    </property>
    <property>
        <name>yarn.nodemanager.env-whitelist</name>
        <value>JAVA_HOME,HADOOP_COMMON_HOME,HADOOP_HDFS_HOME,HADOOP_CONF_DIR,CLASSPATH_PREPEND_DISTCACHE,HADOOP_YARN_HOME,HADOOP_HOME,PATH,LANG,TZ,HADOOP_MAPRED_HOME</value>
    </property>
</configuration>
```

This enables the MapReduce shuffle service and passes `HADOOP_MAPRED_HOME` through the NodeManager environment whitelist.

### 4.5 Worker node and SSH

The worker configuration file `$HADOOP_HOME/etc/hadoop/workers` contains:

```text
localhost
```

The SSH service was enabled:

```bash
sudo systemctl enable --now ssh
```

An Ed25519 key was generated and its public key was added to `authorized_keys`:

```bash
ssh-keygen -t ed25519
cat ~/.ssh/id_ed25519.pub >> ~/.ssh/authorized_keys
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

Passwordless local SSH was tested using `ssh localhost`. The connection succeeded without requesting the Ubuntu account password.

**Task 1 result:** Java and Hadoop were installed; environment variables, Hadoop XML files, worker configuration, and passwordless SSH were configured and verified.

## 5. Task 2 – HDFS Configuration and Operations

### 5.1 NameNode formatting

The NameNode was formatted for the initial setup:

```bash
hdfs namenode -format
```

The terminal confirmed that `/usr/local/hadoop/data/namenode` was successfully formatted and created the `current` metadata directory.

**Caution:** NameNode formatting is an initialisation operation. Do not repeat it on an existing HDFS installation because it can remove existing filesystem metadata.

### 5.2 Start HDFS and YARN

HDFS and YARN were started using:

```bash
start-dfs.sh
start-yarn.sh
```

The processes were checked using:

```bash
jps
```

The five Hadoop daemons were observed:

- NameNode
- DataNode
- SecondaryNameNode
- ResourceManager
- NodeManager

The YARN worker node was checked using:

```bash
yarn node -list
```

One node was reported in the `RUNNING` state.

After the Ubuntu virtual machine was restarted, the HDFS and YARN services were started again. The `jps` output again showed all five daemons.

### 5.3 Create the HDFS input directory

```bash
hdfs dfs -mkdir -p /user/student/cloud-lab9/input
hdfs dfs -ls /user/student/cloud-lab9
hdfs dfs -ls -d /user/student/cloud-lab9/input
```

The HDFS directory `/user/student/cloud-lab9/input` was created and verified.

### 5.4 Create sample input data

A local working directory and `input.txt` file were created:

```bash
mkdir -p ~/cloud-lab9
cd ~/cloud-lab9
```

The following six-line dataset was saved:

```text
cloud computing provides scalable resources
cloud computing uses distributed systems
hadoop processes large datasets
hadoop provides distributed storage
mapreduce processes data in parallel
mapreduce is used for large scale data processing
```

The file was checked:

```bash
cat input.txt
wc -l input.txt
```

The line count was **6**.

### 5.5 Upload and verify the HDFS file

```bash
hdfs dfs -put input.txt /user/student/cloud-lab9/input/
hdfs dfs -ls /user/student/cloud-lab9/input
hdfs dfs -cat /user/student/cloud-lab9/input/input.txt
```

The upload succeeded. HDFS reported a file size of **240 bytes**, and the file contents matched the local dataset.

**Task 2 result:** HDFS was formatted and started; the experiment directory was created; and the six-line input file was uploaded and read successfully from HDFS.

## 6. Task 3 – MapReduce Application Development

### 6.1 WordCount Java application

A Java source file named `WordCount.java` was created in `~/cloud-lab9`.

The program implements:

- **TokenizerMapper:** Tokenises each input line into words, converts each word to lowercase, and emits `(word, 1)` key-value pairs.
- **IntSumReducer:** Adds the values associated with each word and emits `(word, frequency)`.
- **Combiner:** Uses the reducer class for intermediate aggregation before the final reduce phase.

### 6.2 Compile the Java application

The Hadoop classpath was exported and the Java program was compiled:

```bash
export HADOOP_CLASSPATH=$(hadoop classpath)
javac -classpath "$HADOOP_CLASSPATH" -d . WordCount.java
```

Compilation completed without errors and generated:

```text
WordCount.class
WordCount$TokenizerMapper.class
WordCount$IntSumReducer.class
```

### 6.3 Create the JAR file

```bash
jar -cvf wordcount.jar *.class
jar -tf wordcount.jar
```

The `wordcount.jar` archive was created successfully and contained the WordCount, TokenizerMapper, and IntSumReducer class files.

**Task 3 result:** The Java WordCount application compiled successfully and was packaged into `wordcount.jar`.

## 7. Task 4 – MapReduce Execution and Result Analysis

### 7.1 MapReduce runtime configuration correction

The first MapReduce attempt failed because YARN could not load:

```text
org.apache.hadoop.mapreduce.v2.app.MRAppMaster
```

The MapReduce application classpath and `HADOOP_MAPRED_HOME` settings were added to `mapred-site.xml`. The `HADOOP_MAPRED_HOME` variable was also added to the NodeManager environment whitelist in `yarn-site.xml`.

The `MRAppMaster` class was verified inside:

`/usr/local/hadoop/share/hadoop/mapreduce/hadoop-mapreduce-client-app-3.5.0.jar`

YARN was restarted after the configuration changes.

### 7.2 Execute the MapReduce job

The previous output directory was checked/removed before the successful run:

```bash
hdfs dfs -rm -r /user/student/cloud-lab9/output
```

The WordCount job was submitted:

```bash
cd ~/cloud-lab9

hadoop jar wordcount.jar WordCount /user/student/cloud-lab9/input /user/student/cloud-lab9/output
```

The successful job reported:

```text
map 100% reduce 100%
Job job_1794832494344_0001 completed successfully
```

### 7.3 Job execution statistics

| Metric | Observed result |
|---|---:|
| Job ID | `job_1794832494344_0001` |
| Job name | `word count` |
| Job state | SUCCEEDED |
| Map tasks launched | 1 |
| Reduce tasks launched | 1 |
| Map input records | 6 |
| Map output records | 31 |
| Combine output records | 22 |
| Reduce output records | 22 |
| HDFS input bytes read | 366 |
| HDFS output bytes written | 209 |

The MapReduce JobHistory page was opened successfully. It showed the job state as `SUCCEEDED`, with one Map task and one Reduce task completed.

### 7.4 Verify output files in HDFS

The output directory was listed:

```bash
hdfs dfs -ls /user/student/cloud-lab9/output
```

The following files were present:

```text
_SUCCESS
part-r-00000
```

The reducer output was displayed using:

```bash
hdfs dfs -cat /user/student/cloud-lab9/output/part-r-00000
```

### 7.5 Actual WordCount results

| Word | Frequency |
|---|---:|
| cloud | 2 |
| computing | 2 |
| data | 2 |
| datasets | 1 |
| distributed | 2 |
| for | 1 |
| hadoop | 2 |
| in | 1 |
| is | 1 |
| large | 2 |
| mapreduce | 2 |
| parallel | 1 |
| processes | 2 |
| processing | 1 |
| provides | 2 |
| resources | 1 |
| scalable | 1 |
| scale | 1 |
| storage | 1 |
| systems | 1 |
| used | 1 |
| uses | 1 |

**Result analysis:**

- Input records: 6
- Total word occurrences: 31
- Unique words: 22
- Map tasks completed: 1
- Reduce tasks completed: 1
- Final job state: SUCCEEDED

The actual reducer output matches the supplied six-line input dataset.

## 8. Additional Practical Activities – Status

The lab PDF specifies additional activities after the successful MapReduce job. The status below distinguishes completed work from activities still requiring practical verification.

| Activity | Required work | Status |
|---|---|---|
| Task 4A – Hadoop Web Interfaces | Explore NameNode UI at `http://localhost:9870` and ResourceManager UI at `http://localhost:8088` | Pending practical verification |
| JobHistory | Open the successful job's history page using port `19888` | Completed; successful job page observed |
| Task 4B – HDFS File Blocks | Run `hdfs fsck /user/student/cloud-lab9/input/input.txt -files -blocks` | Pending |
| Task 4C – Basic HDFS Operations | List/read the input file, copy it to local storage, and delete a test file | Pending |
| Task 4D – Stop Hadoop Services | Stop YARN and HDFS, then verify using `jps` | Pending |

### Commands for the remaining activities

**Task 4A – Hadoop web interfaces**

- NameNode: `http://localhost:9870`
- ResourceManager: `http://localhost:8088`

**Task 4B – Examine HDFS file blocks**

```bash
hdfs fsck /user/student/cloud-lab9/input/input.txt -files -blocks
```

**Task 4C – Basic HDFS operations**

```bash
hdfs dfs -ls /user/student/cloud-lab9/input

hdfs dfs -cat /user/student/cloud-lab9/input/input.txt

hdfs dfs -get /user/student/cloud-lab9/input/input.txt downloaded.txt

cat downloaded.txt
```

The PDF also asks for deletion of a test file. Perform deletion only after all required observations are complete. Do not delete the original input file until the demonstration and verification are finished.

**Task 4D – Stop Hadoop services**

```bash
stop-yarn.sh
stop-dfs.sh
jps
```

After shutdown, the Hadoop daemons should no longer appear in the `jps` output.

## 9. Conclusion

A single-node pseudo-distributed Hadoop 3.5.0 environment was configured on Ubuntu 26.04 LTS using OpenJDK 17. HDFS and YARN services were started, a sample dataset was uploaded to HDFS, and a Java WordCount MapReduce application was developed, compiled, and packaged into a JAR file. The application was executed successfully, and its reducer output was verified against the original input. The successful job completed one Map task and one Reduce task and generated 22 distinct word-frequency records.

## 10. Suggested Project Folder Structure

```text
Experiment-9-Hadoop-MapReduce/
├── README.md
├── WordCount.java
├── wordcount.jar
├── input.txt
├── downloaded.txt
└── screenshots/
    ├── task-1-java-hadoop-installation/
    ├── task-1-hadoop-configuration/
    ├── task-1-ssh-verification/
    ├── task-2-hdfs-setup/
    ├── task-2-input-upload/
    ├── task-3-wordcount-code/
    ├── task-3-compilation-jar/
    ├── task-4-job-execution/
    ├── task-4-wordcount-output/
    ├── task-4a-web-interfaces/
    ├── task-4b-hdfs-blocks/
    ├── task-4c-hdfs-operations/
    └── task-4d-stop-services/
```

Add the actual screenshots captured during the practical. Add `downloaded.txt` after completing the HDFS download operation.
