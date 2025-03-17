---
title: Watsonx.AI Installation and Model Deployment
layout: default
parent: Watsonx.AI
---

# **Watsonx.AI Installation and Model Deployment**  

## **Cartridge Installation**  
Watsonx.AI is deployed as a cartridge within IBM Cloud Pak for Data (CP4D). The installation process involves setting up the necessary components to run AI workloads effectively.  

For detailed instructions on installing the Watsonx.AI cartridge, refer to the official IBM documentation:  
Ref: [IBM Docs - Installing Watsonx.AI](https://www.ibm.com/docs/en/software-hub/5.1.x?topic=watsonxai-installing)  

---

## **Operator Installation**  
Operators are essential for managing and automating the deployment of Watsonx.AI. The `cpd-cli` command is used to install the required operator:  
```sh
cpd-cli manage apply-olm  
--release=5.1.1  
--cpd_operator_ns=cpd-operators  
--components=watsonx_ai  
```

This command ensures that the Watsonx.AI operator is installed in the `cpd-operators` namespace, allowing for seamless management of AI workloads.  

---

## **Specifying Installation Options**  
Watsonx.AI provides various installation options that can be customized to suit specific deployment needs. These options are defined in a YAML configuration file named `install-options.yml` in the working directory.  

If these parameters are not explicitly set, the system will use the default values.  

### **Installation Parameters**  

| **Property**        | **Description** |
|---------------------|----------------|
| **tuning_disabled** | Determines whether **prompt-tuning** is enabled in the Tuning Studio tool. Prompt-tuning requires additional computing resources, so it is recommended to disable it if not needed. |
| **lite_install**    | Specifies whether to install the **full Watsonx.AI service** or the **lightweight Watsonx.AI engine**. The lightweight engine is suitable for resource-constrained environments. |

### **Default Installation Configuration**  
```yaml
custom_spec:  
 watsonx_ai:  
  tuning_disabled: false  
  lite_install: false  
```

By default, Watsonx.AI installs the full service with prompt-tuning enabled. Users can modify these settings based on their resource availability and requirements.  

---

## **Operand Installation**  
After installing the Watsonx.AI operator, the next step is to apply the operands, which are the runtime components required for Watsonx.AI to function properly.  

This can be done using the following `cpd-cli` command:  
```sh
cpd-cli manage apply-cr  
--components=watsonx_ai  
--release=5.1.1
--cpd_instance_ns=${PROJECT_CPD_INST_OPERANDS}  
--block_storage_class=${STG_CLASS_BLOCK}  
--file_storage_class=${STG_CLASS_FILE}  
--param-file=<path_to_working_directory>/install-options.yml  
--license_acceptance=true  
```

This command ensures that Watsonx.AI is deployed in the specified namespace, utilizing the appropriate storage classes and configuration parameters.  

---

## **Model Patching**  
Watsonx.AI allows for the addition of new foundation models post-installation. If a specific model is required, it can be patched into the existing Watsonx.AI instance.  

For example, to add the **Flan-UL2-20B** model to Watsonx.AI, use the following `oc patch` command:  
```sh
oc patch watsonxaiifm watsonxaiifm-cr  
--namespace=${PROJECT_CPD_INST_OPERANDS}  
--type=merge  
--patch='{"spec":{"install_model_list": ["google-flan-ul2"]}}'  
```
This command updates the Watsonx.AI configuration, ensuring that the specified model is available for use in AI workloads.  

---

By following these steps, Watsonx.AI can be successfully installed, configured, and extended with additional foundation models, enabling advanced AI/ML capabilities within the IBM Cloud Pak for Data ecosystem. 
