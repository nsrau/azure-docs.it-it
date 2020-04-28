---
title: Configurare Azure Red Hat OpenShift V4. x con monitoraggio di Azure per i contenitori | Microsoft Docs
description: Questo articolo descrive come configurare il monitoraggio di un cluster Kubernetes con monitoraggio di Azure ospitato in Azure Red Hat OpenShift versione 4 e successive.
ms.topic: conceptual
ms.date: 04/22/2020
ms.openlocfilehash: 4b827524845874dabaabe535163d99c408f77a60
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82196296"
---
# <a name="configure-azure-red-hat-openshift-v4x-with-azure-monitor-for-containers"></a>Configurare Azure Red Hat OpenShift V4. x con monitoraggio di Azure per i contenitori

Monitoraggio di Azure per i contenitori offre un'esperienza di monitoraggio avanzata per i cluster di Azure Kubernetes Service (AKS) e del motore AKS. Questo articolo descrive come abilitare il monitoraggio dei cluster Kubernetes ospitati in [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) versione 4. x per ottenere un'esperienza di monitoraggio simile.

>[!NOTE]
>Il supporto per Azure Red Hat OpenShift è una funzionalità di anteprima pubblica al momento.
>

È possibile abilitare monitoraggio di Azure per i contenitori per una o più distribuzioni esistenti di Azure Red Hat OpenShift V4. x usando i metodi supportati seguenti:

- Per un cluster esistente usando lo script bash fornito ed eseguito nell'interfaccia della riga di comando di [Azure](https://docs.microsoft.com/cli/azure/openshift?view=azure-cli-latest#az-openshift-create).

## <a name="supported-and-unsupported-features"></a>Funzionalità supportate e non supportate

Monitoraggio di Azure per contenitori supporta il monitoraggio di Azure Red Hat OpenShift V4. x, come descritto nell'articolo [introduttivo](container-insights-overview.md) , ad eccezione delle funzionalità seguenti:

- Dati in tempo reale (anteprima)
- [Raccogliere le metriche](container-insights-update-metrics.md) dai nodi e dai pod del cluster e archiviarle nel database di metriche di monitoraggio di Azure

## <a name="prerequisites"></a>Prerequisiti

- Versione 2.0.72 o successiva dell'interfaccia della riga di comando di Azure

- [Helm 3](https://helm.sh/docs/intro/install/) Strumento dell'interfaccia della riga di comando

- [Bash versione 4](https://www.gnu.org/software/bash/)

- Strumento da riga di comando [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

- Per abilitare e accedere alle funzionalità di monitoraggio di Azure per i contenitori, è necessario essere almeno un membro del ruolo *collaboratore* di Azure nella sottoscrizione di Azure e un membro del ruolo [*collaboratore log Analytics*](../platform/manage-access.md#manage-access-using-azure-permissions) dell'area di lavoro log Analytics configurato con monitoraggio di Azure per i contenitori.

- Per visualizzare i dati di monitoraggio, l'utente è membro dell'autorizzazione [*log Analytics Role Reader*](../platform/manage-access.md#manage-access-using-azure-permissions) con l'area di lavoro log Analytics configurata con monitoraggio di Azure per i contenitori.

## <a name="enable-for-an-existing-cluster"></a>Abilita per un cluster esistente

Eseguire la procedura seguente per abilitare il monitoraggio di un cluster Azure Red Hat OpenShift versione 4 e successive distribuito in Azure usando lo script bash fornito.

1. Accesso ad Azure

    ```azurecli
    az login
    ```

2. Scaricare e salvare lo script in una cartella locale che configuri il cluster con il componente aggiuntivo monitoraggio usando i comandi seguenti:

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aroV4/onboarding_azuremonitor_for_containers.sh.`

3. Per identificare il **contesto di Kube** del cluster, dopo avere `oc login` eseguito il comando nel cluster, eseguire il comando `kubectl config current-context` e copiare il valore.

### <a name="integrate-with-an-existing-workspace"></a>Eseguire l'integrazione con un'area di lavoro esistente

Il passaggio seguente consente di monitorare il cluster usando lo script bash scaricato in precedenza. Per eseguire l'integrazione con un'area di lavoro Log Analytics esistente, attenersi alla procedura seguente per identificare prima l'ID risorsa completo dell'area di `workspaceResourceId` lavoro log Analytics necessaria per il parametro, quindi eseguire il comando per abilitare il componente aggiuntivo di monitoraggio per l'area di lavoro specificata. Se non si dispone di un'area di lavoro da specificare, è possibile procedere al passaggio 5 e consentire allo script di creare una nuova area di lavoro.

1. Elencare tutte le sottoscrizioni a cui si ha accesso usando il comando seguente:

    ```azurecli
    az account list --all -o table
    ```

    L'output sarà simile al seguente:

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   68627f8c-91fO-4905-z48q-b032a81f8vy0  Enabled  True
    ```

    Copiare il valore per **SubscriptionId**.

2. Passare alla sottoscrizione che ospita l'area di lavoro Log Analytics usando il comando seguente:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. Nell'esempio seguente viene visualizzato l'elenco delle aree di lavoro nelle sottoscrizioni nel formato JSON predefinito.

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    Nell'output trovare il nome dell'area di lavoro, quindi copiare l'ID risorsa completo dell'area di lavoro Log Analytics sotto l' **ID**campo.

4. Eseguire il comando seguente per abilitare il monitoraggio, sostituendo il valore `workspaceResourceId` per il parametro: 

    `bash onboarding_azuremonitor_for_containers.sh <kube-context> <azureAroV4ResourceId> <LogAnayticsWorkspaceResourceId>`

    Esempio:

    `bash onboarding_azuremonitor_for_containers.sh MyK8sTestCluster /subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/test-aro-v4-rg/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/test-aro-v4  /subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourcegroups/test-la-workspace-rg/providers/microsoft.operationalinsights/workspaces/test-la-workspace`

Dopo aver abilitato il monitoraggio, possono essere necessari circa 15 minuti prima di poter visualizzare le metriche di integrità per il cluster.

### <a name="integrate-with-default-workspace"></a>Integrazione con l'area di lavoro predefinita

Il passaggio seguente abilita il monitoraggio del cluster di Azure Red Hat OpenShift V4. x con lo script bash scaricato. In questo esempio non è necessario creare o specificare un'area di lavoro esistente. Questo comando semplifica il processo creando un'area di lavoro predefinita nel gruppo di risorse predefinito della sottoscrizione del cluster, se non ne esiste già una nell'area. L'area di lavoro predefinita creata ricorda il formato di *DefaultWorkspace-\<GUID>-\<Region>*.  

    `bash onboarding_azuremonitor_for_containers.sh <kube-context> <azureAroV4ResourceId>`

    For example:

    `bash onboarding_azuremonitor_for_containers.sh MyK8sTestCluster /subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/test-aro-v4-rg/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/test-aro-v4`

Dopo aver abilitato il monitoraggio, possono essere necessari circa 15 minuti prima di poter visualizzare le metriche di integrità per il cluster.

### <a name="from-the-azure-portal"></a>Dal portale di Azure

La visualizzazione multicluster in monitoraggio di Azure per i contenitori evidenzia i cluster OpenShift di Azure Red Hat che non dispongono di monitoraggio abilitato nella scheda **cluster non monitorati** . L'opzione **Abilita** accanto al cluster non avvia l'onboarding del monitoraggio dal portale. Si viene reindirizzati a questo articolo per abilitare manualmente il monitoraggio attenendosi alla procedura descritta in precedenza in questo articolo.

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Dal menu portale di Azure o dalla Home page selezionare **monitoraggio di Azure**. Nella sezione **Informazioni dettagliate** selezionare **Contenitori**.

3. Nella pagina **Monitoraggio - Contenitori** selezionare **Cluster non monitorati**.

4. Dall'elenco dei cluster non monitorati trovare il cluster nell'elenco e fare clic su **Abilita**. È possibile identificare i risultati nell'elenco cercando il valore **Aro** sotto il **tipo di cluster**di colonne. Dopo aver fatto clic su **Abilita**, si verrà reindirizzati a questo articolo.

## <a name="next-steps"></a>Passaggi successivi

- Con il monitoraggio abilitato per raccogliere lo stato e l'utilizzo delle risorse del cluster RedHat OpenShift versione 4. x e dei carichi di lavoro in esecuzione su di essi, informazioni su [come usare](container-insights-analyze.md) monitoraggio di Azure per i contenitori.

- Per impostazione predefinita, l'agente in contenitori raccoglie i log del contenitore stdout/stderr di tutti i contenitori in esecuzione in tutti gli spazi dei nomi ad eccezione di Kube-System. Per configurare la raccolta di log del contenitore specifica per determinati spazi dei nomi o spazi dei nomi, esaminare la [configurazione dell'agente di container Insights](container-insights-agent-config.md) per configurare le impostazioni di raccolta dati desiderate nel file di configurazione di ConfigMap.

- Per rimuovere e analizzare le metriche di Prometeo dal cluster, vedere [configurare la metrica di Prometeo](container-insights-prometheus-integration.md)

- Per informazioni su come interrompere il monitoraggio del cluster con monitoraggio di Azure per i contenitori, vedere [come arrestare il monitoraggio del cluster OpenShift di Azure Red Hat](container-insights-optout-openshift.md).
