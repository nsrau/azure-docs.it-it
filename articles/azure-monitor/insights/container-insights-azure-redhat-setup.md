---
title: Configure Azure Red Hat OpenShift clusters with Azure Monitor for containers | Microsoft Docs
description: This article describes how you can configure Azure Monitor for containers to monitor Kubernetes clusters hosted on Azure Red Hat OpenShift.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 11/21/2019
ms.openlocfilehash: 965ebdb5b6450a0826872ac31d96d8e61b7542f5
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74382267"
---
# <a name="configure-azure-red-hat-openshift-clusters-with-azure-monitor-for-containers"></a>Configure Azure Red Hat OpenShift clusters with Azure Monitor for containers

Azure Monitor for containers provides rich monitoring experience for the Azure Kubernetes Service (AKS) and AKS Engine clusters. This article describes how to enable monitoring of Kubernetes clusters hosted on [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) to achieve a similar monitoring experience.

>[!NOTE]
>Support for Red Hat OpenShift is a feature in public preview at this time.
>

Azure Monitor for containers can be enabled for new, or one or more existing deployments of Azure Red Hat OpenShift using the following supported methods:

- For an existing cluster from the Azure portal or using Azure Resource Manager template
- For a new cluster using Azure Resource Manager template 

## <a name="supported-and-unsupported-features"></a>Supported and unsupported features

Azure Monitor for containers supports monitoring Azure Red Hat OpenShift as described in the [Overview](container-insights-overview.md) article, except for the following features:

- Live data
- Prometheus metrics scraping
- [Collect metrics](container-insights-update-metrics.md) from cluster nodes and pods and storing them in the Azure Monitor metrics database
- Health feature

## <a name="prerequisites"></a>Prerequisiti

- To enable and access the features in Azure Monitor for containers, at a minimum you need to be a member of the Azure *Contributor* role in the Azure subscription, and a member of the [*Log Analytics Contributor*](../platform/manage-access.md#manage-access-using-azure-permissions) role of the Log Analytics workspace configured with Azure Monitor for containers.

- To view the monitoring data, you are a member of the [*Log Analytics reader*](../platform/manage-access.md#manage-access-using-azure-permissions) role permission with the Log Analytics workspace configured with Azure Monitor for containers.

## <a name="enable-for-a-new-cluster-using-an-azure-resource-manager-template"></a>Enable for a new cluster using an Azure Resource Manager template

Perform the following steps to deploy an Azure Red Hat OpenShift cluster with monitoring enabled. Before proceeding, review the tutorial [Create an Azure Red Hat OpenShift cluster](../../openshift/tutorial-create-cluster.md#prerequisites) to understand the dependencies that you need to configure so your environment is set up correctly.

Questo metodo include due modelli JSON. One template specifies the configuration to deploy the cluster with monitoring enabled, and the other contains parameter values that you configure to specify the following:

- The Azure Red Hat OpenShift cluster resource ID. 

- The resource group the cluster is deployed in.

- [Azure Active Directory tenant ID](../../openshift/howto-create-tenant.md#create-a-new-azure-ad-tenant) noted after performing the steps to create one or one already created.

- [Azure Active Directory client application ID](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-app-registration) noted after performing the steps to create one or one already created.

- [Azure Active Directory Client secret](../../openshift/howto-aad-app-configuration.md#create-a-client-secret) noted after performing the steps to create one or one already created.

- [Azure AD security group](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-security-group) noted after performing the steps to create one or one already created.

- Resource ID of an existing Log Analytics workspace.

- The number of master nodes to create in the cluster.

- The number of compute nodes in the agent pool profile.

- The number of infrastructure nodes in the agent pool profile. 

Se non si ha familiarità con il concetto di distribuzione delle risorse tramite un modello, vedere:

- [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)

- [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Se si sceglie di usare l'interfaccia della riga di comando di Azure, è prima necessario installarla ed eseguirla in locale. You must be running the Azure CLI version 2.0.65 or later. Per identificare la versione in uso, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

The Log Analytics workspace has to be created before you enable monitoring using Azure PowerShell or CLI. Per creare l'area di lavoro, è possibile configurarla tramite [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md), [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json) o il [portale di Azure](../../azure-monitor/learn/quick-create-workspace.md).

1. Download and save to a local folder, the Azure Resource Manager template and parameter file, to create a cluster with the monitoring add-on using the following commands:

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoring.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoringParam.json` 

2. Accedere a Azure 

    ```azurecli
    az login    
    ```
    
    Se si ha accesso a più sottoscrizioni, eseguire `az account set -s {subscription ID}` sostituendo `{subscription ID}` con la sottoscrizione che si vuole usare.
 
3. Create a resource group for your cluster if you don't already have one. For a list of Azure regions that supports OpenShift on Azure, see [Supported Regions](../../openshift/supported-resources.md#azure-regions). 

    ```azurecli
    az group create -g <clusterResourceGroup> -l <location> 
    ```

4. Edit the JSON parameter file **newClusterWithMonitoringParam.json** and update the following values:

    - *location*
    - *clusterName*
    - *aadTenantId*
    - *aadClientId*
    - *aadClientSecret* 
    - *aadCustomerAdminGroupId* 
    - *workspaceResourceId*
    - *masterNodeCount*
    - *computeNodeCount*
    - *infraNodeCount*

5. The following step deploys the cluster with monitoring enabled by using the Azure CLI. 

    ```azurecli
    az group deployment create --resource-group <ClusterResourceGroupName> --template-file ./newClusterWithMonitoring.json --parameters @./newClusterWithMonitoringParam.json 
    ```
 
    L'output sarà simile al seguente:

    ```azurecli
    provisioningState       : Succeeded
    ```

## <a name="enable-for-an-existing-cluster"></a>Enable for an existing cluster

Perform the following steps to enable monitoring of an Azure Red Hat OpenShift cluster deployed in Azure. You can accomplish this from the Azure portal or using the provided templates.

### <a name="from-the-azure-portal"></a>Dal portale di Azure
 
1. Accedere al [portale di Azure](https://portal.azure.com).

2. On the Azure portal menu or from the Home page, select **Azure Monitor**. Nella sezione **Informazioni dettagliate** selezionare **Contenitori**. 

3. Nella pagina **Monitoraggio - Contenitori** selezionare **Cluster non monitorati**.

4. From the list of non-monitored clusters, find the cluster in the list and click **Enable**. You can identify the results in the list by looking for the value **ARO** under the column **CLUSTER TYPE**.

5. Se nella pagina **Onboarding di Monitoraggio di Azure per i contenitori** è già presente un'area di lavoro Log Analytics nella stessa sottoscrizione del cluster, selezionarla nell'elenco a discesa.  
    The list preselects the default workspace and location that the cluster is deployed to in the subscription. 

    ![Enable monitoring for non-monitored clusters](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >Se si vuole creare una nuova area di lavoro Log Analytics per archiviare i dati di monitoraggio dal cluster, seguire le istruzioni in [Creare un'area di lavoro Log Analytics](../../azure-monitor/learn/quick-create-workspace.md). Be sure to create the workspace in the same subscription that the RedHat OpenShift cluster is deployed to. 
 
Dopo aver abilitato il monitoraggio, possono essere necessari circa 15 minuti prima di poter visualizzare le metriche di integrità per il cluster. 

### <a name="enable-using-an-azure-resource-manager-template"></a>Enable using an Azure Resource Manager template

Questo metodo include due modelli JSON. Un modello JSON specifica la configurazione per abilitare il monitoraggio e l'altro contiene i valori dei parametri da configurare per specificare quanto segue:

- The Azure RedHat OpenShift cluster resource ID. 

- The resource group the cluster is deployed in.

- Un'area di lavoro Log Analytics.

Se non si ha familiarità con il concetto di distribuzione delle risorse tramite un modello, vedere:

- [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)

- [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Se si sceglie di usare l'interfaccia della riga di comando di Azure, è prima necessario installarla ed eseguirla in locale. You must be running the Azure CLI version 2.0.65 or later. Per identificare la versione in uso, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

The Log Analytics workspace has to be created before you enable monitoring using Azure PowerShell or CLI. Per creare l'area di lavoro, è possibile configurarla tramite [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md), [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json) o il [portale di Azure](../../azure-monitor/learn/quick-create-workspace.md).

1. Download the template and parameter file to update your cluster with the monitoring add-on using the following commands:

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_existing_cluster/existingClusterOnboarding.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_existing_cluster/existingClusterParam.json` 

2. Accedere a Azure 

    ```azurecli
    az login    
    ```

    Se si ha accesso a più sottoscrizioni, eseguire `az account set -s {subscription ID}` sostituendo `{subscription ID}` con la sottoscrizione che si vuole usare.

3. Specify the subscription of the Azure RedHat OpenShift cluster.

    ```azurecli
    az account set --subscription "Subscription Name"  
    ```

4. Run the following command to identify the cluster location and resource ID:

    ```azurecli
    az openshift show -g <clusterResourceGroup> -n <clusterName> 
    ```

5. Edit the JSON parameter file **existingClusterParam.json** and update the values *araResourceId* and *araResoruceLocation*. Il valore per **workspaceResourceId** è l'ID risorsa completo dell'area di lavoro Log Analytics, che include il nome dell'area di lavoro. 

6. To deploy with Azure CLI, run the following commands: 

    ```azurecli
    az group deployment create --resource-group <ClusterResourceGroupName> --template-file ./ExistingClusterOnboarding.json --parameters @./existingClusterParam.json 
    ```

    L'output sarà simile al seguente:

    ```azurecli
    provisioningState       : Succeeded
    ```

## <a name="next-steps"></a>Passaggi successivi

- With monitoring enabled to collect health and resource utilization of your RedHat OpenShift cluster and workloads running on them, learn [how to use](container-insights-analyze.md) Azure Monitor for containers.

- To learn how to stop monitoring your cluster with Azure Monitor for containers, see [How to Stop Monitoring Your Azure Red Hat OpenShift cluster](container-insights-optout-openshift.md).