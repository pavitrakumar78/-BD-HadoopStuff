File systems  

-blocks


F11 F12 F13 F21
F22 F23 F24 F25
F51 F52 F41 F42
F43 F44 F53 F54



num of blocks = ceil(FILE_SIZE / blocksize)

File allocation table (FAT)
	
Full Format - Removes bit-by-bit
Quick Format - erases file allocation table (RECOVERABLE)


File Allocation table

NAME | STARTING_BLOCK | SIZE
F1	1
F2	4
F4	11
F5	9

Fundamentals of Distributed File Systems(DFS)

Workshop Only steps
	stop 
	clean
	make changes
	format
	start

	Fault tolerance
	Disaster Management

Backup problems
	-Inconsistency
	-Versioning

Rules:

1) Never keep multiple copies of a file in the same machine.

M0

M1	M2	M3	M4
	
M5	M6	M7	

#BLOCK
BLOCK_SIZE
REPLICATION_FACTOR=3

Assuming the machines at picked at random

NAME | BLOCK# | COPY# | MACHINE# | SIZE
F1	1	1	M4
F1	1	2	M3
F1	1	3	M7
F1	2	1	M2	
F1	2	2	M5
F1	2	3	M6	

M0 -> master
M1 - M7 -> Slaves

Master - Namenode (SERVER in Hadoop System) - ONLY ONE
Slave - Datanode runs in several machines (CLIENT)


1) Data Registration(ping)
2) HEART BEAT - Datanode communicates with the namenode (continuous registraition by the datanodes)
if there is no communication the data node is considered dead

  Ping visualization:
	"hi namenode i am datanode and i have this much free space"


Namenode maintains the metadata
Datanode maintains the actual data

we delete the table
datanode deletes the data
	
Unlike quick format, deleting the table deletes the data in dfs.

What happens if the namenode crashes?
	Secondary namenode will take care (command: jps)
	It compacts only edit logs
	So not completely recoverable

Namenode doesn't have backups

Namenode is central point of failure in hadoop 1.2.1
Fixed in later versions (hadoop 2.0)
	
	
Namenode is maintained in MAIN MEMORY (RAM)
	
Reboot will bring the Namenode from the backup
But if the backup is corrupted, data is irrecoverable


http://localhost:50070/dfshealth.jsp ---> front end of the namenode


Where should the namenode run is specified by the core-site.xml file 
i.e. 500070

dfstemp is the directory for namenode backup (internal temporary files)

hdfs-site has the property of dfs (file system)
directory where datanode should store the blocks
	
Live node = data node running currently

how to talk to the hadoop system
bin/hadoop <subsystem> -<cmd> args


Subsytems. two types
	-namenode
	-dfs

Number of under-replicated blocks should be zero
Default block size = 64 MB
Default replication factor = 3


	person1		person2		person3
	B,1		B,1		B,1
	B,1		B,1		B,1
	G,1		G,1		G,1
	Y,1		Y,1		Y,1
	Y,1		Y,1		Y,1
	

	INPUT RECORD - line from file

	map - Key, Value

	Copy record together
	Shuffle (appending keys together) (not arranging randomnly)
	Sort by key
	

	We get,

	Key, list of values - (output of map)
	This is given as an input to REDUCE

	Claculate the average temperature of a country (key - country)

		map
		
			values(4) = split input line by tab
			key = values(0)
			value = convertToNumber values(3)

			output(key, value)

		reduce

			sum = 0
			count = 0
			
			while (no more in list)
				get temperature from list
				sum = sum + temp
				count++

			output (key, sum/count)


	Calculate the average temperature of a zip in a country (key - Counntry, zip)

		map

			values(4) = split input line by tab
			key = concat values(0) values(1)
			value = convertToNumber values(3)

			output(key, value)

		reduce


hadoop/hadoop-1.2.1/playground/src$

map fucntion


----------------------------------------------------------------------------------------------------------

Day - 2


Dont bring data where you want to run the program.
Bring the program to where the data is.


Debug when less than 6 process are running

copy the path from when hadoop is started
change .out to .log

find the exception
correct it

job tracker is the central point of failure for the job processing in hadoop 1.2.1

Job tracker
the person responsible to distribute the task
the workers that actually take the code to their own bucket are called task tracker

only one job tracker
but many task tracker

if namenode crashes data is lost 
if job tracker crahes only the job processing part of the system crashes 

task tracker is a client.
registers to the job tracker
perform heart beat
if no reply marks the task tracker as dead.



Legend:
	Namenode(NN)	
	Secondary Namenode(SNN)		
	DataNode(DN)	
	JobTracker(JT)
	TaskTracker(TT)


	NN	SNN	DN1	DN2
			TT	TT

	DN3	DN4	DN5	DN6
	TT	TT	TT	TT

	DN7	DN8	DN9
	TT	TT	TT
	JT


Namenode's metadata table

	File	Block	Copy	Datanode
	
	F1	1	1	DN1
	F1	1 	2	DN6	(need not process the copies of same code by giving it to multiple datanodes)
	F1	1	3	DN4	(the mapreduce code is sent randomnly to one of the three datanodes)

	F1	2	1	DN7
	F1	2	2	DN8
	F1	2	3	DN2


Datanode and task tracker run int the ame machine (why? - therla)
others run on separate machine

jar is similar to a zip file
three classes are put in a jar
job tracker unzips and starts the process

how does the task tracker know where task tracker is running?
map-red-site
what information does it give?
hi jt this is tt3. i have a quad core processor and i can process three map tasks and four reduce task.

number of blocks

number of map tasks = number of blocks 
default processing capacity of a tt
2 map tasks and 2 reduce tasks

number of default reduce task capacity

for 4 MB (small block size) no of blocks increased and time increased
for 64 MB #blocks decreased and time decreased

reduction in the block size impacts the namenode



Commodity hardware are enough to process large data.


Increase reducer max tasks of this machine - mapred-site
     <property>
         <name>mapred.tasktracker.reduce.tasks.maximum</name>
         <value>4</value>
     </property>
machine dependent


Increase reducer max tasks of a job - mapred-site
     <property>
         <name>mapred.reduce.tasks</name>
         <value>4</value>
     </property>
not machine dependent
job dependent


Increase map max tasks of a job - mapred-site
     <property>
         <name>mapred.tasktracker.map.tasks.maximum</name>
         <value>4</value>
     </property>
machine dependent for map capacity


 #output files = #reduce task processes

order is not guaranteed in each of the output files


Hadoop small file problem
-------------------------
in dfs if the block size if 64MB
if 1000 input files are put and each is 1MB

what is the number of map tasks?

	datasize/blocksize = 16

hadoop does not compiles them together 
it is treated as an entire block


Case 1: 1 file 229 MB with 64 MB block size

Case 2:	1000 files 1 MB with 64 MB block size
every file is treated as a block
unnecesary, inefficient.

How to correct this?

Combine the files so that it is greater than or equal to the block size.


java version must be highes for task trackers than that of job trackers


Host names must be unique

Masters
-------
Namenode
Job trackers
Secondary namenode


To automate the master and slaves ip address
paste the ip address in slaves and master file in conf folder


DATA PIPELINE



http://localhost:50030/jobtracker.jsp	- MapReduce
http://localhost:50070/dfshealth.jsp	- Namenode

##Notes compiled by : Bharath Bhargav
