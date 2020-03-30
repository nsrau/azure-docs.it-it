---
title: 'Problemi noti: Eseguire la migrazione da MongoDB a CosmosDB di AzureKnown issues: Migrate from MongoDB to Azure CosmosDB'
titleSuffix: Azure Database Migration Service
description: Informazioni sui problemi noti e sulle limitazioni della migrazione con le migrazioni da MongoDB a Azure Cosmos DB usando il servizio Migrazione del database di Azure.Learn about known issues and migration limitations with migrations from MongoDB to Azure Cosmos DB using the Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/27/2020
ms.openlocfilehash: 194da036260a78b27748dfc7f755212ab4f30b1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78256021"
---
# <a name="known-issuesmigration-limitations-with-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>Problemi noti/limitazioni di migrazione con le migrazioni da MongoDB all'API di Azure Cosmos DB per MongoDB

I problemi noti e le limitazioni associate alle migrazioni da MongoDB all'API di Cosmos DB per MongoDB sono descritti nelle sezioni seguenti.

## <a name="migration-fails-as-a-result-of-using-the-incorrect-ssl-cert"></a>La migrazione non riesce a causa dell'utilizzo del certificato SSL non corretto

* **Sintomo:** questo problema è evidente quando un utente non può connettersi al server di origine MongoDB. Pur avendo tutte le porte del firewall aperte, l'utente non riesce ancora a connettersi.

| Causa         | Risoluzione |
| ------------- | ------------- |
| L'utilizzo di un certificato autofirmato nel servizio Migrazione del database di Azure può causare l'esito negativo della migrazione a causa del certificato SSL non corretto. Il messaggio di errore può includere "il certificato remoto non è valido in base alla procedura di convalida". | Utilizzare un certificato originale della CA.  I certificati autofirmati vengono generalmente utilizzati solo nei test interni. Quando si installa un certificato autentico da un'autorità CA, è quindi possibile usare SSL nel servizio migrazione del database di Azure senza problemi (le connessioni al database Cosmos usano SSL su Mongo API).<br><br> |

## <a name="unable-to-get-the-list-of-databases-to-map-in-dms"></a>Impossibile ottenere l'elenco dei database di cui eseguire il mapping in DMS

* **Sintomo:** impossibile ottenere l'elenco DB nel pannello **Impostazione database** quando si usano i dati dalla modalità **di archiviazione di Azure** nel pannello Seleziona **origine.**

| Causa         | Risoluzione |
| ------------- | ------------- |
| Nella stringa di connessione dell'account di archiviazione mancano le informazioni di firma di accesso client e pertanto non può essere autenticata. | Creare la chiave di accesso condiviso nel contenitore BLOB in Esplora archivi e usare l'URL con le informazioni sulla chiamata sas del contenitore come stringa di connessione dei dettagli di origine.<br><br> |

## <a name="using-an-unsupported-version-of-the-database"></a>Utilizzo di una versione non supportata del database

* **Sintomo:** la migrazione non riesce.

| Causa         | Risoluzione |
| ------------- | ------------- |
| Si tenta di eseguire la migrazione a Azure Cosmos DB da una versione non supportata di MongoDB. | Man mano che vengono rilasciate le nuove versioni di MongoDB, queste vengono testate per garantire la compatibilità con il servizio di migrazione del database di Azure e il servizio viene aggiornato periodicamente per accettare le versioni più recenti. Se è necessario eseguire la migrazione immediata, come soluzione alternativa è possibile esportare i database/raccolte in Archiviazione di Azure e il punto dell'origine nel dump risultante. Creare la chiave di accesso condiviso nel contenitore BLOB in Esplora archivi e quindi usare l'URL con informazioni sulla chiamata sas del contenitore come stringa di connessione dettagli origine.<br><br> |

## <a name="next-steps"></a>Passaggi successivi

* Visualizzare l'esercitazione [Migrate MongoDB to Azure Cosmos DB's API for MongoDB online using DMS](tutorial-mongodb-cosmos-db-online.md).
* Visualizzare l'esercitazione [Migrate MongoDB to Azure Cosmos DB's API for MongoDB offline using DMS](tutorial-mongodb-cosmos-db.md).