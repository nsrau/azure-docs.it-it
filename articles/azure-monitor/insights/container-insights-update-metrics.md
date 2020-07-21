---
title: Come aggiornare monitoraggio di Azure per i contenitori per le metriche | Microsoft Docs
description: Questo articolo descrive come aggiornare monitoraggio di Azure per i contenitori per abilitare la funzionalità metrica personalizzata che supporta l'esplorazione e l'invio di avvisi sulle metriche aggregate.
ms.topic: conceptual
ms.date: 07/17/2020
ms.openlocfilehash: 78a6612e522accce8c934885a090e66a51850c97
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86498985"
---
# <a name="how-to-update-azure-monitor-for-containers-to-enable-metrics"></a>Come aggiornare Monitoraggio di Azure per contenitori per abilitare le metriche

Il monitoraggio di Azure per i contenitori introduce il supporto per la raccolta di metriche dai nodi e dai cluster dei servizi Kubernetes di Azure (AKS) e li scrive nell'archivio delle metriche di monitoraggio di Azure. Questa modifica ha lo scopo di offrire una tempestività migliorata quando si presentano calcoli di aggregazione (AVG, Count, Max, min, Sum) nei grafici delle prestazioni, supporta l'aggiunta di grafici delle prestazioni in Dashboard portale di Azure e il supporto degli avvisi delle metriche.

>[!NOTE]
>Questa funzionalità non supporta attualmente i cluster OpenShift di Azure Red Hat.
>

Come parte di questa funzionalità sono abilitate le metriche seguenti:

| Spazio dei nomi delle metriche | Metrica | Descrizione |
|------------------|--------|-------------|
| Insights. contenitore/nodi | cpuUsageMillicores, cpuUsagePercentage, memoryRssBytes, memoryRssPercentage, memoryWorkingSetBytes, memoryWorkingSetPercentage, nodesCount, diskUsedPercentage, | Come metrica del *nodo* , includono *host* come dimensione. Includono anche il<br> nome del nodo come valore per la dimensione *host* . |
| Insights. container/Pod | podCount, completedJobsCount, restartingContainerCount, oomKilledContainerCount, podReadyPercentage | Come metrica *Pod* , includono quanto segue come Dimensions: controllerName, spazio dei nomi Kubernetes, nome, fase. |
| Insights. contenitore/contenitori | cpuExceededPercentage, memoryRssExceededPercentage, memoryWorkingSetExceededPercentage | |

Per supportare queste nuove funzionalità, un nuovo agente in contenitori, versione **Microsoft/OMS: ciprod02212019**, è incluso nella versione. Le nuove distribuzioni di AKS includono automaticamente questa modifica di configurazione e le funzionalità. L'aggiornamento del cluster per supportare questa funzionalità può essere eseguito dalla portale di Azure, Azure PowerShell o con l'interfaccia della riga di comando di Azure. Con Azure PowerShell e l'interfaccia della riga di comando. È possibile abilitare questa operazione per cluster o per tutti i cluster nella sottoscrizione.

Entrambi i processi assegnano il ruolo **server di pubblicazione metriche di monitoraggio** all'entità servizio del cluster o all'identità del servizio gestito assegnata dall'utente per il componente aggiuntivo di monitoraggio, in modo che i dati raccolti dall'agente possano essere pubblicati nella risorsa cluster. Il monitoraggio del server di pubblicazione ha l'autorizzazione solo per le metriche push alla risorsa, non può modificare alcuno stato, aggiornare la risorsa o leggere i dati. Per altre informazioni sul ruolo, vedere [monitoraggio delle metriche del ruolo di pubblicazione](../../role-based-access-control/built-in-roles.md#monitoring-metrics-publisher).

## <a name="prerequisites"></a>Prerequisiti

Prima di aggiornare il cluster, verificare quanto segue:

* Le metriche personalizzate sono disponibili solo in un subset di aree di Azure. Un elenco di aree supportate è documentato [qui](../platform/metrics-custom-overview.md#supported-regions).

* Si è un membro del ruolo **[proprietario](../../role-based-access-control/built-in-roles.md#owner)** sulla risorsa del cluster AKS per abilitare la raccolta delle metriche delle prestazioni personalizzate del nodo e del Pod.

Se si sceglie di usare l'interfaccia della riga di comando di Azure, è prima necessario installarla ed eseguirla in locale. È necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.59 o successiva. Per identificare la versione in uso, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

## <a name="upgrade-a-cluster-from-the-azure-portal"></a>Aggiornare un cluster dalla portale di Azure

Per i cluster AKS esistenti monitorati da monitoraggio di Azure per i contenitori, dopo aver selezionato il cluster per visualizzare l'integrità dalla visualizzazione multicluster in monitoraggio di Azure o direttamente dal cluster selezionando **Insights** nel riquadro a sinistra, verrà visualizzato un banner nella parte superiore del portale.

![Aggiornare il banner del cluster AKS in portale di Azure](./media/container-insights-update-metrics/portal-banner-enable-01.png)

Se si fa clic su **Abilita** , viene avviato il processo di aggiornamento del cluster. Questo processo può richiedere alcuni secondi. Per tenere traccia dello stato di avanzamento, è possibile usare la voce Notifiche nel menu.

## <a name="upgrade-all-clusters-using-bash-in-azure-command-shell"></a>Aggiornare tutti i cluster usando bash in Shell comandi di Azure

Eseguire la procedura seguente per aggiornare tutti i cluster nella sottoscrizione usando bash nella shell dei comandi di Azure.

1. Eseguire il comando seguente usando l'interfaccia della riga di comando di Azure.  Modificare il valore di **SubscriptionId** usando il valore della pagina **Panoramica di AKS** per il cluster AKS.

    ```azurecli
    az login
    az account set --subscription "Subscription Name"
    curl -sL https://aka.ms/ci-md-onboard-atscale | bash -s subscriptionId   
    ```

    Il completamento della modifica della configurazione può richiedere alcuni secondi. Al termine dell'operazione, viene visualizzato un messaggio simile al seguente in cui è incluso il risultato:

    ```azurecli
    completed role assignments for all AKS clusters in subscription: <subscriptionId>
    ```

## <a name="upgrade-per-cluster-using-azure-cli"></a>Eseguire l'aggiornamento per cluster usando l'interfaccia della riga di comando

Eseguire la procedura seguente per aggiornare un cluster specifico nella sottoscrizione usando l'interfaccia della riga di comando di Azure.

1. Eseguire il comando seguente usando l'interfaccia della riga di comando di Azure. Modificare i valori per **SubscriptionId**, **resourceGroupName**e **clustername** usando i valori nella pagina **Panoramica di AKS** per il cluster AKS.  Per ottenere il valore di **clientIdOfSPN**, viene restituito quando si esegue il comando, `az aks show` come illustrato nell'esempio riportato di seguito.

    ```azurecli
    az login
    az account set --subscription "<subscriptionName>"
    az aks show -g <resourceGroupName> -n <clusterName> 
    az role assignment create --assignee <clientIdOfSPN> --scope <clusterResourceId> --role "Monitoring Metrics Publisher" 
    ```

    Per ottenere il valore per **clientIdOfSPNOrMsi**, è possibile eseguire il comando `az aks show` come illustrato nell'esempio riportato di seguito. Se l'oggetto **servicePrincipalProfile** dispone di un valore *ClientID* valido, è possibile utilizzarlo. In caso contrario, se è impostato su *MSI*, è necessario passare l'oggetto ClientID da `addonProfiles.omsagent.identity.clientId` .

    ```azurecli
    az login
    az account set --subscription "<subscriptionName>"
    az aks show -g <resourceGroupName> -n <clusterName> 
    az role assignment create --assignee <clientIdOfSPNOrMsi> --scope <clusterResourceId> --role "Monitoring Metrics Publisher"
    ```

## <a name="upgrade-all-clusters-using-azure-powershell"></a>Aggiornare tutti i cluster usando Azure PowerShell

Eseguire la procedura seguente per aggiornare tutti i cluster nella sottoscrizione usando Azure PowerShell.

1. [Scaricare](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/docs/aks/mdmonboarding/mdm_onboarding_atscale.ps1) lo script **mdm_onboarding_atscale.ps1** e salvarlo in una cartella locale dal repository GitHub.
2. Eseguire il comando seguente usando il Azure PowerShell.  Modificare il valore di **SubscriptionId** usando il valore della pagina **Panoramica di AKS** per il cluster AKS.

    ```powershell
    .\mdm_onboarding_atscale.ps1 subscriptionId
    ```
    Il completamento della modifica della configurazione può richiedere alcuni secondi. Al termine dell'operazione, viene visualizzato un messaggio simile al seguente in cui è incluso il risultato:

    ```powershell
    Completed adding role assignment for the aks clusters in subscriptionId :<subscriptionId>
    ```

## <a name="upgrade-per-cluster-using-azure-powershell"></a>Eseguire l'aggiornamento per ogni cluster usando Azure PowerShell

Eseguire la procedura seguente per aggiornare un cluster specifico usando Azure PowerShell.

1. [Scaricare](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/docs/aks/mdmonboarding/mdm_onboarding.ps1) lo script **mdm_onboarding.ps1** e salvarlo in una cartella locale dal repository GitHub.

2. Eseguire il comando seguente usando il Azure PowerShell. Modificare i valori per **SubscriptionId**, **resourceGroupName**e **clustername** usando i valori nella pagina **Panoramica di AKS** per il cluster AKS.

    ```powershell
    .\mdm_onboarding.ps1 subscriptionId <subscriptionId> resourceGroupName <resourceGroupName> clusterName <clusterName>
    ```

    Il completamento della modifica della configurazione può richiedere alcuni secondi. Al termine dell'operazione, viene visualizzato un messaggio simile al seguente in cui è incluso il risultato:

    ```powershell
    Successfully added Monitoring Metrics Publisher role assignment to cluster : <clusterName>
    ```

## <a name="verify-update"></a>Verifica aggiornamento

Dopo aver avviato l'aggiornamento usando uno dei metodi descritti in precedenza, è possibile usare Esplora metriche di monitoraggio di Azure e verificare dallo **spazio dei nomi della metrica** che sono elencate le **informazioni dettagliate** . In tal caso, è possibile iniziare a configurare gli avvisi delle [metriche](../platform/alerts-metric.md) o aggiungere i grafici ai [Dashboard](../../azure-portal/azure-portal-dashboards.md).  
