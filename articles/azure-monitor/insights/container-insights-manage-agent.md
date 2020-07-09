---
title: Come gestire l'agente di Monitoraggio di Azure per i contenitori | Microsoft Docs
description: Questo articolo descrive la gestione delle attività di manutenzione più comuni con l'agente di Log Analytics con contenitori usato da Monitoraggio di Azure per i contenitori.
ms.topic: conceptual
ms.date: 06/15/2020
ms.openlocfilehash: fc5bc0d60cb4ef1e375a997cbb3fe4bd2aed3235
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86107411"
---
# <a name="how-to-manage-the-azure-monitor-for-containers-agent"></a>Come gestire l'agente di Monitoraggio di Azure per i contenitori

Monitoraggio di Azure per i contenitori usa una versione con contenitori dell'agente di Log Analytics per Linux. Dopo la distribuzione iniziale, esistono attività di routine o facoltative che può essere necessario eseguire durante il ciclo di vita dell'agente. Questo articolo contiene informazioni dettagliate su come aggiornare l'agente manualmente e disabilitare la raccolta di variabili di ambiente da un contenitore specifico. 

## <a name="how-to-upgrade-the-azure-monitor-for-containers-agent"></a>Come eseguire l'aggiornamento dell'agente di Monitoraggio di Azure per i contenitori

Monitoraggio di Azure per i contenitori usa una versione con contenitori dell'agente di Log Analytics per Linux. Quando viene rilasciata una nuova versione dell'agente, questo viene aggiornato automaticamente nei cluster Kubernetes gestiti ospitati nel servizio Azure Kubernetes (AKS) e in Azure Red Hat OpenShift versione 3.x. Per un [cluster Kubernetes ibrido](container-insights-hybrid-setup.md) e per Azure Red Hat OpenShift versione 4. x, l'agente non è gestito ed è necessario aggiornarlo manualmente.

Se l'aggiornamento dell'agente non riesce per un cluster ospitato su servizio Azure Kubernetes o Azure Red Hat OpenShift versione 3. x, questo articolo descrive anche il processo di aggiornamento manuale dell'agente. Per seguire le versioni rilasciate, consultare gli [annunci relativi alla versione dell'agente](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).

### <a name="upgrade-agent-on-aks-cluster"></a>Aggiornare l'agente nel cluster servizio Azure Kubernetes

Il processo di aggiornamento dell'agente sui cluster del servizio Azure Kubernetes è costituito da due semplici passaggi. Il primo passaggio prevede la disabilitazione del monitoraggio con Monitoraggio di Azure per i contenitori tramite l'interfaccia della riga di comando di Azure. Seguire i passaggi descritti nell'articolo [Disabilitare il monitoraggio](container-insights-optout.md?#azure-cli). Usando l'interfaccia della riga di comando di Azure è possibile rimuovere l'agente dai nodi del cluster senza conseguenze per la soluzione e i dati corrispondenti archiviati nell'area di lavoro. 

>[!NOTE]
>Mentre si esegue questa attività di manutenzione, i nodi del cluster non inoltrano i dati raccolti e le viste Prestazioni non mostreranno i dati tra la rimozione dell'agente e l'installazione della nuova versione. 
>

Per installare la nuova versione dell'agente, seguire i passaggi descritti nell'articolo [Abilitare tramite l'interfaccia della riga di comando di Azure](container-insights-enable-new-cluster.md#enable-using-azure-cli) per completare questo processo.  

Dopo aver riabilitato il monitoraggio, possono essere necessari circa 15 minuti prima di poter visualizzare le metriche di integrità aggiornate per il cluster. Per verificare che l'aggiornamento dell'agente sia stato completato, eseguire il comando: `kubectl logs omsagent-484hw --namespace=kube-system`

Lo stato dovrebbe essere simile al seguente, dove il valore per *omi* e *omsagent* deve corrispondere alla versione più recente specificata nella [cronologia delle versioni dell'agente](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).  

```console
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
Primary Workspace: b438b4f6-912a-46d5-9cb1-b44069212abc
Status: Onboarded(OMSAgent Running)
omi 1.4.2.5
omsagent 1.6.0-163
docker-cimprov 1.0.0.31
```

### <a name="upgrade-agent-on-hybrid-kubernetes-cluster"></a>Aggiornare l'agente nel cluster Kubernetes ibrido

Seguire questa procedura per aggiornare l'agente in un cluster Kubernetes in esecuzione in:

* Cluster Kubernetes autogestiti ospitati in Azure con il motore del servizio Azure Kubernetes.
* Cluster Kubernetes autogestiti ospitati in Azure Stack o in locale con il motore del servizio Azure Kubernetes.
* Red Hat OpenShift versione 4.x.

Se l'area di lavoro Log Analytics è in Azure commerciale, eseguire questo comando:

```console
$ helm upgrade --name myrelease-1 \
--set omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<my_prod_cluster> incubator/azuremonitor-containers
```

Se l'area di lavoro Log Analytics è in Azure Cina 21Vianet, eseguire il comando seguente:

```console
$ helm upgrade --name myrelease-1 \
--set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
```

Se l'area di lavoro Log Analytics è in Azure US Gov, eseguire questo comando:

```console
$ helm upgrade --name myrelease-1 \
--set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
```

### <a name="upgrade-agent-on-azure-red-hat-openshift-v4"></a>Aggiornare l'agente in Azure Red Hat OpenShift v4

Seguire questa procedura per aggiornare l'agente in un cluster Kubernetes in esecuzione in Azure Red Hat OpenShift versione 4.x. 

>[!NOTE]
>Azure Red Hat OpenShift versione 4.x supporta solo l'esecuzione nel cloud di Azure commerciale.
>

```console
$ helm upgrade --name myrelease-1 \
--set omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterId=<azureAroV4ResourceId> incubator/azuremonitor-containers
```

### <a name="upgrade-agent-on-azure-arc-enabled-kubernetes"></a>Aggiornamento dell'agente in Azure Arc abilitato Kubernetes

Eseguire il comando seguente per aggiornare l'agente in un cluster Kubernetes abilitato per Azure Arc senza un endpoint proxy.

```console
$ helm upgrade --install azmon-containers-release-1  –set omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterId=<resourceIdOfAzureArcK8sCluster>
```

Eseguire il comando seguente per aggiornare l'agente quando viene specificato un endpoint proxy. Per ulteriori informazioni sull'endpoint proxy, vedere [configure proxy endpoint](container-insights-enable-arc-enabled-clusters.md#configure-proxy-endpoint).

```console
$ helm upgrade –name azmon-containers-release-1 –set omsagent.proxy=<proxyEndpoint>,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterId=<resourceIdOfAzureArcK8sCluster>
```

## <a name="how-to-disable-environment-variable-collection-on-a-container"></a>Come disabilitare la raccolta di variabili di ambiente in un contenitore

Monitoraggio di Azure per i contenitori raccoglie le variabili di ambiente dai contenitori in esecuzione in un pod e le presenta nel riquadro delle proprietà del contenitore selezionato nella visualizzazione **Contenitori**. È possibile controllare questo comportamento disabilitando la raccolta per un contenitore specifico durante la distribuzione del cluster Kubernetes oppure successivamente, impostando la variabile di ambiente *AZMON_COLLECT_ENV*. Questa funzionalità è disponibile dalla versione dell'agente ciprod11292018 e successive.  

Per disabilitare la raccolta di variabili di ambiente in un contenitore nuovo o esistente, impostare la variabile **AZMON_COLLECT_ENV** con il valore **False** nel file di configurazione yaml della distribuzione Kubernetes. 

```yaml
- name: AZMON_COLLECT_ENV  
  value: "False"  
```

Eseguire questo comando per applicare la modifica ad altri cluster Kubernetes diversi da Azure Red Hat OpenShift): `kubectl apply -f  <path to yaml file>`. Per modificare ConfigMap e applicare la modifica ai cluster Azure Red Hat OpenShift, eseguire il comando:

```bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

Si apre l'editor di testo predefinito. Dopo l'impostazione della variabile, salvare il file nell'editor.

Per verificare che la modifica della configurazione sia stata eseguita, selezionare un contenitore nella visualizzazione **Contenitori** in Monitoraggio di Azure per i contenitori ed espandere **Variabili di ambiente** nel pannello delle proprietà.  La sezione dovrebbe mostrare solo la variabile creata in precedenza, ovvero **AZMON_COLLECT_ENV = FALSE**. Per tutti gli altri contenitori, la sezione delle variabili di ambiente dovrebbe elencare tutte le variabili di ambiente individuate.

Per abilitare di nuovo il rilevamento delle variabili di ambiente, applicare la stessa procedura precedente e modificare il valore da **False** a **True**, quindi eseguire nuovamente il comando `kubectl` per aggiornare il contenitore.  

```yaml
- name: AZMON_COLLECT_ENV  
  value: "True"  
```  

## <a name="next-steps"></a>Passaggi successivi

Se si verificano problemi durante l'aggiornamento dell'agente, esaminare la [guida risoluzione dei problemi](container-insights-troubleshoot.md) per il supporto.
