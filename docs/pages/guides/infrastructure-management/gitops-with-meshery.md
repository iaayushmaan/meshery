---
layout: default
title: GitOps with Meshery
abstract: Integrating your CI/CD pipelines with Meshery's GitHub Actions
permalink: guides/infrastructure-management/gitops-with-meshery
type: guides
category: infrastructure
language: en
list: include
published: true
---

## <img style="height: 4rem; width: 4rem;" src="{{site.baseurl}}/assets/img/kanvas-icon-color.svg" /> Kanvas Snapshot GitHub Action

- See your deployment before you merge
- Connect Kanvas to your GitHub repo and see changes pull request-to-pull request
- Get snapshots of your infrastructure directly in your PRs

<h3>Using Meshery's SnapShot GitHub Action</h3>
See your deployment before you merge. Connect Kanvas to your GitHub repo and see changes pull request-to-pull request. Get snapshots of your infrastructure directly in your PRs.

See [Extension: Kanvas Snapshot](/extensions/snapshot) for more details.

## <img src="{{ site.baseurl }}/assets/img/smp-dark-text-side.svg" className="image-left-no-shadow" alt="Infrastructure fperformance logo" style="width: 60%; max-width: 200px;vertical-align:middle" /> Meshery Performance Analysis GitHub Action

- See your performance regressions before you merge
- Connect Performance Management to your GitHub repo and see changes pull request-to-pull request
- Red light performance regressions
- Baseline and analyze the performance of your services is key to efficient operation of any application
  - Meshery is the canonical implementation of the Cloud Native Performance specification
- Define your performance profiles upfront. See statistical analysis with microservice latency and throughput quartiles
- Meshery includes your choice of load generator, so that you can meausure your way
- Meshery packages all these features into an easy-to-use GitHub Action

Measuring and managing the performance of your infrastructure is key to efficient operation. <!-- Meshery is the canonical implementation of the Service Mesh Performance specification. --> You can choose from multiple load generators and use a highly configurable set of load profiles with variable tunable facets to run a performance test. Meshery packages all these features into an easy-to-use GitHub Action.

<h3>Using Meshery's Performance Analysis GitHub Action</h3>

The <a href="https://github.com/marketplace/actions/performance-testing-with-meshery">Meshery Performance Analysis GitHub Action</a> is available in the GitHub Marketplace. You can create your own performance profiles to run repeatable tests with Meshery. You can configure this action to trigger with each of your releases, on every pull request or any GitHub workflow trigger event. A sample configuration of the action is shown below.

```yaml
name: Meshery SMP Action
on:
  push:
    branches:
      'master'

jobs:
  performance-test:
    name: Performance Test
    runs-on: ubuntu-latest
    steps:
      - name: checkout
        uses: actions/checkout@v2
        with:
          ref: 'perf'

      - name: Deploy k8s-minikube
        uses: manusa/actions-setup-minikube@v2.4.1
        with:
          minikube version: 'v1.21.0'
          kubernetes version: 'v1.20.7'
          driver: docker

      - name: Run Performance Test
        uses: layer5io/meshery-smp-action@master
        with:
          provider_token: ${{ secrets.PROVIDER_TOKEN }}
          platform: docker
          profile_name: soak-test
```

You can also define your test configuration in an SMP compatible configuration file as shown below.

```yaml
smp_version: v0.0.1
id:
name: Istio Performance Test
labels: {}
clients:
- internal: false
  load_generator: fortio
  protocol: 1
  connections: 2
  rps: 10
  headers: {}
  cookies: {}
  body: ""
  content_type: ""
  endpoint_urls:
  - http://localhost:2323/productpage
duration: "30m"
```

See this sample GitHub workflow (<a href="https://github.com/layer5io/meshery-smp-action/blob/master/action.yml">action.yml</a>) for more configuration details.

<a href="{{ site.baseurl }}/assets/img/service-mesh-performance-profile-test-results.png"><img src="{{ site.baseurl }}/assets/img/service-mesh-performance-profile-test-results.png" className="image-center" alt="performance management dashboard" /></a>

The results from the tests are updated on the Performance Management dashboard in Meshery. To learn more about interpreting the test results, check out <a href="https://docs.meshery.io/guides/interpreting-performance-test-results">this guide</a>. You can always checkout the <a href="https://docs.meshery.io/guides">Meshery User Guides</a> to dive deep into these features.

<a href="https://smp-spec.io">Service Mesh Performance</a> standardizes service mesh value measurement, characterizing any deployment's performance by capturing the details of infrastructure capacity, service mesh configuration and workload metadata.

## Service Mesh Conformance GitHub Action (Archived)

<details>
<summary>
<h2>Service Mesh Interface Conformance GitHub Action</h2>
</summary>

<img src="{{ site.baseurl }}/assets/img/servicemeshinterface-horizontal-stackedtext-color.svg" className="image-right-no-shadow" alt="service mesh interface logo" style="width: 60%; max-width: 250px;" />

<a href="https://smi-spec.io">Service Mesh Interface</a> provides a standard interface for service meshes on Kubernetes. These (currently) four specfications offer a common denominator set of interfaces to support most common service mesh use cases and the flexibility to evolve to support new service mesh capabilities over time.

The <a href="https://github.com/layer5io/meshery-smi-conformance-action">Meshery SMI Conformance Action</a> which <a href="https://meshery.io/blog/validating-smi-conformance-with-meshery">validates SMI conformance</a> in your pipeline and the <a href="https://github.com/layer5io/meshery-smp-action">Meshery SMP Action</a> which runs <a href="{{ site.baseurl }}/guides/performance-management/managing-performance">SMP compatible performance benchmarks</a>.

<h2>Service Mesh Interface Conformance GitHub Action</h2>

Conformance of SMI specifications is defined as a series of test assertions. These test assertions are categorised by SMI specification (of which, there are currently four specifications) and comprise the complete suite of SMI conformance tests. Conformance requirements will change appropriately as each new version of the SMI spec is released. Refer to Meshery's documentation for details of how <a href="{{ site.baseurl }}/tasks/service-mesh-interface">Meshery performs SMI conformance</a>.


<h3>Using Meshery's SMI Conformance GitHub Action</h3>

The <a href="https://github.com/marketplace/actions/service-mesh-interface-conformance-with-meshery">Service Mesh Interface Conformance GitHub Action</a> is available in the GitHub Marketplace. You can configure this action to trigger with each of your releases, on every pull request. or any GitHub workflow trigger event.

An example of the action configuration which runs on every release is shown below. The action handles setting up a Kubernetes environment, deploying the service mesh (see supported service meshes), running the conformance tests and reporting back the results to the SMI Conformance dashboard in Meshery.

<code>
name: SMI Conformance with Meshery
on:
  push:
    tags:
      - 'v*'

jobs:
  smi-conformance:
    name: SMI Conformance
    runs-on: ubuntu-latest
    steps:

      - name: SMI conformance tests
        uses: layer5io/mesheryctl-smi-conformance-action@master
        with:
          provider_token: ${{ secrets.MESHERY_PROVIDER_TOKEN }}
          service_mesh: open_service_mesh
          mesh_deployed: false
</code>

You can also bring in their own cluster with specific capabilities and with a service mesh already installed.

<pre><code>
name: SMI Conformance with Meshery
on:
  push:
    branches:
      - 'master'

jobs:
  smi-conformance:
    name: SMI Conformance tests on master
    runs-on: ubuntu-latest
    steps:

      - name: Deploy k8s-minikube
        uses: manusa/actions-setup-minikube@v2.4.1
        with:
          minikube version: 'v1.21.0'
          kubernetes version: 'v1.20.7'
          driver: docker

      - name: Install OSM
        run: |
           curl -LO https://github.com/openservicemesh/osm/releases/download/v0.9.1/osm-v0.9.1-linux-amd64.tar.gz
           tar -xzf osm-v0.9.1-linux-amd64.tar.gz
           mkdir -p ~/osm/bin
           mv ./linux-amd64/osm ~/osm/bin/osm-bin
           PATH="$PATH:$HOME/osm/bin/"
           osm-bin install --osm-namespace default

      - name: SMI conformance tests
        uses: layer5io/mesheryctl-smi-conformance-action@master
        with:
          provider_token: ${{ secrets.MESHERY_PROVIDER_TOKEN }}
          service_mesh: open_service_mesh
          mesh_deployed: true
</code></pre>
<p>
You can download a token from Meshery and add it as a GitHub secret (in the example above, the secret is <code>MESHERY_PROVIDER_TOKEN</code>). After the test is run, you can view the results from the Service Mesh Interface dashboard in Meshery UI.</p>

<a href="{{ site.baseurl }}/assets/img/smi-conformance-result.png"><img src="{{ site.baseurl }}/assets/img/smi-conformance-result.png" className="image-center-shadow" style="width:70%" alt="smi conformance dashboard" /></a> <br />
<i>Meshery's Service Mesh Interface Conformance Results</i>

<p>Participating service mesh projects can also <a href="{{ site.baseurl }}/tasks/service-mesh-interface#reporting-conformance">automatically report their conformance test results</a> to the <a href="https://meshery.io/service-mesh-interface">SMI Conformance dashboard</a></p>
</details>
