---
title: Spostare un account Azure Cosmos DB in un'altra area
description: Informazioni su come spostare un account di Azure Cosmos DB in un'altra area.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 09/12/2020
ms.author: mjbrown
ms.openlocfilehash: 29b5c5d3cf55cd11fe505c0d9ab9b894dc2ad267
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93342029"
---
# <a name="move-an-azure-cosmos-db-account-to-another-region"></a>Spostare un account Azure Cosmos DB in un'altra area
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Questo articolo descrive come eseguire una delle operazioni seguenti:

- Spostare un'area in cui i dati vengono replicati in Azure Cosmos DB.
- Migrare i metadati e i dati dell'account (Azure Resource Manager) da un'area a un'altra.

## <a name="move-data-from-one-region-to-another"></a>Spostare i dati da un'area a un'altra

Azure Cosmos DB supporta la replica dei dati in modo nativo, quindi lo stato di trasferimento dei dati da un'area a un'altra è semplice. Per eseguire questa operazione, è possibile usare l'interfaccia della riga di comando di portale di Azure, Azure PowerShell o Azure. Prevede i passaggi seguenti:

1. Aggiungere una nuova area all'account.

    Per aggiungere una nuova area a un account Azure Cosmos DB, vedere [aggiungere o rimuovere aree a un account di Azure Cosmos DB](how-to-manage-database-account.md#addremove-regions-from-your-database-account).

1. Eseguire un failover manuale nella nuova area.

    Quando l'area da rimuovere è attualmente l'area di scrittura per l'account, è necessario avviare un failover nella nuova area aggiunta nel passaggio precedente. Si tratta di un'operazione di tempo di inattività pari a zero. Se si sta migrando un'area di lettura in un account a più aree, è possibile ignorare questo passaggio. 
    
    Per avviare un failover, vedere [eseguire il failover manuale su un account Azure Cosmos](how-to-manage-database-account.md#manual-failover).

1. Rimuovere l'area originale.

    Per rimuovere un'area da un account Azure Cosmos DB, vedere [aggiungere/rimuovere aree dall'account di Azure Cosmos DB](how-to-manage-database-account.md#addremove-regions-from-your-database-account).

## <a name="migrate-azure-cosmos-db-account-metadata"></a>Migrare i metadati dell'account Azure Cosmos DB

Azure Cosmos DB non supporta in modo nativo la migrazione dei metadati dell'account da un'area a un'altra. Per eseguire la migrazione dei dati del cliente e dei metadati dell'account da un'area a un'altra, è necessario creare un nuovo account nell'area desiderata, quindi copiare i dati manualmente. 

Una migrazione del tempo di inattività quasi zero per l'API SQL richiede l'uso del [feed delle modifiche](change-feed.md) o di uno strumento che lo usa. Se si esegue la migrazione dell'API MongoDB, della API Cassandra o di un'altra API o di altre informazioni sulle opzioni per la migrazione dei dati tra gli account, vedere [Opzioni per eseguire la migrazione dei dati locali o cloud a Azure Cosmos DB](cosmosdb-migrationchoices.md). 

I passaggi seguenti illustrano come eseguire la migrazione di un account Azure Cosmos DB per l'API SQL e i relativi dati da un'area a un'altra:

1. Creare un nuovo account Azure Cosmos DB nell'area desiderata.

    Per creare un nuovo account tramite il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure, vedere [creare un account di Azure Cosmos DB](how-to-manage-database-account.md#create-an-account).

1. Creare un nuovo database e un nuovo contenitore.

    Per creare un nuovo database e un nuovo contenitore, vedere [creare un contenitore di Azure Cosmos](how-to-create-container.md).

1. Eseguire la migrazione dei dati tramite lo strumento di Migrator dati live di Azure Cosmos DB.

    Per eseguire la migrazione dei dati con tempi di inattività quasi nulli, vedere [Azure Cosmos DB lo strumento Migrator Live Data](https://github.com/Azure-Samples/azure-cosmosdb-live-data-migrator).

1. Aggiornare la stringa di connessione dell'applicazione.

    Con lo strumento Migrator Live Data ancora in esecuzione, aggiornare le informazioni di connessione nella nuova distribuzione dell'applicazione. È possibile recuperare gli endpoint e le chiavi per l'applicazione dal portale di Azure.

    :::image type="content" source="./media/secure-access-to-data/nosql-database-security-master-key-portal.png" alt-text="Controllo degli accessi nella portale di Azure, che illustra la sicurezza del database NoSQL.":::

1. Reindirizza le richieste alla nuova applicazione.

    Dopo che la nuova applicazione è stata connessa alla Azure Cosmos DB, è possibile reindirizzare le richieste client alla nuova distribuzione.

1. Eliminare tutte le risorse che non sono più necessarie.

    Con le richieste ora completamente reindirizzate alla nuova istanza, è possibile eliminare l'account Azure Cosmos DB precedente e lo strumento Migrator data live.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni ed esempi su come gestire l'account Azure Cosmos e i database e i contenitori, vedere gli articoli seguenti:

* [Gestire un account Azure Cosmos](how-to-manage-database-account.md)
* [Feed delle modifiche in Azure Cosmos DB](change-feed.md)
