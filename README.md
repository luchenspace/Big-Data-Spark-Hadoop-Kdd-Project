# Big-Data-Spark-Hadoop-Kdd-Project

This document describes how to run the Decision Tree and Logistic Regression programs using Apache Spark on ECS.

=================================================
1. Prerequisites
=================================================
- Access to an ECS machine (e.g., CO246A-1)
- Dataset file: kdd.data
- Code files: decision_tree.py and logistic_regression.py
- Spark environment correctly configured on the ECS node

=================================================
2. Python Dependencies
=================================================
The following Python modules are required and are preinstalled on ECS:

- argparse
- time
- numpy
- pyspark (including pyspark.sql and pyspark.ml)

If testing locally, install with:
    pip install numpy pyspark

=================================================
3. Setup Instructions
=================================================

(1) SSH into the ECS node:
    ssh CO246A-1

(2) Navigate to your working directory:
    cd /path/to/your/project

(3) Load the Spark environment:
    source SetupSparkClasspath.sh

(4) Upload Python scripts to HDFS:
    hdfs dfs -put decision_tree.py logistic_regression.py /user/<your_username>/inputs

(5) Ensure the dataset is located at:
    /user/<your_username>/inputs/kdd.data

=================================================
4. Running the Programs
=================================================

Option 1: Run both models sequentially:

    spark-submit --master yarn decision_tree.py \
        --input hdfs:///user/<your_username>/inputs/kdd.data \
        --output hdfs:///user/<your_username>/output/dt_result && \
    spark-submit --master yarn logistic_regression.py \
        --input hdfs:///user/<your_username>/inputs/kdd.data \
        --output hdfs:///user/<your_username>/output/lr_result

Option 2: Run models individually:

    spark-submit --master yarn decision_tree.py \
        --input hdfs:///user/<your_username>/inputs/kdd.data \
        --output hdfs:///user/<your_username>/output/output_minimal

    spark-submit --master yarn logistic_regression.py \
        --input hdfs:///user/<your_username>/inputs/kdd.data \
        --output hdfs:///user/<your_username>/output/output_minimal

=================================================
5. Output
=================================================
Each program performs 10 runs with different random seeds and writes results as a CSV file to the specified HDFS output directory.

To view the result:
    hdfs dfs -cat <output_path>/part-*.csv

To download the result:
    hdfs dfs -get <output_path>

=================================================
6. Managing HDFS Files (Optional)
=================================================

To list the files in your HDFS directory:

    hdfs dfs -ls /user/<your_username>/inputs

To replace a file (delete first, then upload the new version):

    hdfs dfs -rm /user/<your_username>/inputs/decision_tree.py
    hdfs dfs -put decision_tree.py /user/<your_username>/inputs

    hdfs dfs -rm /user/<your_username>/inputs/logistic_regression.py
    hdfs dfs -put logistic_regression.py /user/<your_username>/inputs

Note: The `-rm` command is required because `hdfs dfs -put` does not automatically overwrite existing files.

=================================================
End of Readme
=================================================
