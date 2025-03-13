---
title: cp4d download
layout: default
parent: Cloud Pak for Data (CP4D)
---



CP4D download
Download CPD-CLI 
CPD-CLI (Cloud Pak for Data Command Line Interface) is a powerful tool used to manage IBM Cloud Pak for Data deployments. It enables administrators and developers to automate installations, updates, and configurations efficiently.
https://github.com/IBM/cpd-cli/releases/download/v14.1.1/cpd-cli-linux-SE-14.1.1.tgz

Log in to the IBM Entitled Registry registry:
cpd-cli manage login-entitled-registry \ ${IBM_ENTITLEMENT_KEY}
Download cases
In IBM Cloud Pak for Data (CP4D), "cases" refer to specific software bundles, components, or updates required for deployment and management. These cases contain necessary installation files, configurations, and dependencies.
cpd-cli manage case-download \ --components=cpd_platform,watsonx_ai,cpfs \ --release=5.0.3 \ --from_oci=true
List images
To check the container images required for IBM Cloud Pak for Data (CP4D), use the CPD-CLI or OpenShift commands. This helps in verifying the images before mirroring or deploying them.
cpd-cli manage list-images \ --components=cpd_platform,watsonx_ai,cpfs \ --release=5.0.3 \ --inspect_source_registry=true


Download
At this stage the actual download begins to the intermediate repository created in the cpd-cli-workspace folder
cpd-cli manage mirror-images \ --components= cpd_platform,watsonx_ai,cpfs \ --release=5.0.3 \ --target_registry=127.0.0.1:12443 \ --arch=amd64 \ --case_download=false

