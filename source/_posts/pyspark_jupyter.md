title: Setting up pyspark

date: 2015-08-01

categories:
- spark
tags:
- python
- spark
- ipython
- jupyter


---

Spark is a general-purpose cluster computing system.

In a previous {% post_link setting_up_pyspark.md [post] %}, I showed how to setup spark with ipython.

The ipython-notebook went through some changes such that the previous setup, didn't work for me.

Here are my notes for getting it to work, assuming a clean install

Download a version of spark with a package type of pre-built for hadoop 2/6 or later
```
wget http://www.apache.org/dyn/closer.lua/spark/spark-1.5.0/spark-1.5.0-bin-hadoop2.6.tgz
```
Unzip folder
```
tar xvf spark-1.5.0-bin-hadoop2.6.tgz
```
Set environment variables Spark Home and update python path

```
nano ~/.bashrc

export SPARK_HOME=~spark-1.5.0-bin-hadoop2.6
export PATH=$SPARK_HOME/bin:$PATH

export PYTHONPATH=$SPARK_HOME/python/:$PYTHONPATH
export PYTHONPATH=$SPARK_HOME/python/lib/py4j-0.8.2.1-src.zip:$PYTHONPATH

```

Test  if thing works

Start ipython-notebook
```
ipython notebook
```

Below is some setup code

```
#import statements
from pyspark import  SparkContext
from pyspark import SparkConf

#spark conf
conf = ( SparkConf()
         .setMaster('local[*]')
         .setAppName('pyspark')
        )

sc = SparkContext(conf=conf)


```
Change log level.
By default,  spark logs by level of INFO, which can include a lot of info
```
sc.setLogLevel('WARN')

```

Naive Prime testes
```
def isprime(n):
    """
    check if integer n is a prime
    """
    # make sure n is a positive integer
    n = abs(int(n))
    # 0 and 1 are not primes
    if n < 2:
        return False
    # 2 is the only even prime number
    if n == 2:
        return True
    # all other even numbers are not primes
    if not n & 1:
        return False
    # range starts with 3 and only needs to go up the square root of n
    # for all odd numbers
    for x in range(3, int(n**0.5)+1, 2):
        if n % x == 0:
            return False
    return True

# Create an RDD of numbers from 0 to 1,000,000
nums = sc.parallelize(xrange(1000000))

# Compute the number of primes in the RDD
print nums.filter(isprime).count()

```

If everything works, the output should be 78498
