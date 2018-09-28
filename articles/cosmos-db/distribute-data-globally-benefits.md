---
title: Vantaggi chiave della distribuzione globale di Azure Cosmos DB
description: Informazioni sui vantaggi chiave del multimaster di Azure Cosmos DB offerti dalla replica geografica, sul multimaster e sui casi d'uso in cui è utile.
services: cosmos-db
author: markjbrown
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 11d70a648bfc1882753688e5352835b04cee6b9f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46968347"
---
# <a name="distribute-data-globally-with-azure-cosmos-db"></a>Distribuire i dati a livello globale con Azure Cosmos DB

Questo articolo descrive i vantaggi chiave della distribuzione globale dei dati e alcuni scenari in tempo reale in cui è necessaria la distribuzione dei dati globale.

## <a name="key-benefits"></a>Vantaggi principali

I seguenti sono i vantaggi chiave disponibili per gli account che sfruttano le funzionalità di distribuzione globale dei dati di Azure Cosmos DB:

* **Latenza minima**: Azure Cosmos DB offre una latenza in lettura e scrittura inferiore a 10 ms, al 99° percentile, garantita da [contratti di servizio con copertura finanziaria](https://azure.microsoft.com/support/legal/sla/cosmos-db/).

* **Disponibilità del 99,999%**: Azure Cosmos DB offre una disponibilità in lettura e scrittura del 99,999%, garantita da [contratti di servizio con copertura finanziaria](https://azure.microsoft.com/support/legal/sla/cosmos-db/).

* **Risoluzione dei conflitti flessibile**: per i clienti che sfruttano le funzionalità multimaster, Azure Cosmos DB offre tre modalità di gestione dei conflitti che assicurano la coerenza e l'integrità dei dati globali.

* **Coerenza perfezionabile**: Azure Cosmos DB supporta 5 diversi [livelli di coerenza](consistency-levels.md) per la distribuzione globale con supporto completo tranne la coerenza assoluta per gli account con funzionalità multimaster.

* **Tolleranza di errore implicita** : con i dati replicati in più aree, le applicazioni possono usufruire di una tolleranza di errore elevata in caso di interruzioni a livello di area.

## <a name="use-cases"></a>Casi d'uso

Ecco alcuni esempi di scenari che possono sfruttare le funzionalità di distribuzione globale di Azure Cosmos DB.

* **App di social media**: le applicazioni di social media richiedono bassa latenza, disponibilità elevata e scalabilità per offrire un'esperienza ottimale agli utenti di tutto il mondo.

* **IoT** : le distribuzioni periferiche geograficamente distribuite spesso devono tenere traccia dei dati delle serie temporali provenienti da più località. Ogni dispositivo deve essere disponibile nell'area più vicina. Quando i dispositivi passano in località diverse, possono tornare disponibili per la scrittura nella più vicina area disponibile.

* **E-commerce** : l'e-commerce richiede latenza molto bassa e disponibilità elevata. La distribuzione geografica dei dati con operazioni di lettura e scrittura posiziona i dati molto vicino agli utenti, aumentando la reattività delle applicazioni. In questo modo aumenta la disponibilità di operazioni di lettura e scrittura in più aree.

* **Rilevamento di frodi/anomalie** : le applicazioni che monitorano le attività degli utenti o degli account spesso sono distribuite a livello globale e devono tenere traccia di numerosi eventi contemporaneamente per aggiornare i punteggi e poter mantenere le metriche di rischio in linea.

* **Misurazione** : il conteggio e la regolazione dell'utilizzo (ad esempio, chiamate API, transazioni/secondo, minuti usati) possono essere implementati a livello globale con semplicità usando il supporto multimaster di Microsoft Azure Cosmos DB. La risoluzione dei conflitti integrata assicura sia l'accuratezza dei conteggi che la regolazione in tempo reale.

## <a name="next-steps"></a>Passaggi successivi  

In questo articolo sono stati illustrati i vantaggi chiave e i casi d'uso delle funzionalità di distribuzione globale dei dati di Azure Cosmos DB. Esaminare quindi le risorse seguenti:

* [Come Azure Cosmos DB abilita la distribuzione globale chiavi in mano](distribute-data-globally-turnkey.md)

* [Come configurare la replica del database globale di Azure Cosmos DB](tutorial-global-distribution-sql-api.md)

* [How to enable multi-master for Azure Cosmos DB accounts (Come abilitare il multimaster per gli account di Azure Cosmos DB)](enable-multi-master.md)

* [Come funziona il failover automatico e manuale in Azure Cosmos DB](regional-failover.md)

* [Understanding conflict resolution in Azure Cosmos DB (Informazioni sulla risoluzione dei conflitti in Azure Cosmos DB)](multi-master-conflict-resolution.md)

* [Uso del multimaster di Azure Cosmos DB con database NoSQL open source](multi-master-oss-nosql.md)
