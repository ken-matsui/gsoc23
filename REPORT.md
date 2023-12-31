# GSoC'23 Final Report

Student: Ken Matsui

Mentor: Patrick Palka

Organization: [GNU Compiler Collection](https://gcc.gnu.org)

## Short description

**C++: Implement compiler built-ins for the standard library traits**

Many C++ standard library traits are often implemented using template metaprogramming, which can result in worse compilation performance due to expensive instantiations of multiple class templates.  The most likely way to address the costly instantiations is to define compiler built-in traits and get library traits to dispatch to these built-ins.  It is also essential to conduct thorough benchmarking and compare the existing implementations with built-in traits, as there may be some library traits that are already optimal or non-built-in approaches that outperform built-ins.  Thus, the main goal of this project is to optimize the standard library traits by incorporating built-ins and investigating alternative approaches, with the aim of enhancing the compilation performance.

## What I did so far

I created a total of 35 patches, out of which 33 are in the final stages of review and on track to be merged for the next release of GCC.  These patches not only include the implementation of built-in traits, but also address the issue of handling built-in trait identifiers more efficiently.  Earlier, built-in traits were handled as registered keywords, but since the number of keywords is limited to 8 bits (i.e., up to 255 keywords), adding built-in traits through our project exceeded the limit.  My first approach was to increase the limit to 16 bits[^1]; however, this approach caused 0.95% of compilation time regression[^2][^3].  The second approach was to reserve built-in traits as a single keyword and look up the actual trait through a perfect hash function generated via gperf since we know all built-in traits beforehand[^4].   With this approach, we could look up built-ins in O(1) time complexity, while we needed to maintain the `cp-trait.gperf` file as well as `cp-trait.def`.  Although `cp-trait.gperf` was automatically generated from `cp-trait.def`, we needed to re-generate the gperf file every time we modified the list of built-in traits.  As the final approach, we discovered a new approach that uses the identifier kind to handle built-in traits instead[^5].  This approach does not need to maintain the gperf file as the identifier index behaves the same, leading to the most maintainable and performant.

[^1]: https://gcc.gnu.org/pipermail/gcc-patches/2023-September/630611.html
[^2]: https://gcc.gnu.org/pipermail/gcc-patches/2023-September/630606.html
[^3]: https://github.com/ken-matsui/gsoc23/tree/main/reports/gcc-build
[^4]: https://gcc.gnu.org/pipermail/gcc-patches/2023-October/632548.html
[^5]: https://gcc.gnu.org/pipermail/gcc-patches/2023-October/633737.html

Additionally, our patches now allow for the acceptance of code that was not previously possible.  Before our patches, the following code could not be accepted.  Our patches, however, can now accept the code like this by recognizing built-in traits only with the preceding token `(` or `<` (only for `__type_pack_element`) to reduce potential breakage of existing codes.  For example, older versions of libstdc++ that may have name conflicts with some of the newly added built-in trait names.

```cpp
#include <type_traits>

template<typename T>
struct __is_pointer : std::bool_constant<__is_pointer(T)> {};
```

I implemented 15 built-in traits while carefully taking many benchmarks and writing tests for each of the built-ins.  Some of the built-in implementations were discarded because no reasonable improvements could be made, such as `is_lvalue_reference`[^6].

[^6]: https://github.com/ken-matsui/gsoc23/blob/main/reports/is_lvalue_reference.md

## The current state

The top 3 improvements for each category are as follows:

* Compilation Time (% faster)
  1. `is_member_object_pointer_v`: 75.89%
  2. `is_member_pointer_v`: 70.46%
  3. `is_object_v`: 68.61%
* Compilation Peak Memory (% efficient)
  1. `is_member_object_pointer_v`: 72.06%
  2. `is_member_pointer_v`: 65.81%
  3. `is_member_object_pointer`: 57.52%
* Compilation Total Memory (% efficient)
  1. `is_member_object_pointer_v`: 74.59%
  2. `is_member_pointer_v`: 69.18%
  3. `is_member_object_pointer`: 59.87%

On average, there are a 24.31% improvement in compilation time, a 20.37% improvement in peak memory usage, and a 21.81% improvement in total memory usage.  All statistics here can be seen by the following command and charts.

```console
$ python3 ./scripts/stat-builtins.py  # update `base_directory` in main to `./final-report-assets/builtins/`
Best 3 Improvements:
  time:
    is_member_object_pointer_v: 75.89%
    is_member_pointer_v: 70.46%
    is_object_v: 68.61%

  peak_mem:
    is_member_object_pointer_v: 72.06%
    is_member_pointer_v: 65.81%
    is_member_object_pointer: 57.52%

  total_mem:
    is_member_object_pointer_v: 74.59%
    is_member_pointer_v: 69.18%
    is_member_object_pointer: 59.87%

Overall averages:
  time: 24.31%
  peak_mem: 20.37%
  total_mem: 21.81%
```

<p align="center">
  <img src="/final-report-assets/time.png" width="45%" />
</p>

<p align="center">
  <img src="/final-report-assets/peak_mem.png" width="45%" /> 
  <img src="/final-report-assets/total_mem.png" width="45%" />
</p>

## What's left to do

I will implement the following remaining built-in traits:

* is(_nothrow)_invocable(_r)
* invoke_result
* is_integral
* add_lvalue_reference
* add_rvalue_reference
* is_floating_point
* add_pointer
* negation
* remove_extent
* remove_all_extent
* extent
* make_signed
* make_unsigned
* decay
* rank
* is_arithmetic
* is_fundamental
* is_compound
* is_unsigned
* is_signed
* is_scalar
* common_type
* common_reference
* is_destructible
* is_swappable
* aligned_storage
* aligned_union
* result_of
* conjunction
* disjunction

## What code got merged (or is under review) upstream

### Merged

1. [libstdc++: Use __is_enum built-in trait](https://gcc.gnu.org/git/?p=gcc.git;a=commit;h=ef42efe373b012a297e534f7fb5b30e601cc7017)
2. [libstdc++: Define _GLIBCXX_USE_BUILTIN_TRAIT](https://gcc.gnu.org/git/?p=gcc.git;a=commit;h=286655d04678cb61dee9cac43b139571247c4ad1)

### Under review

Overview of the patch series:

https://gcc.gnu.org/pipermail/gcc-patches/2023-October/633775.html

1. [c++: Sort built-in traits alphabetically](https://gcc.gnu.org/pipermail/gcc-patches/2023-October/633736.html)
2. [c-family, c++: Look up built-in traits via identifier node](https://gcc.gnu.org/pipermail/gcc-patches/2023-October/633737.html)
3. [c++: Accept the use of built-in trait identifiers](https://gcc.gnu.org/pipermail/gcc-patches/2023-October/633739.html)
4. [c++: Implement __is_const built-in trait](https://gcc.gnu.org/pipermail/gcc-patches/2023-October/633740.html)
5. [libstdc++: Optimize std::is_const compilation performance](https://gcc.gnu.org/pipermail/gcc-patches/2023-October/633749.html)
6. [c++: Implement __is_volatile built-in trait](https://gcc.gnu.org/pipermail/gcc-patches/2023-October/633769.html)
7. [libstdc++: Optimize std::is_volatile compilation performance](https://gcc.gnu.org/pipermail/gcc-patches/2023-October/633753.html)
8. [c++: Implement __is_array built-in trait](https://gcc.gnu.org/pipermail/gcc-patches/2023-October/633760.html)
9. [libstdc++: Optimize std::is_array compilation performance](https://gcc.gnu.org/pipermail/gcc-patches/2023-October/633743.html)
10. [c++: Implement __is_unbounded_array built-in trait](https://gcc.gnu.org/pipermail/gcc-patches/2023-October/633744.html)
11. [libstdc++: Optimize std::is_unbounded_array compilation performance](https://gcc.gnu.org/pipermail/gcc-patches/2023-October/633747.html)
12. [c++: Implement __is_bounded_array built-in trait](https://gcc.gnu.org/pipermail/gcc-patches/2023-October/633754.html)
13. [libstdc++: Optimize std::is_bounded_array compilation performance](https://gcc.gnu.org/pipermail/gcc-patches/2023-October/633746.html)
14. [c++: Implement __is_scoped_enum built-in trait](https://gcc.gnu.org/pipermail/gcc-patches/2023-October/633767.html)
15. [libstdc++: Optimize std::is_scoped_enum compilation performance](https://gcc.gnu.org/pipermail/gcc-patches/2023-October/633741.html)
16. [c++: Implement __is_member_pointer built-in trait](https://gcc.gnu.org/pipermail/gcc-patches/2023-October/633759.html)
17. [libstdc++: Optimize std::is_member_pointer compilation performance](https://gcc.gnu.org/pipermail/gcc-patches/2023-October/633745.html)
18. [c++: Implement __is_member_function_pointer built-in trait](https://gcc.gnu.org/pipermail/gcc-patches/2023-October/633758.html)
19. [libstdc++: Optimize std::is_member_function_pointer compilation](https://gcc.gnu.org/pipermail/gcc-patches/2023-October/633748.html)
20. [c++: Implement __is_member_object_pointer built-in trait](https://gcc.gnu.org/pipermail/gcc-patches/2023-October/633764.html)
21. [libstdc++: Optimize std::is_member_object_pointer compilation performance](https://gcc.gnu.org/pipermail/gcc-patches/2023-October/633751.html)
22. [c++: Implement __is_reference built-in trait](https://gcc.gnu.org/pipermail/gcc-patches/2023-October/633755.html)
23. [libstdc++: Optimize std::is_reference compilation performance](https://gcc.gnu.org/pipermail/gcc-patches/2023-October/633768.html)
24. [c++: Implement __is_function built-in trait](https://gcc.gnu.org/pipermail/gcc-patches/2023-October/633757.html)
25. [libstdc++: Optimize std::is_function compilation performance](https://gcc.gnu.org/pipermail/gcc-patches/2023-October/633742.html)
26. [c++: Implement __is_object built-in trait](https://gcc.gnu.org/pipermail/gcc-patches/2023-October/633756.html)
27. [libstdc++: Optimize std::is_object compilation performance](https://gcc.gnu.org/pipermail/gcc-patches/2023-October/633761.html)
28. [c++: Implement __remove_pointer built-in trait](https://gcc.gnu.org/pipermail/gcc-patches/2023-October/633752.html)
29. [libstdc++: Optimize std::remove_pointer compilation performance](https://gcc.gnu.org/pipermail/gcc-patches/2023-October/633766.html)
30. [c++: Implement __is_pointer built-in trait](https://gcc.gnu.org/pipermail/gcc-patches/2023-October/633750.html)
31. [libstdc++: Optimize std::is_pointer compilation performance](https://gcc.gnu.org/pipermail/gcc-patches/2023-October/633762.html)
32. [c++: Implement __is_invocable built-in trait](https://gcc.gnu.org/pipermail/gcc-patches/2023-October/633765.html)
33. [libstdc++: Optimize std::is_invocable compilation performance](https://gcc.gnu.org/pipermail/gcc-patches/2023-October/633776.html)

## Any challenges or important things I learned during the project

Participating in this Google Summer of Code project was definitely a critical catalyst, not only in solidifying my deep-seated passion for compilers and performance engineering but also in shaping my professional orientation.  Hitherto, I have been plagued by wavering interests, oscillating between multiple fields before focusing on the aforementioned areas.  However, this project presented an invaluable opportunity to dissect, discern, and accentuate my pursuits.

The structured goals of the project resonated with my evolving interests, acting as an illuminating beacon that helped me navigate the intricate sea of open source projects.  It was an intensively rewarding experience that transformed my understanding and reinforced my commitment to the field.  Drawing upon the challenges and triumphs of the project, I can confidently affirm my decisiveness in my selected interests.

My journey, however, would have remained incomplete and less enriching had it not been for the mentorship I received.  My mentor's guidance and interactions with a diverse plethora of knowledgeable individuals added invaluable layers of insights to my experience.  These interactions expanded my professional network and honed my collaborative skills, which are essential for flourishing in open source projects.  This project and the associations born out of it have undoubtedly molded a significant chapter of my professional narrative.

## Acknowledgment

Firstly, I wish to express my profound gratitude to Patrick Palka, my mentor, for his continuous support, patience, and guidance throughout the course of this project.  His insights and vast knowledge have been an invaluable catalyst that significantly shaped the trajectory and eventual success of this endeavor.

My sincere gratitude also extends to the diligent reviewers of my code; notably, Jonathan Wakely and Jason Merrill, as well as my mentor, for their discerning eye and constructive input.  Their profound critiques, insights, and suggestions have immensely elevated the caliber of the final code patches, ensuring an output of exceptional quality.

Additionally, I am profoundly thankful for the vibrant community of contributors on the GCC mailing list.  Their timely aid, stimulating discourse, and shared experiences have added a layer of richness to this project, playing a pivotal role in its ultimate realization.  The eco-system of collaboration they nurtured fostered a sense of belonging and provided an important learning landscape that has undoubtedly contributed to the success of this project.

## Related Links

* https://summerofcode.withgoogle.com/programs/2023/projects/SuvI1tlp
* https://github.com/ken-matsui/gsoc23
* https://github.com/ken-matsui/gcc-gsoc23
