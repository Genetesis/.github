# Branching Strategy

## Cloud Services

- `qa`, `staging`, and `production` branches are continuously deployed
  - This is achieved by using the built artifact that comes from PR CI and adding the `base ref` tag to it and pushing that tag up
    - The AppRunner cloud resource is configured to monitor digest changes in the ECR, and upon a new digest with the `base ref` tag being pushed, redeploys the service using that image digest
    - _NOTE_ that this requires any necessary infrastructure configuration changes to have been submitted/applied prior to the tag being assigned and deployed (e.g. any environment variable changes)
- All development branches except for hotfixes should stem from the `qa` branch
- Hotfix branches (branches meant to patch bugs or vulnerabilities used by the public) should stem from the `production` branch

## Code Libraries

- A long-running, shared branch (currently called `production` across most repositories) represents the primary branching point for all work
  - It also represents code that sits in the most recent release
- Release branches of the form `release/vX.Y.Z` should be created from the `production` branch
  - Typically this step is done as a follow-up to planning sessions
  - If new release branches are needed that are not already created, contact the code owners
- Feature branches should come from their respective `release/vX.Y.Z` branch

# Pull Requests (PRs)

Code contributors should confirm that CI has successfully integrated their PR patch by ensuring all jobs have passed status checks.

PR approval should not be given unless all CI checks have passed, in addition to passing code review standards (good variable names, proper code abstraction, etc.).

## Cloud Services

### Tagging/Versioning

When a cloud service artifact is built and deployed, it is tagged against the PR number.

PR numbers are used for initial tagging for multiple reasons.

1. Permits deterministically adding and removing images that are managed entirely by the lifetime of a PR
1. At some point in the future, during the lifetime of the PR, entire infrastructure can be spun up that allows the PR to be tested in its entirety at a live URL

Image tags based on PR numbers also permit more manageable automated reversions/rollbacks of deployments.

### Strategy

PRs should be opened that merge feature branches into the `qa` branch (except for the `hotfix` branch case described above).

## Code Libraries

### Tagging/Versioning

Versioning for code libraries relies on version-controlled (`git`) taggging.

In the case of strict published packaging ecosystems (like the `npm` registry), versioning information is also included in the published artifact.

For repositories like Python libraries that are consumed as dependencies of other Python repositories, the dependency tree is fully defined using the repository URL and the explicit tag/commit that consumer will use, for example:

`PyMCP2221A = {subdirectory = "pypi/main", ref = "v1.5.2", git = "https://github.com/Genetesis/PyMCP2221A.git"}`

  - No artifacts are currently built for our Python packages and thus no registry is necessary with more strict/published versioning requirements

### Strategy

PRs should be opened that merge feature branches into their respective `release/vX.Y.Z` branch.

# Opening a PR

When first opening a PR, be sure to add a brief description of what the PR is accomplishing and how best to test the behavior the PR is addressing.

All PRs should also include a reference to an Issue that the PR addresses.

[Reference](https://docs.github.com/en/get-started/writing-on-github/working-with-advanced-formatting/autolinked-references-and-urls#issues-and-pull-requests)

If the PR is simply related to an issue or you would like to associate the PR to an Issue for whatever reason, you can use the following syntax:

For same-repository PRs/Issues

- Example: `#2`

For other repository PRs/Issues

- Example: `Genetesis/genetesis-api#2`

If the PR will close any related issues, add a line with the syntax `Closes #{ISSUE_NUMBER}`

- Example: `Closes #4`

- Example: `Closes #Genetesis/genetesis/api#102`

Keep in mind: GitHub PRs are Issues under the hood, with branches attached to them. As such, you can also tag other PRs to your PR using the same syntax as above (using the PR number instead of the Issue number).

# PR Walkthrough

- If necessary, for each file changed in your PR, use GitHub's PR self review commenting feature to mark notes on important lines, points of discussion, etc.
