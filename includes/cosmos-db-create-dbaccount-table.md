---
title: File di inclusione
description: File di inclusione
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/13/2018
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 4fec9be34e390498b85ecfcb3f3b61055a08fdd2
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179838"
---
1. In una nuova finestra del browser accedere al [portale di Azure](https://portal.azure.com/).
2. Nel riquadro di spostamento sinistro selezionare **Crea una risorsa**. Selezionare **Database** e quindi **Azure Cosmos DB**.
   
   ![Screenshot del portale di Azure in cui sono evidenziati Altri servizi e Azure Cosmos DB](./media/cosmos-db-create-dbaccount-table/create-nosql-db-databases-json-tutorial-1.png)

3. Nella pagina **Crea account Azure Cosmos DB** immettere le impostazioni per il nuovo account Azure Cosmos DB:
 
    Impostazione|Valore|DESCRIZIONE
    ---|---|---
    Sottoscrizione|Sottoscrizione in uso|Selezionare la sottoscrizione di Azure da usare per l'account Azure Cosmos DB. 
    Gruppo di risorse|Creare un nuovo gruppo di risorse<br><br>Immettere quindi lo stesso nome univoco specificato in ID|Selezionare **Crea nuovo**. Immettere quindi il nome di un nuovo gruppo di risorse per l'account. Per semplicità, usare lo stesso nome usato come ID. 
    Nome account|Immettere un nome univoco|Immettere un nome univoco per identificare l'account Azure Cosmos DB.<br><br>L'ID può contenere solo lettere minuscole, numeri e il carattere trattino (-). Deve essere composto da un numero di caratteri compreso tra 3 e 31.
    API|tabella di Azure|L'API determina il tipo di account da creare. Azure Cosmos DB offre cinque API: Core (SQL) per i database di documenti, Gremlin per i database a grafo, MongoDB per i database di documenti, Tabella di Azure e Cassandra. Attualmente, è necessario creare un account separato per ogni API. <br><br>Selezionare **Tabella di Azure** perché in questo argomento di avvio rapido si crea una tabella che funziona con l'API Tabella. <br><br>[Altre informazioni sull'API Tabella](../articles/cosmos-db/table-introduction.md).|
    Località|Selezionare l'area più vicina agli utenti|Selezionare una posizione geografica in cui ospitare l'account Azure Cosmos DB. Usare la località più vicina agli utenti per offrire loro la massima velocità di accesso ai dati.

    È possibile lasciare i valori predefiniti **(Disabilita)** per le opzioni **Ridondanza geografica** e **Scritture in più aree** per evitare ulteriori addebiti di UR. È possibile ignorare le sezioni **Rete** e **Tag**.

5. Selezionare **Rivedi+Crea**. Dopo aver completato la convalida, selezionare **Crea** per creare l'account. 
 
   ![Pagina del nuovo account per Azure Cosmos DB](./media/cosmos-db-create-dbaccount-table/azure-cosmos-db-create-new-account.png)

6. La creazione dell'account richiede alcuni minuti. Verrà visualizzato il messaggio **La distribuzione è in corso**. Attendere il completamento della distribuzione e quindi selezionare **Vai alla risorsa**.

    ![Riquadro Notifiche del portale di Azure](./media/cosmos-db-create-dbaccount-table/azure-cosmos-db-account-created.png)
