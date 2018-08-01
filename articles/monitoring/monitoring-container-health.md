---
title: Monitorare l'integrità del servizio Kubernetes di Azure (AKS) (anteprima) | Microsoft Docs
description: Questo articolo descrive come esaminare le prestazioni del contenitore AKS per comprendere rapidamente l'uso dell'ambiente Kubernetes ospitato.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/18/2018
ms.author: magoedte
ms.openlocfilehash: 806487ec731a1b7fe02ccdfe6b285f5b2e119787
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/25/2018
ms.locfileid: "39249098"
---
# <a name="monitor-azure-kubernetes-service-aks-container-health-preview"></a>Monitorare l'integrità del servizio Kubernetes di Azure (AKS) (anteprima)

Questo articolo descrive come configurare e usare l'integrità dei contenitori di Monitoraggio di Azure per monitorare le prestazioni dei carichi di lavoro distribuiti negli ambienti Kubernetes e ospitati nel servizio Kubernetes di Azure (AKS). Il monitoraggio del cluster e dei contenitori Kubernetes ha un'importanza critica, soprattutto quando si gestisce un cluster di produzione su larga scala con più applicazioni.

L'integrità dei contenitori offre la possibilità di monitorare le prestazioni raccogliendo metriche sulla memoria e sul processore da controller, nodi e contenitori disponibili in Kubernetes tramite l'API per le metriche. Dopo che si è abilitata l'integrità dei contenitori, queste metriche vengono raccolte automaticamente tramite una versione dell'agente di Operations Management Suite (OMS) per Linux inclusa in un contenitore e vengono archiviate nell'area di lavoro di [Log Analytics](../log-analytics/log-analytics-overview.md). Le visualizzazioni predefinite incluse mostrano i carichi di lavoro nei contenitori residenti e gli elementi che influiscono sull'integrità delle prestazioni del cluster Kubernetes. È così possibile:  

* Identificare i contenitori in esecuzione nel nodo e il relativo uso medio del processore e della memoria, in modo da individuare facilmente i colli di bottiglia delle risorse.
* Identificare la posizione del contenitore in un controller o in un pod, in modo da visualizzare facilmente le prestazioni complessive del controller o del pod. 
* Esaminare l'uso delle risorse dei carichi di lavoro in esecuzione nell'host non correlati ai processi standard che supportano il pod.
* Comprendere il comportamento del cluster con carichi medi e più pesanti. Queste informazioni sono utili per identificare i requisiti di capacità e determinare il carico massimo che può sostenere il cluster. 

Per informazioni sul monitoraggio e la gestione degli host di contenitori Docker e Windows per visualizzare la configurazione, il controllo e l'uso delle risorse, vedere la [soluzione Monitoraggio contenitori](../log-analytics/log-analytics-containers.md).

## <a name="prerequisites"></a>Prerequisiti 
Prima di iniziare, verificare di disporre degli elementi seguenti:

- Un cluster AKS nuovo o esistente.
- Un agente di OMS per Linux incluso in un contenitore, versione microsoft/oms:ciprod04202018 o successiva. Il numero di versione è rappresentato da una data nel formato seguente: *mmggaaaa*. L'agente viene installato automaticamente durante l'onboarding dell'integrità dei contenitori. 
- Un'area di lavoro di Log Analytics. È possibile crearne una quando si abilita il monitoraggio del nuovo cluster AKS oppure tramite [Azure Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md), [PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json) o il [portale di Azure](../log-analytics/log-analytics-quick-create-workspace.md).
- Il ruolo di collaboratore di Log Analytics per abilitare il monitoraggio dei contenitori. Per altre informazioni su come controllare l'accesso a un'area di lavoro di Log Analytics, vedere [Gestire le aree di lavoro](../log-analytics/log-analytics-manage-access.md).

## <a name="components"></a>Componenti 

La capacità di monitorare le prestazioni si basa su un agente di OMS per Linux incluso in un contenitore che raccoglie i dati su prestazioni ed eventi da tutti i nodi del cluster. L'agente viene distribuito e registrato automaticamente con l'area di lavoro di Log Analytics specificata dopo aver abilitato il monitoraggio dei contenitori. 

>[!NOTE] 
>Se è già stato distribuito un cluster AKS, il monitoraggio può essere abilitato usando un modello di Azure Resource Manager, come illustrato più avanti in questo articolo. Non è possibile usare `kubectl` per aggiornare, eliminare, distribuire o ridistribuire l'agente. 
>

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure
Accedere al [portale di Azure](https://portal.azure.com). 

## <a name="enable-container-health-monitoring-for-a-new-cluster"></a>Abilitare il monitoraggio dell'integrità dei contenitori per un nuovo cluster
Durante la distribuzione è possibile abilitare il monitoraggio di un nuovo cluster AKS nel portale di Azure. Seguire la procedura descritta nell'articolo introduttivo [Distribuire un cluster Azure Kubernetes Service (AKS)](../aks/kubernetes-walkthrough-portal.md). Nella pagina **Monitoraggio** selezionare **Sì** per l'opzione **Abilita monitoraggio** e quindi selezionare un'area di lavoro di Log Analytics esistente o crearne una nuova. 

Dopo aver abilitato il monitoraggio e completato tutte le attività di configurazione, è possibile monitorare le prestazioni del cluster in uno dei due modi seguenti:

* Direttamente dal cluster AKS, selezionando **Integrità** nel riquadro sinistro.
* Facendo clic sul riquadro **Monitorare l'integrità dei contenitori** nella pagina relativa al cluster AKS selezionato. In Monitoraggio di Azure selezionare **Integrità** nel riquadro sinistro. 

  ![Opzioni per la selezione dell'integrità dei contenitori in AKS](./media/monitoring-container-health/container-performance-and-health-select-01.png)

Dopo aver abilitato il monitoraggio, può essere necessario attendere circa 15 minuti prima di poter visualizzare i dati operativi per il cluster. 

## <a name="enable-container-health-monitoring-for-existing-managed-clusters"></a>Abilitare il monitoraggio dell'integrità dei contenitori per cluster gestiti esistenti
È possibile abilitare il monitoraggio di un cluster AKS già distribuito tramite il portale di Azure o il modello di Azure Resource Manager fornito usando il cmdlet `New-AzureRmResourceGroupDeployment` di PowerShell o l'interfaccia della riga di comando di Azure. 

### <a name="enable-monitoring-in-the-azure-portal"></a>Abilitare il monitoraggio nel portale di Azure
Per abilitare il monitoraggio del contenitore AKS nel portale di Azure completare la procedura seguente:

1. Nel portale di Azure fare clic su **Tutti i servizi**. 
2. Nell'elenco delle risorse digitare **Contenitori**.  
    L'elenco viene filtrato in base all'input. 
3. Selezionare **Servizi Kubernetes**.  

    ![Collegamento ai servizi Kubernetes](./media/monitoring-container-health/azure-portal-01.png)

4. Nell'elenco dei contenitori selezionare un contenitore.
5. Nella pagina di panoramica del contenitore selezionare **Monitorare l'integrità dei contenitori**.  
6. Se nella pagina **Onboarding in Integrità contenitori e log** è già presente un'area di lavoro di Log Analytics nella stessa sottoscrizione del cluster, selezionarla nell'elenco a discesa.  
    Nell'elenco sono preselezionate l'area di lavoro e la località predefinite in cui è distribuito il contenitore AKS nella sottoscrizione. 

    ![Abilitare il monitoraggio dell'integrità dei contenitori AKS](./media/monitoring-container-health/container-health-enable-brownfield-02.png)

>[!NOTE]
>Se si vuole creare una nuova area di lavoro di Log Analytics per archiviare i dati di monitoraggio dal cluster, seguire le istruzioni in [Creare un'area di lavoro di Log Analytics](../log-analytics/log-analytics-quick-create-workspace.md). Assicurarsi di creare l'area di lavoro nella stessa sottoscrizione in cui viene distribuito il contenitore AKS. 
 
Dopo aver abilitato il monitoraggio, può essere necessario attendere circa 15 minuti prima di poter visualizzare i dati operativi per il cluster. 

### <a name="enable-monitoring-by-using-an-azure-resource-manager-template"></a>Abilitare il monitoraggio usando un modello di Azure Resource Manager
Questo metodo include due modelli JSON. Un modello JSON specifica la configurazione per abilitare il monitoraggio e l'altro contiene i valori dei parametri da configurare per specificare quanto segue:

* L'ID risorsa del contenitore AKS. 
* Il gruppo di risorse in cui viene distribuito il cluster.
* L'area di lavoro di Log Analytics e l'area geografica in cui creare l'area di lavoro. 

L'area di lavoro di Log Analytics deve essere creata manualmente. Per creare l'area di lavoro, è possibile configurarla tramite [Azure Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md), [PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json) o il [portale di Azure](../log-analytics/log-analytics-quick-create-workspace.md).

Se non si ha familiarità con il concetto di distribuzione delle risorse tramite un modello, vedere:
* [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
* [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](../azure-resource-manager/resource-group-template-deploy-cli.md)

Se si sceglie di usare l'interfaccia della riga di comando di Azure, è prima necessario installarla ed eseguirla in locale. È richiesta la versione 2.0.27 o successiva. Per identificare la versione in uso, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

#### <a name="create-and-execute-a-template"></a>Creare ed eseguire un modello

1. Copiare e incollare nel file la sintassi JSON seguente:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "aksResourceId": {
        "type": "string",
        "metadata": {
           "description": "AKS Cluster Resource ID"
           }
    },
    "aksResourceLocation": {
    "type": "string",
     "metadata": {
        "description": "Location of the AKS resource e.g. \"East US\""
       }
    },
    "workspaceResourceId": {
      "type": "string",
      "metadata": {
         "description": "Azure Monitor Log Analytics Resource ID"
       }
    },
    "workspaceRegion": {
    "type": "string",
    "metadata": {
       "description": "Azure Monitor Log Analytics workspace region"
      }
     }
    },
    "resources": [
      {
    "name": "[split(parameters('aksResourceId'),'/')[8]]",
    "type": "Microsoft.ContainerService/managedClusters",
    "location": "[parameters('aksResourceLocation')]",
    "apiVersion": "2018-03-31",
    "properties": {
      "mode": "Incremental",
      "id": "[parameters('aksResourceId')]",
      "addonProfiles": {
        "omsagent": {
          "enabled": true,
          "config": {
            "logAnalyticsWorkspaceResourceID": "[parameters('workspaceResourceId')]"
          }
         }
       }
      }
     },
    {
        "type": "Microsoft.Resources/deployments",
        "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
        "apiVersion": "2017-05-10",
        "subscriptionId": "[split(parameters('workspaceResourceId'),'/')[2]]",
        "resourceGroup": "[split(parameters('workspaceResourceId'),'/')[4]]",
        "properties": {
            "mode": "Incremental",
            "template": {
                "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                "contentVersion": "1.0.0.0",
                "parameters": {},
                "variables": {},
                "resources": [
                    {
                        "apiVersion": "2015-11-01-preview",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "location": "[parameters('workspaceRegion')]",
                        "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                        "properties": {
                            "workspaceResourceId": "[parameters('workspaceResourceId')]"
                        },
                        "plan": {
                            "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                            "product": "[Concat('OMSGallery/', 'ContainerInsights')]",
                            "promotionCode": "",
                            "publisher": "Microsoft"
                        }
                    }
                ]
            },
            "parameters": {}
        }
       }
     ]
    }
    ```

2. Salvare questo file come **existingClusterOnboarding.json** in una cartella locale.
3. Incollare nel file la sintassi JSON seguente:

    ```json
    {
       "$schema": "https://schema.management.azure.com/  schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
         "aksResourceId": {
           "value": "/subscriptions/<SubscroptiopnId>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
       },
       "aksResourceLocation": {
         "value": "East US"
       },
       "workspaceResourceId": {
         "value": "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroup>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>"
       },
       "workspaceRegion": {
         "value": "eastus"
       }
     }
    }
    ```

4. Sostituire i valori di **aksResourceId** e **aksResourceLocation** con i valori indicati nella pagina di **panoramica del servizio Kubernetes di Azure** per il cluster AKS. Il valore per **workspaceResourceId** è l'ID risorsa completo dell'area di lavoro di Log Analytics, che include il nome dell'area di lavoro. Specificare anche l'area geografica in cui si trova l'area di lavoro per **workspaceRegion**. 
5. Salvare questo file come **existingClusterParam.json** in una cartella locale.
6. A questo punto è possibile distribuire il modello. 

    * Usare i comandi di PowerShell seguenti nella cartella che contiene il modello:

        ```powershell
        New-AzureRmResourceGroupDeployment -Name OnboardCluster -ResourceGroupName ClusterResourceGroupName -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
        ```
        Il completamento della modifica della configurazione può richiedere alcuni minuti. Al termine dell'operazione, viene visualizzato un messaggio simile al seguente in cui è incluso il risultato:

        ```powershell
        provisioningState       : Succeeded
        ```

    * Eseguire il comando seguente usando l'interfaccia della riga di comando di Azure su Linux:
    
        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --resource-group <ResourceGroupName> --template-file ./existingClusterOnboarding.json --parameters @./existingClusterParam.json
        ```

        Il completamento della modifica della configurazione può richiedere alcuni minuti. Al termine dell'operazione, viene visualizzato un messaggio simile al seguente in cui è incluso il risultato:

        ```azurecli
        provisioningState       : Succeeded
        ```
Dopo aver abilitato il monitoraggio, può essere necessario attendere circa 15 minuti prima di poter visualizzare i dati operativi per il cluster. 

## <a name="verify-agent-and-solution-deployment"></a>Verificare la distribuzione dell'agente e della soluzione
Con la versione dell'agente *06072018* o successiva è possibile verificare che l'agente e la soluzione siano stati distribuiti correttamente. Con le versioni precedenti è possibile verificare solo la distribuzione dell'agente.

### <a name="agent-version-06072018-or-later"></a>Versione dell'agente 06072018 o successiva
Eseguire il comando seguente per verificare che l'agente sia distribuito correttamente. 

```
kubectl get ds omsagent --namespace=kube-system
```

L'output dovrebbe essere simile al seguente, in cui è indicato che la distribuzione è stata eseguita correttamente:

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

Per verificare la distribuzione della soluzione, eseguire questo comando:

```
kubectl get deployment omsagent-rs -n=kube-system
```

L'output dovrebbe essere simile al seguente, in cui è indicato che la distribuzione è stata eseguita correttamente:

```
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>Agente con versione precedente alla 06072018

Per verificare che la versione dell'agente OMS rilasciata prima della *06072018* sia distribuita correttamente, eseguire il comando seguente:  

```
kubectl get ds omsagent --namespace=kube-system
```

L'output dovrebbe essere simile al seguente, in cui è indicato che la distribuzione è stata eseguita correttamente:  

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-performance-utilization"></a>Visualizzare l'uso delle prestazioni
Quando si apre l'integrità dei contenitori, nella pagina viene visualizzato immediatamente l'uso dell'intero cluster. La visualizzazione delle informazioni sul cluster AKS è organizzata in base a quattro prospettive:

- HDInsight
- Nodi 
- Controller  
- Contenitori

Nella scheda **Cluster** i quattro grafici a linee mostrano le metriche delle prestazioni chiave del cluster. 

![Grafici delle prestazioni di esempio nella scheda Cluster](./media/monitoring-container-health/container-health-cluster-perfview.png)

Nei grafici vengono visualizzate quattro metriche delle prestazioni:

- **Node CPU Utilization&nbsp;%** (% uso CPU nodo): una prospettiva aggregata dell'uso della CPU per l'intero cluster. È possibile filtrare i risultati per l'intervallo di tempo selezionando **Avg** (Media), **Min**, **Max**, **50th** (50°), **90th** (90°) e **95th** (95°) tramite il selettore di percentili nella parte superiore del grafico, singolarmente o in combinazione. 
- **Node memory utilization&nbsp;%** (% uso memoria nodo): una prospettiva aggregata dell'uso della memoria per l'intero cluster. È possibile filtrare i risultati per l'intervallo di tempo selezionando **Avg** (Media), **Min**, **Max**, **50th** (50°), **90th** (90°) e **95th** (95°) tramite il selettore di percentili nella parte superiore del grafico, singolarmente o in combinazione. 
- **Node count** (Numero di nodi): il numero e lo stato dei nodi in Kubernetes. Lo stato dei nodi del cluster rappresentati è *All* (Tutto), *Ready* (Pronto) o *Not Ready* (Non pronto) e può essere filtrato singolarmente o in combinazione tramite il selettore nella parte superiore del grafico. 
- **Activity pod count** (Numero di pod attività) : il numero e lo stato dei pod in Kubernetes. Lo stato dei pod rappresentati può essere *All* (Tutto), *Pending* (In sospeso), *Running* (In esecuzione) o *Unknown* (Sconosciuto) e può essere filtrato singolarmente o in combinazione tramite il selettore nella parte superiore del grafico. 

Se si passa alla scheda **Nodi**, la gerarchia delle righe segue il modello a oggetti Kubernetes a partire da un nodo nel cluster. Espandere il nodo per visualizzare uno o più pod in esecuzione sul nodo. Se in un pod sono raggruppati più contenitori, questi vengono visualizzati come ultima riga nella gerarchia. È possibile visualizzare anche il numero di carichi di lavoro non correlati a pod in esecuzione sull'host nel caso di uso intensivo del processore o della memoria sull'host.

![Gerarchia di nodi Kubernetes di esempio nella vista prestazioni](./media/monitoring-container-health/container-health-nodes-view.png)

È possibile selezionare controller o contenitori nella parte superiore della pagina e controllare lo stato e l'uso delle risorse per tali oggetti. Usare le caselle a discesa nella parte superiore della schermata per filtrare i dati in base a spazio dei nomi, servizio e nodo. Se invece si vuole controllare l'uso della memoria, nell'elenco a discesa **Metrica** selezionare **Memoria RSS** o **Working set della memoria**. L'opzione **Memoria RSS** è supportata solo per Kubernetes versione 1.8 e successive. Se si usa un'altra versione, per i valori di **Min&nbsp;%** verrà visualizzato *NaN&nbsp;%*, ovvero un valore di tipo di dati numerico che indica un valore non definito o non rappresentabile. 

![Visualizzazione delle prestazioni dei nodi del contenitore](./media/monitoring-container-health/container-health-node-metric-dropdown.png)

Per impostazione predefinita, i dati sulle prestazioni sono basati sulle ultime sei ore, ma è possibile modificare l'intervallo tramite l'elenco a discesa **Intervallo di tempo** disponibile nell'angolo superiore destro della pagina. Attualmente, la pagina non viene aggiornata in modo automatico, quindi è necessario aggiornarla manualmente. È anche possibile filtrare i risultati compresi nell'intervallo di tempo selezionando **Avg** (Media), **Min**, **Max**, **50th** (50°), **90th** (90°) e **95th** (95°) tramite il selettore di percentile. 

![Selezione del percentile per filtrare i dati](./media/monitoring-container-health/container-health-metric-percentile-filter.png)

Nell'esempio seguente è possibile osservare che per il nodo *aks-nodepool-3977305* il valore per **Contenitori** è 5, ovvero un rollup del numero totale di contenitori distribuiti.

![Esempio di rollup dei contenitori per nodo](./media/monitoring-container-health/container-health-nodes-containerstotal.png)

In questo modo, è possibile determinare rapidamente se i contenitori sono ripartiti in modo equilibrato tra i nodi del cluster. 

Le informazioni presentate quando si visualizzano i nodi sono descritte nella tabella seguente:

| Colonna | Descrizione | 
|--------|-------------|
| Name (Nome) | Nome dell'host. |
| Status (Stato) | Visualizzazione Kubernetes dello stato del nodo. |
| Avg&nbsp;%, Min&nbsp;%, Max&nbsp;%, 50th&nbsp;%, 90th&nbsp;% (% media, % min, % max, % 50°, % 90°) | Percentuale media dei nodi in base al percentile durante l'intervallo di tempo selezionato. |
| Avg, Min, Max, 50th, 90th (Media, Min, Max, 50°, 90°) | Valore medio effettivo dei nodi durante l'intervallo di tempo selezionato. Il valore medio viene misurato dal limite di CPU/memoria impostato per un nodo. Per i pod e i contenitori, è il valore medio segnalato dall'host. |
| Containers (Contenitori) | Numero di contenitori. |
| Uptime (Tempo di attività) | Rappresenta il tempo dall'avvio o dal riavvio di un nodo. |
| Controllers (Controller) | Solo per i contenitori e i pod. Mostra il controller in cui è presente. Non tutti i pod sono presenti in un controller. È quindi possibile che per alcuni sia visualizzato **N/A** per indicare che non sono disponibili. | 
| Trend Avg&nbsp;%, Min&nbsp;%, Max&nbsp;%, 50th&nbsp;%, 90th&nbsp;% (Tendenza % media, % min, % max, % 50°, % 90°) | Grafico a barre della tendenza che presenta il valore percentuale delle metriche di percentile del controller. |


Nel selettore selezionare **Controller**.

![Selezione della visualizzazione per i controller](./media/monitoring-container-health/container-health-controllers-tab.png)

Qui è possibile visualizzare l'integrità delle prestazioni dei controller.

![Vista prestazioni dei controller di <nome>](./media/monitoring-container-health/container-health-controllers-view.png)

La gerarchia di righe inizia con un controller e lo espande, visualizzando uno o più contenitori. Espandendo un pod, nell'ultima riga viene mostrato il contenitore raggruppato nel pod. 

Le informazioni presentate quando si visualizzano i controller sono descritte nella tabella seguente:

| Colonna | Descrizione | 
|--------|-------------|
| Name (Nome) | Nome del controller.|
| Status (Stato) | Stato di rollup dei contenitori quando l'esecuzione è stata completata con uno stato, ad esempio *OK*, *Terminated* (Terminato), *Failed* (Non riuscito), *Stopped* (Arrestato) o *Paused* (In pausa). Se il contenitore è in esecuzione, ma lo stato non è stato presentato correttamente o non è stato rilevato dall'agente e non è stata inviata alcuna risposta per più di 30 minuti, lo stato è *Unknown* (Sconosciuto). La tabella seguente contiene dettagli aggiuntivi sull'icona dello stato.|
| Avg&nbsp;%, Min&nbsp;%, Max&nbsp;%, 50th&nbsp;%, 90th&nbsp;% (% media, % min, % max, % 50°, % 90°) | Rollup della percentuale media di ogni entità per la metrica e il percentile selezionati. |
| Avg, Min, Max, 50th, 90th (Media, Min, Max, 50°, 90°)  | Rollup della media di millicore della CPU o delle prestazioni di memoria del contenitore per il percentile selezionato. Il valore medio viene misurato dal limite di CPU/memoria impostato per un pod. |
| Containers (Contenitori) | Numero totale di contenitori per il controller o il pod. |
| Restarts (Riavvii) | Rollup del numero di riavvii dai contenitori. |
| Uptime (Tempo di attività) | Rappresenta il tempo dall'avvio di un contenitore. |
| Node (Nodo) | Solo per i contenitori e i pod. Mostra il controller in cui è presente. | 
| Trend Avg&nbsp;%, Min&nbsp;%, Max&nbsp;%, 50th&nbsp;%, 90th&nbsp;% (Tendenza % media, % min, % max, % 50°, % 90°)| Grafico a barre della tendenza che presenta il valore del percentile del controller. |

Le icone nel campo relativo allo stato indicano lo stato online dei contenitori:
 
| Icona | Stato | 
|--------|-------------|
| ![Icona di stato In esecuzione (Pronto)](./media/monitoring-container-health/container-health-ready-icon.png) | In esecuzione (Pronto)|
| ![Icona di stato In attesa o In pausa](./media/monitoring-container-health/container-health-waiting-icon.png) | In attesa o In pausa|
| ![Icona di stato Ultima esecuzione segnalata](./media/monitoring-container-health/container-health-grey-icon.png) | Ultima esecuzione segnalata ma senza risposta da oltre 30 minuti|
| ![Icona di stato Operazione riuscita](./media/monitoring-container-health/container-health-green-icon.png) | Arresto completato o arresto non riuscito|

L'icona dello stato mostra un numero in base ai dati forniti dal pod. Vengono visualizzati i due stati peggiori. Quando si passa il puntatore del mouse sullo stato, viene visualizzato uno stato di rollup di tutti i pod nel contenitore. Se non è presente alcuno stato Pronto, come stato viene visualizzato **(0)**. 

Nel selettore selezionare **Contenitori**.

![Selezione della visualizzazione per i contenitori](./media/monitoring-container-health/container-health-containers-tab.png)

Qui è possibile visualizzare l'integrità delle prestazioni dei contenitori.

![Vista prestazioni dei controller di <nome>](./media/monitoring-container-health/container-health-containers-view.png)

Le informazioni presentate quando si visualizzano i contenitori sono descritte nella tabella seguente:

| Colonna | Descrizione | 
|--------|-------------|
| Name (Nome) | Nome del controller.|
| Status (Stato) | Stato dei contenitori, se presente. La tabella seguente contiene dettagli aggiuntivi sull'icona dello stato.|
| Avg&nbsp;%, Min&nbsp;%, Max&nbsp;%, 50th&nbsp;%, 90th&nbsp;% (% media, % min, % max, % 50°, % 90°) | Rollup della percentuale media di ogni entità per la metrica e il percentile selezionati. |
| Avg, Min, Max, 50th, 90th (Media, Min, Max, 50°, 90°)  | Rollup della media di millicore della CPU o delle prestazioni di memoria del contenitore per il percentile selezionato. Il valore medio viene misurato dal limite di CPU/memoria impostato per un pod. |
| Pod | Contenitore in cui è presente il pod.| 
| Node (Nodo) |  Nodo in cui è presente il contenitore. | 
| Restarts (Riavvii) | Rappresenta il tempo dall'avvio di un contenitore. |
| Uptime (Tempo di attività) | Rappresenta il tempo dall'avvio o dal riavvio di un contenitore. |
| Trend Avg&nbsp;%, Min&nbsp;%, Max&nbsp;%, 50th&nbsp;%, 90th&nbsp;% (Tendenza % media, % min, % max, % 50°, % 90°) | Grafico a barre della tendenza che presenta il valore medio percentuale delle metriche del contenitore. |

Le icone nel campo relativo allo stato indicano lo stato online dei pod, come descritto nella tabella seguente:
 
| Icona | Stato | 
|--------|-------------|
| ![Icona di stato In esecuzione (Pronto)](./media/monitoring-container-health/container-health-ready-icon.png) | In esecuzione (Pronto)|
| ![Icona di stato In attesa o In pausa](./media/monitoring-container-health/container-health-waiting-icon.png) | In attesa o In pausa|
| ![Icona di stato Ultima esecuzione segnalata](./media/monitoring-container-health/container-health-grey-icon.png) | Ultima esecuzione segnalata ma senza risposta da oltre 30 minuti|
| ![Icona di stato Terminato](./media/monitoring-container-health/container-health-terminated-icon.png) | Arresto completato o arresto non riuscito|
| ![Icona di stato Non riuscito](./media/monitoring-container-health/container-health-failed-icon.png) | Stato Non riuscito |

## <a name="container-data-collection-details"></a>Informazioni dettagliate sulla raccolta di dati dei contenitori
L'integrità dei contenitori raccoglie le varie metriche delle prestazioni e i vari dati di log da host del contenitore e contenitori. I dati vengono raccolti ogni tre minuti.

### <a name="container-records"></a>Record dei contenitori

La tabella seguente mostra esempi di record raccolti dall'integrità dei contenitori e i tipi di dati visualizzati nei risultati della ricerca nei log:

| Tipo di dati | Tipo di dati in Ricerca log | Campi |
| --- | --- | --- |
| Prestazioni per host e contenitori | `Perf` | Computer, ObjectName, CounterName &#40;% tempo processore, MB di letture disco, MB di scritture disco MB utilizzo di memoria, byte di ricezione di rete, byte di invio di rete, utilizzo del processore in secondi, rete&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem |
| Inventario contenitori | `ContainerInventory` | TimeGenerated, Computer, container name, ContainerHostname, Image, ImageTag, ContainerState, ExitCode, EnvironmentVar, Command, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| Inventario delle immagini dei contenitori | `ContainerImageInventory` | TimeGenerated, Computer, Image, ImageTag, ImageSize, VirtualSize, Running, Paused, Stopped, Failed, SourceSystem, ImageID, TotalContainer |
| Log contenitori | `ContainerLog` | TimeGenerated, Computer, image ID, container name, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| Log servizio contenitori | `ContainerServiceLog`  | TimeGenerated, Computer, TimeOfCommand, Image, Command, SourceSystem, ContainerID |
| Inventario di nodi contenitore | `ContainerNodeInventory_CL`| TimeGenerated, Computer, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Processo contenitore | `ContainerProcess_CL` | TimeGenerated, Computer, Pod_s, Namespace_s, ClassName_s, InstanceID_s, Uid_s, PID_s, PPID_s, C_s, STIME_s, Tty_s, TIME_s, Cmd_s, Id_s, Name_s, SourceSystem |
| Inventario dei pod in un cluster Kubernetes | `KubePodInventory` | TimeGenerated, Computer, ClusterId, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, ServiceName, ControllerKind, ControllerName, ContainerStatus, ContainerID, ContainerName, Name, PodLabel, Namespace, PodStatus, ClusterName, PodIp, SourceSystem |
| Inventario dei nodi di un cluster Kubernetes | `KubeNodeInventory` | TimeGenerated, Computer, ClusterName, ClusterId, LastTransitionTimeReady, Labels, Status, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
| Eventi di Kubernetes | `KubeEvents_CL` | TimeGenerated, Computer, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, Message,  SourceSystem | 
| Servizi nel cluster Kubernetes | `KubeServices_CL` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, SourceSystem | 
| Metriche delle prestazioni per la parte dei nodi del cluster Kubernetes | Perf &#124; where ObjectName == “K8SNode” | Computer, ObjectName, CounterName &#40;cpuUsageNanoCores, , memoryWorkingSetBytes, memoryRssBytes, networkRxBytes, networkTxBytes, restartTimeEpoch, networkRxBytesPerSec, networkTxBytesPerSec, cpuAllocatableNanoCores, memoryAllocatableBytes, cpuCapacityNanoCores, memoryCapacityBytes&#41;,CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Metriche delle prestazioni per la parte dei contenitori del cluster Kubernetes | Perf &#124; where ObjectName == “K8SContainer” | CounterName &#40;cpuUsageNanoCores, memoryWorkingSetBytes, memoryRssBytes, restartTimeEpoch, cpuRequestNanoCores, memoryRequestBytes, cpuLimitNanoCores, memoryLimitBytes&#41;,CounterValue, TimeGenerated, CounterPath, SourceSystem | 

## <a name="search-logs-to-analyze-data"></a>Eseguire ricerche nei log per analizzare i dati
Log Analytics consente di individuare tendenze, diagnosticare i colli di bottiglia, effettuare previsioni o correlare dati che consentono di determinare se la configurazione corrente del cluster è ottimale. Sono disponibili ricerche predefinite nei log che è possibile iniziare a usare immediatamente o personalizzare per restituire le informazioni nel modo che si preferisce. 

È possibile eseguire un'analisi interattiva dei dati nell'area di lavoro selezionando l'opzione **Visualizza log** disponibile nella parte più a destra quando si espande un controller o un contenitore. La pagina **Ricerca log** viene visualizzata in primo piano rispetto alla pagina attiva del portale di Azure.

![Analizzare i dati in Log Analytics](./media/monitoring-container-health/container-health-view-logs.png)   

L'output dei log dei contenitori inoltrato a Log Analytics è costituito da STDOUT e STDERR. Poiché l'integrità dei contenitori monitora il servizio Kubernetes gestito da Azure (AKS), i dati di Kube-system non vengono attualmente raccolti a causa della grande quantità di dati generati. 

### <a name="example-log-search-queries"></a>Esempio di query di ricerca log
Spesso è utile creare una query a partire da qualche esempio e quindi modificarla in base ai propri requisiti. Per creare query più avanzate, è possibile provare a usare le query di esempio seguenti:

| Query | Descrizione | 
|-------|-------------|
| ContainerInventory<br> &#124; project Computer, Name, Image, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime<br> &#124; render table | Elencare tutte le informazioni sul ciclo di vita di un contenitore| 
| KubeEvents_CL<br> &#124; where not(isempty(Namespace_s))<br> &#124; sort by TimeGenerated desc<br> &#124; render table | Eventi di Kubernetes|
| ContainerImageInventory<br> &#124; summarize AggregatedValue = count() by Image, ImageTag, Running | Inventario delle immagini | 
| **In Analisi avanzata selezionare i grafici a linee**:<br> Perf<br> &#124; where ObjectName == "Container" and CounterName == "% Processor Time"<br> &#124; summarize AvgCPUPercent = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | CPU del contenitore | 
| **In Analisi avanzata selezionare i grafici a linee**:<br> Perf &#124; where ObjectName == "Container" and CounterName == "Memory Usage MB"<br> &#124; summarize AvgUsedMemory = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | Memoria del contenitore |

## <a name="how-to-stop-monitoring-with-container-health"></a>Come interrompere il monitoraggio con l'integrità dei contenitori
Se, dopo aver abilitato il monitoraggio di un contenitore AKS, si decide che il monitoraggio non è più necessario, è possibile *disabilitarlo esplicitamente* usando i modelli di Azure Resource Manager disponibili con il cmdlet **New-AzureRmResourceGroupDeployment** di PowerShell o l'interfaccia della riga di comando di Azure. Un modello JSON specifica la configurazione per *disabilitare esplicitamente il monitoraggio*. L'altro contiene i valori dei parametri configurati per specificare l'ID risorsa del cluster AKS e il gruppo di risorse in cui è distribuito il cluster. 

Se non si ha familiarità con il concetto di distribuzione delle risorse tramite un modello, vedere:
* [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
* [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](../azure-resource-manager/resource-group-template-deploy-cli.md)

Se si sceglie di usare l'interfaccia della riga di comando di Azure, è prima necessario installarla ed eseguirla in locale. È richiesta la versione 2.0.27 o successiva. Per identificare la versione in uso, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

### <a name="create-and-execute-template"></a>Creare ed eseguire il modello

1. Copiare e incollare nel file la sintassi JSON seguente:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
           "type": "string",
           "metadata": {
             "description": "AKS Cluster Resource ID"
           }
       },
      "aksResourceLocation": {
        "type": "string",
        "metadata": {
           "description": "Location of the AKS resource e.g. \"East US\""
         }
       }
    },
    "resources": [
      {
        "name": "[split(parameters('aksResourceId'),'/')[8]]",
        "type": "Microsoft.ContainerService/managedClusters",
        "location": "[parameters('aksResourceLocation')]",
        "apiVersion": "2018-03-31",
        "properties": {
          "mode": "Incremental",
          "id": "[parameters('aksResourceId')]",
          "addonProfiles": {
            "omsagent": {
              "enabled": false,
              "config": null
            }
           }
         }
       }
      ]
    }
    ```

2. Salvare il file con il nome **OptOutTemplate.json** in una cartella locale.
3. Incollare nel file la sintassi JSON seguente:

    ```json
    {
     "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "aksResourceId": {
         "value": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
      },
      "aksResourceLocation": {
        "value": "<aksClusterRegion>"
        }
      }
    }
    ```

4. Sostituire i valori di **aksResourceId** e **aksResourceLocation** con i valori del cluster AKS, indicati nella pagina **Proprietà** per il cluster selezionato.

    ![Pagina delle proprietà del contenitore](./media/monitoring-container-health/container-properties-page.png)

    Nella pagina **Proprietà** copiare anche l'**ID risorsa dell'area di lavoro**. Questo valore è necessario se si decide di eliminare l'area di lavoro di Log Analytics in un secondo momento. Questa operazione che non viene eseguita nell'ambito di questo processo. 

5. Salvare il file con il nome **OptOutParam.json** in una cartella locale.
6. A questo punto è possibile distribuire il modello. 

    * Per usare i comandi di PowerShell seguenti nella cartella che contiene il modello:

        ```powershell
        Connect-AzureRmAccount
        Select-AzureRmSubscription -SubscriptionName <yourSubscriptionName>
        New-AzureRmResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
        ```

        Il completamento della modifica della configurazione può richiedere alcuni minuti. Al termine, viene restituito un messaggio simile al seguente che include il risultato:

        ```powershell
        ProvisioningState       : Succeeded
        ```

    * Per eseguire il comando seguente con l'interfaccia della riga di comando di Azure in Linux:

        ```azurecli
        az login   
        az account set --subscription "Subscription Name" 
        az group deployment create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
        ```

        Il completamento della modifica della configurazione può richiedere alcuni minuti. Al termine, viene restituito un messaggio simile al seguente che include il risultato:

        ```azurecli
        ProvisioningState       : Succeeded
        ```

Se l'area di lavoro è stata creata solo per supportare il monitoraggio del cluster e non è più richiesta, è necessario eliminarla manualmente. Se non si ha familiarità con le procedure per l'eliminazione di un'area di lavoro, vedere [Eliminare un'area di lavoro di Azure Log Analytics con il portale di Azure](../log-analytics/log-analytics-manage-del-workspace.md). Non dimenticare il valore di **ID risorsa dell'area di lavoro** che è stato copiato in precedenza nel passaggio 4, perché sarà necessario in seguito. 

## <a name="troubleshooting"></a>risoluzione dei problemi
Questa sezione fornisce informazioni sulla risoluzione dei problemi relativi all'integrità dei contenitori.

Se l'integrità dei contenitori è stata abilitata e configurata correttamente, ma non è possibile visualizzare né informazioni sullo stato né risultati in Log Analytics quando si esegue una ricerca nei log, è possibile eseguire la procedura seguente per diagnosticare il problema: 

1. Controllare lo stato dell'agente eseguendo questo comando: 

    `kubectl get ds omsagent --namespace=kube-system`

    L'output dovrebbe essere simile al seguente, in cui è indicato che la distribuzione è stata eseguita correttamente:

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```  
2. Verificare lo stato di distribuzione della soluzione tramite la versione dell'agente *06072018* o successiva eseguendo questo comando:

    `kubectl get deployment omsagent-rs -n=kube-system`

    L'output dovrebbe essere simile al seguente, in cui è indicato che la distribuzione è stata eseguita correttamente:

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

3. Controllare lo stato del pod per verificare se è in esecuzione con il comando seguente: `kubectl get pods --namespace=kube-system`

    L'output dovrebbe essere simile al seguente con uno stato *In esecuzione* per omsagent:

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system 
    NAME                                READY     STATUS    RESTARTS   AGE 
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d 
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d 
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d 
    omsagent-484hw                      1/1       Running   0          1d 
    omsagent-fkq7g                      1/1       Running   0          1d 
    ```

4. Controllare i log dell'agente. Quando l'agente incluso in un contenitore viene distribuito, effettua un rapido controllo eseguendo i comandi OMI e quindi mostra la versione dell'agente e il provider. 

5. Per verificare che l'onboarding dell'agente sia stato completato, eseguire questo comando: `kubectl logs omsagent-484hw --namespace=kube-system`

    Lo stato dovrebbe essere simile al seguente:

    ```
    User@aksuser:~$ kubectl logs omsagent-484hw --namespace=kube-system
    :
    :
    instance of Container_HostInventory
    {
        [Key] InstanceID=3a4407a5-d840-4c59-b2f0-8d42e07298c2
        Computer=aks-nodepool1-39773055-0
        DockerVersion=1.13.1
        OperatingSystem=Ubuntu 16.04.3 LTS
        Volume=local
        Network=bridge host macvlan null overlay
        NodeRole=Not Orchestrated
        OrchestratorType=Kubernetes
    }
    Primary Workspace: b438b4f6-912a-46d5-9cb1-b44069212abc    Status: Onboarded(OMSAgent Running)
    omi 1.4.2.2
    omsagent 1.6.0.23
    docker-cimprov 1.0.0.31
    ```

## <a name="next-steps"></a>Passaggi successivi

Per visualizzare informazioni dettagliate sull'integrità dei contenitori e sulle prestazioni delle applicazioni, vedere [Eseguire ricerche nei log](../log-analytics/log-analytics-log-search.md). 
