---
title: Introduzione all'API etcd di Azure Cosmos DB
description: Questo articolo fornisce una panoramica e i principali vantaggi dell'API etcd in Azure Cosmos DB
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2020
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: 208f0d73b4c8bb11adc9c73796ebc6a622f5ed50
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85118169"
---
# <a name="introduction-to-the-azure-cosmos-db-etcd-api-preview"></a>Introduzione all'API etcd di Azure Cosmos DB (anteprima)

Azure Cosmos DB è il servizio di database multimodello distribuito a livello globale di Microsoft per applicazioni cruciali. Offre distribuzione globale predefinita, scalabilità elastica in termini di archiviazione e velocità effettiva, latenze pari a singole unità di millisecondi al 99° percentile e disponibilità elevata garantita, il tutto supportato da contratti di servizio leader del settore.

[Etcd](https://github.com/etcd-io/etcd) è un archivio chiave/valore distribuito. In [Kubernetes](https://kubernetes.io/), etcd viene usato per archiviare lo stato e la configurazione dei cluster Kubernetes. Garantire la disponibilità, l'affidabilità e le prestazioni di etcd è fondamentale per l'integrità complessiva del cluster, la scalabilità, la disponibilità, l'elasticità e le prestazioni di un cluster Kubernetes.

L'API etcd in Azure Cosmos DB consente di usare Azure Cosmos DB come archivio back-end per Azure Kubernetes. L'API etcd in Azure Cosmos DB attualmente è disponibile in anteprima. Azure Cosmos DB implementa il protocollo Wire etcd. Con l'API etcd in Azure Cosmos DB, gli sviluppatori otterranno automaticamente cluster Kubernetes altamente affidabili, [disponibili](high-availability.md) e [distribuiti a livello globale](distribute-data-globally.md). Questa API consente agli sviluppatori di ridimensionare la gestione dello stato di Kubernetes in un servizio PaaS nativo del cloud completamente gestito. 

> [!NOTE]
> A differenza di altre API in Azure Cosmos DB, non è possibile effettuare il provisioning di un account API etcd tramite il portale di Azure, l'interfaccia della riga di comando o SDK. È possibile effettuare il provisioning di un account API etcd distribuendo solo il modello di Resource Manager; per i passaggi dettagliati, vedere l'articolo [How to provision Azure Kubernetes with Azure Cosmos DB](bootstrap-kubernetes-cluster.md) (Procedura di provisioning di Azure Kubernetes con Azure Cosmos DB). L'API etcd di Azure Cosmos DB è attualmente disponibile in anteprima limitata. È possibile [iscriversi per l'anteprima](https://aka.ms/cosmosetcdapi-signup) compilando il modulo di iscrizione.

## <a name="wire-level-compatibility"></a>Compatibilità a livello di Wire

Azure Cosmos DB implementa il protocollo Wire etcd versione 3 e consente ai server API del [nodo master](https://kubernetes.io/docs/concepts/overview/components/) di usare Azure Cosmos DB esattamente come in un ambiente etcd installato localmente. L'API etcd supporta l'autenticazione reciproca di TLS. 

Il diagramma seguente illustra i componenti di un cluster Kubernetes. Nel master del cluster, il server API usa l'API etcd di Azure Cosmos DB anziché etcd installato localmente. 

:::image type="content" source="./media/etcd-api-introduction/etcd-api-wire-protocol.png" alt-text="Implementazione del protocollo Wire etcd con Azure Cosmos DB" border="false":::

## <a name="key-benefits"></a>Vantaggi principali

### <a name="no-etcd-operations-management"></a>Nessuna gestione delle operazioni etcd

Come servizio nativo del cloud completamente gestito, Azure Cosmos DB elimina la necessità per gli sviluppatori Kubernetes di configurare e gestire etcd. L'API etcd in Azure Cosmos DB è scalabile, a disponibilità elevata, a tolleranza di errore e offre prestazioni elevate. Il sovraccarico legato alla configurazione della replica tra più nodi ed esecuzione di aggiornamenti in sequenza, le patch di sicurezza e il monitoraggio dell'integrità etcd vengono gestiti da Azure Cosmos DB.

### <a name="global-distribution--high-availability"></a>Distribuzione globale e disponibilità elevata 

Con l'API etcd, Azure Cosmos DB garantisce una disponibilità del 99,99% per letture e scritture di dati in una singola area e del 99,999% tra più aree. 

### <a name="elastic-scalability"></a>Scalabilità elastica

Azure Cosmos DB offre scalabilità elastica per le richieste di lettura e scrittura in aree diverse.
Man mano che il cluster Kubernetes cresce, l'account API etcd in Azure Cosmos DB viene ridimensionato in modo elastico senza tempi di inattività. L'archiviazione di dati etcd in Azure Cosmos DB, invece dei nodi master di Kubernetes, consente anche il ridimensionamento più flessibile del nodo master. 

### <a name="security--enterprise-readiness"></a>Idoneità aziendale e sicurezza

Quando i dati etcd vengono archiviati in Azure Cosmos DB, gli sviluppatori Kubernetes ottengono automaticamente la [crittografia dei dati a riposo](database-encryption-at-rest.md), [le certificazioni e la conformità](compliance.md) predefinite, oltre alle [funzionalità di backup e ripristino](online-backup-and-restore.md) supportate da Azure Cosmos DB. 

## <a name="next-steps"></a>Passaggi successivi

* [Come usare Azure Kubernetes con Azure Cosmos DB](bootstrap-kubernetes-cluster.md)
* [Principali vantaggi di Azure Cosmos DB](introduction.md)
* [Guida introduttiva al motore del servizio Azure Kubernetes](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md)