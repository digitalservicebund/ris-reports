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
- You'll find an existing key pair in our password manager

```yaml
name: Pipeline
# ...
env:
  REPORTS_REPOSITORY: digitalservicebund/ris-reports

jobs:
  # ...
  push-reports:
    # ...
    steps:
      - uses: actions/checkout@v3
        with:
          repository: ${{ env.REPORTS_REPOSITORY }}
          ssh-key: ${{ secrets.REPORTS_DEPLOY_KEY }}
      - name: Setup git
        run: |
          git config user.name "${{ github.repository }}"
          git config user.email "..."
      - name: ER-diagram - Download artifact
        uses: actions/download-artifact@v3
        with:
          name: erdiagram # assumes you have used actions/upload-artifact before
          path: tmp/erdiagram-reports/
      - name: ER-diagram - git add report
        uses: digitalservicebund/ris-reports/actions/git-add-report@728043a0126de421396dde2b11e7c61dfe5c1892
        with:
          filePath: tmp/erdiagram-reports/latest.mmd
          destinationDir: entity-relationship-diagrams/ris-backend-service
      - name: Push reports
        # we use `toJSON(...)` below to escape double quotation marks
        run: |
          git diff-index --cached --quiet HEAD ||
            git commit \
              -m $toJSON({{ toJSON(github.event.head_commit.message) }}) \
              -m "From commit: ${{ github.server_url }}/${{ github.repository }}/commit/${{ github.sha }}" &&
            git push origin main &&
            echo "Pushed reports to ${{ github.server_url }}/${{ env.REPORTS_REPOSITORY }}" >> $GITHUB_STEP_SUMMARY
```
