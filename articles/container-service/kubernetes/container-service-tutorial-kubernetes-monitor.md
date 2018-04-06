---
title: Esercitazione sul servizio contenitore di Azure - Monitorare Kubernetes
description: Esercitazione sul servizio contenitore di Azure - Monitorare Kubernetes con Log Analytics
services: container-service
author: dlepow
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 02/26/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: e7d55f1579ce45a39f9b07225bc88c8ef8ff6b66
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2018
---
# <a name="monitor-a-kubernetes-cluster-with-log-analytics"></a>Monitorare un cluster Kubernetes con Log Analytics

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

Il monitoraggio del cluster e dei contenitori Kubernetes è critico, soprattutto quando si gestisce un cluster di produzione su larga scala con più app. 

È possibile sfruttare diverse soluzioni di monitoraggio di Kubernetes, da Microsoft o da altri provider. In questa esercitazione si monitora il cluster Kubernetes con la soluzione Contenitori in [Log Analytics](../../operations-management-suite/operations-management-suite-overview.md), la soluzione di gestione IT di Microsoft basata sul cloud (la soluzione Contenitori è in anteprima).

Questa esercitazione, parte sette di sette, illustra le attività seguenti:

> [!div class="checklist"]
> * Ottenere le impostazioni dell'area di lavoro di Log Analytics
> * Configurare gli agenti OMS nei nodi Kubernetes
> * Accedere alle informazioni di monitoraggio nel portale di OMS o nel portale di Azure

## <a name="before-you-begin"></a>Prima di iniziare

Nelle esercitazioni precedenti è stato creato un pacchetto di un'applicazione in immagini del contenitore, caricate poi nel Registro contenitori di Azure, ed è stato creato un cluster Kubernetes. 

Se questi passaggi non sono stati ancora eseguiti e si vuole procedere, tornare a [Tutorial 1 – Create container images](./container-service-tutorial-kubernetes-prepare-app.md) (Esercitazione 1: Creare immagini del contenitore). 

## <a name="get-workspace-settings"></a>Ottenere le impostazioni dell'area di lavoro

Quando è possibile accedere al [portale OMS](https://mms.microsoft.com), passare a **Impostazioni** > **Origini connesse** > **Server Linux**. Qui è possibile trovare l'*ID area di lavoro* e una *Chiave dell'area di lavoro* primaria o secondaria. Prendere nota di questi valori, per cui è necessario configurare gli agenti OMS nel cluster.

## <a name="create-kubernetes-secret"></a>Creare un segreto Kubernetes

Archiviare le impostazioni dell'area di lavoro di Log Analytics in un segreto Kubernetes denominato `omsagent-secret` usando il comando [kubectl create secret][kubectl-create-secret]. Aggiornare `WORKSPACE_ID` con l'ID dell'area di lavoro di Log Analytics e `WORKSPACE_KEY` con la chiave dell'area di lavoro.

```console
kubectl create secret generic omsagent-secret --from-literal=WSID=WORKSPACE_ID --from-literal=KEY=WORKSPACE_KEY
```

## <a name="set-up-oms-agents"></a>Configurare gli agenti OMS

Di seguito è riportato un file con estensione YAML per configurare gli agenti OMS nei nodi del cluster di Linux. Questa operazione crea un [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) di Kubernetes, che esegue un singolo pod identico in ogni nodo del cluster. La risorsa DaemonSet è ideale per la distribuzione di un agente di monitoraggio. 

Salvare il testo seguente in un file denominato `oms-daemonset.yaml` e sostituire i valori dei segnaposto *myWorkspaceID* e *myWorkspaceKey* con l'ID e la chiave dell'area di lavoro di Log Analytics (nell'ambiente di produzione, è possibile codificare questi valori come segreti).

```YAML
apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
 name: omsagent
spec:
 template:
  metadata:
   labels:
    app: omsagent
    agentVersion: 1.4.3-174
    dockerProviderVersion: 1.0.0-30
  spec:
   containers:
     - name: omsagent 
       image: "microsoft/oms"
       imagePullPolicy: Always
       securityContext:
         privileged: true
       ports:
       - containerPort: 25225
         protocol: TCP 
       - containerPort: 25224
         protocol: UDP
       volumeMounts:
        - mountPath: /var/run/docker.sock
          name: docker-sock
        - mountPath: /var/log 
          name: host-log
        - mountPath: /etc/omsagent-secret
          name: omsagent-secret
          readOnly: true
        - mountPath: /var/lib/docker/containers 
          name: containerlog-path  
       livenessProbe:
        exec:
         command:
         - /bin/bash
         - -c
         - ps -ef | grep omsagent | grep -v "grep"
        initialDelaySeconds: 60
        periodSeconds: 60
   nodeSelector:
    beta.kubernetes.io/os: linux    
   # Tolerate a NoSchedule taint on master that ACS Engine sets.
   tolerations:
    - key: "node-role.kubernetes.io/master"
      operator: "Equal"
      value: "true"
      effect: "NoSchedule"     
   volumes:
    - name: docker-sock 
      hostPath:
       path: /var/run/docker.sock
    - name: host-log
      hostPath:
       path: /var/log 
    - name: omsagent-secret
      secret:
       secretName: omsagent-secret
    - name: containerlog-path
      hostPath:
       path: /var/lib/docker/containers 
```

Creare il DaemonSet con il comando seguente:

```azurecli-interactive
kubectl create -f oms-daemonset.yaml
```

Per verificare che sia stato creato il DaemonSet, eseguire:

```azurecli-interactive
kubectl get daemonset
```

L'output è simile al seguente:

```azurecli-interactive
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE-SELECTOR   AGE
omsagent   3         3         3         0            3           <none>          5m
```

Con gli agenti in esecuzione, Log Analytics richiede diversi minuti per inserire ed elaborare i dati.

## <a name="access-monitoring-data"></a>Accesso ai dati di monitoraggio

Visualizzare e analizzare i dati di monitoraggio del contenitore con la [Soluzione Contenitore](../../log-analytics/log-analytics-containers.md) nel portale di OMS o nel portale di Azure. 

Per installare la Soluzione Contenitore tramite il [portale di OMS](https://mms.microsoft.com), passare a **Raccolta soluzioni**. Aggiungere quindi la **Soluzione Contenitore**. In alternativa, aggiungere la Soluzione Contenitore da [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft.containersoms?tab=Overview).

Nel portale di OMS cercare un riquadro di riepilogo **Contenitori** nel dashboard. Fare clic sul riquadro per i dettagli, tra cui: eventi del contenitore, errori, stato, inventario dell'immagine e uso di CPU e memoria. Per informazioni più granulari, fare clic su una riga in qualsiasi riquadro o eseguire una [ricerca log](../../log-analytics/log-analytics-log-searches.md).

![Dashboard dei contenitori nel portale OMS](./media/container-service-tutorial-kubernetes-monitor/oms-containers-dashboard.png)

Analogamente, nel portale di Azure passare a **Log Analytics** e selezionare il nome della propria area di lavoro. Per visualizzare il riquadro di riepilogo dei **Contenitori**, fare clic su **Soluzioni** > **Contenitori**. Per visualizzare i dettagli, fare clic sul riquadro.

Vedere la [Documentazione su Log Analytics](../../log-analytics/index.yml) per istruzioni dettagliate sulla creazione di query e sull'analisi dei dati di monitoraggio.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato monitorato il cluster Kubernetes con Log Analytics. Le attività descritte includevano:

> [!div class="checklist"]
> * Ottenere le impostazioni dell'area di lavoro di Log Analytics
> * Configurare gli agenti OMS nei nodi Kubernetes
> * Accedere alle informazioni di monitoraggio nel portale di OMS o nel portale di Azure


Seguire questo collegamento per vedere esempi di script predefiniti per il servizio contenitore.

> [!div class="nextstepaction"]
> [Esempi di script del servizio contenitore di Azure](cli-samples.md)
