pre-commit hook

#!/bin/sh

echo "Checking code format with prettier..."
npx prettier --check .

if [ $? -ne 0]; then
	echo "Code is not formatted well. Commit aborted."
	exit 1
fi
echo "Code is formatted ok."

pre-push hook

#!/bin/sh

echo "Running pre-push hook..."
npm test

if [ $? -ne 0]; then
	echo "Tests failed, push aborted."
	exit 1
fi
echo "Tests passed. Proceeding with push."

Output
 PASS  ./multyply_func.test.js
  √ multiply 2*3 to equal 6 (10 ms)

Test Suites: 1 passed, 1 total
Tests:       1 passed, 1 total
Snapshots:   0 total
Time:        2.87 s
Ran all test suites.
.git/hooks/pre-push: line 6: [: missing `]'
Tests passed. Proceeding with push.
Enumerating objects: 13, done.
Counting objects: 100% (13/13), done.
Delta compression using up to 4 threads
Compressing objects: 100% (12/12), done.
Writing objects: 100% (13/13), 36.38 KiB | 2.80 MiB/s, d
one.
Total 13 (delta 4), reused 0 (delta 0), pack-reused 0 (f
rom 0)
remote: Resolving deltas: 100% (4/4), done.
To github.com:alexbjov/Git-Exam-02-Automating-Workflow-w
ith-Git-Hooks-and-CICD-Integration.git
 * [new branch]      main -> main
branch 'main' set up to track 'origin/main'.


pull yml

name: Node.js CI

on:
  pull_request:
    branches: [ main, develop ]

jobs:
  build:
    runs-on: ubuntu-latest

    strategy:
      matrix:
        node-version: [22.x] # Specify Node.js versions to test against

    steps:
    - uses: actions/checkout@v4 # Checks out your repository under $GITHUB_WORKSPACE
    - name: Use Node.js ${{ matrix.node-version }}
      uses: actions/setup-node@v4
      with:
        node-version: ${{ matrix.node-version }}
        cache: 'npm' # Caches node modules for faster builds
    - name: Install dependencies
      run: npm ci # Installs dependencies from package-lock.json
    - name: Run tests
      run: npm test # Executes your test command (e.g., Jest, Mocha)


approval before merge yml

branches:
  - name: main
    protection:
      required_pull_request_reviews:
        required_approving_review_count: 1 # Minimum number of approvals required
        dismiss_stale_reviews: true # Dismiss approvals when new commits are pushed
        require_code_owner_reviews: false # Require review from code owners (if CODEOWNERS file exists)
      required_status_checks:
        strict: true
        contexts:
          - "ci/build" # Example: Require CI build to pass
      enforce_admins: true # Enforce rules for administrators as well
