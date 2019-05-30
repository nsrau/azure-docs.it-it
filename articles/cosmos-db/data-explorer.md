---
title: Usare Azure Cosmos DB Explorer per gestire i dati
description: Azure Cosmos DB Explorer è un'interfaccia autonoma basata sul Web che consente di visualizzare e gestire i dati archiviati in Azure Cosmos DB.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dech
ms.openlocfilehash: 04cfdd1f96f83898710b6f292116f0afddc8df96
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237245"
---
# <a name="work-with-data-using-azure-cosmos-explorer"></a>Utilizzare i dati con Azure Cosmos Explorer 

Azure Cosmos DB Explorer è un'interfaccia autonoma basata sul Web che consente di visualizzare e gestire i dati archiviati in Azure Cosmos DB. Azure Cosmos DB Explorer è equivalente alla scheda **Esplora dati** disponibile nel portale di Azure quando si crea un account di Azure Cosmos DB. I vantaggi principali di Azure Cosmos DB Explorer rispetto alla scheda Esplora dati esistente sono i seguenti:

* Disponibilità dello schermo completo per visualizzare dati ed eseguire query, stored procedure e trigger e visualizzarne i risultati.  

* Possibilità di concedere l'accesso temporaneo o permanente in lettura o in lettura/scrittura all'account del database e alle relative raccolte ad altri utenti che non dispongono dell'accesso al portale o alla sottoscrizione di Azure.  

* Possibilità di condividere i risultati delle query con altri utenti che non dispongono dell'accesso al portale o alla sottoscrizione di Azure.  

## <a name="access-azure-cosmos-db-explorer"></a>Accesso ad Azure Cosmos DB Explorer

1. Accedere al [portale di Azure](https://portal.azure.com/). 

2. In **Tutte le risorse** individuare e accedere all'account di Azure Cosmos DB, selezionare Chiavi e copiare il valore del campo **Stringa di connessione primaria**.  

3. Passare a https://cosmos.azure.com/, incollare la stringa di connessione e quindi selezionare **Connetti**. Se si usa la stringa di connessione, è possibile accedere ad Azure Cosmos DB Explorer senza alcun limite di tempo.  

   Se si vuole concedere ad altri utenti l'accesso temporaneo all'account di Azure Cosmos DB, è possibile usare gli URL di accesso in lettura e in lettura/scrittura. 

4. Aprire il pannello **Esplora dati** e selezionare **Apri a schermo intero**. Nella finestra di dialogo popup è possibile visualizzare due URL di accesso, ovvero **Lettura-Scrittura** e **Lettura**. Tali URL consentono di condividere temporaneamente l'account Azure Cosmos DB con altri utenti. L'accesso all'account scade dopo 24 ore. Al termine di questo intervallo è possibile riconnettersi usando un nuovo URL di accesso o la stringa di connessione. 

   **Lettura-Scrittura**: quando si condivide l'URL di lettura/scrittura con altri utenti, questi ultimi possono visualizzare e modificare database, raccolte, query e altre risorse associati all'account specifico.

   **Lettura**: quando si condivide l'URL di sola lettura con altri utenti, è possibile visualizzare database, raccolte, query e altre risorse associati all'account specifico. Se ad esempio si vogliono condividere i risultati di una query con i colleghi del team che non dispongono dell'accesso al portale di Azure o all'account di Azure Cosmos DB, è possibile fornire loro questo URL.

   Scegliere il tipo di accesso da usare per aprire l'account e quindi fare clic su **Apri**. Dopo aver aperto l'interfaccia di esplorazione dei dati, l'esperienza è analoga a quella della scheda Esplora dati nel portale di Azure.   

   ![Aprire Azure Cosmos DB Explorer](./media/data-explorer/open-data-explorer-with-access-url.png)

## <a name="known-issues"></a>Problemi noti

L'opzione **Apri a schermo intero** che consente di condividere l'accesso temporaneo in lettura o in lettura/scrittura non è ancora supportata per gli account dell'API di tabella e Gremlin per Azure Cosmos DB. È comunque possibile visualizzare questi account passando la stringa di connessione ad Azure Cosmos DB Explorer. 

## <a name="next-steps"></a>Passaggi successivi
Ora che si è appreso come iniziare a usare Azure Cosmos DB Explorer per gestire i dati, è possibile eseguire questa operazione:

* Iniziare a definire [query](sql-api-query-reference.md) usando la sintassi SQL ed eseguire la [programmazione sul lato server](stored-procedures-triggers-udfs.md) tramite stored procedure, funzioni definite dall'utente e trigger. 
