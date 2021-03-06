# DSC: Dynamic Statistical Comparisons

## Overview of DSC

DSC provides a framework for managing **computational benchmarking experiments** that compare several competing methods for a task across datasets or simulation scenarios. 

DSC helps execute such comparisons in an **organized and reproducible way**, and provides convenient ways to query the results.

DSC is designed to help make these comparisons *dynamic* — that is, it is easy to extend by adding new methods or simulation scenarios. Hence the name, *Dynamic Statistical Comparisons*.

DSC is able to run benchmarks written in most languages that can be compiled or run as executables.  DSC is particularly well-suited to run methods implemented in **R and Python**, the two predominant interactive programming languages in scientific research. DSC also provides support for experiments that combine R and Python.

DSC is implemented in [Python 3](http://python.org), building on the [SoS framework](https://github.com/vatlab/SoS), with additional tools developed for [R](https://www.r-project.org).

## Getting started with DSC

If you are new to DSC, we recommend taking a look at the [introductory DSC tutorial](first_course/Intro_DSC). This tutorial  gives an overview of DSC's main features, and illustrates how DSC can be used to quickly implement a simple computational experiment.

If you would like to try DSC for yourself, follow the [installation instructions](installation.html) to download and set up DSC on your computer. Then return to the [introductory tutorial](first_course/Intro_DSC) and try implementing the example on your computer.

If you would like to use DSC for your project, start by reading through the [Introduction to DSC Syntax Part I](first_course/Intro_Syntax_I) and [Part II](first_course/Intro_Syntax_I). To learn more, we provide [additional tutorials on other DSC topics](first_course/first_course), an [FAQ page](faq), a [DSC reference manual](reference/reference), and [some complete DSC examples](examples) illustrating how DSC can be used to implement a variety of computational experiments.

If you have any questions or want to share some information with the developer / user community, please open a [github issue](https://github.com/stephenslab/dsc/issues).

## Acknowledgement

This work is supported by the the Gordon and Betty Moore Foundation via an Investigator Award to Matthew Stephens, [Grant GBMF4559](https://www.moore.org/grants/list/GBMF4559), as part of the [Data-Driven Discovery program](https://www.moore.org/programs/science/data-driven-discovery).
