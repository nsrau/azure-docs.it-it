---
title: Configurare i cluster OpenShift di Azure Red Hat con monitoraggio di Azure per i contenitori | Microsoft Docs
description: Questo articolo descrive come configurare monitoraggio di Azure per i contenitori per monitorare i cluster Kubernetes ospitati in Azure Red Hat OpenShift.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 11/21/2019
ms.openlocfilehash: 07d84175325226af755712ec38ab93b901bbd361
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707517"
---
# <a name="configure-azure-red-hat-openshift-clusters-with-azure-monitor-for-containers"></a>Configurare i cluster OpenShift di Azure Red Hat con monitoraggio di Azure per i contenitori

Monitoraggio di Azure per i contenitori offre un'esperienza di monitoraggio avanzata per i cluster di Azure Kubernetes Service (AKS) e del motore AKS. Questo articolo descrive come abilitare il monitoraggio dei cluster Kubernetes ospitati in [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) per ottenere un'esperienza di monitoraggio simile.

>[!NOTE]
>Il supporto per Azure Red Hat OpenShift è una funzionalità di anteprima pubblica al momento.
>

È possibile abilitare il monitoraggio di Azure per i contenitori per le distribuzioni nuove o di una o più distribuzioni esistenti di Azure Red Hat OpenShift usando i metodi supportati seguenti:

- Per un cluster esistente dal portale di Azure o utilizzando Azure Resource Manager modello
- Per un nuovo cluster che usa il modello di Azure Resource Manager 

## <a name="supported-and-unsupported-features"></a>Funzionalità supportate e non supportate

Monitoraggio di Azure per contenitori supporta il monitoraggio di Azure Red Hat OpenShift, come descritto nell'articolo [introduttivo](container-insights-overview.md) , ad eccezione delle funzionalità seguenti:

- Dati dinamici
- Frammentazione metrica Prometeo
- [Raccogliere le metriche](container-insights-update-metrics.md) dai nodi e dai pod del cluster e archiviarle nel database di metriche di monitoraggio di Azure
- Funzionalità integrità

## <a name="prerequisites"></a>Prerequisiti

- Per abilitare e accedere alle funzionalità di monitoraggio di Azure per i contenitori, è necessario essere almeno un membro del ruolo *collaboratore* di Azure nella sottoscrizione di Azure e un membro del ruolo [*collaboratore log Analytics*](../platform/manage-access.md#manage-access-using-azure-permissions) dell'area di lavoro log Analytics configurato con monitoraggio di Azure per i contenitori.

- Per visualizzare i dati di monitoraggio, l'utente è membro dell'autorizzazione [*log Analytics Role Reader*](../platform/manage-access.md#manage-access-using-azure-permissions) con l'area di lavoro log Analytics configurata con monitoraggio di Azure per i contenitori.

## <a name="enable-for-a-new-cluster-using-an-azure-resource-manager-template"></a>Abilitare per un nuovo cluster usando un modello di Azure Resource Manager

Eseguire la procedura seguente per distribuire un cluster Azure Red Hat OpenShift con il monitoraggio abilitato. Prima di procedere, vedere l'esercitazione [creare un cluster Azure Red Hat OpenShift](../../openshift/tutorial-create-cluster.md#prerequisites) per comprendere le dipendenze che è necessario configurare in modo che l'ambiente sia configurato correttamente.

Questo metodo include due modelli JSON. Un modello specifica la configurazione per distribuire il cluster con il monitoraggio abilitato e l'altro contiene i valori dei parametri configurati per specificare gli elementi seguenti:

- ID della risorsa cluster OpenShift di Azure Red Hat. 

- Il gruppo di risorse in cui è distribuito il cluster.

- [Azure Active Directory ID tenant](../../openshift/howto-create-tenant.md#create-a-new-azure-ad-tenant) indicato dopo aver eseguito i passaggi per crearne uno o uno già creato.

- [Azure Active Directory ID applicazione client](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-app-registration) annotato dopo aver eseguito i passaggi per crearne uno o uno già creato.

- [Azure Active Directory segreto client](../../openshift/howto-aad-app-configuration.md#create-a-client-secret) annotato dopo aver eseguito i passaggi per crearne uno o uno già creato.

- [Azure ad gruppo di sicurezza](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-security-group) annotato dopo aver eseguito i passaggi per crearne uno o uno già creato.

- ID risorsa di un'area di lavoro Log Analytics esistente.

- Il numero di nodi master da creare nel cluster.

- Numero di nodi di calcolo nel profilo del pool di agenti.

- Il numero di nodi dell'infrastruttura nel profilo del pool di agenti. 

Se non si ha familiarità con il concetto di distribuzione delle risorse tramite un modello, vedere:

- [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)

- [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Se si sceglie di usare l'interfaccia della riga di comando di Azure, è prima necessario installarla ed eseguirla in locale. È necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.65 o successiva. Per identificare la versione in uso, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

Prima di abilitare il monitoraggio tramite Azure PowerShell o CLI, è necessario creare l'area di lavoro Log Analytics. Per creare l'area di lavoro, è possibile configurarla tramite [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md), [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json) o il [portale di Azure](../../azure-monitor/learn/quick-create-workspace.md).

1. Scaricare e salvare in una cartella locale, il modello di Azure Resource Manager e il file dei parametri, per creare un cluster con il componente aggiuntivo monitoraggio usando i comandi seguenti:

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoring.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoringParam.json` 

2. Accedere a Azure 

    ```azurecli
    az login    
    ```
    
    Se si ha accesso a più sottoscrizioni, eseguire `az account set -s {subscription ID}` sostituendo `{subscription ID}` con la sottoscrizione che si vuole usare.
 
3. Creare un gruppo di risorse per il cluster, se non ne è già presente uno. Per un elenco delle aree di Azure che supportano OpenShift in Azure, vedere [aree supportate](../../openshift/supported-resources.md#azure-regions). 

    ```azurecli
    az group create -g <clusterResourceGroup> -l <location> 
    ```

4. Modificare il file di parametri JSON **newClusterWithMonitoringParam. JSON** e aggiornare i valori seguenti:

    - *location*
    - *NomeCluster*
    - *aadTenantId*
    - *aadClientId*
    - *aadClientSecret* 
    - *aadCustomerAdminGroupId* 
    - *workspaceResourceId*
    - *masterNodeCount*
    - *computeNodeCount*
    - *infraNodeCount*

5. Il passaggio seguente consente di distribuire il cluster con monitoraggio abilitato mediante l'interfaccia della riga di comando di Azure. 

    ```azurecli
    az group deployment create --resource-group <ClusterResourceGroupName> --template-file ./newClusterWithMonitoring.json --parameters @./newClusterWithMonitoringParam.json 
    ```
 
    L'output sarà simile al seguente:

    ```azurecli
    provisioningState       : Succeeded
    ```

## <a name="enable-for-an-existing-cluster"></a>Abilita per un cluster esistente

Eseguire la procedura seguente per abilitare il monitoraggio di un cluster OpenShift di Azure Red Hat distribuito in Azure. È possibile eseguire questa operazione dalla portale di Azure o utilizzando i modelli forniti.

### <a name="from-the-azure-portal"></a>Dal portale di Azure
 
1. Accedere al [portale di Azure](https://portal.azure.com).

2. Dal menu portale di Azure o dalla Home page selezionare **monitoraggio di Azure**. Nella sezione **Informazioni dettagliate** selezionare **Contenitori**. 

3. Nella pagina **Monitoraggio - Contenitori** selezionare **Cluster non monitorati**.

4. Dall'elenco dei cluster non monitorati trovare il cluster nell'elenco e fare clic su **Abilita**. È possibile identificare i risultati nell'elenco cercando il valore **Aro** sotto il **tipo di cluster**di colonne.

5. Se nella pagina **Onboarding di Monitoraggio di Azure per i contenitori** è già presente un'area di lavoro Log Analytics nella stessa sottoscrizione del cluster, selezionarla nell'elenco a discesa.  
    L'elenco preseleziona l'area di lavoro predefinita e la posizione in cui è distribuito il cluster nella sottoscrizione. 

    ![Abilitare il monitoraggio per i cluster non monitorati](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >Se si vuole creare una nuova area di lavoro Log Analytics per archiviare i dati di monitoraggio dal cluster, seguire le istruzioni in [Creare un'area di lavoro Log Analytics](../../azure-monitor/learn/quick-create-workspace.md). Assicurarsi di creare l'area di lavoro nella stessa sottoscrizione in cui è distribuito il cluster RedHat OpenShift. 
 
Dopo aver abilitato il monitoraggio, possono essere necessari circa 15 minuti prima di poter visualizzare le metriche di integrità per il cluster. 

### <a name="enable-using-an-azure-resource-manager-template"></a>Abilitare l'uso di un modello di Azure Resource Manager

Questo metodo include due modelli JSON. Un modello JSON specifica la configurazione per abilitare il monitoraggio e l'altro contiene i valori dei parametri da configurare per specificare quanto segue:

- ID risorsa cluster OpenShift di Azure RedHat. 

- Il gruppo di risorse in cui è distribuito il cluster.

- Un'area di lavoro Log Analytics.

Se non si ha familiarità con il concetto di distribuzione delle risorse tramite un modello, vedere:

- [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)

- [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Se si sceglie di usare l'interfaccia della riga di comando di Azure, è prima necessario installarla ed eseguirla in locale. È necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.65 o successiva. Per identificare la versione in uso, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

Prima di abilitare il monitoraggio tramite Azure PowerShell o CLI, è necessario creare l'area di lavoro Log Analytics. Per creare l'area di lavoro, è possibile configurarla tramite [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md), [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json) o il [portale di Azure](../../azure-monitor/learn/quick-create-workspace.md).

1. Scaricare il modello e il file dei parametri per aggiornare il cluster con il componente aggiuntivo di monitoraggio usando i comandi seguenti:

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_existing_cluster/existingClusterOnboarding.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_existing_cluster/existingClusterParam.json` 

2. Accedere a Azure 

    ```azurecli
    az login    
    ```

    Se si ha accesso a più sottoscrizioni, eseguire `az account set -s {subscription ID}` sostituendo `{subscription ID}` con la sottoscrizione che si vuole usare.

3. Specificare la sottoscrizione del cluster OpenShift di Azure RedHat.

    ```azurecli
    az account set --subscription "Subscription Name"  
    ```

4. Eseguire il comando seguente per identificare la posizione del cluster e l'ID risorsa:

    ```azurecli
    az openshift show -g <clusterResourceGroup> -n <clusterName> 
    ```

5. Modificare il file di parametri JSON **existingClusterParam. JSON** e aggiornare i valori *araResourceId* e *araResoruceLocation*. Il valore per **workspaceResourceId** è l'ID risorsa completo dell'area di lavoro Log Analytics, che include il nome dell'area di lavoro. 

6. Per la distribuzione con l'interfaccia della riga di comando di Azure, eseguire i comandi seguenti: 

    ```azurecli
    az group deployment create --resource-group <ClusterResourceGroupName> --template-file ./ExistingClusterOnboarding.json --parameters @./existingClusterParam.json 
    ```

    L'output sarà simile al seguente:

    ```azurecli
    provisioningState       : Succeeded
    ```

## <a name="next-steps"></a>Passaggi successivi

- Con il monitoraggio abilitato per raccogliere lo stato e l'utilizzo delle risorse del cluster RedHat OpenShift e dei carichi di lavoro in esecuzione su di essi, Scopri [come usare](container-insights-analyze.md) monitoraggio di Azure per i contenitori.

- Per informazioni su come interrompere il monitoraggio del cluster con monitoraggio di Azure per i contenitori, vedere [come arrestare il monitoraggio del cluster OpenShift di Azure Red Hat](container-insights-optout-openshift.md).