## Sat Oct 21 09:01:12 PM PDT 2023

```console
$ xg++ --version
xg++ (GCC) 14.0.0 20231016 (experimental)
Copyright (C) 2023 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.

```

### Time

```console
$ perf stat xg++ -std=c++2b -c is_const.cc
x /tmp/tmp.9dqSmzOiRO/time_no_builtin.txt
+ /tmp/tmp.9dqSmzOiRO/time_builtin.txt
+----------------------------------------------------------------------+
|        x      +                                                      |
|        x     ++                                                      |
|    +xx*x  x +++x+   x              x                                +|
||__|____M____A_M__A___|______________|                                |
+----------------------------------------------------------------------+
    N           Min           Max        Median           Avg        Stddev
x  10      3.618787     4.1102948     3.6687154     3.7365217     0.1528304
+  10     3.5984001     4.6478377     3.7691096     3.8286552    0.29521045
No difference proven at 95.0% confidence
```

### Peak Memory Usage

```console
$ /usr/bin/time -v xg++ -std=c++2b -c is_const.cc
x /tmp/tmp.9dqSmzOiRO/peak_mem_no_builtin.txt
+ /tmp/tmp.9dqSmzOiRO/peak_mem_builtin.txt
+----------------------------------------------------------------------+
|    +                                                              x  |
|  + ++                                                             x  |
|+ ++++  +                                                    x xxxxx x|
| |__A_|                                                        |__AM| |
+----------------------------------------------------------------------+
    N           Min           Max        Median           Avg        Stddev
x  10       1023944       1024388       1024244     1024196.8     133.69019
+  10       1020404       1020888       1020652     1020615.2     130.75915
Difference at 95.0% confidence
	-3581.6 +/- 124.245
	-0.349698% +/- 0.012131%
	(Student's t, pooled s = 132.233)
```

### Total Memory Usage

```console
$ xg++ -ftime-report -std=c++2b -c is_const.cc
x /tmp/tmp.9dqSmzOiRO/total_mem_no_builtin.txt
+ /tmp/tmp.9dqSmzOiRO/total_mem_builtin.txt
+----------------------------------------------------------------------+
|+                                                                    x|
|+                                                                    x|
|+                                                                    x|
|+                                                                    x|
|+                                                                    x|
|+                                                                    x|
|+                                                                    x|
|+                                                                    x|
|+                                                                    x|
|+                                                                    x|
|A                                                                    A|
+----------------------------------------------------------------------+
    N           Min           Max        Median           Avg        Stddev
x  10          1210          1210          1210          1210             0
+  10          1207          1207          1207          1207             0
Difference at 95.0% confidence
	-3 +/- 0
	-0.247934% +/- 0%
	(Student's t, pooled s = 0)
```

---

## Sun Oct 22 09:16:44 PM PDT 2023

### Time

```console
x ./reports/built-ins/is_const/time_no_builtin.txt
+ ./reports/built-ins/is_const/time_builtin.txt
+----------------------------------------------------------------------+
|                                +           x                         |
|*               +x    x x      ++  +      + *x  x  x         +   +   x|
|                ||_________________MA_______M__________|_|            |
+----------------------------------------------------------------------+
    N           Min           Max        Median           Avg        Stddev
x  10     4.1996501     4.8232341     4.6010308     4.5287841    0.18225062
+  10     4.2012922     4.7864696      4.515359      4.523833     0.1721143
No difference proven at 95.0% confidence
```

### Peak Memory Usage

```console
x ./reports/built-ins/is_const/peak_mem_no_builtin.txt
+ ./reports/built-ins/is_const/peak_mem_builtin.txt
+----------------------------------------------------------------------+
|    +                                                             x   |
|   +++                                                            x  x|
|++++++                                                      x x  xxxxx|
| |_AM|                                                         |__A_| |
+----------------------------------------------------------------------+
    N           Min           Max        Median           Avg        Stddev
x  10       1023064       1023532       1023376     1023352.8     148.59101
+  10       1019744       1019996       1019948     1019909.6     90.356332
Difference at 95.0% confidence
	-3443.2 +/- 115.543
	-0.336463% +/- 0.0112906%
	(Student's t, pooled s = 122.971)
```

### Total Memory Usage

```console
x ./reports/built-ins/is_const/total_mem_no_builtin.txt
+ ./reports/built-ins/is_const/total_mem_builtin.txt
+----------------------------------------------------------------------+
|+                                                                    x|
|+                                                                    x|
|+                                                                    x|
|+                                                                    x|
|+                                                                    x|
|+                                                                    x|
|+                                                                    x|
|+                                                                    x|
|+                                                                    x|
|+                                                                    x|
|A                                                                    A|
+----------------------------------------------------------------------+
    N           Min           Max        Median           Avg        Stddev
x  10          1210          1210          1210          1210             0
+  10          1207          1207          1207          1207             0
Difference at 95.0% confidence
	-3 +/- 0
	-0.247934% +/- 0%
	(Student's t, pooled s = 0)
```

---

