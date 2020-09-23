---
title: Disponibilità elevata per i contenitori Edge di Azure SQL-Azure SQL Edge
description: Informazioni sulla disponibilità elevata per i contenitori Edge di Azure SQL
keywords: SQL Edge, contenitori, disponibilità elevata
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 56b9f06547f737bc05d573f98ce1dbac2ba48758
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90937628"
---
# <a name="high-availability-for-azure-sql-edge-containers"></a>Disponibilità elevata per i contenitori Edge di Azure SQL

Creare e gestire le istanze di Azure SQL Edge in modo nativo in Kubernetes. Distribuire Azure SQL Edge nei contenitori Docker gestiti da [Kubernetes](https://kubernetes.io/). In Kubernetes un contenitore con un'istanza di Azure SQL Edge può essere ripristinato automaticamente in caso di errore di un nodo del cluster. È possibile configurare l'immagine del contenitore di SQL Edge con un'attestazione del volume permanente Kubernetes (PVC). Kubernetes monitora il processo Azure SQL Edge nel contenitore. In caso di problemi di un processo, un pod, un contenitore o un nodo, Kubernetes esegue automaticamente il bootstrap di un'altra istanza ed esegue la riconnessione alla risorsa di archiviazione.

## <a name="azure-sql-edge-containers-on-kubernetes"></a>Contenitori Edge di Azure SQL in Kubernetes

Kubernetes 1,6 e versioni successive supportano [*le classi di archiviazione, le*](https://kubernetes.io/docs/concepts/storage/storage-classes/) [*attestazioni di volume permanenti*](https://kubernetes.io/docs/concepts/storage/storage-classes/#persistentvolumeclaims).

In questa configurazione Kubernetes svolge il ruolo di agente di orchestrazione del contenitore. 

![Diagramma di Azure SQL Edge in un cluster Kubernetes](media/deploy-kubernetes/kubernetes-sql-edge.png)

Nel diagramma precedente `azure-sql-edge` è un contenitore in un [pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/). Kubernetes orchestra le risorse nel cluster. Un [set di repliche](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/) garantisce che il pod venga automaticamente ripristinato dopo un errore del nodo. Le applicazioni si connettono al servizio. In questo caso, il servizio rappresenta un servizio di bilanciamento del carico che ospita un indirizzo IP che rimane invariato dopo l'errore di `azure-sql-edge`.

Nel diagramma seguente si è verificato un errore nel contenitore `azure-sql-edge`. Come agente di orchestrazione, Kubernetes garantisce il numero corretto di istanze integre nel set di repliche e avvia un nuovo contenitore in base alla configurazione. L'agente di orchestrazione avvia un nuovo pod nello stesso nodo e `azure-sql-edge` si riconnette alla stessa risorsa di archiviazione permanente. Il servizio si connette al contenitore `azure-sql-edge` ricreato.

![SQL Edge di Azure in un cluster Kubernetes dopo un errore nel pod](media/deploy-kubernetes/kubernetes-sql-edge-after-pod-fail.png)

Nel diagramma seguente si è verificato un errore nel nodo che ospita il contenitore `azure-sql-edge`. L'agente di orchestrazione avvia il nuovo pod in un nodo diverso e `azure-sql-edge` si riconnette alla stessa risorsa di archiviazione permanente. Il servizio si connette al contenitore `azure-sql-edge` ricreato.

![SQL Edge di Azure in un cluster Kubernetes dopo un errore nel nodo](media/deploy-kubernetes/kubernetes-sql-edge-after-node-fail.png).

Per creare un contenitore in Kubernetes, vedere [distribuire un contenitore Edge di Azure SQL in Kubernetes](deploy-Kubernetes.md)

## <a name="next-steps"></a>Passaggi successivi

Per distribuire i contenitori Edge di Azure SQL in Azure Kubernetes Service (AKS), vedere gli articoli seguenti:
- [Distribuire un contenitore Edge di Azure SQL in Kubernetes](deploy-Kubernetes.md)
- [Machine Learning e intelligenza artificiale con ONNX in SQL Edge](onnx-overview.md).
- [Creazione di una soluzione IoT end-to-end con SQL Edge usando IoT Edge](tutorial-deploy-azure-resources.md).
- [Streaming dei dati in SQL Edge di Azure](stream-data.md)