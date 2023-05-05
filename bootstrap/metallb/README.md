# MetalLB w/ kustomize
<div id="top"></div>

<!-- TABLE OF CONTENTS -->
<details open>
  <summary>Table of Contents</summary>
  <ol>
    <li>
      <a href="#about-the-project">About The Project</a>
      <ul>
        <li><a href="#built-with">Built With</a></li>
      </ul>
    </li>
    <li>
      <a href="#getting-started">Getting Started</a>
      <ul>
        <li><a href="#prerequisites">Prerequisites</a></li>
        <li><a href="#installation">Installation</a></li>
      </ul>
    </li>
    <li><a href="#contact">Contact</a></li>
  </ol>
</details>
</br>



<!-- ABOUT THE PROJECT -->
## About The Project
This project allows you to deploy [MetalLB](https://metallb.universe.tf/) on Kubernetes with Kustomize binary.

<p align="right">(<a href="#top">back to top</a>)</p>


### Built With
* [Kubernetes](https://kubernetes.io/)
* [Kustomize](https://kustomize.io/)

<p align="right">(<a href="#top">back to top</a>)</p>



<!-- GETTING STARTED -->
## Getting Started

### Prerequisites

You need to have :
* Kubernetes cluster
* Kustomize binary

### Installation

1. Clone the repo :
   ```sh
   git clone https://github.com/aamoyel/metallb.git && cd metallb
   ```
2. Create change ip pool config in layer2-config.yml. You can find docs here: https://metallb.universe.tf/configuration/#defining-the-ips-to-assign-to-the-load-balancer-services
3. Deploy the project on your cluster
   ```sh
   kustomize build . | kubectl apply -f -
   ```
4. If you have errors like `no matches for kind "IPAddressPool" in version ...` or `Internal error occurred: failed calling webhook ...` ensure pods are running in metallb-system namespace and re run step 3 (build and apply command).

<p align="right">(<a href="#top">back to top</a>)</p>


<!-- CONTACT -->
## Contact

Alan Amoyel - [@AlanAmoyel](https://twitter.com/AlanAmoyel)

Project Link: [https://github.com/aamoyel/metallb](https://github.com/aamoyel/metallb)

<p align="right">(<a href="#top">back to top</a>)</p>
