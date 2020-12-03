# ParallelComputeLab- Amazon Top100 Reviewers

## Execution steps:
Compile Java code:
javac -cp /opt/cloudera/parcels/CDH/lib/hadoop/client/*:/opt/cloudera/parcels/CDH/lib/hbase/* AmazonTopReviewers.java -d build -Xlint

wrap up  code into a Java "jar" file: jar -cvf process_topReviewers.jar -C build/ .

delete the output folder if it already exists: hadoop fs -rm -r /user/rprakas5/top_reviewers

execute the map-reduce job:
HADOOP_CLASSPATH=$(hbase mapredcp):/etc/hbase/conf:process_topReviewers.jar hadoop jar process_topReviewers.jar AmazonTopReviewers '/user/rprakas5/top_reviewers'

redirect all output to reviewersOutput.txt:
hadoop fs -cat /user/rprakas5/top_reviewers/* > reviewersOutput.txt

                                // This code below post-processes the output by sorting and filtering to the top 100 rows
                                // bash shell commands, taken from https://unix.stackexchange.com/questions/10524/how-to-numerical-sort-by-last-column
                                // awk '{print $NF,$0}' q2_output.txt | sort -nr | cut -f2- -d' ' | head -100 > q2.txt

Print top100 reviewers:
awk '{print $NF,$0}' reviewersOutput.txt | sort -nr | cut -f2- -d' ' | head -100 > top100Reviewers.txt

Print least100 reviewers:
awk '{print $NF,$0}' reviewersOutput.txt | sort -n | cut -f2- -d' ' | head -100 > leastReviewers.txt

download file to local:
pscp -P 22 rprakas5@dsba-hadoop.uncc.edu:/users/rprakas5/ParallelComputeLab/top100Reviewers.txt .
pscp -P 22 rprakas5@dsba-hadoop.uncc.edu:/users/rprakas5/ParallelComputeLab/reviewersOutput.txt .
