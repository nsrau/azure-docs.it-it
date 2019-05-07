---
title: Introduzione a etcd di Azure Cosmos DB API
description: Questo articolo offre una panoramica e chiave di vantaggi di etcd API in Azure Cosmos DB
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: 6c7fcb1429438ee024cb226b63cfcdcab05ed9f8
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65205796"
---
# <a name="introduction-to-the-azure-cosmos-db-etcd-api-preview"></a>Introduzione a etcd di Azure Cosmos DB API (anteprima)

Azure Cosmos DB è servizio di database multimodello distribuito a livello globale di Microsoft per applicazioni mission-critical. Offre distribuzione chiavi in mano globale, scalabilità della velocità effettiva e archiviazione, latenze di millisecondi in pochissimi millisecondi al 99 ° percentile, elastica e disponibilità elevata garantita, tutti prodotti coperti dal contratto di servizio leader del settore.

[Etcd](https://github.com/etcd-io/etcd) è un archivio chiave/valore distribuita. Nelle [Kubernetes](https://kubernetes.io/), etcd viene usato per archiviare lo stato e la configurazione dei cluster Kubernetes. Garantire la disponibilità, affidabilità e prestazioni di etcd è fondamentale per l'integrità complessiva del cluster, la scalabilità, elasticità disponibilità e prestazioni di un cluster Kubernetes. 

Etcd API in Azure Cosmos DB consente di usare Azure Cosmos DB come archivio di back-end per [Azure Kubernetes](../aks/index.yml). etcd API in Azure Cosmos DB è attualmente in anteprima. Azure Cosmos DB implementa il protocollo di trasmissione etcd. Con etcd API in Azure Cosmos DB, gli sviluppatori avranno automaticamente altamente affidabili [disponibili](high-availability.md), [distribuito a livello globale](distribute-data-globally.md) Kubernetes. Questa API consente agli sviluppatori di scalare la gestione dello stato di Kubernetes in un servizio PaaS nativo cloud completamente gestito. 

> [!NOTE]
> A differenza di altre API in Azure Cosmos DB, è possibile eseguire il provisioning di un account di API etcd tramite il portale di Azure, SDK o della riga di comando. È possibile eseguire il provisioning di un account di API etcd distribuendo il modello di Resource Manager di sola lettura. Per informazioni dettagliate, vedere [come eseguire il provisioning Azure Kubernetes con Azure Cosmos DB](bootstrap-kubernetes-cluster.md) articolo. Azure Cosmos DB etcd API è attualmente in anteprima limitata. È possibile [iscrizione all'anteprima](https://aka.ms/cosmosetcdapi-signup), completando il modulo di iscrizione.

## <a name="wire-level-compatibility"></a>Compatibilità a livello di rete

Azure Cosmos DB implementa il protocollo di trasmissione etcd versione 3 e consente la [del nodo master](https://kubernetes.io/docs/concepts/overview/components/) Server API per usare Azure Cosmos DB, esattamente come farebbe in un ambiente etcd installata in locale. Etcd API supporta l'autenticazione reciproca TLS. 

Il diagramma seguente vengono illustrati i componenti di un cluster Kubernetes. Nella pagina master del cluster, il API Server Usa API etcd Azure Cosmos DB, anziché etcd installata in locale. 

![Azure Cosmos DB, che implementa il protocollo di trasmissione etcd](./media/etcd-api-introduction/etcd-api-wire-protocol.png)

## <a name="key-benefits"></a>Vantaggi principali

### <a name="no-etcd-operations-management"></a>Nessuna gestione delle operazioni etcd

Come un servizio cloud native completamente gestito, Azure Cosmos DB Elimina la necessità per gli sviluppatori di Kubernetes per configurare e gestire etcd. Etcd API in Azure Cosmos DB è scalabile e altamente disponibile, a tolleranza di errore e offre prestazioni elevate. Il sovraccarico della configurazione di replica tra più nodi, esegue in sequenza gli aggiornamenti, patch di sicurezza, e il monitoraggio dell'integrità etcd vengono gestiti da Azure Cosmos DB.

### <a name="global-distribution--high-availability"></a>Distribuzione globale e disponibilità elevata 

Tramite API etcd, Azure Cosmos DB assicura una disponibilità del 99,99% per le letture dei dati e scrive in una singola area con disponibilità del 99,999% in più aree. 

### <a name="elastic-scalability"></a>Scalabilità elastica

Azure Cosmos DB offre scalabilità elastica per la lettura e richieste di scrittura in aree geografiche diverse.
Man mano che aumenta il cluster Kubernetes, l'account etcd API in Azure Cosmos DB consente di scalare in modo elastico senza tempi di inattività. L'archiviazione dei dati di etcd in Azure Cosmos DB, anziché i nodi master di Kubernetes consente inoltre di ridimensionamento più flessibile nodo master. 

### <a name="security--enterprise-readiness"></a>Conformità di sicurezza ed enterprise

Quando etcd dati vengono archiviati in Azure Cosmos DB, gli sviluppatori di Kubernetes ottengono automaticamente il [incorporato crittografia dei dati inattivi](database-encryption-at-rest.md), [certificazioni e conformità](compliance.md), e [backup e ripristino funzionalità](online-backup-and-restore.md) supportati da Azure Cosmos DB. 

## <a name="next-steps"></a>Passaggi successivi

* [Come usare Azure Kubernetes con Azure Cosmos DB](bootstrap-kubernetes-cluster.md)
* [Vantaggi principali di Azure Cosmos DB](introduction.md)
* [Guida di avvio rapido di Gestione servizio contenitore di AZURE](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md)