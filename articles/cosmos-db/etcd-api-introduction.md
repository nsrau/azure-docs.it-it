---
title: Introduzione all'API Azure Cosmos DB ETCD
description: Questo articolo fornisce una panoramica e i principali vantaggi dell'API ETCD in Azure Cosmos DB
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: 6c7fcb1429438ee024cb226b63cfcdcab05ed9f8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79498586"
---
# <a name="introduction-to-the-azure-cosmos-db-etcd-api-preview"></a>Introduzione all'API Azure Cosmos DB ETCD (anteprima)

Azure Cosmos DB è il servizio di database multimodello distribuito a livello globale di Microsoft per applicazioni cruciali. Offre una distribuzione globale chiavi in mano, scalabilità elastica della velocità effettiva e archiviazione, latenze di millisecondi a una sola cifra al 99 ° percentile e disponibilità elevata garantita, il tutto supportato da contratti di contratto leader del settore.

[ETCD](https://github.com/etcd-io/etcd) è un archivio chiave/valore distribuito. In [Kubernetes](https://kubernetes.io/)viene usato ETCD per archiviare lo stato e la configurazione dei cluster Kubernetes. Garantire la disponibilità, l'affidabilità e le prestazioni di ETCD sono fondamentali per l'integrità complessiva del cluster, la scalabilità, la disponibilità dell'elasticità e le prestazioni di un cluster Kubernetes. 

L'API ETCD in Azure Cosmos DB consente di usare Azure Cosmos DB come archivio back-end per [Azure Kubernetes](../aks/index.yml). L'API etcd in Azure Cosmos DB attualmente è disponibile in anteprima. Azure Cosmos DB implementa il protocollo wire ETCD. Con l'API ETCD in Azure Cosmos DB, gli sviluppatori otterranno automaticamente Kubernetes altamente affidabili, [disponibili](high-availability.md)e [distribuiti a livello globale](distribute-data-globally.md) . Questa API consente agli sviluppatori di ridimensionare la gestione dello stato di Kubernetes in un servizio PaaS nativo cloud completamente gestito. 

> [!NOTE]
> A differenza di altre API in Azure Cosmos DB, non è possibile effettuare il provisioning di un account API ETCD tramite portale di Azure, CLI o SDK. È possibile effettuare il provisioning di un account API ETCD distribuendo solo il modello di Gestione risorse; per i passaggi dettagliati, vedere [come eseguire il provisioning di Azure Kubernetes con Azure Cosmos DB](bootstrap-kubernetes-cluster.md) articolo. Azure Cosmos DB API ETCD è attualmente disponibile in anteprima limitata. È possibile [iscriversi per l'anteprima](https://aka.ms/cosmosetcdapi-signup), compilando il modulo di iscrizione.

## <a name="wire-level-compatibility"></a>Compatibilità a livello di Wire

Azure Cosmos DB implementa il protocollo di trasmissione di ETCD versione 3 e consente ai server API del [nodo master](https://kubernetes.io/docs/concepts/overview/components/) di usare Azure Cosmos DB esattamente come in un ambiente ETCD installato localmente. L'API ETCD supporta l'autenticazione reciproca TLS. 

Il diagramma seguente mostra i componenti di un cluster Kubernetes. Nel master del cluster il server API USA Azure Cosmos DB API ETCD anziché ETCD installato localmente. 

![Azure Cosmos DB implementazione del protocollo wire-ETCD](./media/etcd-api-introduction/etcd-api-wire-protocol.png)

## <a name="key-benefits"></a>Vantaggi principali

### <a name="no-etcd-operations-management"></a>Nessuna gestione delle operazioni ETCD

Come servizio cloud nativo completamente gestito, Azure Cosmos DB Elimina la necessità per gli sviluppatori Kubernetes di configurare e gestire ETCD. L'API ETCD in Azure Cosmos DB è scalabile, a disponibilità elevata, a tolleranza di errore e offre prestazioni elevate. Il sovraccarico della configurazione della replica tra più nodi, l'esecuzione di aggiornamenti in sequenza, le patch di sicurezza e il monitoraggio dell'integrità ETCD vengono gestiti da Azure Cosmos DB.

### <a name="global-distribution--high-availability"></a>Distribuzione globale & disponibilità elevata 

Con l'API ETCD, Azure Cosmos DB garantisce una disponibilità del 99,99% per letture e scritture di dati in una singola area e disponibilità del 99,999% tra più aree. 

### <a name="elastic-scalability"></a>Scalabilità elastica

Azure Cosmos DB offre scalabilità elastica per le richieste di lettura e scrittura in aree diverse.
Man mano che aumenta il cluster Kubernetes, l'account API ETCD in Azure Cosmos DB scalabilità elastica senza tempi di inattività. L'archiviazione dei dati di ETCD in Azure Cosmos DB, invece dei nodi master di Kubernetes, consente anche il ridimensionamento più flessibile del nodo master. 

### <a name="security--enterprise-readiness"></a>Sicurezza & conformità aziendale

Quando i dati di ETCD vengono archiviati in Azure Cosmos DB, gli sviluppatori Kubernetes ottengono automaticamente le funzionalità [predefinite di crittografia](database-encryption-at-rest.md)dei dati inattivi, [certificazioni e conformità](compliance.md)e [funzionalità di backup e ripristino](online-backup-and-restore.md) supportate da Azure Cosmos DB. 

## <a name="next-steps"></a>Passaggi successivi

* [Come usare Azure Kubernetes con Azure Cosmos DB](bootstrap-kubernetes-cluster.md)
* [Vantaggi principali di Azure Cosmos DB](introduction.md)
* [Guida introduttiva al motore AKS](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md)