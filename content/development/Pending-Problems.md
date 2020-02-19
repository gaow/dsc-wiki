This page documents some pre-mature thoughts that do not make to the github issues, along with temporary reminders during the coding process.

## Pending
### TODO
*  Fix pip installation js files
*  Issue 55
*  Issue 49
*  Pack() change to List() / Dict() along with documentation fixes
*  DSC::parameters change to DSC::params along with documentation fixes
*  Fix --remove
*  Fix interface (grouped args, better connections between annotate, extract, etc.)
*  Resource usage monitor and extract: time, CPU and Memory
   * should be part of the output data
   * may be achieved by `.option`
*  Force build result database -- should add a command to build what has been done
   * so that at least we can have something to show for when errors occur
*  On the fly mode, via `.option`
*  [Unit tests](https://github.com/stephenslab/dsc2/issues/6)
*  Allow for mixture Python and R in **the same** section (i.e. design a compatible output alias syntax)
*  Matlab support
*  Get command based system work 100%
   *  Currently the only major difficulty is that in plugin mode I only allow for one return file per block. I am not sure how to write it for multiple file output and more importantly how to distinguish different groups of files when they are passed to the next step.
*  Looped DSC steps, e.g. perform `transform` multiple times
*  Resource usage per option translate to SoS
*  Better result browsing, e.g., a separate tool based on fancybox?
*  Portability
*  Clean up the code
*  Force plugin to be non-plug in
*  Need an example and a tutorial running DSC on the cluster via rq
*  R function definition overload check
   * after making lib_path and exec_path be block specific

### Hard TODO
*  NA, NAN, NULL in R are impossible to distinguish in Python generated database.

## Resolved
### Done
*  exec specific return alias (what should be the syntax??)
*  Improve engineering for file signature and caching
*  Formal documentations
*  Parameter by natural groups
*  Implement `dsc view` for extracting data from results
 *  for source codes, parameters, input and output
*  Embarrassing parallel
*  Possible to execute only subset DSC sections, from command line [bad design, not done]
*  Handle multiple sequences
*  Auto install R library
*  Get 3 examples to work: ashr, one sample location I and II
 *  complete their vignettes
*  Documentation: a short course on dsc2, based on examples in vignettes
 *  Example vignettes should be well written
*  Release version 0.1.0 via pip
*  Reset parameters via command options
*  Executable signatures
   * And dependent source code signature! Syntax is hard. Instead I can allow for manually re-enforce it via command line.
*  Output file naming conventions and query methods
*  Documentation catch-up: make a gh-pages branch to publish examples (at least from my own)
*  Get a complex example work and post (perhaps my own examples)

### DSC interface design thoughts
*  Make fake dsc configs for Matthew (dsc_shrink), Mengyin, Wei and Ben's existing dsc routines
 *  e.g. introduce syntax such as method[1], method[2] / args[1], args[2]
 *  Allow for pair-wise combination in addition to Cartesian product
 *  Allow for variable name mapping
*  Move R interaction to rpy2 and properly source R scripts
*  Implement these fake dsc configs and refactor codes
 *  Use the idea of action classes applied to data
 *  Modulize these action classes
 *  Separate Scenario, Method and Replicate (seed) and keep md5 for each of them
 *  Formalize the md5 lookup procedure

### Two design problems
Problems

*  Methods sequence and methods dependency
*  "On the fly" mode

Solutions

*  Introduce return parameters
 *  Return parameters are also in global parameter space
 *  If return param overlaps other param it means this param is to be altered not produced afterwards
 *  Introduce syntax such as exe[1]$param in param section
 *  In either of the above cases at least one of the exe involved cannot be executed by itself so some sanity checks on exe sequence are needed after the jobs are created. For example if some exe's params is exe[1]$xx then that exe cannot be used alone; other sections can only access global returns of previous sections
*  Introduce asis() for on the fly mode

### A more general design theme
A sequence of steps we want to run consists of a pipeline. Each step will take some input and create some output. The user has two jobs: define the steps, and define sequences of steps. These are logically distinct jobs, so when defining a step, you should not really have to know what pipeline it is a part of. [In practice this is an idealization, because there has to be some consistency of naming of objects as they pass through the pipeline…]

Once you have defined the steps (and have ids assigned to each step) then defining the pipelines seems to be relatively straightforward in principle: you can simply list all pipelines you want run. In practice you will want shortcuts to allow efficient specification of lots of pipelines. Boolean operator type of syntax should work for example `(normal | t | uniform) + (mean | median) + (RMSE | MAE)`.

logically, we should be separating out the definitions of the different types of steps here, and not putting them all under one yaml block.

### Some implementation thoughts
*  Run via SoS:
 *  Dry run first, let `get_md5` do the job to create md5 matching with parameters
 *  Build lookup database via pandas (fill missing)
 *  Run SoS and output all in `output` dir
 *  Write a wsqlite query to allow for queries `--items (table) --condition (where)`
 *  Output of scores, ideally stored in database, may not be necessarily the case: if they are in files as long as their file names are searchable we should be good. But maybe develop a procedure to use for R, and a utility to merge all RDS into one?? -- yes that'd be the idea: for all data we use database, but for the last step the output we line it up as a flat file and output that file as a plain text file!
*  Searchable database? Want to use pytable + PrettyPrint
*  Results storage:
 *  Parameters kept in a plan pytable, with MD5 for each set-up.
 *  New entries are added as parameters change
 *  files also have MD5 and are stored elsewhere (or just also within filename entry so that change in file MD5 will naturally result in change in param MD5?).
*  Utility of SoS: currently (I think) almost all I need from SoS are in place, syntax-wise, to translate from DSC2.
 *  Make each DSC sequence separate SoS code, then each step will be unindexed and have an alias
 *  Figure out in the sequence if previous step is R, and then handle return value as parameter properly
 *  Figure out parameter dependency (of which step) and tag that; raise an error if the dependency is not clear
 *  Output file name convert to MD5 in SoS
 *  Properly handle input if it depends on multiple previous output: expand names first then do `input:...,for_each = ...`. Right now focus on RDS type of files first.
*  MD5: check if is file for each input, and feed that info to SoS accordingly.
*  What if a command generates the same file name? Need to introduce Prefix() syntax for such params so that they'll be expanded to proper FN in the format of SA?R?M?SO?
*  Job dependency now to store and how to search? Not sure yet. Maybe use that DAG class with ID being job ID in the format of SA?R?M?SO? (with 4 matching MD5) and whatever contents as contents in separate objects because that will not fit into the DAG class
*  When DSC files change SA?R?M?O? will not change for existing ones and just add additional such entries. Have to track each ? separately
*  Need a good mechanism to record and report possible bug for specific user case (that a user may have an issue with) so that bugs can be conveniently reported to me. Perhaps unit-test for this will follow the same line of thought? Guessing export helps ...
*  Do not submit anything for cluster: jobs are splitted for users to run. Write lock is trick and will discuss with Bo on this.
*  When to use pre/post processor? Any ways to use processor in SA and M?
 *  If output is simple or uniformly formatted for all methods
 *  Is this possible? It has to be! Files are Ok if not data matrix etc but files have to be the same format for all exe in / out. Thus there is need to introduce pre/post processor exe
*  Incremental executation: if SA/R changes everything be rerun (a new scenario / entry anyways); if M changes rerun all related M & SO; if SO changes just rerun SO
*  How to force rerun?
 *  Get various ? IDs inside database and use --force SA1 SA1R1 SA1R1M1 SA1R1MSO1 (so that all downstream will be re-analyzed) or blank for all
*  How to output results?
 *  PrettyPrint for param DB, and a wrapper of ddls for results DB. wrapper because it has to dump data in both python and R formats ...
*  Important detail to handle: interaction with R. Should be least painful (for users) and most efficient (for interfacing)
