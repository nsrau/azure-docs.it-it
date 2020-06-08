---
title: Connettere un cluster Kubernetes abilitato per Azure Arc (anteprima)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Connettere un cluster Kubernetes abilitato per Azure Arc con Azure Arc
keywords: Kubernetes, Arc, Azure, K8s, contenitori
ms.openlocfilehash: 962b6a17743ea2beed1e16503739c55c83babbce
ms.sourcegitcommit: 95269d1eae0f95d42d9de410f86e8e7b4fbbb049
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83860546"
---
# <a name="connect-an-azure-arc-enabled-kubernetes-cluster-preview"></a>Connettere un cluster Kubernetes abilitato per Azure Arc (anteprima)

Connettere un cluster Kubernetes ad Azure Arc. 

## <a name="before-you-begin"></a>Prima di iniziare

Verificare che siano disponibili i requisiti seguenti:

* Un cluster Kubernetes operativo
* È necessario disporre dell'accesso con kubeconfig e dell'accesso cluster-admin. 
* L'utente o l'entità servizio usata con i comandi `az login` e `az connectedk8s connect` deve avere le autorizzazioni 'Read' (Lettura) e 'Write' (Scrittura) per il tipo di risorsa 'Microsoft.Kubernetes/connectedclusters'. Il ruolo "Azure Arc for Kubernetes onboarding" (Onboarding Azure Arc per Kubernetes) con queste autorizzazioni può essere usato per le assegnazioni di ruolo nell'utente o nell'entità servizio usata con l'interfaccia della riga di comando di Azure per l'onboarding.
* Versione più recente delle estensioni *connectedk8s* e *k8sconfiguration*

## <a name="supported-regions"></a>Aree supportate

* Stati Uniti orientali
* Europa occidentale

## <a name="network-requirements"></a>Requisiti di rete

Per il funzionamento degli agenti di Azure Arc sono necessari i protocolli, le porte e gli URL in uscita seguenti.

* TCP sulla porta 443 --> `https://:443`
* TCP sulla porta 9418 --> `git://:9418`

| Endpoint (DNS)                                                                                               | Descrizione                                                                                                                 |
| ------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------- |
| `https://management.azure.com`                                                                                 | Necessario per consentire all'agente di connettersi ad Azure e registrare il cluster                                                        |
| `https://eastus.dp.kubernetesconfiguration.azure.com`, `https://westeurope.dp.kubernetesconfiguration.azure.com` | Endpoint del piano dati che consente all'agente di eseguire il push dello stato e recuperare le informazioni di configurazione                                      |
| `https://docker.io`                                                                                            | Obbligatorio per il pull delle immagini del contenitore                                                                                         |
| `https://github.com`, git://github.com                                                                         | Esempi di repository GitOps sono ospitati in GitHub. L'agente di configurazione richiede la connettività a qualsiasi endpoint git specificato. |
| `https://login.microsoftonline.com`                                                                            | Obbligatorio per recuperare e aggiornare i token di Azure Resource Manager                                                                                    |
| `https://azurearcfork8s.azurecr.io`                                                                            | Obbligatorio per il pull delle immagini del contenitore per gli agenti di Azure Arc                                                                  |

## <a name="register-the-two-providers-for-azure-arc-enabled-kubernetes"></a>Registrare i due provider per Kubernetes con abilitazione di Azure Arc:

```console
az provider register --namespace Microsoft.Kubernetes
Registering is still on-going. You can monitor using 'az provider show -n Microsoft.Kubernetes'

az provider register --namespace Microsoft.KubernetesConfiguration
Registering is still on-going. You can monitor using 'az provider show -n Microsoft.KubernetesConfiguration'
```

La registrazione è un processo asincrono. La registrazione può richiedere circa 10 minuti. È possibile monitorare il processo di registrazione con i comandi seguenti:

```console
az provider show -n Microsoft.Kubernetes -o table
```

```console
az provider show -n Microsoft.KubernetesConfiguration -o table
```

## <a name="install-azure-cli-extensions"></a>Installare le estensioni dell'interfaccia della riga di comando di Azure

Installare l'estensione `connectedk8s`, che consente di connettere i cluster Kubernetes ad Azure:

```console
az extension add --name connectedk8s
```

Installare l'estensione `k8sconfiguration`:

```console
az extension add --name k8sconfiguration
```

Eseguire i comandi seguenti per aggiornare le estensioni alle versioni più recenti.

```console
az extension update --name connectedk8s
az extension update --name k8sconfiguration
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Usare un gruppo di risorse per archiviare metadati per il cluster.

Prima di tutto creare un gruppo di risorse in cui inserire la risorsa cluster connessa.

```console
az group create --name AzureArcTest -l EastUS -o table
```

**Output:**

```console
Location    Name
----------  ------------
eastus      AzureArcTest
```

## <a name="connect-a-cluster"></a>Connettere un cluster

Ora si eseguirà la connessione del cluster Kubernetes ad Azure. Il flusso di lavoro per `az connectedk8s connect` prevede le fasi seguenti:

1. Verificare la connettività al cluster Kubernetes: tramite `KUBECONFIG`, `~/.kube/config` o `--kube-config`
1. Distribuire agenti Azure Arc per Kubernetes usando Helm 3 nello spazio dei nomi `azure-arc`

```console
az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest
```

**Output:**

```console
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Helm release deployment succeeded

{
  "aadProfile": {
    "clientAppId": "",
    "serverAppId": "",
    "tenantId": ""
  },
  "agentPublicKeyCertificate": "...",
  "agentVersion": "0.1.0",
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1",
  "identity": {
    "principalId": null,
    "tenantId": null,
    "type": "None"
  },
  "kubernetesVersion": "v1.15.0",
  "location": "eastus",
  "name": "AzureArcTest1",
  "resourceGroup": "AzureArcTest",
  "tags": {},
  "totalNodeCount": 1,
  "type": "Microsoft.Kubernetes/connectedClusters"
}
```

## <a name="verify-connected-cluster"></a>Verificare il cluster connesso

Elencare i cluster connessi:

```console
az connectedk8s list -g AzureArcTest -o table
```

**Output:**

```console
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Name           Location    ResourceGroup
-------------  ----------  ---------------
AzureArcTest1  eastus      AzureArcTest
```

Kubernetes abilitato per Azure Arc distribuisce alcuni operatori nello spazio dei nomi `azure-arc`. È possibile visualizzare le distribuzioni e i pod qui:

```console
kubectl -n azure-arc get deployments,pods
```

**Output:**

```console
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

## <a name="azure-arc-agents-for-kubernetes"></a>Agenti Azure Arc per Kubernetes

Kubernetes abilitato per Azure Arc è costituito da alcuni agenti (operatori) che vengono eseguiti nel cluster distribuito nello spazio dei nomi `azure-arc`.

* `deployment.apps/config-agent`: verifica nel cluster connesso le risorse di configurazione controllo del codice sorgente applicate al cluster e aggiorna lo stato di conformità
* `deployment.apps/controller-manager`: è un operatore di operatori e orchestra le interazioni tra i componenti di Azure Arc
* `deployment.apps/metrics-agent`: raccoglie le metriche di altri agenti Arc per assicurarsi che registrino prestazioni ottimali
* `deployment.apps/cluster-metadata-operator`: raccoglie i metadati del cluster: versione del cluster, numero di nodi e versione dell'agente Arc
* `deployment.apps/resource-sync-agent`: sincronizza con Azure i metadati del cluster sopra indicati
* `deployment.apps/clusteridentityoperator`: gestisce il certificato di identità del servizio gestita (MSI) usato da altri agenti per le comunicazioni con Azure
* `deployment.apps/flux-logs-agent`: raccoglie log degli operatori Flux distribuiti nel contesto della configurazione del controllo del codice sorgente

## <a name="delete-a-connected-cluster"></a>Eliminare un cluster connesso

È possibile eliminare una risorsa `Microsoft.Kubernetes/connectedcluster` usando l'interfaccia della riga di comando di Azure o il portale di Azure.

Il comando dell'interfaccia della riga di comando di Azure `az connectedk8s delete` rimuove la risorsa `Microsoft.Kubernetes/connectedCluster` in Azure. L'interfaccia della riga di comando di Azure elimina tutte le risorse di `sourcecontrolconfiguration` associate in Azure. L'interfaccia della riga di comando di Azure usa il comando helm uninstall per rimuovere gli agenti dal cluster.

Il portale di Azure elimina la risorsa `Microsoft.Kubernetes/connectedcluster` in Azure ed elimina tutte le risorse `sourcecontrolconfiguration` associate in Azure.

Per rimuovere gli agenti nel cluster è necessario eseguire `az connectedk8s delete` o `helm uninstall azurearcfork8s`.

## <a name="next-steps"></a>Passaggi successivi

* [Usare GitOps in un cluster connesso](./use-gitops-connected-cluster.md)
* [Usare Criteri di Azure per gestire la configurazione del cluster](./use-azure-policy.md)
