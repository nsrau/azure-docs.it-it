---
title: Esercitazione sul servizio contenitore di Azure - Distribuire un'applicazione | Microsoft Docs
description: Esercitazione sul servizio contenitore di Azure - Distribuire un'applicazione
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: Docker, contenitori, Micro-Service, Kubernetes, DC/OS, Azure
ms.assetid: 
ms.service: container-service
ms.devlang: aurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/26/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 445f905a2905d049964fb59bd7a5a7826dd1e700
ms.contentlocale: it-it
ms.lasthandoff: 06/28/2017

---

# <a name="run-applications-in-kubernetes"></a>Eseguire applicazioni in Kubernetes

In questa esercitazione verrà distribuita un'applicazione di esempio in un cluster Kubernetes. I passaggi completati comprendono:

> [!div class="checklist"]
> * Introduzione agli oggetti Kubernetes
> * Scaricare i file manifesto Kubernetes
> * Eseguire un'applicazione in Kubernetes
> * Test dell'applicazione

Nelle esercitazioni successive questa applicazione verrà scalata orizzontalmente e aggiornata e verrà monitorato il cluster Kubernetes.

## <a name="before-you-begin"></a>Prima di iniziare

Nelle esercitazioni precedenti è stato creato un pacchetto di un'applicazione in immagini del contenitore, caricate poi nel Registro contenitori di Azure, ed è stato creato un cluster Kubernetes. Se questi passaggi non sono stati ancora eseguiti e si vuole procedere, tornare a [Tutorial 1 – Create container images](./container-service-tutorial-kubernetes-prepare-app.md) (Esercitazione 1: Creare immagini del contenitore). 

Il requisito minimo per questa esercitazione è un cluster Kubernetes.

## <a name="kubernetes-objects"></a>Oggetti Kubernetes

Quando si distribuisce un'applicazione in contenitore in Kubernetes, vengono creati molti oggetti Kubernetes diversi. Ogni oggetto rappresenta lo stato desiderato per il cluster. Ad esempio, un'applicazione semplice può essere costituita da un pod, ovvero un raggruppamento di contenitori strettamente correlati, un volume permanente, ovvero una parte di spazio di archiviazione in rete, e una distribuzione, che gestisce lo stato dell'applicazione. 

Per informazioni dettagliate su tutti gli oggetti Kubernetes, vedere [Kubernetes Concepts](https://kubernetes.io/docs/concepts/) (Nozioni base su Kubernetes ) in kubernetes.io.

## <a name="get-manifest-files"></a>Ottenere i file manifesto

Per questa esercitazione, gli oggetti Kubernetes vengono distribuiti con i file manifesto Kubernetes. Un manifesto Kubernetes è un file YAML contenente le istruzioni sulla configurazione degli oggetti.

I file manifesto per ogni oggetto in questa esercitazione sono disponibili nell'archivio dell'applicazione Azure Vote, che è stato clonato in un'esercitazione precedente. Se questa operazione non è già stata eseguita, clonare l'archivio con il comando seguente: 

```bash
git clone https://github.com/Azure-Samples/azure-voting-app.git
```

I file manifesto si trovano nella seguente directory dell'archivio clonato.

```bash
/azure-voting-app/kubernetes-manifests/
```

## <a name="run-application"></a>Eseguire l'applicazione

### <a name="storage-objects"></a>Oggetti di archiviazione

Poiché l'applicazione Azure Vote include un database MySQL, è possibile archiviare il file di database in un volume che può essere condiviso tra pod. In questa configurazione, se viene ricreato il pod MySQL, il file di database rimane intatto.

Il file manifesto `storage-resources.yaml` crea un [oggetto classe di archiviazione](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#storageclasses), che definisce come e dove viene creato un volume permanente. Per Kubernetes sono disponibili più plug-in per il volume. In questo caso, viene usato il plug-in [Azure disk](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#azure-disk). 

Viene anche creata un'[attestazione di volume permanente](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistentvolumeclaims), che consente di configurare una parte di archiviazione usando una classe di archiviazione e la assegna a un pod.

Eseguire il comando seguente per creare gli oggetti di archiviazione.

```bash
kubectl create -f storage-resources.yaml
```

Al termine un disco virtuale viene creato e collegato al pod Kubernetes risultante. Il disco virtuale viene creato automaticamente in un account di archiviazione presente nello stesso gruppo di risorse del cluster Kubernetes e con la stessa configurazione dell'oggetto di classe di archiviazione (Standard_LRS).

### <a name="secure-sensitive-values"></a>Proteggere i valori sensibili

I [segreti Kubernetes](https://kubernetes.io/docs/concepts/configuration/secret/) assicurano l'archiviazione protetta per le informazioni riservate. Usando il file `pod-secrets.yaml`, le credenziali del database di Azure Vote vengono archiviate in un segreto. 

Eseguire quanto segue per creare gli oggetti segreti.

```bash
kubectl create -f pod-secrets.yaml
```

### <a name="create-deployments"></a>Creare distribuzioni

Una [distribuzione Kubernetes](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) gestisce lo stato dei pod Kubernetes. La gestione include operazioni come garantire che il numero desiderato di repliche sia in esecuzione, che i volumi vengano montati e che vengano usate le immagini del contenitore appropriate.

Il file manifesto `azure-vote-deployment.yaml` crea una distribuzione per le parti front-end e back-end dell'applicazione Azure Vote.

#### <a name="update-image-names"></a>Aggiornare i nomi delle immagini

Se si usa il Registro contenitori di Azure per archiviare le immagini, è necessario che ai nomi delle immagini venga anteposto il nome dei server di accesso del Registro contenitori di Azure.

Ottenere il nome del server di accesso del Registro contenitori di Azure con il comando [az acr list](/cli/azure/acr#list).

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Aggiornare i nomi delle immagini del contenitore *azure-vote-front* e *azure-vote-back* nel file `azure-vote-deployment.yaml`.

Esempio di nome dell'immagine front-end:

```yaml
containers:
      - name: azure-vote-front
        image: <acrLoginServer>/azure-vote-front:v1
```

Esempio di nome dell'immagine back-end:

```yaml
containers:
      - name: azure-vote-back
        image: <acrLoginServer>/azure-vote-front:v1
```

#### <a name="create-deployment-objects"></a>Creare oggetti di distribuzione

Eseguire [kubectl create](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#create) per avviare l'applicazione Azure Vote.

```bash
kubectl create -f azure-vote-deployment.yaml
```

### <a name="expose-application"></a>Esporre l'applicazione

Un [servizio Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/) definisce come accedere a un pod. Con l'app Azure Vote, la distribuzione del back-end deve essere accessibile internamente dal nome della distribuzione. La distribuzione del front-end deve essere accessibile tramite internet. Le configurazioni del servizio app Azure Vote vengono definite nel file di manifesto `services.yaml`.

Eseguire quanto segue per creare i servizi.

```bash
kubectl create -f services.yaml
```

## <a name="test-application"></a>Testare l'applicazione

Dopo avere creato tutti gli oggetti, è possibile accedere all'applicazione tramite l'indirizzo IP esterno per il servizio azure-vote-front. La creazione di questo servizio può richiedere alcuni minuti. Per monitorare il processo di creazione del servizio, eseguire il comando seguente. Quando il valore *EXTERNAL-IP* per il servizio *azure-vote-front* passa da *in sospeso* a un indirizzo IP, l'applicazione è pronta e accessibile all'indirizzo IP esterno.

```bash
kubectl get service -w
```

Output:

```bash
NAME               CLUSTER-IP    EXTERNAL-IP     PORT(S)        AGE
azure-vote-back    10.0.77.30    <none>          3306/TCP       4m
azure-vote-front   10.0.120.96   40.71.227.124   80:31482/TCP   4m
kubernetes         10.0.0.1      <none>          443/TCP        7m
```

Dopo che il servizio è pronto, eseguire `CTRL-C` per terminare l'espressione di controllo kubectl.

Selezionare l'indirizzo IP esterno restituito per visualizzare l'applicazione.

![Immagine del cluster Kubernetes in Azure](media/container-service-kubernetes-tutorials/vote-app-external.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata distribuita un'applicazione Azure Vote in un cluster Kubernetes del servizio contenitore di Azure. Le attività completate comprendono:  

> [!div class="checklist"]
> * Introduzione agli oggetti Kubernetes
> * Scaricare i file manifesto Kubernetes
> * Eseguire un'applicazione in Kubernetes
> * Test dell'applicazione

Passare all'esercitazione successiva per informazioni sulla scalabilità sia di un'applicazione Kubernetes sia dell'infrastruttura Kubernetes sottostante. 

> [!div class="nextstepaction"]
> [Scalare l'applicazione e l'infrastruttura Kubernetes](./container-service-tutorial-kubernetes-scale.md)
