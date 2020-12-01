---
title: Usare Esplora Azure Cosmos DB per gestire i dati
description: Azure Cosmos DB Explorer è un'interfaccia basata sul Web autonoma che consente di visualizzare e gestire i dati archiviati nel Azure Cosmos DB.
author: deborahc
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/23/2020
ms.author: dech
ms.openlocfilehash: d4baa92fe4aa2ed402c394198684c4deec2bf9f1
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348603"
---
# <a name="work-with-data-using-azure-cosmos-db-explorer"></a>Usare i dati con Esplora Azure Cosmos DB 
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB Explorer è un'interfaccia basata sul Web autonoma che consente di visualizzare e gestire i dati archiviati nel Azure Cosmos DB. Azure Cosmos DB Explorer è equivalente alla scheda **Esplora dati** esistente disponibile in portale di Azure quando si crea un account Azure Cosmos DB. I vantaggi principali di Azure Cosmos DB Explorer rispetto a Esplora dati esistenti sono i seguenti:

* Disponibilità dello schermo completo per visualizzare dati ed eseguire query, stored procedure e trigger e visualizzarne i risultati.  

* Possibilità di concedere l'accesso temporaneo o permanente in lettura o in lettura/scrittura all'account del database e alle relative raccolte ad altri utenti che non dispongono dell'accesso al portale o alla sottoscrizione di Azure.  

* Possibilità di condividere i risultati delle query con altri utenti che non dispongono dell'accesso al portale o alla sottoscrizione di Azure.  

## <a name="access-azure-cosmos-db-explorer"></a>Accedere Azure Cosmos DB Explorer

1. Accedere al [portale di Azure](https://portal.azure.com/). 

2. In **Tutte le risorse** individuare e accedere all'account di Azure Cosmos DB, selezionare Chiavi e copiare il valore del campo **Stringa di connessione primaria**.  

3. Passare a https://cosmos.azure.com/, incollare la stringa di connessione e quindi selezionare **Connetti**. Utilizzando la stringa di connessione, è possibile accedere al Azure Cosmos DB Explorer senza limiti di tempo.  

   Se si vuole concedere ad altri utenti l'accesso temporaneo all'account di Azure Cosmos DB, è possibile usare gli URL di accesso in lettura e in lettura/scrittura. 

4. Aprire il pannello **Esplora dati** e selezionare **Apri a schermo intero**. Nella finestra di dialogo popup è possibile visualizzare due URL di accesso, ovvero **Lettura-Scrittura** e **Lettura**. Tali URL consentono di condividere temporaneamente l'account Azure Cosmos DB con altri utenti. L'accesso all'account scade dopo 24 ore. Al termine di questo intervallo è possibile riconnettersi usando un nuovo URL di accesso o la stringa di connessione. 

   **Lettura-Scrittura**: quando si condivide l'URL di lettura/scrittura con altri utenti, questi ultimi possono visualizzare e modificare database, raccolte, query e altre risorse associati all'account specifico.

   **Lettura**: quando si condivide l'URL di sola lettura con altri utenti, è possibile visualizzare database, raccolte, query e altre risorse associati all'account specifico. Se ad esempio si vogliono condividere i risultati di una query con i colleghi del team che non dispongono dell'accesso al portale di Azure o all'account di Azure Cosmos DB, è possibile fornire loro questo URL.

   Scegliere il tipo di accesso da usare per aprire l'account e quindi fare clic su **Apri**. Dopo aver aperto l'interfaccia di esplorazione dei dati, l'esperienza è analoga a quella della scheda Esplora dati nel portale di Azure.

   :::image type="content" source="./media/data-explorer/open-data-explorer-with-access-url.png" alt-text="Aprire l'utilità di esplorazione di Azure Cosmos DB":::

## <a name="known-issues"></a>Problemi noti

L'opzione **Apri a schermo intero** che consente di condividere l'accesso temporaneo in lettura o in lettura/scrittura non è ancora supportata per gli account dell'API di tabella e Gremlin per Azure Cosmos DB. È comunque possibile visualizzare questi account passando la stringa di connessione ad Azure Cosmos DB Explorer. 

Attualmente, la visualizzazione di documenti che contengono un UUID non è supportata in Esplora dati. Ciò non influisce sul caricamento delle raccolte, visualizzando solo i singoli documenti o le query che includono tali documenti. Per visualizzare e gestire questi documenti, gli utenti devono continuare a usare lo strumento originariamente usato per creare i documenti.

I clienti che ricevono errori HTTP-401 possono essere dovuti a autorizzazioni RBAC di Azure insufficienti per l'account Azure del cliente, in particolare se l'account ha un ruolo personalizzato. Qualsiasi ruolo personalizzato deve disporre `Microsoft.DocumentDB/databaseAccounts/listKeys/*` di un'azione da utilizzare Esplora dati se l'accesso viene effettuato utilizzando le credenziali Azure Active Directory.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come iniziare a usare Azure Cosmos DB Explorer per gestire i dati, è possibile eseguire le operazioni seguenti:

* Iniziare a definire [query](./sql-query-getting-started.md) usando la sintassi SQL ed eseguire la [programmazione sul lato server](stored-procedures-triggers-udfs.md) tramite stored procedure, funzioni definite dall'utente e trigger.
