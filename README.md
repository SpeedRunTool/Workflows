# Workflows
Reusable workflows for use with your plugin's CI-CD workflow.

## Example usage
This shows how to call and use a reusable workflow.

Below we're calling the reusable workflow "GetCSProjVersion" which extracts version details from a csproj file and returns a semver version string.

After we're using the value returned by the reusable workflow to verify it worked correctly.

```yaml
name: Manual Workflow

on:
  workflow_dispatch:
    inputs:
      release-type-tag: # Use -alpha, -beta, or -rc for pre-release. An empty string for stable.
        description: 'Release type tag'
        required: true
        default: '' 
        type: choice
        options:
        - '-alpha'
        - '-beta'
        - '-RC'
        - ''

jobs:
  GetCSProjVersion:
    name: Get C# project version
    uses: SpeedRunTool/Workflows/.github/workflows/GetCSProjVersion.yml@main
    with:
      repository: ${{ github.GITHUB_REPOSITORY  }}
      ref: ${{ github.GITHUB_REF_NAME  }}
      config-path: src/SRTProducerMyPlugin/SRTProducerMyPlugin.csproj
      release-type-tag: ${{ inputs.release-type-tag }}

  Report:
    name: Report version
    runs-on: ubuntu-latest
    needs: GetCSProjVersion
    steps:
    - name: Report
      run: echo ${{ needs.GetCSProjVersion.outputs.version }}
```
