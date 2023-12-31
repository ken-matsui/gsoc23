## Mon Sep 11 07:26:08 PM PDT 2023

https://github.com/ken-matsui/gsoc23/blob/314b3ed53322daeda5574fde1f2362ece569a1d3/is_bounded_array_v.cc


```console
$ xg++ --version
xg++ (GCC) 14.0.0 20230912 (experimental)
Copyright (C) 2023 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.

```

```console
$ git rev-parse HEAD~2  # base commit
fb4b53d964b797e5f3380726175c95110c4ff9ff
```

```console
$ git log -n 2 --pretty=format:%H  # changes from the base
629cedee1c7dfcdc3a9a8625a17a9fc02bf4a299
ad65d2fdeae4f2b8a2200ed36153989133cab077
```

### Time

```console
$ perf stat xg++ -std=c++2b -c is_bounded_array_v.cc
x /tmp/tmp.L3RFIo1okc/time_A.txt
+ /tmp/tmp.L3RFIo1okc/time_B.txt
+----------------------------------------------------------------------+
|               ++ +                                xx            x    |
|+ +           +++++                               xxx            xxx x|
|      |______A__M___|                              |_______A_____M_|  |
+----------------------------------------------------------------------+
    N           Min           Max        Median           Avg        Stddev
x  10     15.332521     16.247809     16.056069     15.766173    0.39079761
+  10     12.940513     13.796551     13.701025     13.566828    0.31598051
Difference at 95.0% confidence
	-2.19935 +/- 0.333898
	-13.9498% +/- 2.11781%
	(Student's t, pooled s = 0.355364)
```

### Peak Memory Usage

```console
$ /usr/bin/time -v xg++ -std=c++2b -c is_bounded_array_v.cc
x /tmp/tmp.L3RFIo1okc/peak_mem_A.txt
+ /tmp/tmp.L3RFIo1okc/peak_mem_B.txt
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
x  10       2257000       2257164       2257128       2257106     59.635932
+  10       2161668       2161880       2161792     2161785.2     76.896468
Difference at 95.0% confidence
	-95320.8 +/- 64.6532
	-4.22314% +/- 0.00286443%
	(Student's t, pooled s = 68.8096)
```

### Total Memory Usage

```console
$ xg++ -ftime-report -std=c++2b -c is_bounded_array_v.cc
x /tmp/tmp.L3RFIo1okc/total_mem_A.txt
+ /tmp/tmp.L3RFIo1okc/total_mem_B.txt
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
x  10          2871          2871          2871          2871             0
+  10          2624          2624          2624          2624             0
Difference at 95.0% confidence
	-247 +/- 0
	-8.60327% +/- 0%
	(Student's t, pooled s = 0)
```

---
## Sat Oct 21 08:37:43 PM PDT 2023

```console
$ xg++ --version
xg++ (GCC) 14.0.0 20231016 (experimental)
Copyright (C) 2023 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.

```

### Time

```console
$ perf stat xg++ -std=c++2b -c is_bounded_array_v.cc
x /tmp/tmp.LjlruqD4rI/time_no_builtin.txt
+ /tmp/tmp.LjlruqD4rI/time_builtin.txt
+----------------------------------------------------------------------+
|  +       +                                      x                   x|
|+ ++ ++   +      +   +                           x        xx x xxx   x|
| |____MA______|                                       |______A_M____| |
+----------------------------------------------------------------------+
    N           Min           Max        Median           Avg        Stddev
x  10     15.667131     16.687553     16.363861      16.25833    0.36225129
+  10     13.152033     14.215469     13.471229     13.535032    0.35657492
Difference at 95.0% confidence
	-2.7233 +/- 0.337714
	-16.7502% +/- 2.07717%
	(Student's t, pooled s = 0.359424)
```

### Peak Memory Usage

```console
$ /usr/bin/time -v xg++ -std=c++2b -c is_bounded_array_v.cc
x /tmp/tmp.LjlruqD4rI/peak_mem_no_builtin.txt
+ /tmp/tmp.LjlruqD4rI/peak_mem_builtin.txt
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
x  10       2263372       2263836       2263712     2263677.6     132.76144
+  10       2167508       2167996       2167852     2167781.2     158.48225
Difference at 95.0% confidence
	-95896.4 +/- 137.358
	-4.23631% +/- 0.00606792%
	(Student's t, pooled s = 146.189)
```

### Total Memory Usage

```console
$ xg++ -ftime-report -std=c++2b -c is_bounded_array_v.cc
x /tmp/tmp.LjlruqD4rI/total_mem_no_builtin.txt
+ /tmp/tmp.LjlruqD4rI/total_mem_builtin.txt
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
x  10          2876          2876          2876          2876             0
+  10          2629          2629          2629          2629             0
Difference at 95.0% confidence
	-247 +/- 0
	-8.58832% +/- 0%
	(Student's t, pooled s = 0)
```

---

## Sun Oct 22 09:16:44 PM PDT 2023

### Time

```console
x ./reports/built-ins/is_bounded_array_v/time_no_builtin.txt
+ ./reports/built-ins/is_bounded_array_v/time_builtin.txt
+----------------------------------------------------------------------+
|      +         +            +                                        |
|+     +         +       +    + +x   +                  xxx   xxx xx  x|
|       |___________A____M_______|               |__________A__M______||
+----------------------------------------------------------------------+
    N           Min           Max        Median           Avg        Stddev
x  10     18.534502     22.366685     21.631279     21.279976     1.0742375
+  10     15.130114     18.892766     17.609576     17.147441     1.2985893
Difference at 95.0% confidence
	-4.13253 +/- 1.11972
	-19.4198% +/- 5.26185%
	(Student's t, pooled s = 1.1917)
```

### Peak Memory Usage

```console
x ./reports/built-ins/is_bounded_array_v/peak_mem_no_builtin.txt
+ ./reports/built-ins/is_bounded_array_v/peak_mem_builtin.txt
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
x  10       2262540       2262872       2262764     2262713.2     112.74435
+  10       2166684       2167172       2167000     2166966.8     149.61937
Difference at 95.0% confidence
	-95746.4 +/- 124.469
	-4.23149% +/- 0.00550089%
	(Student's t, pooled s = 132.471)
```

### Total Memory Usage

```console
x ./reports/built-ins/is_bounded_array_v/total_mem_no_builtin.txt
+ ./reports/built-ins/is_bounded_array_v/total_mem_builtin.txt
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
x  10          2876          2876          2876          2876             0
+  10          2629          2629          2629          2629             0
Difference at 95.0% confidence
	-247 +/- 0
	-8.58832% +/- 0%
	(Student's t, pooled s = 0)
```

---

