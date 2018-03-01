---
title: Esercitazione su Kubernetes in Azure - Monitorare Kubernetes
description: Esercitazione su Servizio contenitore di Azure - Monitorare Kubernetes con Microsoft Operations Management Suite (OMS)
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 02/22/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 2fedd615733e3bf51469d3b69d5fe51e3e99087e
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/24/2018
---
# <a name="monitor-azure-container-service-aks"></a>Monitoraggio di Servizio contenitore di Azure (AKS)

Il monitoraggio del cluster e dei contenitori Kubernetes è critico, soprattutto quando si gestisce un cluster di produzione su larga scala con più app.

In questa esercitazione si configura il monitoraggio del cluster del servizio contenitore di Azure usando la [soluzione contenitori per Log Analytics][log-analytics-containers].

Questa esercitazione, parte sette di otto, illustra le attività seguenti:

> [!div class="checklist"]
> * Configurazione della soluzione monitoraggio contenitori
> * Configurazione degli agenti di monitoraggio
> * Accedere alle informazioni di monitoraggio nel portale di Azure

## <a name="before-you-begin"></a>Prima di iniziare

Nelle esercitazioni precedenti è stato creato un pacchetto di un'applicazione in immagini del contenitore, caricate poi nel Registro contenitori di Azure, ed è stato creato un cluster Kubernetes.

Se questi passaggi non sono stati ancora eseguiti e si vuole procedere, tornare a [Tutorial 1 – Create container images][aks-tutorial-prepare-app] (Esercitazione 1: Creare immagini del contenitore).

## <a name="configure-the-monitoring-solution"></a>Configurare la soluzione di monitoraggio

Nel portale di Azure fare clic su **Crea una risorsa** e cercare `Container Monitoring Solution`. Dopo averlo individuato selezionare **Crea**.

![Aggiungere la soluzione](./media/container-service-tutorial-kubernetes-monitor/add-solution.png)

Creare una nuova area di lavoro OMS o selezionarne una esistente. Il modulo dell'area di lavoro OMS serve da guida attraverso il processo.

Quando si crea l'area di lavoro, selezionare **Aggiungi al dashboard** per semplificarne il recupero.

![Area di lavoro di OMS](./media/container-service-tutorial-kubernetes-monitor/oms-workspace.png)

Al termine selezionare **OK**. Quando la convalida è stata completata, selezionare **Crea** per creare la soluzione monitoraggio contenitori.

Dopo la creazione, l'area di lavoro viene visualizzata nel portale di Azure.

## <a name="get-workspace-settings"></a>Ottenere le impostazioni dell'area di lavoro

L'ID area di lavoro di Log Analytics e la chiave sono necessari per configurare l'agente della soluzione nei nodi Kubernetes.

Per recuperare questi valori, selezionare **Area di lavoro OMS** nel menu a sinistra delle soluzioni contenitore. Selezionare **Impostazioni avanzate** e prendere nota dell'**ID AREA DI LAVORO** e della **CHIAVE PRIMARIA**.

## <a name="configure-monitoring-agents"></a>Configurare gli agenti di monitoraggio

Per configurare gli agenti di monitoraggio del contenitore in un cluster Kubernetes, è possibile usare il file manifesto Kubernetes seguente. Viene creato un [DaemonSet][kubernetes-daemonset] di Kubernetes, che esegue un singolo pod in ogni nodo del cluster.

Salvare il testo seguente in un file denominato `oms-daemonset.yaml` e sostituire i valori segnaposto `WSID` e `KEY` con l'ID e la chiave dell'area di lavoro di Log Analytics.

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
    agentVersion: 1.4.0-12
    dockerProviderVersion: 10.0.0-25
  spec:
   containers:
     - name: omsagent
       image: "microsoft/oms"
       imagePullPolicy: Always
       env:
       - name: WSID
         value: <WSID>
       - name: KEY
         value: <KEY>
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
        - mountPath: /var/opt/microsoft/omsagent/state/containerhostname
          name: container-hostname
        - mountPath: /var/log
          name: host-log
        - mountPath: /var/lib/docker/containers/
          name: container-log
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
    - name: container-hostname
      hostPath:
       path: /etc/hostname
    - name: host-log
      hostPath:
       path: /var/log
    - name: container-log
      hostPath:
       path: /var/lib/docker/containers/
```

Creare il DaemonSet con il comando seguente:

```azurecli
kubectl create -f oms-daemonset.yaml
```

Per verificare che sia stato creato il DaemonSet, eseguire:

```azurecli
kubectl get daemonset
```

L'output è simile al seguente:

```
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE-SELECTOR                 AGE
omsagent   3         3         3         3            3           beta.kubernetes.io/os=linux   8m
```

Con gli agenti in esecuzione, OMS richiede diversi minuti per inserire ed elaborare i dati.

## <a name="access-monitoring-data"></a>Accesso ai dati di monitoraggio

Nel portale di Azure selezionare l'area di lavoro Log Analytics che è stata aggiunta al dashboard del portale. Fare clic sul riquadro **Soluzione Monitoraggio contenitori**. Qui è possibile trovare informazioni sul cluster e i contenitori AKS dal cluster.

![dashboard](./media/container-service-tutorial-kubernetes-monitor/oms-containers-dashboard.png)

Vedere la [documentazione su Log Analytics][log-analytics-docs] per istruzioni dettagliate sulla creazione di query e sull'analisi dei dati di monitoraggio.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato monitorato il cluster di Kubernetes con OMS. Le attività descritte includevano:

> [!div class="checklist"]
> * Configurazione della soluzione monitoraggio contenitori
> * Configurazione degli agenti di monitoraggio
> * Accedere alle informazioni di monitoraggio nel portale di Azure

Passare all'esercitazione successiva per apprendere come aggiornare Kubernetes a una nuova versione.

> [!div class="nextstepaction"]
> [Aggiornare Kubernetes][aks-tutorial-upgrade]

<!-- LINKS - external -->
[kubernetes-daemonset]: https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/

<!-- LINKS - internal -->
[aks-tutorial-deploy-app]: ./tutorial-kubernetes-deploy-application.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-upgrade]: ./tutorial-kubernetes-upgrade-cluster.md
[log-analytics-containers]: ../log-analytics/log-analytics-containers.md
[log-analytics-docs]: ../log-analytics/index.yml
