# HiBench Suite #
## The bigdata micro benchmark suite ##


- Current version: 4.0
- Release date: 2015-4-30
- Contact: [Lv Qi](mailto:qi.lv@intel.com), [Grace Huang](mailto:jie.huang@intel.com), [Jiangang Duan] (mailto:jiangang.duan@intel.com)
- Homepage: https://github.com/intel-hadoop/HiBench

- Contents:
  1. Overview
  2. Getting Started
  3. Advanced Configuration
  4. Possible Issues

---
### OVERVIEW ###

This benchmark suite contains 10 typical micro workloads. This benchmark suite also has options for users to enable input/output compression for most workloads with default compression codec (zlib). Some initial work based on this benchmark suite please refer to the included ICDE workshop paper (i.e., WISS10_conf_full_011.pdf).

Note: 
 1. Since HiBench-2.2, the input data of benchmarks are all automatically generated by their corresponding prepare scripts.
 2. Since HiBench-3.0, it introduces Yarn support
 3. Since HiBench-4.0, it consists of more workload implementations on both Hadoop MR and Spark. For Spark, three different APIs including Scala, Java, Python are supportive.

  **Micro benchmarks:**

1. Sort (sort)

    This workload sorts its *text* input data, which is generated using RandomTextWriter.

2. WordCount (wordcount)

    This workload counts the occurrence of each word in the input data, which are generated using RandomTextWriter. It is representative of another typical class of real world MapReduce jobs - extracting a small amount of interesting data from large data set.
	
3. TeraSort (terasort)

    TeraSort is a standard benchmark created by Jim Gray. Its input data is generated by Hadoop TeraGen example program.

4. Sleep (sleep)

    This workload sleep an amount of seconds in each task to test framework scheduler.

  **SQL:**

5. Scan (scan), Join(join), Aggregate(aggregation)

    This workload is developed based on SIGMOD 09 paper "A Comparison of Approaches to Large-Scale Data Analysis" and HIVE-396. It contains Hive queries (Aggregation and Join) performing the typical OLAP queries described in the paper. Its input is also automatically generated Web data with hyperlinks following the Zipfian distribution.

  **Web Search Benchmarks:**

6. PageRank (pagerank)

    This workload benchmarks PageRank algorithm implemented in Spark-MLLib/Hadoop (a search engine ranking benchmark included in pegasus 2.0) examples. The data source is generated from Web data whose hyperlinks follow the Zipfian distribution.
	
7. Nutch indexing (nutchindexing)

    Large-scale search indexing is one of the most significant uses of MapReduce. This workload tests the indexing sub-system in Nutch, a popular open source (Apache project) search engine. The workload uses the automatically generated Web data whose hyperlinks and words both follow the Zipfian distribution with corresponding parameters. The dict used to generate the Web page texts is the default linux dict file /usr/share/dict/linux.words.

  **Machine Learning:**

9. Bayesian Classification (bayes)

    This workload benchmarks NaiveBayesian Classification implemented in Spark-MLLib/Mahout examples.

    Large-scale machine learning is another important use of MapReduce. This workload tests the Naive Bayesian (a popular classification algorithm for knowledge discovery and data mining)  trainer in Mahout 0.7, which is an open source (Apache project) machine learning library. The workload uses the automatically generated documents whose words follow the zipfian distribution. The dict used for text generation is also from the default linux file /usr/share/dict/linux.words.

10. K-means clustering (kmeans)

    This workload tests the K-means (a well-known clustering algorithm for knowledge discovery and data mining) clustering in Mahout 0.7/Spark-MLlib. The input data set is generated by GenKMeansDataset based on Uniform Distribution and Guassian Distribution.

  **HDFS Benchmarks:**

10. enhanced DFSIO (dfsioe)

    Enhanced DFSIO tests the HDFS throughput of the Hadoop cluster by generating a large number of tasks performing writes and reads simultaneously. It measures the average I/O rate of each map task, the average throughput of each map task, and the aggregated throughput of HDFS cluster. Note: this benchmark doesn't have Spark corresponding implementation.
    
**Supported hadoop/spark release:**

  - Apache release of Hadoop 1.x and Hadoop 2.x
  - CDH4/CDH5 release of MR1 and MR2.
  - Spark1.2
  - Spark1.3

---
### Getting Started ###

1. System setup.

     Setup JDK, Hadoop-YARN, Spark runtime environment properly.

     Download/checkout HiBench benchmark suite

     Run `<HiBench_Root>/bin/build-all.sh` to build HiBench.
      
     Note: Begin from HiBench V4.0, HiBench will need python 2.x(>=2.6) .

2. HiBench Configurations.

     For minimum requirements: create & edit `conf/99-user_defined_properties.conf`：
     
          cd conf 
          cp 99-user_defined_properties.conf.template 99-user_defined_properties.conf
     
     And Make sure below properties has been set:

          hibench.hadoop.home      The Hadoop installation location
          hibench.spark.home       The Spark installation location
          hibench.hdfs.master      HDFS master
          hibench.spark.master     SPARK master
	  
     Note: For YARN mode, set `hibench.spark.master` to `yarn-client`. (`yarn-cluster` is not supported yet)

3. Run

   Execute the `<HiBench_Root>/bin/run-all.sh` to run all workloads with all language APIs with `large` data scale.

4. View the report:
   
   Goto `<HiBench_Root>/report` to check for the final report:
      - `report/hibench.report`: Overall report about all workloads.
      - `report/<workload>/<language APIs>/bench.log`: Raw logs on client side.
      - `report/<workload>/<language APIs>/monitor.html`: System utilization monitor results.
      - `report/<workload>/<language APIs>/conf/<workload>.conf`: Generated environment variable configurations for this workload.
      - `report/<workload>/<language APIs>/conf/sparkbench/<workload>/sparkbench.conf`: Generated configuration for this workloads, which is used for mapping to environment variable.
      - `report/<workload>/<language APIs>/conf/sparkbench/<workload>/spark.conf`: Generated configuration for spark.
      
   [Optional] Execute `<HiBench root>/bin/report_gen_plot.py report/hibench.report` to generate report figures.

   Note: `report_gen_plot.py` requires `python2.x` and `python-matplotlib`.

---
### Advanced Configurations ###

1. Parallelism, memory, executor number tuning:

          hibench.default.map.parallelism       Mapper numbers in MR, 
                                                partition numbers in Spark
          hibench.default.shuffle.parallelism   Reducer numbers in MR, shuffle 
                                                partition numbers in Spark
          hibench.yarn.executors.num            Number executors in YARN mode
          hibench.yarn.executors.cores          Number executor cores in YARN mode 
          spark.executors.memory                Executor memory, standalone or YARN mode
          spark.driver.memory                   Driver memory, standalone or YARN mode

   Note: All `spark.*` properties will be passed to Spark runtime configuration.

2. Compress options:

          hibench.compress.profile              Compression option `enable` or `disable`
          hibench.compress.codec.profile        Compression codec, `snappy`, `lzo` or `default`
     
3. Data scale profile selection:

          hibench.scale.profile                 Data scale profile, `tiny`, `small`, `large`, `huge`, `gigantic`, `bigdata`
                                                  
   You can add more data scale profiles in `conf/10-data-scale-profile.conf`. And please don't change `conf/00-default-properties.conf` if you have no confidence.

4. Configure for each workload or each language API:

     1. All configurations will be loaded in a nested folder structure:

              conf/*.conf                                         Configure globally
              workloads/<workload>/conf/*.conf                    Configure for each workload
              workloads/<workload>/<language APIs>/.../*.conf     Configure for various languages

     2. For configurations in same folder, the loading sequence will be
     sorted according to configure file name. 

     3. Values in latter configure will override former.

     4. The final values for all properties will be stored in a single
     config file located at `report/<workload><language APIs>/conf/<workload>.conf`,
     which contain all values and pinpoint the source of the configures.

5. Configure for future Spark release

   By default, `bin/build-all.sh` will build HiBench for all running
   environments:

          - MR1, Spark1.2
          - MR1, Spark1.3
          - MR2, Spark1.2
          - MR2, Spark1.3

   And HiBench will probe Hadoop & Spark release version and choose proper HiBench release automatically. However, for furture Spark release (for example, Spark1.4) which is API compatibled with Spark1.3. HiBench'll fail due to lack the profile. You can define Hadoop/Spark release version by setting to force HiBench using Spark1.3 profile:

          hibench.spark.version          spark1.3

6. Configures for running workloads and language APIs:
  
  The `conf/benchmarks.lst` file under the package folder defines the
  workloads to run when you execute the `bin/run-all.sh` script under
  the package folder. Each line in the list file specifies one
  workload. You can use `#` at the beginning of each line to skip the
  corresponding bench if necessary.

  You can also run each workload separately. In general, there are 3
  different files under one workload folder.

      prepare/prepare.sh            Generate input data in HDFS for
                                    running the benchmark
      mapreduce/bin/run.sh          run MapReduce language API
      spark/java/bin/run.sh         run Spark/java language API
      spark/scala/bin/run.sh        run Spark/scala language API
      spark/python/bin/run.sh       run Spark/python language API


---
### Possible issues ###

1. Running Spark/Python API with YARN:

   Due to [SPARK-1703](https://issues.apache.org/jira/browse/SPARK-1703) and [SPARK-1911](https://issues.apache.org/jira/browse/SPARK-1911), Oracle-JDK-1.6 is preferred.

2. Running with CDH/MR1:

   For a tarball deployed CDH/MR1, please recreate symlink file `hadoop-*-cdh*/share/hadoop/mapreduce` to point to correct folder:

          cd share/hadoop
          rm mapreduce
          ln -s mapreduce1 mapreduce

3. Running Spark/Python, MLLib related workloads:

   You'll need to install numpy (version > 1.4) in master & all slave nodes.

   For CentOS(6.2+):
     
     `yum install numpy`

   For Ubuntu/Debian:

     `aptitude install python-numpy`

4. Execute `<HiBench_Root>/bin/report_gen_plot.py`:

   You'll need to install python-matplotlib(version > 0.9).

   For CentOS(6.2+):
     
     `yum install python-matplotlib`

   For Ubuntu/Debian:

     `aptitude install python-matplotlib`



