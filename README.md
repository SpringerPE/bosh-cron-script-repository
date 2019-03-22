# bosh-cron-script-repository
A BOSH release that is intended to be like a repository for your cronjob scripts

# Intro
This release gives the option to add scripts as cronjobs, each with its own schedules.

We are aware that there are other options (like https://github.com/cloudfoundry-community/cron-boshrelease), but they
don't allow to properly modularize the cron scripts.
This means that they are not made to manage cron scripts if the scripts are too long.

This release copies the scripts under `jobs/cronscript/templates/scripts` into the BOSH instance where it is being
deployed and adds an entry to a cron-schedule file so that they can be executed.

# How to use
In order to use this release, you will need to manually add the scripts into it.

For that, there are 3 steps:

1) Add the scripts to the `jobs/cronscript/templates/scripts` folder
2) Add the schedules to the `jobs/cronscript/templates/schedules` folder
3) Add a new property to the spec file.

## Adding the scripts
All you need to do is to copy your script into the `jobs/cronscript/templates/scripts` folder.
Then the release will copy the scripts into `$JOB_DIR/scripts` and make them executable.

## Scheduling the cron jobs
To add cron entry with your script, you will need to create a file under `jobs/cronscript/templates/schedules` with 
*the same name* as your script. This file can only contain 1 line, which will be the cron schedule that you want for
  that cron job.

If you want to make that configurable (which will probably will be a good idea), you need to add an entry to the spec
file with your chron schedule.
Then you can read that property in the schedule file, like:
`<%= p("schedules.hello") %>`

# Adding the release into your deployment
To add the release into your deployment, just create an operation file like
```
- type: replace
  path: /releases/-
  value: 
    name: bosh-cron-script-repository
    version: 0+dev.43
    sha1: 70b3831+

- type: replace
  path: /instance_groups/name=kibana?/jobs/-
  value: 
    name: cronscript
    release: bosh-cron-script-repository
    properties:
      schedules:
        hello: '11 * * * *'
```
