
# Spatial Scaling

For many purposes is important to scale up or down a trace. Some times we have a lack of availability of posibles traces. Other times we want to pressure the system in a way the original trace not allow us. For that KV-replay provides a way in which we can spatial scale up or down a trace. To spatialy scale our trace KV-replay provides three Bash Scripts , each of them different and with a different aproach . Cloning script and intensifying script scales up the original trace. Subtracing script scale down the original trace. Each of them output a different trace based in the input trace. The explanation of the approach, how works, and examples of use is below.

## Format of the input trace

First of all the format the trace file should have, for the three scripts,  is these:
```
READ,1bhOE7xcZyg,1201639757.082532,965
READ,3TKi92CP-vc,1201639761.780669,2
READ,1bhOE7xcZyg,1201639762.360242,965
```
Were the first column is the **kind of operation** to do, the second is **the key that reference the object** in the database , the third is the **timestamp** , and the fourth is the **size of the object**. Also the separator that split the columns should be the coma.

To highlight that the scripts will work even if the fourth column, the object size, is not provided.

## Clone Scaling

### Explanation
Cloning is one of the ways to scale up the trace . For that it takes the original input trace and for each record , the script makes n copies of it , depending on the number of copies the user want. Each key of the record is refformated so each copy is unique. Is important to notice that , although each key is formatted, the original operation, timestamp and size of the record remains the same for each copy. The output trace will have n times the amount of records of the original trace.


### Description of how to use the script


The script has 3 **parameters** to enter. The first is the **original trace** file that we want to scale, the second is the **number of copies** we want to made, the third is the **name of the output file** that will be generated. 

### Example of use
```
./cloneScript.sh originalTraceFile.dat 4 originalTraceFile-scaled4times.dat
```


## SubTracing Scaling

### Explanation

Subtracing is a way in which we can scale down a trace. To accomplish that these script divides the original trace in n subtraces in which n is the numbers of divisons the user wants. The script then will generate n files , in which each of them is a subtrace with references to unique objects. That means that the references to one object will only remain in one of the subtraces. The sum of the records of all the subtraces will have the same amount of records as the original trace.


### Description of how to use the script

The script has 3 **parameters** to enter. The first is the **original trace file** that we want to scale, the second is the **number of subtraces** we want to made.

The third is a an optional value that **defines the way in which the algorithm work**. To define the subtraces the script use the keys of the trace and transform each of them into numbers. These parameter define the way that key is converted to a number. For that there are defined the values that these parameter can have: **sha, md5, tr, onlynumbers and ascii**. And each of them have different time performances. The order of time performance from best to worst is : onlynumbers, md5 , sha , tr and finally ascii. The variance of registries in each trace is nearly the same * . Is important to notice that parameter value only numbers only works when the trace keys are numbers. Also ascii works when the keys are composed by ascii characters and tr when keys are composed by alphanumeric characters or/and the symbols @%-_ . Default value is md5 .

After the trace is executed it will print the name of each subtrace and the number of records each of one has.

The name of each subtrace will have the next **format**:
```
$inputTrace-subtrace-$number-of-$numberOfSubtraces-method-$typeAlgorithm
```
where 
<br>
**$inputTrace** is the input original trace.
<br>
**$number** the corresponding number of the subtrace.
<br>
**$numberOfSubtraces** is the total of subtraces.
<br>
**$typeAlgorithm** is the algorithm selected.

### Example of use

**1**
```
./subTracingScript.sh traceExample.dat 4
```
Would generate 4 traces using the md5 method. And it will print the name of the output subtraces and the corresponding records of each of them.

```
 the subtrace : traceExample.dat-subtrace-1-of-4-method-md5 has 243 records
 the subtrace : traceExample.dat-subtrace-2-of-4-method-md5 has 249 records
 the subtrace : traceExample.dat-subtrace-3-of-4-method-md5 has 282 records
 the subtrace : traceExample.dat-subtrace-4-of-4-method-md5 has 226 records
```


**2**
```
./subTracingScript.sh traceExample.dat 4 sha
```
Would generate 4 traces using sha. And it will print the name of the output subtraces and the corresponding records of each of them.

```
 the subtrace : traceExample.dat-subtrace-1-of-4-method-sha has 266 records
 the subtrace : traceExample.dat-subtrace-2-of-4-method-sha has 265 records
 the subtrace : traceExample.dat-subtrace-3-of-4-method-sha has 255 records
 the subtrace : traceExample.dat-subtrace-4-of-4-method-sha has 214 records

```