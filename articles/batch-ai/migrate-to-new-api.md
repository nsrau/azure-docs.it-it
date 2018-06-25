---
title: Eseguire la migrazione all'API Azure Batch per intelligenza artificiale | Microsoft Docs
description: Come aggiornare il codice e gli script di Azure Batch per intelligenza artificiale per usare le risorse area di lavoro ed esperimento
services: batch-ai
documentationcenter: na
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 06/08/2018
ms.author: danlep
ms.openlocfilehash: b59173259aa86a429b9f926a8e5ffbfd046451a1
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294878"
---
# <a name="migrate-to-the-updated-batch-ai-api"></a>Eseguire la migrazione all'API Batch per intelligenza artificiale aggiornata

Nell'imminente versione 2018-05-01 dell'API REST Batch per intelligenza artificiale e negli SDK e strumenti di Batch per intelligenza artificiale correlati verranno introdotte nuove funzionalità e modifiche significative.

Se è stata usata una versione precedente dell'API Batch per intelligenza artificiale, questo articolo illustra come modificare il codice e gli script per usare la nuova API. Apportare queste modifiche solo dopo che la nuova API è disponibile.

## <a name="whats-changing"></a>Modifiche introdotte

In risposta ai commenti dei clienti, verranno rimossi i limiti al numero di processi e verrà semplificata la gestione delle risorse di Batch per intelligenza artificiale. La nuova API introduce due nuove risorse, *area di lavoro* ed *esperimento*. Raccogliere i processi di training correlati in un esperimento e organizzare tutte le risorse di Batch per intelligenza artificiale correlate (cluster, file server, esperimenti, processi) in un'area di lavoro.  

* **Area di lavoro**: raccolta di primo livello di tutti i tipi di risorse di Batch per intelligenza artificiale. File server e cluster sono contenuti in un'area di lavoro. Il lavoro appartenente a gruppi o progetti diversi viene in genere separato in aree di lavoro. Ad esempio, potrebbero essere presenti un'area di lavoro per lo sviluppo e una per i test. È probabilmente necessario solo un numero limitato di aree di lavoro per ogni sottoscrizione. 

* **Esperimento**: raccolta di processi correlati che è possibile sottoporre a query e gestire insieme. Ad esempio, usare un esperimento per raggruppare tutti i processi che vengono eseguiti nell'ambito dello sweep di ottimizzazione degli iperparametri. 

Nella figura seguente viene illustrata una gerarchia di risorse di esempio. 

![](./media/migrate-to-new-api/batch-ai-resource-hierarchy.png)

## <a name="monitor-and-manage-existing-resources"></a>Monitorare e gestire le risorse esistenti
In ogni gruppo di risorse in cui sono già stati creati cluster, processi o file server di Batch per intelligenza artificiale, il servizio Batch per intelligenza artificiale creerà un'area di lavoro denominata `migrated-<region>` (ad esempio, `migrated-eastus`) e un esperimento denominato `migrated`. Per accedere ai processi, ai cluster o ai file server creati in precedenza, è necessario usare l'area di lavoro e l'esperimento di cui è stata eseguita la migrazione. 

### <a name="portal"></a>Portale 
Per accedere tramite il portale ai processi, ai cluster o ai file server creati in precedenza, selezionare prima l'area di lavoro `migrated-<region>`. Dopo avere selezionato l'area di lavoro, eseguire operazioni quali il ridimensionamento o l'eliminazione di un cluster e la visualizzazione degli output e dello stato dei processi. 

### <a name="sdks"></a>SDK 
Per accedere tramite gli SDK di Batch per intelligenza artificiale ai processi, ai cluster o ai file server creati in precedenza, fornire i parametri del nome dell'area di lavoro e del nome dell'esperimento. 

Se si usa l'SDK per Python, le modifiche pertinenti sono visualizzate negli esempi seguenti. Le modifiche sono simili negli altri SDK di Batch per intelligenza artificiale. 


#### <a name="get-old-cluster"></a>Ottenere il cluster precedente 

```python
cluster = client.clusters.get(resource_group_name, 'migrated-<region>', cluster_name)
```

#### <a name="delete-old-cluster"></a>Eliminare il cluster precedente 

```python
client.clusters.delete(resource_group_name, 'migrated-<region>', cluster_name)
```

#### <a name="get-old-file-server"></a>Ottenere il file server precedente 

```python
cluster = client.fileservers.get(resource_group_name, 'migrated-<region>', fileserver_name)
```

#### <a name="delete-old-file-server"></a>Eliminare il file server precedente  

```python
client.fileservers.delete(resource_group_name, 'migrated-<region>', fileserver_name)
``` 


#### <a name="get-old-job"></a>Ottenere il processo precedente 

```python
cluster = client.jobs.get(resource_group_name, 'migrated-<region>', 'migrated', job_name)
```

#### <a name="delete-old-job"></a>Eliminare il processo precedente

```python
client.jobs.delete(resource_group_name, 'migrated-<region>', 'migrated', job_name)
```
 
### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure 
 
Quando si usa l'interfaccia della riga di comando di Azure per accedere ai processi, ai cluster o ai file server creati in precedenza, usare i parametri `-w` e `-e` per fornire i nomi dell'area di lavoro e dell'esperimento. 


#### <a name="get-old-cluster"></a>Ottenere il cluster precedente

```azurecli
az batchai cluster show -g resource-group-name -w migrated-<region> -n cluster-name
```


#### <a name="delete-old-cluster"></a>Eliminare il cluster precedente 

```azurecli
az batchai cluster delete -g resource-group-name -w migrated-<region> -n cluster-name
```

#### <a name="get-old-file-server"></a>Ottenere il file server precedente

```azurecli
az batchai file-server show -g resource-group-name -w migrated-<region> -n fileserver-name
```


#### <a name="delete-old-file-server"></a>Eliminare il file server precedente 

```azurecli
az batchai file-server delete -g resource-group-name -w migrated-<region> -n fileserver-name
``` 


#### <a name="get-old-job"></a>Ottenere il processo precedente

```azurecli
az batchai job show -g resource-group-name -w migrated-<region> -e migrated -n job-name
```


#### <a name="delete-old-job"></a>Eliminare il processo precedente 

```azurecli
az batchai job delete -g resource-group-name -w migrated-<region> -e migrated -n job-name
``` 

## <a name="create-batch-ai-resources"></a>Creare le risorse di Batch per intelligenza artificiale 
 
Se si usa uno degli SDK di Batch per intelligenza artificiale esistenti, è possibile continuare a usarlo per creare le risorse di Batch per intelligenza artificiale (processi, cluster o file server) senza apportare modifiche al codice. Tuttavia, dopo l'aggiornamento al nuovo SDK, è necessario apportare le modifiche seguenti.
 
### <a name="create-workspace"></a>Creare un'area di lavoro 
A seconda dello scenario, è possibile creare manualmente un'area di lavoro monouso tramite il portale o l'interfaccia della riga di comando. Se si usa l'interfaccia della riga di comando, creare un'area di lavoro usando il comando seguente: 

```azurecli
az batchai workspace create -g resource-group-name -n workspace-name
```

### <a name="create-experiment"></a>Creare un esperimento 


A seconda dello scenario, è possibile creare manualmente un esperimento monouso tramite il portale o l'interfaccia della riga di comando. Se si usa l'interfaccia della riga di comando, creare un esperimento usando il comando seguente: 

```azurecli
az batchai experiment create -g resource-group-name -w workspace-name -n experiment-name

```

### <a name="create-clusters-file-servers-and-jobs"></a>Creare processi, file server e cluster
 
Se si usa il portale per creare processi, cluster o file server, il portale guiderà l'utente durante l'esperienza di creazione per fornire i parametri del nome dell'area di lavoro e del nome dell'esperimento.

Per creare processi, cluster o file server tramite l'SDK aggiornato, specificare il parametro del nome dell'area di lavoro. Se si usa l'SDK per Python, le modifiche pertinenti sono visualizzate negli esempi seguenti. Sostituire *workspace_name* ed *experiment_name* con l'area di lavoro e l'esperimento creati in precedenza. Le modifiche sono simili negli altri SDK di Batch per intelligenza artificiale. 


#### <a name="create-cluster"></a>Creare cluster 

```python
_ = client.clusters.create(resource_group_name, workspace_name, cluster_name, cluster_create_parameters).result()
```

#### <a name="create-file-server"></a>Crea il file server 

```python
_ = client.fileservers.create(resource_group_name, workspace_name, fileserver_name, fileserver_create_parameters).result()
```

#### <a name="create-job"></a>Creare il processo 

```python
_ = client.jobs.create(resource_group_name, workspace_name, experiment_name, job_name job_create_parameters).result()
```


## <a name="next-steps"></a>Passaggi successivi

* Vedere la documentazione di riferimento sull'API Batch per intelligenza artificiale: [interfaccia della riga di comando](/cli/azure/batchai), [.NET](/dotnet/api/overview/azure/batchai), [Java](/java/api/overview/azure/batchai), [Node.js](/javascript/api/overview/azure/batchai), [Python](/python/api/overview/azure/batchai) e [REST](/rest/api/batchai)