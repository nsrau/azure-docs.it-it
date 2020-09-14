---
title: Informazioni su come spostare un account di Azure Cosmos DB in un'altra area
description: Informazioni su come spostare un account di Azure Cosmos DB in un'altra area
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 09/12/2020
ms.author: mjbrown
ms.openlocfilehash: 60c28a96008355491c058cd08dbbb3a1cbffad98
ms.sourcegitcommit: 94c750edd4d755d6ecee50ac977328098a277479
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/13/2020
ms.locfileid: "90059368"
---
# <a name="how-to-move-an-azure-cosmos-db-account-to-another-region"></a>Come spostare un account di Azure Cosmos DB in un'altra area

Questo articolo descrive come spostare un'area in cui i dati vengono replicati in Azure Cosmos DB o come migrare i metadati dell'account (Azure Resource Manager) e i dati da un'area a un'altra.

## <a name="move-data-from-one-region-to-another"></a>Spostare i dati da un'area a un'altra

Azure Cosmos DB supporta la replica dei dati in modo nativo, quindi lo stato di trasferimento dei dati da un'area a un'altra è semplice e può essere eseguito usando l'interfaccia della riga di comando di portale di Azure, Azure PowerShell o Azure e prevede i passaggi seguenti:

1. Aggiungere una nuova area all'account

    Per aggiungere una nuova area a un account Azure Cosmos DB, vedere [aggiungere o rimuovere aree a un account di Azure Cosmos DB](how-to-manage-database-account.md#addremove-regions-from-your-database-account)

1. Eseguire un failover manuale nella nuova area

    In situazioni in cui l'area da rimuovere è attualmente l'area di scrittura per l'account, sarà necessario avviare un failover alla nuova area aggiunta. Si tratta di un'operazione di tempo di inattività zero. Se si sta migrando un'area di lettura in un account a più aree, è possibile ignorare questo passaggio. Per avviare un failover, vedere [eseguire un failover manuale su un account Azure Cosmos](how-to-manage-database-account.md#manual-failover)

1. Rimuovere l'area originale

    Per rimuovere un'area da un account Azure Cosmos DB, vedere [aggiungere o rimuovere aree a un account di Azure Cosmos DB](how-to-manage-database-account.md#addremove-regions-from-your-database-account)

## <a name="migrate-azure-cosmos-db-account-meta-data"></a>Migrare i metadati dell'account Azure Cosmos DB

Azure Cosmos DB non supporta in modo nativo la migrazione dei metadati dell'account da un'area a un'altra. Per eseguire la migrazione sia dei metadati dell'account che dei dati del cliente da un'area a un'altra, è necessario creare un nuovo account nell'area desiderata, quindi i dati devono essere copiati manualmente. Una migrazione del tempo di inattività quasi zero per l'API SQL richiede l'uso di [offre](change-feed.md) o di uno strumento che lo utilizza. Se si esegue la migrazione dell'API MongoDB, Cassandra o altre API o per altre informazioni sulle opzioni per la migrazione dei dati tra account, vedere [Opzioni per la migrazione dei dati locali o cloud a Azure Cosmos DB](cosmosdb-migrationchoices.md). La procedura seguente illustra come eseguire la migrazione di un account Azure Cosmos DB per l'API SQL e i relativi dati da un'area a un'altra:

1. Creare un nuovo account Azure Cosmos DB nell'area desiderata

    Per creare un nuovo account tramite portale di Azure, PowerShell o l'interfaccia della riga di comando, vedere [creare un account di Azure Cosmos DB](how-to-manage-database-account.md#create-an-account).

1. Creare un nuovo database e un nuovo contenitore

    Per creare un nuovo database e un nuovo contenitore, vedere [creare un contenitore di Azure Cosmos](how-to-create-container.md)

1. Migrare i dati con lo strumento Migrator Live Data di Azure Cosmos DB

    Per eseguire la migrazione dei dati con tempi di inattività quasi nulli, vedere [Azure Cosmos DB Live Data Migrator Tool](https://github.com/Azure-Samples/azure-cosmosdb-live-data-migrator)

1. Aggiornare la stringa di connessione dell'applicazione

    Con lo strumento Migrator attivo ancora in esecuzione, aggiornare le informazioni di connessione nella nuova distribuzione delle applicazioni. È possibile recuperare gli endpoint e le chiavi per l'applicazione dal portale di Azure.

    :::image type="content" source="./media/secure-access-to-data/nosql-database-security-master-key-portal.png" alt-text="Controllo di accesso (IAM) nel portale di Azure: dimostrazione della sicurezza del database NoSQL":::

1. Reindirizza le richieste alla nuova applicazione

    Una volta che la nuova applicazione è connessa a Azure Cosmos DB è possibile reindirizzare le richieste client alla nuova distribuzione.

1. Elimina tutte le risorse non più necessarie

    Con le richieste ora completamente reindirizzate alla nuova istanza, è possibile eliminare l'account Azure Cosmos DB precedente e lo strumento Migrator data live.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni ed esempi su come gestire l'account Azure Cosmos, nonché il database e i contenitori, leggere gli articoli seguenti:

* [Gestire un account Azure Cosmos](how-to-manage-database-account.md)
* [Feed delle modifiche in Azure Cosmos DB](change-feed.md)
