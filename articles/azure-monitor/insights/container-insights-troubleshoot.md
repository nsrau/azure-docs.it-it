---
title: Come risolvere i problemi di Monitoraggio di Azure per contenitori | Microsoft Docs
description: Questo articolo illustra come individuare e risolvere i problemi con Monitoraggio di Azure per contenitori.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 17a2817b320599b2aa2c331c354d316b9d864a32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75403372"
---
# <a name="troubleshooting-azure-monitor-for-containers"></a>Risoluzione dei problemi di Monitoraggio di Azure per contenitori

Quando si configura il monitoraggio del cluster del servizio Azure Kubernetes con Monitoraggio di Azure per contenitori, è possibile riscontrare un problema che impedisce la raccolta di dati o la segnalazione dello stato. Questo articolo illustra alcuni problemi comuni e i passaggi per la risoluzione dei problemi.

## <a name="authorization-error-during-onboarding-or-update-operation"></a>Errore di autorizzazione durante l'operazione di onboarding o aggiornamentoAuthorization error during onboarding or update operation
Mentre l'abilitazione di Monitoraggio di Azure per i contenitori o l'aggiornamento di un cluster per supportare la raccolta di metriche, è possibile che venga visualizzato un errore simile al seguente: *l'id di> identità dell'utente <client con ID oggetto '<'id objectId>' dell'utente non dispone dell'autorizzazione per eseguire l'azione 'Microsoft.Authorization/roleAssignments/write' sull'ambito*

Durante il processo di onboarding o aggiornamento, la concessione dell'assegnazione di ruolo **Di tipo Monitoring Metrics Publisher** viene tentata nella risorsa cluster. L'utente che avvia il processo per abilitare Monitoraggio di Azure per i contenitori o l'aggiornamento per supportare la raccolta di metriche deve avere accesso all'autorizzazione **Microsoft.Authorization/roleAssignments/write** nell'ambito delle risorse cluster AKS. Solo ai membri dei ruoli predefiniti **Proprietario** e **Amministratore accesso utenti** viene concesso l'accesso a questa autorizzazione. Se i criteri di sicurezza richiedono l'assegnazione di autorizzazioni a livello granulare, è consigliabile visualizzare [i ruoli personalizzati](../../role-based-access-control/custom-roles.md) e assegnarli agli utenti che le richiedono. 

È anche possibile concedere manualmente questo ruolo dal portale di Azure eseguendo la procedura seguente:You can also manually grant this role from the Azure portal by performing the following steps:

1. Accedere al [portale](https://portal.azure.com)di Azure . 
2. Nel portale di Azure fare clic su **Tutti i servizi** nell'angolo superiore sinistro. Nell'elenco delle risorse digitare **Kubernetes**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Selezionare **Azure Kubernetes**.
3. Nell'elenco dei cluster Kubernetes selezionarne uno dall'elenco.
2. Dal menu a sinistra, fai clic su **Controllo di accesso (IAM)**.
3. Selezionare **Aggiungi** per aggiungere un'assegnazione di ruolo e selezionare il ruolo **di editore Monitoring Metrics** e nella casella **Seleziona** digitare **AKS** per filtrare i risultati solo nelle entità servizio dei cluster definite nella sottoscrizione. Selezionare quello dall'elenco specifico per il cluster.
4. Selezionare **Salva** per completare l'assegnazione del ruolo. 

## <a name="azure-monitor-for-containers-is-enabled-but-not-reporting-any-information"></a>Monitoraggio di Azure per contenitori è abilitato ma non segnala alcuna informazione
Se Monitoraggio di Azure per i contenitori è abilitato e configurato correttamente, ma non è possibile visualizzare le informazioni sullo stato o non viene restituito alcun risultato da una query di log, diagnosticare il problema attenendosi alla procedura seguente:If Azure Monitor for containers is successfully enabled and configured, but you cannot view status information or no results are returned from a log query, you diagnose the problem by following these steps: 

1. Controllare lo stato dell'agente eseguendo il comando: 

    `kubectl get ds omsagent --namespace=kube-system`

    L'output dovrebbe essere simile al seguente, in cui è indicato che la distribuzione è stata eseguita correttamente:

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```  
2. Verificare lo stato della distribuzione con la versione dell'agente *06072018* o successiva con il comando:

    `kubectl get deployment omsagent-rs -n=kube-system`

    L'output dovrebbe essere simile all'esempio seguente, da cui risulta che la distribuzione è stata eseguita correttamente:

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

3. Controllare lo stato del pod per verificare se è in esecuzione con il comando: `kubectl get pods --namespace=kube-system`

    L'output dovrebbe essere simile all'esempio seguente con uno stato *In esecuzione* per omsagent:

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

5. Per verificare che l'agente sia stato distribuito correttamente, eseguire il comando:`kubectl logs omsagent-484hw --namespace=kube-system`

    Lo stato dovrebbe essere simile all'esempio seguente:

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

## <a name="error-messages"></a>messaggi di errore

La tabella seguente riepiloga gli errori noti che è possibile si verifichino durante l'uso di Monitoraggio di Azure per i contenitori.

| messaggi di errore  | Azione |  
| ---- | --- |  
| Messaggio di errore `No data for selected filters`  | L'individuazione del flusso di dati di monitoraggio per i nuovi cluster potrebbe richiedere alcuni minuti. Attendere almeno 10-15 minuti per la visualizzazione dei dati per il cluster. |   
| Messaggio di errore `Error retrieving data` | Mentre il cluster del servizio Azure Kubernetes sta configurando il monitoraggio dell'integrità e delle prestazioni, viene stabilita una connessione tra il cluster e l'area di lavoro di Azure Log Analytics.While Azure Kubernetes Service cluster is setting up for health and performance monitoring, a connection is established between the cluster and Azure Log Analytics workspace. Un'area di lavoro Log Analytics viene usata per archiviare tutti i dati di monitoraggio per il cluster. Questo errore può verificarsi quando l'area di lavoro di Log Analytics è stata eliminata. Verificare se l'area di lavoro è stata eliminata e se lo è stato, sarà necessario riattivare il monitoraggio del cluster con Monitoraggio di Azure per i contenitori e specificare una nuova area di lavoro esistente o crearne una nuova. Per riabilitare, è necessario [disabilitare](container-insights-optout.md) il monitoraggio per il cluster e [abilitare](container-insights-enable-new-cluster.md) nuovamente Monitoraggio di Azure per i contenitori. |  
| `Error retrieving data` dopo l'aggiunta di Monitoraggio di Azure per contenitori tramite l'interfaccia della riga di comando servizio Azure Kubernetes | Quando si `az aks cli`abilita il monitoraggio tramite , Monitoraggio di Azure per i contenitori potrebbe non essere distribuito correttamente. Verificare se la soluzione è distribuita. A tale scopo, passare all'area di lavoro Log Analytics e verificare se la soluzione è disponibile selezionando **Soluzioni** dal riquadro sul lato sinistro. Per risolvere questo problema, è necessario ridistribuire la soluzione seguendo le istruzioni per la [distribuzione di Monitoraggio di Azure per i contenitori](container-insights-onboard.md) |  

Per eseguire la diagnosi del problema, è disponibile uno script di risoluzione dei problemi [qui](https://github.com/Microsoft/OMS-docker/tree/ci_feature_prod/Troubleshoot#troubleshooting-script).

## <a name="azure-monitor-for-containers-agent-replicaset-pods-are-not-scheduled-on-non-azure-kubernetes-cluster"></a>Monitoraggio di Azure per i pod ReplicaSet dell'agente contenitori non sono pianificati in cluster non Azure KubernetesAzure Monitor for containers agent ReplicaSet Pods are not scheduled on-non-Azure Kubernetes cluster

L'agente ReplicaSet di Monitor di Azure ha una dipendenza dai selettori di nodo seguenti nei nodi di lavoro (o agente) per la pianificazione:Azure Monitor for containers agent ReplicaSet Pods has a dependency on the following node selectors on the worker (or agent) nodes for the scheduling:

```
nodeSelector:
  beta.kubernetes.io/os: Linux
  kubernetes.io/role: agent
```

Se ai nodi di lavoro non sono associate etichette di nodo, i pod ReplicaSet dell'agente non verranno pianificati. Fare riferimento a [Kubernetes assegnare i selettori](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/) di etichetta per istruzioni su come allegare l'etichetta.

## <a name="performance-charts-dont-show-cpu-or-memory-of-nodes-and-containers-on-a-non-azure-cluster"></a>I grafici delle prestazioni non mostrano la CPU o la memoria di nodi e contenitori in un cluster non AzurePerformance charts don't show CPU or memory of nodes and containers on a non-Azure cluster

Monitor di Azure per gli agenti contenitori Pods usa l'endpoint di cAdvisor nell'agente di nodo per raccogliere le metriche delle prestazioni. Verificare che l'agente in contenitori nel nodo sia configurato per consentire `cAdvisor port: 10255` l'apertura in tutti i nodi del cluster per raccogliere le metriche delle prestazioni.

## <a name="non-azure-kubernetes-cluster-are-not-showing-in-azure-monitor-for-containers"></a>Cluster non Azure Kubernetes non vengono visualizzati in Monitoraggio di Azure per i contenitori

Per visualizzare il cluster non Azure Kubernetes in Monitoraggio di Azure per i contenitori, è necessario l'accesso in lettura nell'area di lavoro di Log Analytics che supporta questa Insight e nella risorsa della soluzione Container Insights **ContainerInsights (*area di lavoro*).**

## <a name="next-steps"></a>Passaggi successivi

Con il monitoraggio abilitato per acquisire le metriche di integrità sia per i nodi del cluster servizio Azure Kubernetes che per i pod, queste metriche di integrità sono disponibili nel portale di Azure. Per informazioni su come usare Monitoraggio di Azure per contenitori, vedere [Visualizzare l'integrità del servizio Azure Kubernetes](container-insights-analyze.md).
