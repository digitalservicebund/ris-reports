---
title: Home
layout: home
nav_order: 1
---

# Home
This page is used to publish all reports for RIS.

All raw reports can be found here: [https://github.com/digitalservicebund/ris-reports](https://github.com/digitalservicebund/ris-reports)

## How to publish reports
New reports can be published by pushing them to [digitalservicebund/ris-reports](https://github.com/digitalservicebund/ris-reports). 

If you want to add reports via the pipeline, you'll have to use an SSH key pair to push to this repo:
- add the public key as a _Deploy key_ [here](https://github.com/digitalservicebund/ris-reports/settings/keys).
- add the private key to your repo as a _Repository secret_
- add a job to your pipeline for pushing them (use the provided GitHub Action git-add-report)
  - see the [example-workflow.yml](https://github.com/digitalservicebund/ris-reports/blob/main/example-workflow.yml) for an example pipeline job
- You'll find an existing key pair in our password manager
