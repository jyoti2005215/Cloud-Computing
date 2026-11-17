# Cloud Computing Lab -- Experiment 5: Cloud Scheduling

## Experiment Details

-   **Course:** CSE12045 -- Cloud Computing Lab
-   **Experiment:** 5
-   **Title:** Implementation of Various Scheduling Mechanisms Using an
    Open-Source Cloud Simulator
-   **Simulator/library:** CloudSim Plus
-   **Build tool:** Apache Maven
-   **Programming language:** Java 17
-   **Operating system used:** Ubuntu

## Aim

To implement and compare various scheduling mechanisms using the
open-source CloudSim Plus cloud simulator.

## Software Requirements

-   Ubuntu Linux
-   OpenJDK 17 (JDK)
-   Apache Maven
-   Git (optional, for version control)
-   Internet connection for downloading Maven dependencies

## 1. Update Ubuntu

Refresh the package index and install available updates:

``` bash
sudo apt update
sudo apt upgrade -y
```

## 2. Install Java, Maven and Git

``` bash
sudo apt install openjdk-17-jdk maven git -y
```

Verify the installations:

``` bash
java -version
javac -version
mvn -version
git --version
```

Java and `javac` should report version 17.x. Maven should also show that
it is using Java 17.

## 3. Create the CloudSim Plus Maven Project

Create the standard Maven source directory:

``` bash
mkdir -p ~/CloudSchedulingLab/src/main/java/lab
cd ~/CloudSchedulingLab
```

Create the Maven configuration file:

``` bash
nano pom.xml
```

Use the following `pom.xml`:

``` xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>lab</groupId>
    <artifactId>cloud-scheduling-lab</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <maven.compiler.release>17</maven.compiler.release>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.cloudsimplus</groupId>
            <artifactId>cloudsimplus</artifactId>
            <version>8.5.5</version>
        </dependency>
    </dependencies>
</project>
```

Resolve the project dependencies and clean previous build output:

``` bash
mvn dependency:resolve
mvn clean
```

## 4. Implement the Scheduling Experiment

Create the Java source file:

``` bash
nano src/main/java/lab/SchedulingExperiment.java
```

Add the complete Java program provided in the Experiment 5 lab manual.
Preserve the `package lab;` declaration and the class and method names.

### Simulation Configuration

The program defines the following simulation parameters:

  Parameter                          Value
  -------------------------------- -------
  Host processing elements (PEs)         4
  Number of virtual machines             2
  Number of cloudlets                    4
  MIPS per PE / VM                    1000
  Cloudlet length                    20000

The simulation creates a datacenter, a broker, virtual machines and
cloudlets. It runs four combinations of VM scheduling and cloudlet
scheduling.

### Scheduling Scenarios

  Scenario   VM scheduler   Cloudlet scheduler
  ---------- -------------- --------------------
  A          SpaceShared    SpaceShared
  B          SpaceShared    TimeShared
  C          TimeShared     SpaceShared
  D          TimeShared     TimeShared

The program prints a result table for the finished cloudlets, including
cloudlet ID, VM ID, status, start time and finish time. It also prints
the number of completed cloudlets for each scenario.

## 5. Compile and Execute

### Compile the project

``` bash
mvn clean compile
```

This compiles the Java source code and reports compilation errors, if
any.

### Run the experiment

``` bash
mvn exec:java -Dexec.mainClass="lab.SchedulingExperiment" -Dexec.classpathScope=compile
```

If Maven does not resolve the Exec plugin prefix, use the fully
qualified command:

``` bash
mvn org.codehaus.mojo:exec-maven-plugin:3.5.0:java \
  -Dexec.mainClass="lab.SchedulingExperiment" \
  -Dexec.classpathScope=compile
```

## 6. Expected Output

The program should display four scenario headings:

1.  A: VM SpaceShared + Cloudlet SpaceShared
2.  B: VM SpaceShared + Cloudlet TimeShared
3.  C: VM TimeShared + Cloudlet SpaceShared
4.  D: VM TimeShared + Cloudlet TimeShared

For each scenario, the output table contains:

-   Cloudlet
-   VM
-   Status
-   Start
-   Finish

The program also reports the number of completed cloudlets out of four.
Actual execution times and scheduling results should be taken from the
output generated on the system; they are not specified in this README.

## Project Structure

``` text
CloudSchedulingLab/
├── pom.xml
└── src/
    └── main/
        └── java/
            └── lab/
                └── SchedulingExperiment.java
```

Maven creates the `target/` directory during compilation. It contains
generated build output and does not need to be maintained manually.

## Result

The experiment implements four combinations of VM-level and
cloudlet-level scheduling mechanisms using CloudSim Plus. The simulation
output can be used to observe cloudlet allocation, execution status,
start time and finish time under each scheduling combination.

## References

-   CloudSim Plus: https://cloudsimplus.org/
-   Apache Maven: https://maven.apache.org/
-   OpenJDK: https://openjdk.org/
