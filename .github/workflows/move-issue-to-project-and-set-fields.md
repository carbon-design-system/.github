# Triage issues by label
### Adds an issue to project and sets the field and field option given a specific label

The purpose of this workflow is to help teams triage issues within the context of GitHub Projects. At a high level, this workflow will:

- Be triggered when an issue receives a specific label
- Add the issue to a GitHub Project
- Categorize the issue by the field and field option specified from the [caller workflow](https://docs.github.com/en/actions/using-workflows/reusing-workflows#example-caller-workflow).

It is built as a [reusable workflow](https://docs.github.com/en/actions/using-workflows/reusing-workflows) (also referred to as a called workflow) so that it can be used by repositories outside of this one with one of the biggest incentives being to **automate the triage process** when issues are opened in a repository.

With the combination of this action and creating issue templates that add default labels to issues, teams can automatically have issues be added to their respective GitHub Project and filter those issues into the appropriate field/area of the project for _single select_ fields.

## Required inputs / secrets

### Inputs

- `field`: This is the name of the field category the issue will be moved to.
- `field_option`: This is the name of the field option/value the issue will be assigned.
- `project_number`: This is the project number the issue will be added to.

### Secrets

- `token`: A personal access token (PAT) must be provided from the caller workflow to the called workflow. This token must have both the `repo` and `project` scope.

### Example usage

As this workflow is meant to be used when an issue receives a label, the [`issue` event type](https://docs.github.com/en/rest/using-the-rest-api/issue-event-types?apiVersion=2022-11-28#labeled) should be used.

```yml
name: Caller workflow example for triaging issues

on:
  issues:
    types: [ labeled ]

jobs:
  move-ts-issues:
    if: ${{ github.event.label.name == 'TypeScript' }}
    uses: carbon-design-system/.github/.github/workflows/move-issue-to-project-and-set-fields.yml
    with:
      field: 'Area'
      field_option: '🟦 Typescript'
      project_number: '1'
    secrets:
      token: ${{ secrets.TOKEN }}
```

This workflow will work if an issue has already been added to a project and can move an issue within the same field as long as a valid `field_option` is provided. If the `field_option` input is not included or blank, the action will fail and the issue will not be assigned a field within the project.
