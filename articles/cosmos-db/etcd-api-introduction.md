---
title: Introduzione all'API Azure Cosmos DB eccdIntroduction to the Azure Cosmos DB etcd API
description: Questo articolo offre una panoramica e i vantaggi principali dell'API etcd in Azure Cosmos DB
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: acd87fac5ec2edc40d27d98f073e13c0acae8d8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79498586"
---
# <a name="introduction-to-the-azure-cosmos-db-etcd-api-preview"></a>Introduzione all'API Azure Cosmos DB etcd (anteprima)Introduction to the Azure Cosmos DB etcd API (preview)

Azure Cosmos DB è il servizio di database multimodello distribuito a livello globale di Microsoft per applicazioni cruciali. Offre una distribuzione globale chiavi in verso il basso contenuto, la scalabilità elastica della velocità effettiva e dell'archiviazione, latenze di millisecondi a una cifra al 99esimo percentile e una disponibilità elevata garantita, il tutto supportato da contratti di servizio leader del settore.

[Etcd](https://github.com/etcd-io/etcd) è un archivio di chiavi/valori distribuito. In [Kubernetes](https://kubernetes.io/), etcd viene utilizzato per memorizzare lo stato e la configurazione dei cluster Kubernetes. Garantire la disponibilità, l'affidabilità e le prestazioni di etcd è fondamentale per l'integrità complessiva del cluster, la scalabilità, la disponibilità di elasticità e le prestazioni di un cluster Kubernetes. 

L'API etcd in Azure Cosmos DB consente di usare Azure Cosmos DB come archivio back-end per [Azure Kubernetes.](../aks/index.yml) L'API etcd in Azure Cosmos DB attualmente è disponibile in anteprima. Azure Cosmos DB implementa il protocollo wire etcd. Con l'API etcd in Azure Cosmos DB, gli sviluppatori otterranno automaticamente Kubernetes altamente affidabili, [disponibili](high-availability.md)e distribuiti a [livello globale.](distribute-data-globally.md) Questa API consente agli sviluppatori di scalare la gestione dello stato di Kubernetes su un servizio PaaS nativo cloud completamente gestito. 

> [!NOTE]
> A differenza di altre API in Azure Cosmos DB, non è possibile eseguire il provisioning di un account API etcd tramite il portale di Azure, l'interfaccia della riga di comando o gli SDK. È possibile eseguire il provisioning di un account API etcd distribuendo solo il modello Resource Manager. Per la procedura dettagliata, vedere l'articolo Come eseguire il provisioning di [Azure Kubernetes con Azure Cosmos DB.](bootstrap-kubernetes-cluster.md) L'API Azure Cosmos DB eccd è attualmente in anteprima limitata. Puoi [iscriverti all'anteprima](https://aka.ms/cosmosetcdapi-signup)compilando il modulo di iscrizione.

## <a name="wire-level-compatibility"></a>Compatibilità a livello di filo

Azure Cosmos DB implementa il protocollo wire di eccd versione 3 e consente ai server API [del nodo master](https://kubernetes.io/docs/concepts/overview/components/) di usare Azure Cosmos DB come in un ambiente etcd installato localmente. L'API etcd supporta l'autenticazione reciproca TLS. 

Il diagramma seguente mostra i componenti di un cluster Kubernetes. Nel master cluster, il server API usa l'API Cosmos DB di Azure e così via, anziché l'API e così via installata localmente. 

![Azure Cosmos DB che implementa il protocollo wire etcdAzure Cosmos DB implementing the etcd wire-protocol](./media/etcd-api-introduction/etcd-api-wire-protocol.png)

## <a name="key-benefits"></a>Vantaggi principali

### <a name="no-etcd-operations-management"></a>Gestione delle operazioni senza eccd

Come servizio cloud nativo completamente gestito, Azure Cosmos DB elimina la necessità per gli sviluppatori di Kubernetes di configurare e gestire eccd. L'API etcd in Azure Cosmos DB è scalabile, a disponibilità elevata, a tolleranza di errore e offre prestazioni elevate. L'overhead della configurazione della replica su più nodi, l'esecuzione di aggiornamenti in sequenza, le patch di sicurezza e il monitoraggio dell'integrità e così via sono gestiti da Azure Cosmos DB.

### <a name="global-distribution--high-availability"></a>Distribuzione globale & disponibilità elevata 

Usando l'API etcd, Azure Cosmos DB garantisce una disponibilità del 99,99% per le letture e le scritture dei dati in un'unica area e il 99,999% di disponibilità in più aree. 

### <a name="elastic-scalability"></a>Scalabilità elastica

Azure Cosmos DB offre scalabilità elastica per le richieste di lettura e scrittura in diverse aree.
Con l'aumento dell'aumento del cluster Kubernetes, l'account API etcd in Azure Cosmos DB viene ridimensionato in modo elastico senza tempi di inattività. L'archiviazione di dati etcd in Azure Cosmos DB, anziché nei nodi master Kubernetes, consente anche una scalabilità più flessibile dei nodi master. 

### <a name="security--enterprise-readiness"></a>Sicurezza & la prontezza aziendale

Quando i dati etcd vengono archiviati in Azure Cosmos DB, gli sviluppatori Kubernetes ottengono automaticamente la [crittografia incorporata inattivi,](database-encryption-at-rest.md) [le certificazioni e la conformità](compliance.md)e le funzionalità di backup e [ripristino](../synapse-analytics/sql-data-warehouse/backup-and-restore.md) supportate da Azure Cosmos DB. 

## <a name="next-steps"></a>Passaggi successivi

* [Come usare Azure Kubernetes con Azure Cosmos DB](bootstrap-kubernetes-cluster.md)
* [Vantaggi principali di Azure Cosmos DB](introduction.md)
* [Guida introduttiva al motore AKS](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md)