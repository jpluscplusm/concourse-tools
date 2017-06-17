# concourse-tools
A collection of useful things and tools to do with https://concourse.ci

## tail-pipeline

This helps you keep an eye on the output of all the jobs flowing down a named
Concourse pipeline. Concurrently running jobs' outputs are interleaved,
hopefully line-by-line. Jobs started whilst the script is running will begin to
be tailed within a couple of seconds of the job starting.

```
$ FLY="fly -t myserver" CONCOURSE_PIPELINE_NAME=a-pipeline ./tail-pipeline
some-job 2 21:36:26> using version of resource found in cache
some-job 2 21:36:26> Starting ...
some-job 2 21:36:27> Continuing ...
another-job 15 21:36:27> using version of resource found in cache
another-job 15 21:36:27> Some output ...
another-job 15 21:36:27> Some more eutput ...
some-job 2 21:36:27> Continuing ...
some-job 2 21:36:28> Continuing ...
...
```

NB Each line's timestamp is generated at the time the data is *received*. The
`fly watch` command prints a job's output from the start, so long-running jobs
will have an incorrect timestamp associated with their early-on output.

You can disable the timestamp generation by setting the envvar
`DISABLE_TIMESTAMP` to the empty string. This may be useful if the script's
output already contains timestamps.

```
$ DISABLE_TIMESTAMP="" FLY="fly -t myserver" CONCOURSE_PIPELINE_NAME=a-pipeline ./tail-pipeline
some-job 2> using version of resource found in cache
some-job 2> Starting ...
some-job 2> Continuing ...
another-job 15> using version of resource found in cache
another-job 15> Some output ...
another-job 15> Some more eutput ...
some-job 2> Continuing ...
some-job 2> Continuing ...
...
```

On a particularly busy CI server, you may have to increase the amount of job
history the tool requests from Concourse each time. Set the envvar
`BUILD_COUNT` to something higher than the default of 25.

On a particularly complex or lengthy pipeline, you may have to raise the
script's hardcoded limit of 32 running jobs being tailed at once.

