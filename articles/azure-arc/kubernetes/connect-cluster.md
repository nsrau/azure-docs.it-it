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
ms.custom: references_regions
ms.openlocfilehash: eb3921d3ab2090b6bac54c9b68e9def3949ed4b5
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2020
ms.locfileid: "88723742"
---
# <a name="connect-an-azure-arc-enabled-kubernetes-cluster-preview"></a>Connettere un cluster Kubernetes abilitato per Azure Arc (anteprima)

Questo documento descrive il processo di connessione di un cluster Kubernetes certificato CNCF (cloud native Computing Foundation), ad esempio il motore AKS in Azure, il servizio contenitore di Azure su Azure Stack Hub, GKE, EKS e il cluster VMware vSphere ad Azure Arc.

## <a name="before-you-begin"></a>Prima di iniziare

Verificare che siano disponibili i requisiti seguenti:

* Un cluster Kubernetes in esecuzione. Se non si dispone di un cluster Kubernetes esistente, è possibile usare una delle guide seguenti per creare un cluster di test:
  * Creare un cluster Kubernetes usando [Kubernetes in Docker (Kind)](https://kind.sigs.k8s.io/)
  * Creare un cluster Kubernetes con Docker per [Mac](https://docs.docker.com/docker-for-mac/#kubernetes) o [Windows](https://docs.docker.com/docker-for-windows/#kubernetes)
* È necessario un file kubeconfig per accedere al cluster e al ruolo di amministratore del cluster nel cluster per la distribuzione degli agenti Kubernetes abilitati per Arc.
* L'utente o l'entità servizio usata con i comandi `az login` e `az connectedk8s connect` deve avere le autorizzazioni 'Read' (Lettura) e 'Write' (Scrittura) per il tipo di risorsa 'Microsoft.Kubernetes/connectedclusters'. Il ruolo "cluster Kubernetes-caricamento di Azure Arc" dispone di queste autorizzazioni e può essere usato per assegnazioni di ruolo all'utente o all'entità servizio.
* Helm 3 è necessario per l'onboarding del cluster usando l'estensione connectedk8s. Per soddisfare questo requisito, [installare la versione più recente di Helm 3](https://helm.sh/docs/intro/install) .
* L'interfaccia della riga di comando di Azure versione 2.3 + è necessaria per l'installazione delle estensioni dell'interfaccia della riga di comando Kubernetes [Installare l'interfaccia](/cli/azure/install-azure-cli?view=azure-cli-latest) della riga di comando di Azure o eseguire l'aggiornamento alla versione più recente per assicurarsi che l'interfaccia della riga di comando di Azure versione 2.3
* Installare le estensioni dell'interfaccia della riga di comando di Arc Enabled Kubernetes:
  
  Installare l'estensione `connectedk8s`, che consente di connettere i cluster Kubernetes ad Azure:
  
  ```console
  az extension add --name connectedk8s
  ```
  
  Installare l'estensione `k8sconfiguration`:
  
  ```console
  az extension add --name k8sconfiguration
  ```
  
  Se si desidera aggiornare le estensioni in un secondo momento, eseguire i comandi seguenti:
  
  ```console
  az extension update --name connectedk8s
  az extension update --name k8sconfiguration
  ```

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
| `https://eus.his.arc.azure.com`, `https://weu.his.arc.azure.com`                                                                            |  Obbligatorio per eseguire il pull dei certificati di identità gestiti assegnati dal sistema                                                                  |

## <a name="register-the-two-providers-for-azure-arc-enabled-kubernetes"></a>Registrare i due provider per Kubernetes con abilitazione di Azure Arc:

```console
az provider register --namespace Microsoft.Kubernetes

az provider register --namespace Microsoft.KubernetesConfiguration
```

La registrazione è un processo asincrono. La registrazione può richiedere circa 10 minuti. È possibile monitorare il processo di registrazione con i comandi seguenti:

```console
az provider show -n Microsoft.Kubernetes -o table
```

```console
az provider show -n Microsoft.KubernetesConfiguration -o table
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

È anche possibile visualizzare questa risorsa nella [portale di Azure](https://portal.azure.com/). Dopo aver aperto il portale nel browser, passare al gruppo di risorse e alla risorsa Kubernetes di Azure Arc abilitata in base al nome della risorsa e agli input del nome del gruppo di risorse usati in precedenza nel `az connectedk8s connect` comando.

> [!NOTE]
> Dopo l'onboarding del cluster, sono necessari circa 5-10 minuti per la visualizzazione dei metadati del cluster (versione del cluster, versione dell'agente, numero di nodi) nella pagina Panoramica della risorsa Kubernetes abilitata per Azure Arc in portale di Azure.

## <a name="connect-using-an-outbound-proxy-server"></a>Connettersi tramite un server proxy in uscita

Se il cluster si trova dietro un server proxy in uscita, l'interfaccia della riga di comando di Azure e l'arco abilitato per gli agenti Kubernetes devono instradare le richieste tramite il server proxy in uscita. La configurazione seguente consente di:

1. Controllare la versione dell' `connectedk8s` estensione installata nel computer eseguendo questo comando:

    ```bash
    az -v
    ```

    `connectedk8s`Per configurare gli agenti con il proxy in uscita, è necessaria la versione dell'estensione >= 0.2.3. Se è presente la versione < 0.2.3 nel computer, seguire i [passaggi di aggiornamento](#before-you-begin) per ottenere la versione più recente dell'estensione nel computer.

2. Impostare le variabili di ambiente necessarie per l'interfaccia della riga di comando di Azure:

    ```bash
    export HTTP_PROXY=<proxy-server-ip-address>:<port>
    export HTTPS_PROXY=<proxy-server-ip-address>:<port>
    export NO_PROXY=<cluster-apiserver-ip-address>:<port>
    ```

3. Eseguire il comando Connect con i parametri proxy specificati:

    ```bash
    az connectedk8s connect -n <cluster-name> -g <resource-group> \
    --proxy-https https://<proxy-server-ip-address>:<port> \
    --proxy-http http://<proxy-server-ip-address>:<port> \
    --proxy-skip-range <excludedIP>,<excludedCIDR>
    ```

> [!NOTE]
> 1. La specifica di excludedCIDR in--proxy-Skip-range è importante per garantire che la comunicazione nel cluster non venga interruppe per gli agenti.
> 2. La specifica del proxy precedente è attualmente applicata solo per gli agenti di arco e non per i pod Flux usati in sourceControlConfiguration. Il team Kubernetes di Arc Enabled sta lavorando attivamente a questa funzionalità e sarà presto disponibile.

## <a name="azure-arc-agents-for-kubernetes"></a>Agenti Azure Arc per Kubernetes

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

Kubernetes abilitato per Azure Arc è costituito da alcuni agenti (operatori) che vengono eseguiti nel cluster distribuito nello spazio dei nomi `azure-arc`.

* `deployment.apps/config-agent`: verifica nel cluster connesso le risorse di configurazione controllo del codice sorgente applicate al cluster e aggiorna lo stato di conformità
* `deployment.apps/controller-manager`: è un operatore di operatori e orchestra le interazioni tra i componenti di Azure Arc
* `deployment.apps/metrics-agent`: raccoglie le metriche di altri agenti Arc per assicurarsi che registrino prestazioni ottimali
* `deployment.apps/cluster-metadata-operator`: raccoglie i metadati del cluster, la versione del cluster, il numero di nodi e la versione dell'agente di Azure Arc
* `deployment.apps/resource-sync-agent`: sincronizza con Azure i metadati del cluster sopra indicati
* `deployment.apps/clusteridentityoperator`: Azure Arc Enabled Kubernetes supporta attualmente l'identità assegnata dal sistema. clusteridentityoperator gestisce il certificato dell'identità del servizio gestito usato da altri agenti per la comunicazione con Azure.
* `deployment.apps/flux-logs-agent`: raccoglie log degli operatori Flux distribuiti nel contesto della configurazione del controllo del codice sorgente

## <a name="delete-a-connected-cluster"></a>Eliminare un cluster connesso

È possibile eliminare una risorsa `Microsoft.Kubernetes/connectedcluster` usando l'interfaccia della riga di comando di Azure o il portale di Azure.


* **Eliminazione con l'interfaccia**della riga di comando di Azure: è possibile usare l'interfaccia della riga di comando di Azure seguente per avviare l'eliminazione della risorsa Kubernetes abilitata per Azure Arc.
  ```console
  az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
  ```
  Questo comando rimuove la `Microsoft.Kubernetes/connectedCluster` risorsa e tutte le `sourcecontrolconfiguration` risorse associate in Azure. L'interfaccia della riga di comando di Azure usa Helm Uninstall per rimuovere anche gli agenti in esecuzione nel cluster.

* **Eliminazione in portale di Azure**: l'eliminazione della risorsa Kubernetes abilitata per Azure Arc in portale di Azure Elimina la `Microsoft.Kubernetes/connectedcluster` risorsa e tutte le `sourcecontrolconfiguration` risorse associate in Azure, ma non elimina gli agenti in esecuzione nel cluster. Per eliminare gli agenti in esecuzione nel cluster, eseguire il comando seguente.

  ```console
  az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
  ```

## <a name="next-steps"></a>Passaggi successivi

* [Usare GitOps in un cluster connesso](./use-gitops-connected-cluster.md)
* [Usare Criteri di Azure per gestire la configurazione del cluster](./use-azure-policy.md)
