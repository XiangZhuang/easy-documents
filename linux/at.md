#`at`

##Description
A tool for one-time task scheduling.

Example: `at now +5 hours`

##How to use `at`

`at <time> <?path_to_execution_file>`

`time`:
- Must be provided
- Examples:
  - `now +2 minutes`
  - `23:59 31/12/2021`

`path_to_execution_file`:
- Optional

##Workflow

###1. Set when the task to be triggerred

Set a task to be triggerred after 2 minutes.

`at now +2 miuntes`

###2. Set the task content

After the first command line you will enter editing mode.

Create a text file named `hello.txt`

`touch hello.txt`

Press `Enter` to go to next line.

Press `Ctrl + d` to finish.

###3. Check the created task

`atq` or `at -l`

You will see

`11	Mon Feb  7 22:54:00 2022`

###4. Remove a task set by `at`

`at -r <task_id>`

`task_id`: from step 3 is `11`

##Potential Problems

###1. Use `at` on Mac
`atrun` is disabled by default on Mac

To enable it, run

`sudo launchctl load -w /System/Library/LaunchDaemons/com.apple.atrun.plist`