---
layout: post
title:  "Creating a Tekton Tast for calling another Tekton Pipeline"
tags: openshift tekton
categories: tekton
---

# Tekton taks for callling another tekton pipeline

The Task below will call a tekton pipeline (passed in by the `PIPELINE_NAME` param) and wait for it to complete. 

**Note:** that the task completes successfully regardless of success or failure but adding logic to chage that shoudl be fairly simple.

```
apiVersion: tekton.dev/v1beta1
kind: Task
metadata:
  name: run-tekton-pipeline
spec:
  params:
    - description: Name of the pipeline to start and follow.
      name: PIPELINE_NAME
      type: string
  workspaces:
    - name: source
      description: Source Workspace.
  steps:
    - script: |
        #!/bin/bash
        echo "************************************"
        echo "Starting Pipeline: $(params.PIPELINE_NAME)"
        echo "************************************"
        tknOutput=$(tkn pipeline start $(params.PIPELINE_NAME) --use-param-defaults --workspace name=source-workspace,claimName=smoke-test-run-pipeline-workspace)
        pipelinerun=$(echo $tknOutput | grep -Po "PipelineRun started: \K([a-zA-z0-9\-]*)")
        reason="UNKNOWN"
        status="UNKNOWN"
        shopt -s nocasematch
        echo "Waiting for pipeline to complete..."
        while [[ "$status" != "True" && "$status" != "False" ]]
        do
          sleep 15
          reason=$(tkn pipelinerun describe $pipelinerun -o jsonpath='{.status.conditions[0].reason}')
          status=$(tkn pipelinerun describe $pipelinerun -o jsonpath='{.status.conditions[0].status}')
          echo "$pipelinerun currently in status: $status with reason: $reason"
        done
        echo "************************************"
        echo "Pipeline Completed with reason: $reason"
        echo "************************************"
      image: <IMAGE CONTAINING THE `tkn` BINARY>
      name: run-pipeline-and-wait
      timeout: 1h
      resources: {}
```