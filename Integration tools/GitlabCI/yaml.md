# GITLABCI specific yaml

This document is a reminder of different part of a gitlabCI yml.

## Basics

### Variables
 
To be more explicit later on the document, assign default values, change values of different parts, you can call a
variables part.

```yaml
variables:
  VAR1: titi
  VAR2: toto
  
  [...]
```

### Stages

They are useful to segment your integration into differents steps. Note that a stage can't start as long the precedent 
one isn't over. 

```yaml
stages:
  - stage_1
  - stage_2
```

### Jobs

Jobs are the unitary tasks that you are running along your pipeline. No specific name required, you just give the name
that you want to see on your pipeline (and obviously not a reserved name as stages or variables).

You need to define some parameters to make it work.

+ image: chose the docker image that your runner will use for this stage. You may define it earlier in file if you use
 the same image for all jobs
+  
### Example

## Advanced
