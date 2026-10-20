# shoalcheck gate — the pipeline that stops a merge granting cluster-admin

A live demonstration of [`shoalcheck gate`](https://shoalcheck.github.io/) in GitHub Actions. Every pull request
reads a throwaway Kubernetes cluster built from the pull request's own manifests (kind, inside the runner) and
compares the reading with the baseline in [`.shoalcheck/baseline.json`](.shoalcheck/baseline.json). The job fails
— exit 2 — when the policy fires: a new finding at high or above, a regression, or a kind the baseline read and
this run could not. A finding that merely became visible because the tool changed is withheld, not reported as new.

- The pull request that grants a ServiceAccount `cluster-admin` is stopped: the gate names the new critical finding,
  the check is red, and the finding lands in code scanning.
- The commit that narrows the grant to a namespaced Role passes the same check.

The workflow is [`docs/ci/github-actions.yml`](https://github.com/Shoalcheck/shoalcheck) from the product, with the
cluster swapped for kind so it can run anywhere; in your pipeline it points at your staging cluster. It installs the
published release and checks its signature (`cosign.pub`) and checksum before it runs.

The cluster is kind and the manifests are a demonstration: nothing here describes a real cluster.
