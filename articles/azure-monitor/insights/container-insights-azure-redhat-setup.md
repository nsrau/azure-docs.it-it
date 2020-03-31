---
title: Configurare i cluster OpenShift di Azure Red Hat con Monitoraggio di Azure per i contenitori. Documenti Microsoft
description: Questo articolo descrive come configurare il monitoraggio di un cluster Kubernetes con Monitoraggio di Azure ospitato in Azure Red Hat OpenShift.This article describes how to configure monitoring of a Kubernetes cluster with Azure Monitor hosted on Azure Red Hat OpenShift.
ms.topic: conceptual
ms.date: 02/12/2020
ms.openlocfilehash: c2fd3568be2c51296bb1377e91031ebfb7ca6ee3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275516"
---
# <a name="configure-azure-red-hat-openshift-clusters-with-azure-monitor-for-containers"></a>Configurare i cluster OpenShift di Azure Red Hat con Monitoraggio di Azure per i contenitoriConfigure Azure Red Hat OpenShift clusters with Azure Monitor for containers

Monitoraggio di Azure per i contenitori offre un'esperienza di monitoraggio avanzata per i cluster del servizio Kubernetes di Azure (AKS) e del motore AKS. Questo articolo descrive come abilitare il monitoraggio dei cluster Kubernetes ospitati in [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) per ottenere un'esperienza di monitoraggio simile.

>[!NOTE]
>Il supporto per Azure Red Hat OpenShift è una funzionalità in anteprima pubblica in questo momento.
>

Monitoraggio di Azure per i contenitori può essere abilitato per una o più distribuzioni nuove o per una o più distribuzioni esistenti di Azure Red Hat OpenShift usando i metodi supportati seguenti:Azure Monitor for containers can be enabled for new, or one or more existing deployments of Azure Red Hat OpenShift using the following supported methods:

- Per un cluster esistente dal portale di Azure o nel modello di Azure Resource Manager.For an existing cluster from the Azure portal or using Azure Resource Manager template.
- Per un nuovo cluster usando il modello di Azure Resource Manager o durante la creazione di un nuovo cluster usando [l'interfaccia della riga di comando](https://docs.microsoft.com/cli/azure/openshift?view=azure-cli-latest#az-openshift-create)di Azure.For a new cluster using Azure Resource Manager template, or while creating a new cluster using the Azure CLI .

## <a name="supported-and-unsupported-features"></a>Funzionalità supportate e non supportate

Monitoraggio di Azure per i contenitori supporta il monitoraggio di Azure Red Hat OpenShift come descritto nell'articolo [Panoramica,](container-insights-overview.md) ad eccezione delle funzionalità seguenti:Azure Monitor for containers supports Monitoring Azure Red Hat OpenShift as described in the Overview article, except for the following features:

- Live Data (anteprima)
- [Raccogliere metriche](container-insights-update-metrics.md) da nodi e pod del cluster e archiviarle nel database delle metriche di Monitoraggio di AzureCollect metrics from cluster nodes and pods and storing them in the Azure Monitor metrics database

## <a name="prerequisites"></a>Prerequisiti

- Per abilitare e accedere alle funzionalità di Monitoraggio di Azure per i contenitori, è necessario almeno essere membri del ruolo *Collaboratore* di Azure nella sottoscrizione di Azure e un membro del ruolo [*Collaboratore*](../platform/manage-access.md#manage-access-using-azure-permissions) di Log Analytics dell'area di lavoro Log Analytics configurato con Monitoraggio di Azure per i contenitori.

- Per visualizzare i dati di monitoraggio, si è membri dell'autorizzazione del ruolo [*lettore di Log Analytics*](../platform/manage-access.md#manage-access-using-azure-permissions) con l'area di lavoro Di Log Analytics configurata con Monitoraggio di Azure per i contenitori.

## <a name="enable-for-a-new-cluster-using-an-azure-resource-manager-template"></a>Abilitare per un nuovo cluster usando un modello di Azure Resource ManagerEnable for a new cluster using an Azure Resource Manager template

Eseguire la procedura seguente per distribuire un cluster Azure Red Hat OpenShift con il monitoraggio abilitato. Prima di procedere, esaminare l'esercitazione [Creare un cluster Azure Red Hat OpenShift](../../openshift/tutorial-create-cluster.md#prerequisites) per comprendere le dipendenze che è necessario configurare in modo che l'ambiente sia configurato correttamente.

Questo metodo include due modelli JSON. Un modello specifica la configurazione per distribuire il cluster con il monitoraggio abilitato e l'altro contiene valori di parametro configurati per specificare quanto segue:

- ID risorsa cluster OpenShift di Azure Red Hat.

- Il gruppo di risorse in cui viene distribuito il cluster.

- [ID tenant](../../openshift/howto-create-tenant.md#create-a-new-azure-ad-tenant) di Azure Active Directory annotato dopo aver eseguito i passaggi per crearne uno o uno già creato.

- [ID applicazione client di Azure Active Directory](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-app-registration) annotato dopo aver eseguito i passaggi per crearne uno o uno già creato.

- [Segreto client di Azure Active Directory](../../openshift/howto-aad-app-configuration.md#create-a-client-secret) annotato dopo aver eseguito i passaggi per creare uno o uno già creato.

- [Gruppo](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-security-group) di sicurezza di Azure AD annotato dopo aver eseguito i passaggi per creare uno o uno già creato.

- ID risorsa di un'area di lavoro di Log Analytics esistente.

- Numero di nodi master da creare nel cluster.

- Numero di nodi di calcolo nel profilo del pool di agenti.

- Numero di nodi dell'infrastruttura nel profilo del pool di agenti.

Se non si ha familiarità con il concetto di distribuzione delle risorse tramite un modello, vedere:

- [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)

- [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](../../azure-resource-manager/templates/deploy-cli.md)

Se si sceglie di usare l'interfaccia della riga di comando di Azure, è prima necessario installarla ed eseguirla in locale. È necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.65 o successiva. Per identificare la versione in uso, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

L'area di lavoro log Analytics deve essere creata prima di abilitare il monitoraggio tramite Azure PowerShell o l'interfaccia della riga di comando. Per creare l'area di lavoro, è possibile configurarla tramite [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md), [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json) o il [portale di Azure](../../azure-monitor/learn/quick-create-workspace.md).

1. Scaricare e salvare in una cartella locale, il file di modello e parametri di Azure Resource Manager, per creare un cluster con il componente aggiuntivo di monitoraggio usando i comandi seguenti:Download and save to a local folder, the Azure Resource Manager template and parameter file, to create a cluster with the monitoring add-on using the following commands:

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoring.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoringParam.json`

2. Accedere ad Azure

    ```azurecli
    az login    
    ```

    Se si ha accesso a più sottoscrizioni, eseguire `az account set -s {subscription ID}` sostituendo `{subscription ID}` con la sottoscrizione che si vuole usare.

3. Creare un gruppo di risorse per il cluster, se non è già presente. Per un elenco delle aree di Azure che supporta OpenShift in Azure, vedere [Aree supportate](../../openshift/supported-resources.md#azure-regions).

    ```azurecli
    az group create -g <clusterResourceGroup> -l <location>
    ```

4. Modificare il file di parametri JSON **newClusterWithMonitoringParam.json** e aggiornare i valori seguenti:

    - *Posizione*
    - *clusterName (nome cluster)*
    - *aadTenantId*
    - *aadClientId (informazioni in due)*
    - *aadClientSecret*
    - *aadCustomerAdminGroupId*
    - *workspaceResourceId*
    - *masterNodeCount*
    - *computeNodeCount (conteggio di calcolo)*
    - *infraNodeCount (informazioni in sul nome del presente*

5. The following step deploys the cluster with monitoring enabled by using the Azure CLI.

    ```azurecli
    az group deployment create --resource-group <ClusterResourceGroupName> --template-file ./newClusterWithMonitoring.json --parameters @./newClusterWithMonitoringParam.json
    ```

    L'output sarà simile al seguente:

    ```output
    provisioningState       : Succeeded
    ```

## <a name="enable-for-an-existing-cluster"></a>Abilitare per un cluster esistenteEnable for an existing cluster

Eseguire la procedura seguente per abilitare il monitoraggio di un cluster Azure Red Hat OpenShift distribuito in Azure.Perform the following steps to enable monitoring of an Azure Red Hat OpenShift cluster deployed in Azure. È possibile eseguire questa operazione dal portale di Azure o usando i modelli forniti.

### <a name="from-the-azure-portal"></a>Dal portale di Azure

1. Accedere al [portale](https://portal.azure.com)di Azure .

2. Nel menu del portale di Azure o nella home page selezionare **Monitoraggio di Azure**. Nella sezione **Informazioni dettagliate** selezionare **Contenitori**.

3. Nella pagina **Monitoraggio - Contenitori** selezionare **Cluster non monitorati**.

4. Nell'elenco dei cluster non monitorati individuare il cluster nell'elenco e fare clic su **Abilita**. È possibile identificare i risultati nell'elenco cercando il valore **ARO** nella colonna **TIPO CLUSTER**.

5. Se nella pagina **Onboarding di Monitoraggio di Azure per i contenitori** è già presente un'area di lavoro Log Analytics nella stessa sottoscrizione del cluster, selezionarla nell'elenco a discesa.  
    L'elenco preseleziona l'area di lavoro e il percorso predefiniti in cui viene distribuito il cluster nella sottoscrizione.

    ![Abilitare il monitoraggio per i cluster non monitoratiEnable monitoring for non-monitored clusters](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >Se si vuole creare una nuova area di lavoro Log Analytics per archiviare i dati di monitoraggio dal cluster, seguire le istruzioni in [Creare un'area di lavoro Log Analytics](../../azure-monitor/learn/quick-create-workspace.md). Assicurarsi di creare l'area di lavoro nella stessa sottoscrizione in cui viene distribuito il cluster RedHat OpenShift.

Dopo aver abilitato il monitoraggio, possono essere necessari circa 15 minuti prima di poter visualizzare le metriche di integrità per il cluster.

### <a name="enable-using-an-azure-resource-manager-template"></a>Abilitare l'uso di un modello di Azure Resource ManagerEnable using an Azure Resource Manager template

Questo metodo include due modelli JSON. Un modello JSON specifica la configurazione per abilitare il monitoraggio e l'altro contiene i valori dei parametri da configurare per specificare quanto segue:

- ID risorsa cluster Azure RedHat OpenShift.The Azure RedHat OpenShift cluster resource ID.

- Il gruppo di risorse in cui viene distribuito il cluster.

- Un'area di lavoro Log Analytics.

Se non si ha familiarità con il concetto di distribuzione delle risorse tramite un modello, vedere:

- [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)

- [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](../../azure-resource-manager/templates/deploy-cli.md)

Se si sceglie di usare l'interfaccia della riga di comando di Azure, è prima necessario installarla ed eseguirla in locale. È necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.65 o successiva. Per identificare la versione in uso, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

L'area di lavoro log Analytics deve essere creata prima di abilitare il monitoraggio tramite Azure PowerShell o l'interfaccia della riga di comando. Per creare l'area di lavoro, è possibile configurarla tramite [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md), [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json) o il [portale di Azure](../../azure-monitor/learn/quick-create-workspace.md).

1. Scaricare il file di modello e di parametro per aggiornare il cluster con il componente aggiuntivo di monitoraggio utilizzando i comandi seguenti:

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_existing_cluster/existingClusterOnboarding.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_existing_cluster/existingClusterParam.json`

2. Accedere ad Azure

    ```azurecli
    az login    
    ```

    Se si ha accesso a più sottoscrizioni, eseguire `az account set -s {subscription ID}` sostituendo `{subscription ID}` con la sottoscrizione che si vuole usare.

3. Specificare la sottoscrizione del cluster Azure RedHat OpenShift.

    ```azurecli
    az account set --subscription "Subscription Name"  
    ```

4. Eseguire il comando seguente per identificare la posizione del cluster e l'ID risorsa:

    ```azurecli
    az openshift show -g <clusterResourceGroup> -n <clusterName>
    ```

5. Modificare il file di parametri JSON **existingClusterParam.json** e aggiornare i valori *araResourceId* e *araResoruceLocation*. Il valore per **workspaceResourceId** è l'ID risorsa completo dell'area di lavoro Log Analytics, che include il nome dell'area di lavoro.

6. Per eseguire la distribuzione con l'interfaccia della riga di comando di Azure, eseguire i comandi seguenti:To deploy with Azure CLI, run the following commands:

    ```azurecli
    az group deployment create --resource-group <ClusterResourceGroupName> --template-file ./ExistingClusterOnboarding.json --parameters @./existingClusterParam.json
    ```

    L'output sarà simile al seguente:

    ```output
    provisioningState       : Succeeded
    ```

## <a name="next-steps"></a>Passaggi successivi

- Con il monitoraggio abilitato per raccogliere l'integrità e l'utilizzo delle risorse del cluster RedHat OpenShift e dei carichi di lavoro in esecuzione su di essi, scopri [come usare](container-insights-analyze.md) Monitoraggio di Azure per i contenitori.

- Per informazioni su come interrompere il monitoraggio del cluster con Monitoraggio di Azure per i contenitori, vedere Come interrompere il [monitoraggio del cluster OpenShift](container-insights-optout-openshift.md)di Azure Red Hat.
