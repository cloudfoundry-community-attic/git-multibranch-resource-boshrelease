BOSH Release for git-multibranch-resource
============================================

Adds multibranch support for git repos, so that a commit to any matching branch can trigger and be fetched.

This BOSH release packages @starkandwayne's [git-multibranch-resource](https://github.com/starkandwayne/git-multibranch-resource) for Concourse, to make it simple to include the additional Concourse resource in your BOSH deployed Concourse system.

Final releases are automatically created based on any changes to the upstream git-multibranch-resource

See the build pipeline https://ci.starkandwayne.com/pipelines/git-multibranch-resource-boshrelease for status.

<!-- TODO: Final releases are available on https://bosh.io/releases as well as this project's own [GitHub releases](https://github.com/cloudfoundry-community/git-multibranch-resource-boshrelease/releases). -->

Installation
------------

To use this bosh release, first upload it to the BOSH/bosh-lite that is running Concourse:

```
bosh upload release https://bosh.io/d/github.com/cloudfoundry-community/git-multibranch-resource-boshrelease
```

Next, update your Concourse deployment manifest to add the resource.

Add the `git-multibranch-resource` release to the list:

```yaml
releases:
  - name: concourse
    version: latest
  - name: garden-linux
    version: latest
  - name: git-multibranch-resource
    version: latest
```

Into the `worker` job, add the `{release: git-multibranch-resource, name: git-multibranch-resource}` job template that will install the package:

```yaml
jobs:
- name: worker
  templates:
    ...
    - {release: git-multibranch-resource, name: git-multibranch-resource}
```

The final change is to add the `git-multibranch-resource` package to the list of `additional_resource_types`:

```yaml
jobs:
- name: worker
  ...
  properties:
    groundcrew:
      additional_resource_types:
      - type: git-branches
        image: /var/vcap/packages/git-multibranch-resource
```

And `bosh deploy` your Concourse manifest.


Setup pipeline in Concourse
---------------------------

```
fly -t snw c -c pipeline.yml --vars-from credentials.yml git-multibranch-resource-boshrelease
```
