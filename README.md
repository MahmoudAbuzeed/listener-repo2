# Create PR with Unique Branch Name After Workflow Completion

This repository is configured to automatically create a pull request with a uniquely named branch whenever it receives a `repository_dispatch` event. The branch name is generated dynamically and includes a timestamp to ensure uniqueness.

## Workflow Overview

This repository uses a GitHub Actions workflow to:

1. **Listen for the `repository_dispatch` event**.
2. **Generate a new branch with a unique name**.
3. **Commit changes to the newly created branch**.
4. **Create a pull request** from the new branch to the `main` branch.

### Workflow File

The workflow is defined in `.github/workflows/create-pr.yml`. It listens for the `repository_dispatch` event of type `workflow_completed`.

### Workflow Steps Explanation

1. **Trigger**:

   - The workflow triggers when a `repository_dispatch` event of type `workflow_completed` is received.

2. **Checkout Code**:

   - This step checks out the repository code using `actions/checkout@v3`.

3. **Get Latest Commit SHA**:

   - This step captures the SHA of the latest commit on the current branch and stores it as an output (`sha`) for later use.

4. **Generate Random Branch Name**:

   - A unique branch name is generated using the current timestamp (`new-feature-branch-{timestamp}`) and stored in an output variable (`branch_name`).

5. **Create New Branch**:

   - A new branch is created using the generated branch name. This is done by sending a `POST` request to the GitHub API’s `/git/refs` endpoint.

6. **Fetch Remote Branches**:

   - This ensures that the local environment is up to date with the remote repository branches.

7. **Set Git User Information**:

   - The Git user’s email and name are set for the commit that will be made. Replace the email and name with your own details if necessary.

8. **Check Out or Create Branch**:

   - This step checks out the newly created branch using the generated branch name. If the branch doesn’t already exist locally, it is created.

9. **Make a Commit in New Branch**:

   - A new file (`file.txt`) is created with some content. This file is staged, committed, and pushed to the new branch.

10. **Create Pull Request**:
    - Finally, a pull request is created from the new branch to the `main` branch using the GitHub API. The title and description of the pull request can be customized as needed.

---

### Setting Up the Personal Access Token (PAT)

To authenticate the API requests for creating branches and pull requests, you need a Personal Access Token (PAT) with appropriate permissions.

1. **Create a GitHub Personal Access Token**:

   - Go to your [GitHub Personal Access Tokens settings](https://github.com/settings/tokens).
   - Click **Generate new token**.
   - Select the following scopes:
     - `repo`: Full control of private repositories (to create branches and pull requests).
     - `workflow`: To allow dispatching GitHub workflows.
   - Copy the generated token.

2. **Store the Token in the Repository**:
   - Go to **Settings** > **Secrets and variables** > **Actions** in this repository.
   - Click on **New repository secret**.
   - Name the secret `PERSONAL_ACCESS_TOKEN`.
   - Paste the Personal Access Token that you generated.

---

## GitHub API Reference

This setup uses the GitHub API to create branches and pull requests.

1. **Create a Branch**:

   - The `/git/refs` endpoint is used to create a new branch:

   ```bash
   POST /repos/{owner}/{repo}/git/refs
   ```

2. **Create a Pull Request**:
   - The `/pulls` endpoint is used to create a pull request:
   ```bash
   POST /repos/{owner}/{repo}/pulls
   ```

For more details, refer to the [GitHub REST API documentation](https://docs.github.com/en/rest/reference/repos).

---

### Security

- **Personal Access Token (PAT)**:
  - Ensure that the PAT is stored securely in the repository secrets and has the least required permissions (only `repo` and `workflow` scopes).
  - Never hardcode the token in your workflow files.

---

## Conclusion

This repository is configured to automatically create a pull request with a unique branch name after receiving a `repository_dispatch` event. This setup is useful for automating PR creation workflows across multiple repositories and ensuring that each PR is created from a uniquely named branch.
