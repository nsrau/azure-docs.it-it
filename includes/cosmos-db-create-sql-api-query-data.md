---
title: File di inclusione
description: File di inclusione
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/05/2019
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 408fb2c40e645d9a8b10f1e04d282e134c5489a1
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80501754"
---
È possibile usare le query in Esplora dati per recuperare e filtrare i dati.

1. Nella parte superiore della scheda **Elementi** di Esplora dati esaminare la query predefinita `SELECT * FROM c`. Questa query recupera e visualizza tutti i documenti presenti nel contenitore ordinati in base all'ID. 
   
   ![La query predefinita in Esplora dati è 'SELECT * FROM c'](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-query.png)
   
1. Per modificare la query, selezionare **Modifica filtro**, sostituire la query predefinita con `ORDER BY c._ts DESC`, quindi selezionare **Applica filtro**.
   
   ![Modificare la query predefinita aggiungendo ORDER BY c._ts DESC e facendo clic su Applica filtro.](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edit-query.png)

   La query modificata elenca i documenti in ordine decrescente in base al timestamp, quindi il secondo documento creato appare ora in cima all'elenco. 
   
   ![Modifica della query in ORDER BY c._ts DESC e selezione di Applica filtro.](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edited-query.png)

Se si ha familiarità con la sintassi SQL, è possibile immettere qualsiasi [query SQL](../articles/cosmos-db/sql-api-sql-query.md) supportata nella casella del predicato della query. È anche possibile usare Esplora dati per creare stored procedure, funzioni definite dall'utente e trigger per eseguire la logica di business sul lato server. 

Esplora dati consente di accedere facilmente dal portale di Azure a tutte le funzionalità di accesso ai dati a livello di codice disponibili nelle API. Si usa il portale anche per ridimensionare la velocità effettiva, ottenere le chiavi e le stringhe di connessione ed esaminare le metriche e i contratti di servizio per l'account Azure Cosmos DB. 

