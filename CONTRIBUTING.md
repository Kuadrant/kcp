# Contributing to kcp

Please read the following guide if you're interested in contributing to kcp.

## Getting started

### Prerequisites

1. Clone this repository.
2. [Install Go](https://golang.org/doc/install) (1.17+).
3. Install [kubectl](https://kubernetes.io/docs/tasks/tools/#kubectl).

### Build & verify
1. In one terminal, build and start `kcp`:
```
go run ./cmd/kcp start
```

2. In another terminal, tell `kubectl` where to find the kubeconfig:

```
export KUBECONFIG=.kcp/admin.kubeconfig
```

3. Confirm you can connect to `kcp`:

```
kubectl api-resources
```

## Finding areas to contribute

Starting to participate in a new project can sometimes be overwhelming, and you may not know where to begin. Fortunately, we are here to help! We track all of our tasks here in GitHub, and we label our issues to categorize them. Here are a couple of handy links to check out:

* [Good first issue](https://github.com/kcp-dev/kcp/issues?q=is%3Aopen+is%3Aissue+label%3A%22good+first+issue%22) issues
* [Help wanted](https://github.com/kcp-dev/kcp/issues?q=is%3Aopen+is%3Aissue+label%3A%22help+wanted%22) issues

You're certainly not limited to only these kinds of issues, though! If you're comfortable, please feel free to try working on anything that is open.

We do use the assignee feature in GitHub for issues. If you find an unassigned issue, comment asking if you can be assigned, and ideally wait for a maintainer to respond. If you find an assigned issue and you want to work on it or help out, please reach out to the assignee first.

Sometimes you might get an amazing idea and start working on a huge amount of code. We love and encourage excitement like this, but we do ask that before you embarking on a giant pull request, please reach out to the community first for an initial discussion. You could [file an issue](https://github.com/kcp-dev/kcp/issues/new/choose), send a discussion to our [mailing list](https://groups.google.com/g/kcp-dev), and/or join one of our [community meetings](https://github.com/kcp-dev/kcp/issues?q=is%3Aissue+is%3Aopen+label%3Acommunity-meeting).

Finally, we welcome and value all types of contributions, beyond "just code"! Other types include triaging bugs, tracking down and fixing flaky tests, improving our documentation, helping answer community questions, proposing and reviewing designs, etc.


## Priorities & milestones

We prioritize issues and features both synchronously (during community meetings) and asynchronously (Slack/GitHub conversations).

We group issues together into milestones. Each milestone represents a set of new features and bug fixes that we want users to try out. We aim for each milestone to take about a month from start to finish.

You can see the [current list of milestones](https://github.com/kcp-dev/kcp/milestones?direction=asc&sort=due_date&state=open) in GitHub.

For a given issue or pull request, its milestone may be:

- **unset/unassigned**: we haven't looked at this yet, or if we have, we aren't sure if we want to do it and it needs more community discussion
- **assigned to a named milestone**
- **assigned to `TBD`** - we have looked at this, decided that it is important and we eventually would like to do it, but we aren't sure exactly when


## Coding guidelines & conventions

- Always be clear about what clients or client configs target. Never use an unqualified `client`. Instead, always qualify. For example:
    - `rootClient`
    - `orgClient`
    - `pclusterClient`
    - `rootKcpClient`
    - `orgKubeClient`
- Configs intended for `NewClusterForConfig` (i.e. today often called "admin workspace config") should uniformly be called `clusterConfig`
    - Note: with org workspaces, `kcp` will no longer default clients to the "root" ("admin") logical cluster
    - Note 2: sometimes we use clients for same purpose, but this can be harder to read
- Cluster-aware clients should follow similar naming conventions:
    - `crdClusterClient`
    - `kcpClusterClient`
    - `kubeClusterClient`
- `clusterName` is a kcp term. It is **NOT** a name of a physical cluster. If we mean the latter, use `pclusterName` or similar.
- In the syncer: upstream = kcp, downstream = pcluster. Depending on direction, "from" and "to" can have different meanings. `source` and `sink` are synonyms for upstream and downstream.
- Qualify "namespace"s in code that handle up- and downstream, e.g. `upstreamNamespace`, `downstreamNamespace`, and also `upstreamObj`, `downstreamObj`.
- When logging, use the `fmt.Sprintf("%s|%s/%s", clusterName, namespace, name` syntax.
- When orgs land: `clusterName` or `fooClusterNane` is always the fully qualified value that you can stick into obj.ObjectMeta.ClusterName. It's not necessarily the `(Cluster)Workspace.Name` from the object. For the latter, use `workspaceName` or `orgName`.
- Generally do `klog.Errorf` or `return err`, but not both together. If you need to make it clear where an error came from, you can wrap it.
