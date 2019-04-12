---
title: Come risolvere i problemi di Monitoraggio di Azure per contenitori | Microsoft Docs
description: Questo articolo illustra come individuare e risolvere i problemi con Monitoraggio di Azure per contenitori.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2018
ms.author: magoedte
ms.openlocfilehash: db4b468c03d93b073067083f4fae1ec86c70dde8
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2019
ms.locfileid: "58577043"
---
# <a name="troubleshooting-azure-monitor-for-containers"></a>Risoluzione dei problemi di Monitoraggio di Azure per contenitori

Quando si configura il monitoraggio del cluster del servizio Azure Kubernetes con Monitoraggio di Azure per contenitori, è possibile riscontrare un problema che impedisce la raccolta di dati o la segnalazione dello stato. Questo articolo illustra alcuni problemi comuni e i passaggi per la risoluzione dei problemi.

## <a name="authorization-error-during-onboarding-or-update-operation"></a>Errore di autorizzazione durante l'operazione di caricamento o aggiornamento
Durante l'abilitazione di monitoraggio di Azure per contenitori o l'aggiornamento di un cluster per supportare la raccolta di metriche, si potrebbe ricevere un errore simile a quanto segue - *client < identità dell'utente >' con oggetto id '< ID oggetto dell'utente >' non dispone autorizzazione per eseguire l'azione 'Microsoft.Authorization/roleAssignments/write' sull'ambito*

Durante il processo di caricamento o l'aggiornamento, la concessione il **editore di metriche di monitoraggio** viene eseguito un tentativo di assegnazione di ruolo sulla risorsa cluster. Utente che ha avviato il processo per abilitare il monitoraggio di Azure per contenitori o l'aggiornamento supportare la raccolta della metrica deve poter eseguire la **Microsoft.Authorization/roleAssignments/write** l'autorizzazione per il cluster AKS ambito di risorse. Solo i membri del **Owner** e **amministratore accesso utenti** ruoli predefiniti vengono concesso l'accesso all'autorizzazione. Se i criteri di sicurezza richiedono l'assegnazione di autorizzazioni a livello granulare, è consigliabile considerare [ruoli personalizzati](../../role-based-access-control/custom-roles.md) e assegnarla a utenti che lo richiedono. 

È possibile concedere manualmente questo ruolo dal portale di Azure attenendosi alla procedura seguente:

1. Accedere al [portale di Azure](https://portal.azure.com). 
2. Nel portale di Azure fare clic su **Tutti i servizi** nell'angolo superiore sinistro. Nell'elenco delle risorse, digitare **Kubernetes**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Selezionare **Kubernetes Azure**.
3. Nell'elenco di cluster Kubernetes, selezionarne uno dall'elenco.
2. Nel menu a sinistra, fare clic su **controllo di accesso (IAM)**.
3. Selezionare **+ Aggiungi** per aggiungere un'assegnazione di ruolo e selezionare il **editore di metriche di monitoraggio** ruolo e nel **selezionare** nella casella **AKS** a i risultati nel solo cluster di entità definite nella sottoscrizione di servizio di filtro. Selezionare l'uno dall'elenco specifico di tale cluster.
4. Selezionare **Salva** per completare l'assegnazione del ruolo. 

## <a name="azure-monitor-for-containers-is-enabled-but-not-reporting-any-information"></a>Monitoraggio di Azure per contenitori è abilitato ma non segnala alcuna informazione
Se il monitoraggio di Azure per contenitori sia correttamente abilitato e configurato, ma non è possibile visualizzare le informazioni sullo stato o viene restituito alcun risultato da una query di log, diagnosticare il problema seguendo questa procedura: 

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

5. Per verificare che l'onboarding dell'agente sia stato completato, eseguire il comando: `kubectl logs omsagent-484hw --namespace=kube-system`

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
| Messaggio di errore `No data for selected filters`  | L'individuazione del flusso di dati di monitoraggio per i nuovi cluster potrebbe richiedere alcuni minuti. Attendere almeno 10/15 minuti perché vengano visualizzati i dati per il cluster. |   
| Messaggio di errore `Error retrieving data` | Durante l'installazione del servizio Kubenetes di Azure per il monitoraggio dell'integrità e delle prestazioni, viene stabilita una connessione tra il cluster e l'area di lavoro Azure Log Analytics. Un'area di lavoro Log Analytics viene usata per archiviare tutti i dati di monitoraggio per il cluster. Questo errore può verificarsi quando l'area di lavoro Log Analytics è stata eliminata o persa. Verificare se l'area di lavoro è disponibile rivedendo le informazioni su come [gestire l'accesso](../../azure-monitor/platform/manage-access.md?toc=/azure/azure-monitor/toc.json#view-workspace-details). Se l'area di lavoro è mancante, è necessario ripetere il caricamento del cluster con Monitoraggio di Azure per i contenitori. Per ripetere il caricamento, è necessario [disabilitare](container-insights-optout.md) il monitoraggio per il cluster e [abilitare](container-insights-onboard.md?toc=%2fazure%2fmonitoring%2ftoc.json#enable-monitoring-for-a-new-cluster) di nuovo Monitoraggio di Azure per i contenitori. |  
| `Error retrieving data` dopo l'aggiunta di Monitoraggio di Azure per contenitori tramite l'interfaccia della riga di comando servizio Azure Kubernetes | Quando si esegue il caricamento usando `az aks cli`, molto raramente Monitoraggio di Azure per i contenitori potrebbe non essere caricato correttamente. Controllare se la soluzione è caricata. A tale scopo, passare all'area di lavoro Log Analytics e verificare se la soluzione è disponibile selezionando **Soluzioni** dal riquadro sul lato sinistro. Per risolvere questo problema, è necessario ridistribuire la soluzione seguendo le istruzioni per la [distribuzione di Monitoraggio di Azure per i contenitori](container-insights-onboard.md?toc=%2fazure%2fmonitoring%2ftoc.json) |  

Per eseguire la diagnosi del problema, è disponibile uno script di risoluzione dei problemi [qui](https://github.com/Microsoft/OMS-docker/tree/ci_feature_prod/Troubleshoot#troubleshooting-script).  

## <a name="next-steps"></a>Passaggi successivi
Con il monitoraggio abilitato per acquisire le metriche di integrità sia per i nodi del cluster servizio Azure Kubernetes che per i pod, queste metriche di integrità sono disponibili nel portale di Azure. Per informazioni su come usare Monitoraggio di Azure per contenitori, vedere [Visualizzare l'integrità del servizio Azure Kubernetes](container-insights-analyze.md).