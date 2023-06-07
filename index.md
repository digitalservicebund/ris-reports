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
- add a job to your pipeline for pushing them:
    ```yaml
    name: Pipeline
    # ...
    env:
      REPORTS_REPOSITORY: digitalservicebund/ris-reports
    
    jobs:
      # ...
      push-reports:
        # ...
        env:
          ER_DIAGRAMS_PATH: entity-relationship-diagrams
        steps:
          - uses: actions/checkout@v3
            with:
              repository: ${{ env.REPORTS_REPOSITORY }}
              ssh-key: ${{ secrets.REPORTS_DEPLOY_KEY }}
          - name: Setup git
            run: |
              git config user.name "${{ github.repository }}"
              git config user.email "..."
          # ... generate or download reports artifacts
          - name: Push reports
            run: |
              git add some-report.csv
              git commit \
                -m "${{ github.event.head_commit.message }}" \
                -m "From commit: ${{ github.server_url }}/${{ github.repository }}/commit/${{ github.sha }}"
              git push origin main
              echo "Pushed reports to ${{ github.server_url }}/${{ env.REPORTS_REPOSITORY }}" >> $GITHUB_STEP_SUMMARY
    ```

{: .note }
You'll find a key pair in our password manager `SSH key pair for ris-reports`.
