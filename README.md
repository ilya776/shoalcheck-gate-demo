# shoalcheck gate — the pipeline that stops a merge granting cluster-admin

A live demonstration of [`shoalcheck gate`](https://shoalcheck.github.io/) in GitHub Actions. Every pull request
builds a throwaway Kubernetes cluster (kind, inside the runner) from the manifests the pull request starts from,
reads it, applies the pull request's manifests and reads it again. The job fails — exit 2 — when the policy fires:
a new finding at high or above, a regression, or a kind the first reading read and the second could not. A finding
that merely became visible because the tool changed is withheld, not reported as new.

- The pull request that grants a ServiceAccount `cluster-admin` is stopped: the gate names the new critical finding,
  the check is red, merging is blocked, and the finding lands in code scanning.
- The commit that narrows the grant to a namespaced Role passes the same check.

The workflow is the product's job for GitHub Actions,
[`ci/github-actions.yml`](https://shoalcheck.github.io/ci/github-actions.yml), with the cluster swapped for kind so it
can run anywhere. It installs the published release, checks its signature with the release key written into the job
and the archive against its checksum, and only then reads the cluster.

**One difference from your pipeline.** There, the cluster is long-lived and the baseline is a file in your
repository, recorded once with `--update-baseline`. shoalcheck refuses to compare readings of two different clusters —
it knows a cluster by its `kube-system` namespace, and kind is a new cluster every run — so here the baseline is
recorded in the same run, from the manifests the change starts from.

The cluster is kind and the manifests are a demonstration: nothing here describes a real cluster.
