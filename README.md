# New Relic Application Deployment Marker

A GitHub Action to add New Relic deployment markers during your release pipeline.


| **Key**        | **Required** | **Default**  | **Description**                                                                                                                            |
|----------------|--------------|--------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| guid           | yes          | n/a          | The entity GUID to apply the change marker                                                                                                 |
| apiKey         | yes          | n/a          | Your New Relic personal API key                                                                                                            |
| changelog      | no           | n/a          | A summary of what went into this change that is visible in the Change tracking page                                                        |
| commit         | no           | n/a          | The Commit SHA for this change that is visible in the Change tracking page                                                                 |
| description    | no           | n/a          | A high-level description of this change, visible in the Overview page and on the Change tracking page when you select an individual change |
| deeplink       | no           | n/a          | A deep link to the source which triggered the change                                                                                       |
| deploymentType | no           | BASIC        | The type of deployment. Besides the default of BASIC, choose from BLUE_GREEN, CANARY, OTHER, ROLLING, or SHADOW                            |
| groupId        | no           | n/a          | A group ID for the change to link to other changes                                                                                         |
| region         | no           | US           | The region of your New Relic account                                                                                                       |
| version        | yes          | n/a          | Metadata to apply to the change marker, such as the latest release tag                                                                     |
| user           | yes          | github.actor | A username to associate with the change that is visible in the Overview and Deployments pages                                              |


#GitHub secrets
Github secrets assumed to be set:

**NEW_RELIC_API_KEY - Personal API key**
**NEW_RELIC_DEPLOYMENT_ENTITY_GUID_APPNAME - New Relic Entity GUID to create the marker on**

#Sample yaml
```
name: Change Tracking Marker
on:
  workflow_dispatch:
  release:
    types: [published]

jobs:
  newrelic:
    runs-on: ubuntu-latest
    name: New Relic
    steps:
      # This step builds a var with the release tag value to use later
      - name: Set Release Version from Tag
        run: echo "RELEASE_VERSION=${{ github.ref_name }}" >> $GITHUB_ENV
      # This step creates a new Change Tracking Marker for AP_REGISTRATION_SERVICE_AKS_DEV
      - name: AP_REGISTRATION_SERVICE_AKS_DEV
        uses: newrelic/deployment-marker-action@v2.2.0
        with:
          apiKey: ${{ secrets.NEW_RELIC_API_KEY }}
          region: "US"
          guid: ${{ secrets.NEW_RELIC_DEPLOYMENT_ENTITY_GUID_AP_REGISTRATION_SERVICE_AKS_DEV }}
          version: "${{ env.RELEASE_VERSION }}"
          changelog: "https://github.com/${{ github.repository }}/blob/master/CHANGELOG.md"
          commit: "${{ github.sha }}"
          description: "Automated Release via Github Actions"
          deploymenttype: "BASIC"
          groupId: "Workshop App Release: ${{ github.ref_name }}"
          user: "${{ github.actor }}"
      - name: View output
        run: echo "${{ env.deploymentId }}"
```

Reference: [Github NR](https://github.com/newrelic/deployment-marker-action)
