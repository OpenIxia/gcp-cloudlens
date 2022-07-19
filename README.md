# Keysight CloudLens network visibility software on Google Cloud Platform

## Overview

CloudLens is a software for [intent-based multi-cloud network visibility](https://blogs.keysight.com/blogs/tech/nwvs.entry.html/2021/03/11/intent-based_visibil-qsu2.html?source=gcp-cloudlens), designed to:
* Enable SoC with control over traffic monitoring across any cloud
* Take advantage of rapid sensor deployment capabilities during incident response
* Extend reach of security detection tooling to edge workloads and inside Kubernetes clusters
* Reduce costs of running complex tooling infrastructure

When deployed on Google Cloud, CloudLens leverages and extends capabilities of the native [GCP Packet Mirroring service](https://cloud.google.com/vpc/docs/packet-mirroring).

For more information about CloudLens, use [CloudLens product page](https://www.keysight.com/us/en/products/network-visibility/cloud-visibility/cloudlens.html?source=gcp-cloudlens).

## Deployment guide for Google Cloud

Please refer to the [guide](DEPLOY.md) for detailed information on how to deploy and configure CloudLens for monitoring traffic in Google Cloud.

## Demo examples of using CloudLens on Google Cloud

* [Google Cloud Traffic Replication to Multiple Tools with Keysight CloudLens](https://github.com/OpenIxia/nas-cloud-demo/blob/main/GCP_CyPerf_CloudLens.md?source=gcp-cloudlens)

## Related examples

### Validation of network security controls on Google Cloud with Keysight Threat Simulator

* [Google Cloud IDS Demo with Keysight Threat Simulator](https://github.com/OpenIxia/nas-cloud-demo/blob/main/GCP_TS_Cloud_IDS.md?source=gcp-cloudlens)
* [Google Cloud Security Monitoring Demo - Palo Alto Networks IDS](https://github.com/OpenIxia/nas-cloud-demo/blob/main/GCP_TS_Demo.md?source=gcp-cloudlens)
* [Google Cloud Network Security Demo - Palo Alto Networks Firewall](https://github.com/OpenIxia/nas-cloud-demo/blob/main/GCP_TS_PAN_NGFW_Demo.md?source=gcp-cloudlens)

# Copyright notice

COPYRIGHT 2022 Keysight Technologies.
