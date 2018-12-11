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
ms.openlocfilehash: a144433e1ff6c0f7ca4e0ed72704d339150f2d56
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2018
ms.locfileid: "52643450"
---
1. In una nuova finestra del browser accedere al [portale di Azure](https://portal.azure.com/).
2. Selezionare **Crea una risorsa** > **Database** > **Azure Cosmos DB**.
   
   ![Riquadro Database nel portale di Azure](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)

3. Nella pagina **Nuovo account** immettere le impostazioni per il nuovo account Azure Cosmos DB. 
 
    Impostazione|Valore|DESCRIZIONE
    ---|---|---
    ID|Immettere un nome univoco|Immettere un nome univoco per identificare l'account Azure Cosmos DB. Poiché all'ID fornito viene aggiunto *documents.azure.com* per creare l'URI, usare un ID univoco.<br><br>L'ID può contenere solo lettere minuscole, numeri e il carattere trattino (-). Deve avere una lunghezza compresa tra 3 e 50 caratteri.
    API|SQL|L'API determina il tipo di account da creare. Azure Cosmos DB mette a disposizione cinque API: SQL per i database di documenti, Gremlin per i database di grafi, MongoDB per i database di documenti, API Tabella e API Cassandra. Attualmente, è necessario creare un account separato per ogni API. <br><br>Selezionare **SQL** poiché in questo articolo si creano un database di documenti e una query usando la sintassi SQL. <br><br>[Altre informazioni sull'API SQL](../articles/cosmos-db/documentdb-introduction.md).|
    Sottoscrizione|Sottoscrizione in uso|Selezionare la sottoscrizione di Azure da usare per l'account Azure Cosmos DB. 
    Gruppo di risorse|Creare un nuovo gruppo di risorse<br><br>Immettere quindi lo stesso nome univoco specificato in ID|Selezionare **Crea nuovo**. Immettere quindi il nome di un nuovo gruppo di risorse per l'account. Per semplicità, usare lo stesso nome usato come ID. 
    Località|Selezionare l'area più vicina agli utenti|Selezionare una posizione geografica in cui ospitare l'account Azure Cosmos DB. Usare la località più vicina agli utenti per offrire loro la massima velocità di accesso ai dati.
    Abilita ridondanza geografica| Lasciare vuoto | Questa opzione consente di creare una versione replicata del database in una seconda area abbinata. Lasciare vuota questa casella di controllo. 

    Selezionare **Create**.

    ![Pagina del nuovo account per Azure Cosmos DB](./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-new-account.png)

4. La creazione dell'account richiede alcuni minuti, Attendere che nel portale venga visualizzata la pagina **Complimenti, l'account Azure Cosmos DB è stato creato**.

    ![Riquadro Notifiche del portale di Azure](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created.png)

