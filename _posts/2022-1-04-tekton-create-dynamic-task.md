---
layout: post
title:  "Creating a Tekton pipeline with a Dynamic Task List"
tags: tekton openshift pipelines cicd
category: tekton
---

Happy New Year!!! :boom:

# Intro

When creating a tekton pipeline one trick I have found particularly useful especially in application pipelines is the ability to create "Dynamic Task". By this I mean allowing the users of your pipelines to specify which task they want run. 

Now if this is limited to just a single task the logic is trivial, simply surround the task by an `if` or `with` and allow the user to set `myTask.enabled`, but what happens if we introduce a `myTask2` directly after `myTask`? Now we need to account for the possibility to `myTask` existing or not which makes thinks more complicated. Fortunately there is a pretty simple fix for this just by adding a "lastTask" variable to keep track of the last task that was run.


Example:

We have a three task that we want to run in a sequential order:
    - `rootTask`
    - `optionalTask1`
    - `optionalTask2`

`optionalTask1` and `optionalTask2` should as their name suggest be optional. Meaning we could modify our pipeline to just run `rootTask` and `optionalTask2` with the following `values.yaml` file

``` yaml
optionalTask1:
    enabled: false
optionalTask2:
    enabled: true
```

```
apiVersion: tekton.dev/v1beta1
kind: Pipeline
metadata:
  name: my-pipeline
  tasks:
  - name: rootTask
    taskRef:
      name: root-task
      kind: Task
    # The `lastTask` variable will represent the last task that was run
    {{ printf "{{ - $lastTask := "rootTask"}}" }} 
  # You may want some additional logic incase `optionalTask` does not exist
  {{- if optionalTask1.enabled }}
  - name: optionalTask1
    taskRef:
      name: optional-task-1
    runAfter:
      - {{ $lastTask }}
    {{- $lastTask = "optionalTask1"}}
  {{- end }}
  {{- if optionalTask2.enabled }}
  - name: optionalTask2
    taskRef:
      name: optional-task-2
    runAfter:
      - {{ $lastTask }}
    {{- $lastTask = "optionalTask2"}}
  {{- end }}
  # Adding any more task from here should be a copy and paste of the above logic.
```
