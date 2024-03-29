# Homework 2

## Information

| Object | Value |
| :---: | :---: |
|Name | Parth Deshpande|
| UIN | 657711378 |
| Email | [pdeshp8@uic.edu](mailto:pdeshp8@uic.edu)
| YouTube Link (AWS EMR) | [YouTube](https://www.youtube.com/watch?v=saT0XpBJd0M&t=3s)


## Explanation
The project involves basic log processing using mappers and reduces to extract the necessary and appropriate information from the log files.

## Important Files
Name | Description |
| :---: | :---: |
|[build.sbt](build.sbt) | Includes all dependencies of the project and versioning info |
|[mr1.scala](src/main/scala/com/parth/scala/mr1.scala) / [mr2.scala](src/main/scala/com/parth/scala/mr2.scala) / [mr3.scala](src/main/scala/com/parth/scala/mr3.scala) / [mr4.scala](src/main/scala/com/parth/scala/mr4.scala) | Classes executing individual functionalities of the project |
|[application.conf](src/main/resources/application.conf) | Includes parameters used while executing classes |
|[logback.xml](src/main/resources/logback.xml) | Used to set up logging |
|[configTest.scala](src/test/scala/com/parth/scala/configTest.scala) | ScalaTest class used to perform basic tests |

## Instructions
1. Clone/ download the project from the repository.
2. Open cmd/terminal and build the project using `sbt clean compile assembly`
3. The jar file is generated here [target/scala-3.0.2/MapperReducer3-assembly-0.1.jar](target/scala-3.0.2/MapperReducer3-assembly-0.1.jar)
4. Create clusters on AWS/hortonworks/other.
5. Copy the jar file in the cluster OS, execute it using `hadoop jar <jar file> <class name>`
   1. Here, the class name can either be 'com.parth.scala.mr<1/2/3/4>'

## AWS Instructions
1. Register/ login to [AWS](https://aws.amazon.com)
2. Setup billing notifications (Optional)
3. Configure your security groups to allow traffic from your local machine IP.
4. Create a S3 bucket and upload the [jar file](target/scala-3.0.2/MapperReducer3-assembly-0.1.jar) in it.
5. Set a key value pair to be used to SSH
6. Create an EMR cluster using the key created in the previous step.
7. Once it is deployed (Status: Waiting), transfer the log files to `/user/hadoop/input` (can be configured in [application.conf](src/main/resources/application.conf))
8. Create a step and add the jar by using the `s3:.....` address.
9. Once the executing is completed and the status is shown as `completed`, the output files can be extracted from `/user/hadoop/mr<1/2/3/4>/output`.

## Classes
### 1. [mr1.scala](src/main/scala/com/parth/scala/mr1.scala)
This class is used to implement `the distribution of different types of messages across predefined time intervals and injected string instances of the designated regex pattern for these log message types`.
The execution is divided into a chain of two mapper-reducer pairs. The first mapreduce is used to filter out all the log messages and group them according to the predefined time interval duration. This results in an output with the first column being an abstract group number and second column holding the count of matching strings in that specific time interval.
The second mapreduce is used to convert the abstract group number back into the starting timestamp of that specific time interval.

Class | Use
:---: | :---:
Mapper1 | Pattern match and assign groups to each log message
Reducer1 | Find sum of matching strings group wise
Mapper2 | Convert abstract group numbers to timestamp values
Reducer2 | Pass

#### Output
Column | Name | Description
| :---: | :---:| :---: |
1 | Time Stamp | The starting timestamp of that interval
2 | Log Level | The logging level considered for this value
3 | Count | Count of the matching strings

### 2. [mr2.scala](src/main/scala/com/parth/scala/mr2.scala)
This class is used to return `the time intervals sorted in the descending order that contained most log messages of the type ERROR with injected regex pattern string instances`.
The execution is divided in two sets of mapreduce pairs as before, however, the second map reduce job also implements a `WritableComparator` which is used to sort the output in descending order instead of ascending.

Class | Use
:---: | :---:
Mapper1 | Pattern match and find error level log messages
Reducer1 | Find sum of matching strings group wise
Mapper2 | Convert abstract group numbers to timestamp values
Comparator | Sort in descending order of number of matches
Reducer2 | Pass

#### Output
Column | Name | Description
| :---: | :---:| :---: |
1 | Count | Count of the matching strings
2 | Time Stamp | The starting timestamp of that interval


### 3. [mr3.scala](src/main/scala/com/parth/scala/mr3.scala)
This class is used to implement `for each message type you will produce the number of the generated log messages`.
The execution uses a mapreduce job to split messages using their log level.

Class | Use
:---: | :---:
Mapper1 | Split log messages based on their log level as the key
Reducer1 | Find sum of log messages for each type

#### Output
Column | Name | Description
| :---: | :---:| :---: |
1 | Log Level | The logging level considered for this value
2 | Count | Count of the strings in that level

### 3. [mr4.scala](src/main/scala/com/parth/scala/mr4.scala)
This class is used to return `the number of characters in each log message for each log message type that contain the highest number of characters in the detected instances of the designated regex pattern.`.
The execution uses a mapreduce job to find the maximum number of characters for each log message matching the predefined regex pattern.

Class | Use
:---: | :---:
Mapper1 | Split log messages based on their log level as the key and their length as the value if they match the predefined regex pattern
Reducer1 | Find maximum length of string for each group

#### Output
Column | Name | Description
| :---: | :---:| :---: |
1 | Log Level | The logging level considered for this value
2 | Count | Maximum length of matching string for this log level

## Sample Results
### Parameters used 

Parameter | Value
:---: | :---:
Time interval length | 2 seconds
Input file | [Log File](results/input_log.log)
Input files location (Hortonworks/AWS) | `/user/hadoop/input`
Output files location (Hortonworks/AWS) | `/user/hadoop/mr<1/2/3/4>/output`

### Log Generator Parameters

Parameter | Value
:---: | :---:
Pattern Frequency | 0.25

### Results
1. mr1 : [Here](results/output-r-00000_mr1)
2. mr2 : [Here](results/part-r-00000_mr2)
3. mr3 : [Here](results/part-r-00000_mr3)
4. mr4 : [Here](results/part-r-00000_mr4)