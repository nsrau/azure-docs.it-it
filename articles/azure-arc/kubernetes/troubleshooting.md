---
title: Risolvere i problemi comuni di Kubernetes con abilitazione di Azure Arc (anteprima)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Risoluzione dei problemi comuni relativi ai cluster Kubernetes con abilitazione di Arc.
keywords: Kubernetes, Arc, Azure, contenitori
ms.openlocfilehash: 1527f8d4ca06c2deaf4ce18b73bfdb515dcadc63
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83725585"
---
# <a name="azure-arc-enabled-kubernetes-troubleshooting-preview"></a>Risoluzione dei problemi di Kubernetes con abilitazione di Azure Arc (anteprima)

In questo documento vengono illustrati alcuni scenari comuni di risoluzione dei problemi relativi a connettività, autorizzazioni e agenti.

## <a name="general-troubleshooting"></a>Risoluzione dei problemi generali

### <a name="azure-cli-set-up"></a>Configurazione dell'interfaccia della riga di comando di Azure
Prima di usare i comandi della riga di comando az connectedk8s o az k8sconfiguration, assicurarsi che az sia impostato in modo da funzionare con la sottoscrizione di Azure corretta.

```console
az account set --subscription 'subscriptionId'
az account show
```

### <a name="azure-arc-agents"></a>agenti azure-arc
Tutti gli agenti per Kubernetes con abilitazione di Azure Arc vengono distribuiti come pod nello spazio dei nomi `azure-arc`. In condizioni normali tutti i pod devono essere in esecuzione e superare i controlli di integrità.

Prima di tutto, verificare la versione di Helm di Azure Arc:

```console
$ helm --namespace default status azure-arc
NAME: azure-arc
LAST DEPLOYED: Fri Apr  3 11:13:10 2020
NAMESPACE: default
STATUS: deployed
REVISION: 5
TEST SUITE: None
```

Se la versione Helm non è stata trovata o è mancante, provare a caricare di nuovo il cluster.

Se la versione Helm è presente e `STATUS: deployed` determinare lo stato degli agenti utilizzando `kubectl`:

```console
$ kubectl -n azure-arc get deployments,pods
NAME                                        READY   UP-TO-DATE AVAILABLE AGE
deployment.apps/cluster-metadata-operator   1/1     1           1        16h
deployment.apps/clusteridentityoperator     1/1     1           1        16h
deployment.apps/config-agent                1/1     1           1        16h
deployment.apps/controller-manager          1/1     1           1        16h
deployment.apps/flux-logs-agent             1/1     1           1        16h
deployment.apps/metrics-agent               1/1     1           1        16h
deployment.apps/resource-sync-agent         1/1     1           1        16h

NAME                                            READY   STATUS   RESTART AGE
pod/cluster-metadata-operator-7fb54d9986-g785b  2/2     Running  0       16h
pod/clusteridentityoperator-6d6678ffd4-tx8hr    3/3     Running  0       16h
pod/config-agent-544c4669f9-4th92               3/3     Running  0       16h
pod/controller-manager-fddf5c766-ftd96          3/3     Running  0       16h
pod/flux-logs-agent-7c489f57f4-mwqqv            2/2     Running  0       16h
pod/metrics-agent-58b765c8db-n5l7k              2/2     Running  0       16h
pod/resource-sync-agent-5cf85976c7-522p5        3/3     Running  0       16h
```

Tutti i pod dovrebbero mostrare `STATUS` come `Running` e `READY` dovrebbero essere `3/3` o `2/2`. Recuperare i log e descrivere i pod che restituiscono `Error` o `CrashLoopBackOff`.

## <a name="unable-to-connect-my-kubernetes-cluster-to-azure"></a>Impossibile connettere un cluster Kubernetes ad Azure

Per la connessione di cluster ad Azure è necessario accedere a una sottoscrizione di Azure e `cluster-admin` accedere a un cluster di destinazione. Se non è possibile raggiungere il cluster o le autorizzazioni non sono sufficienti, il caricamento avrà esito negativo.

### <a name="insufficient-cluster-permissions"></a>Autorizzazioni cluster insufficienti

Se il file kubeconfig fornito non dispone di autorizzazioni sufficienti per installare gli agenti di Azure Arc, il comando dell'interfaccia della riga di comando di Azure restituirà un errore durante il tentativo di chiamare l'API Kubernetes.

```console
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...

Error: list: failed to list: secrets is forbidden: User "myuser" cannot list resource "secrets" in API group "" at the cluster scope
```

Il proprietario del cluster deve usare un utente Kubernetes con autorizzazioni di amministratore del cluster.

### <a name="installation-timeouts"></a>Timeout di installazione

Per l'installazione dell'agente di Azure Arc è necessario eseguire un set di contenitori nel cluster di destinazione. Se il cluster è in esecuzione su una connessione Internet lenta, il pull dell'immagine del contenitore potrebbe richiedere più tempo dei timeout dell'interfaccia della riga di comando di Azure.

```console
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...

There was a problem with connect-agent deployment. Please run 'kubectl -n azure-arc logs -l app.kubernetes.io/component=connect-agent -c connect-agent' to debug the error.
```

## <a name="configuration-management"></a>Gestione della configurazione

### <a name="general"></a>Generale
Per semplificare la risoluzione dei problemi relativi alla configurazione del controllo del codice sorgente, eseguire i comandi az con l'opzione --debug.

```console
az provider show -n Microsoft.KubernetesConfiguration --debug
az k8sconfiguration create <parameters> --debug
```

### <a name="create-source-control-configuration"></a>Creare la configurazione del controllo del codice sorgente
Il ruolo Collaboratore nella risorsa Microsoft.Kubernetes/connectedCluster è necessario e sufficiente per la creazione di risorse Microsoft.KubernetesConfiguration/sourceControlConfiguration.

### <a name="configuration-remains-pending"></a>La configurazione rimane `Pending`

```console
kubectl -n azure-arc logs -l app.kubernetes.io/component=config-agent -c config-agent
$ k -n pending get gitconfigs.clusterconfig.azure.com  -o yaml
apiVersion: v1
items:
- apiVersion: clusterconfig.azure.com/v1beta1
  kind: GitConfig
  metadata:
    creationTimestamp: "2020-04-13T20:37:25Z"
    generation: 1
    name: pending
    namespace: pending
    resourceVersion: "10088301"
    selfLink: /apis/clusterconfig.azure.com/v1beta1/namespaces/pending/gitconfigs/pending
    uid: d9452407-ff53-4c02-9b5a-51d55e62f704
  spec:
    correlationId: ""
    deleteOperator: false
    enableHelmOperator: false
    giturl: git@github.com:slack/cluster-config.git
    helmOperatorProperties: null
    operatorClientLocation: azurearcfork8s.azurecr.io/arc-preview/fluxctl:0.1.3
    operatorInstanceName: pending
    operatorParams: '"--disable-registry-scanning"'
    operatorScope: cluster
    operatorType: flux
  status:
    configAppliedTime: "2020-04-13T20:38:43.081Z"
    isSyncedWithAzure: true
    lastPolledStatusTime: ""
    message: 'Error: {exit status 1} occurred while doing the operation : {Installing
      the operator} on the config'
    operatorPropertiesHashed: ""
    publicKey: ""
    retryCountPublicKey: 0
    status: Installing the operator
kind: List
metadata:
  resourceVersion: ""
  selfLink: ""
```
