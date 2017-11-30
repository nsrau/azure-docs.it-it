---
title: Monitorare il cluster Kubernetes di Azure - Operations Management | Documentazione Microsoft
description: Monitoraggio di cluster Kubernetes nel servizio contenitore di Azure con Microsoft Operations Management Suite
services: container-service
documentationcenter: 
author: bburns
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: c402d81841ed5a79df8a84d1f69f984d010dbe0d
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2017
---
# <a name="monitor-an-azure-container-service-cluster-with-microsoft-operations-management-suite-oms"></a>Monitorare un cluster del servizio contenitore di Azure con Microsoft Operations Management Suite (OMS)

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

## <a name="prerequisites"></a>Prerequisiti
Si presume che questa procedura dettagliata abbia [creato un cluster Kubernetes mediante il servizio contenitore di Azure](container-service-kubernetes-walkthrough.md).

Si presume anche che gli strumenti dell'interfaccia della riga di comando di Azure `az` e `kubectl` siano installati.

È possibile verificare se lo strumento `az` è installato eseguendo:

```console
$ az --version
```

Se lo strumento `az` non è installato, le istruzioni sono disponibili [qui](https://github.com/azure/azure-cli#installation).  
In alternativa, è possibile usare [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview), che presenta l'interfaccia della riga di comando `az` di Azure e gli strumenti `kubectl` già installati per l'utente.  

È possibile verificare se lo strumento `kubectl` è installato eseguendo:

```console
$ kubectl version
```

Se `kubectl` non è installato, è possibile eseguire:
```console
$ az acs kubernetes install-cli
```

Per verificare la presenza di chiavi kubernetes installate nello strumento di kubectl è possibile eseguire:
```console
$ kubectl get nodes
```

Se il comando precedente ha presentato degli errori, è necessario installare le chiavi di cluster kubernetes nello strumento di kubectl desiderato. A tale scopo, eseguire il comando seguente:
```console
RESOURCE_GROUP=my-resource-group
CLUSTER_NAME=my-acs-name
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$CLUSTER_NAME
```

## <a name="monitoring-containers-with-operations-management-suite-oms"></a>Monitoraggio dei contenitori con Operations Management Suite (OMS)

Microsoft Operations Management (OMS) è la soluzione Microsoft per la gestione IT basata sul cloud che consente di gestire e proteggere l'infrastruttura locale e cloud. La soluzione contenitore è una soluzione di Log Analytics di OMS che consente di visualizzare l'inventario, le prestazioni e i log del contenitore in un'unica posizione. È possibile controllare, risolvere i problemi relativi ai contenitori visualizzando i log in una posizione centralizzata e trovare un contenitore con un consumo eccessivo in un host.

![](media/container-service-monitoring-oms/image1.png)

Per altre informazioni sulla soluzione contenitore, consultare [Log Analytics della soluzione contenitore](../../log-analytics/log-analytics-containers.md).

## <a name="installing-oms-on-kubernetes"></a>Installazione di OMS in Kubernetes

### <a name="obtain-your-workspace-id-and-key"></a>Ottenere l'ID e la chiave dell'area di lavoro
Per consentire la comunicazione dell'agente OMS con il servizio, è necessario configurare il rispettivo ID e la rispettiva chiave dell'area di lavoro. Per ottenere l'ID e la chiave dell'area di lavoro è necessario creare un account OMS nel sito <https://mms.microsoft.com>. Attenersi alla procedura di creazione di un account. Al termine della creazione dell'account, è necessario ottenere l'ID e la chiave facendo clic su **Impostazioni**, quindi **Origini connesse** e infine **Server Linux**, come illustrato di seguito.

 ![](media/container-service-monitoring-oms/image5.png)

### <a name="install-the-oms-agent-using-a-daemonset"></a>Installare l'agente OMS usando un oggetto DaemonSet
Gli elementi DaemonSet vengono usati da Kubernetes per eseguire una singola istanza di un contenitore in ogni host del cluster.
Sono ideali per l'esecuzione di agenti di monitoraggio.

Ecco il file [YAML di DaemonSet](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes). Salvarlo in un file denominato `oms-daemonset.yaml` e sostituire i valori segnaposto per `WSID` e `KEY` con l'ID e la chiave dell'area di lavoro disponibili nel file.

Dopo avere aggiunto l'ID e la chiave dell'area di lavoro alla configurazione di DaemonSet, è possibile installare l'agente OMS nel cluster con lo strumento da riga di comando `kubectl`:

```console
$ kubectl create -f oms-daemonset.yaml
```

### <a name="installing-the-oms-agent-using-a-kubernetes-secret"></a>Installazione dell'agente OMS tramite un segreto Kubernetes
Per proteggere l'ID dell'area di lavoro OMS e la chiave è possibile usare il segreto Kubernetes come parte del file YAML di DaemonSet.

 - Copiare lo script, il file modello dei segreti e il file YAML di DaemonSet (dal [repository](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes)) e assicurarsi che siano nella stessa directory. 
      - Script per la generazione di segreti: secret-gen.sh
      - Modello di segreto: secret-template.yaml
   - File DaemonSet YAML: omsagent-ds-secrets.yaml
 - Eseguire lo script. Lo script richiederà l'ID area di lavoro OMS e la chiave primaria. Inserirli e lo script creerà un file yaml del segreto in modo da poterlo eseguire.   
   ```
   #> sudo bash ./secret-gen.sh 
   ```

   - Creare il pod dei segreti eseguendo l'istruzione seguente: ``` kubectl create -f omsagentsecret.yaml ```
 
   - Per verificare, eseguire quanto segue: 

   ``` 
   root@ubuntu16-13db:~# kubectl get secrets
   NAME                  TYPE                                  DATA      AGE
   default-token-gvl91   kubernetes.io/service-account-token   3         50d
   omsagent-secret       Opaque                                2         1d
   root@ubuntu16-13db:~# kubectl describe secrets omsagent-secret
   Name:           omsagent-secret
   Namespace:      default
   Labels:         <none>
   Annotations:    <none>

   Type:   Opaque

   Data
   ====
   WSID:   36 bytes
   KEY:    88 bytes 
   ```
 
  - Creare il DaemonSet dell'agente OMS eseguendo l'istruzione ``` kubectl create -f omsagent-ds-secrets.yaml ```

### <a name="conclusion"></a>Conclusione
L'operazione è terminata. Dopo qualche minuto dovrebbe essere visualizzato il flusso di dati verso il dashboard OMS.
