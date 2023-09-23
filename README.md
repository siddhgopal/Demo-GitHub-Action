# Demo-GitHub-Action
"https://github.com/super-linter/super-linter"
How to use
More in-depth tutorial available

To use this GitHub Action you will need to complete the following:

Create a new file in your repository called .github/workflows/linter.yml
Copy the example workflow from below into that new file, no extra configuration required
Commit that file to a new branch
Open up a pull request and observe the action working
Enjoy your more stable, and cleaner codebase
Check out the Wiki for customization options
NOTE: If you pass the Environment variable GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }} in your workflow, then the GitHub Super-Linter will mark the status of each individual linter run in the Checks section of a pull request. Without this you will only see the overall status of the full run. There is no need to set the GitHub Secret as it is automatically set by GitHub, it only needs to be passed to the action.

Example connecting GitHub Action Workflow
In your repository you should have a .github/workflows folder with GitHub Action similar to below:

.github/workflows/linter.yml
Example file can be found at TEMPLATES/linter.yml
This file should have the following code:

---
#################################
#################################
## Super Linter GitHub Actions ##
#################################
#################################
name: Lint Code Base

#############################
# Start the job on all push #
#############################
on:
  push:
    branches-ignore: [master, main]
    # Remove the line above to run when pushing to master or main
  pull_request:
    branches: [master, main]

###############
# Set the Job #
###############
jobs:
  build:
    # Name the Job
    name: Lint Code Base
    # Set the agent to run on
    runs-on: ubuntu-latest

    ############################################
    # Grant status permission for MULTI_STATUS #
    ############################################
    permissions:
      contents: read
      packages: read
      statuses: write

    ##################
    # Load all steps #
    ##################
    steps:
      ##########################
      # Checkout the code base #
      ##########################
      - name: Checkout Code
        uses: actions/checkout@v4
        with:
          # Full git history is needed to get a proper
          # list of changed files within `super-linter`
          fetch-depth: 0

      ################################
      # Run Linter against code base #
      ################################
      - name: Lint Code Base
        uses: super-linter/super-linter@v5
        env:
          VALIDATE_ALL_CODEBASE: false
          DEFAULT_BRANCH: main
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
Add Super-Linter badge in your repository README
You can show Super-Linter status with a badge in your repository README

GitHub Super-Linter

Format:

[![GitHub Super-Linter](https://github.com/<OWNER>/<REPOSITORY>/actions/workflows/<WORKFLOW_FILE_NAME>/badge.svg)](https://github.com/marketplace/actions/super-linter)
Example:

[![GitHub Super-Linter](https://github.com/super-linter/super-linter/actions/workflows/ci.yml/badge.svg)](https://github.com/marketplace/actions/super-linter)
Images
The GitHub Super-Linter now builds and supports multiple images. We have found as we added more linters, the image size expanded drastically. After further investigation, we were able to see that a few linters were very disk heavy. We removed those linters and created the slim image. This allows users to choose which Super-Linter they want to run and potentially speed up their build time. The available images:

super-linter/super-linter:v5
super-linter/super-linter:slim-v5
Standard Image
The standard super-linter/super-linter:v5 comes with all supported linters. Example usage:

################################
# Run Linter against code base #
################################
- name: Lint Code Base
  uses: super-linter/super-linter@v5
  env:
    VALIDATE_ALL_CODEBASE: false
    DEFAULT_BRANCH: main
    GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
Slim Image
The slim super-linter/super-linter:slim-v5 comes with all supported linters but removes the following:

rust linters
dotenv linters
armttk linters
pwsh linters
c# linters
By removing these linters, we were able to bring the image size down by 2gb and drastically speed up the build and download time. The behavior will be the same for non-supported languages, and will skip languages at run time. Example usage:

################################
# Run Linter against code base #
################################
- name: Lint Code Base
  uses: super-linter/super-linter/slim@v5
  env:
    VALIDATE_ALL_CODEBASE: false
    DEFAULT_BRANCH: main
    GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
Environment variables
The super-linter allows you to pass the following ENV variables to be able to trigger different functionality.

Note: All the VALIDATE_[LANGUAGE] variables behave in a very specific way:

If none of them are passed, then they all default to true.
If any one of the variables are set to true, we default to leaving any unset variable to false (only validate those languages).
If any one of the variables are set to false, we default to leaving any unset variable to true (only exclude those languages).
