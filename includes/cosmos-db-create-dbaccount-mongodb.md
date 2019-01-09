---
title: File di inclusione
description: File di inclusione
services: cosmos-db
author: rimman
ms.service: cosmos-db
ms.topic: include
ms.date: 12/26/2018
ms.author: rimman
ms.custom: include file
ms.openlocfilehash: b8ade5590ac2edb386d06f63995da8c25c678754
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/27/2018
ms.locfileid: "53796172"
---
1. In una nuova finestra accedere al [portale di Azure](https://portal.azure.com/).
2. Nel menu a sinistra fare clic su **Crea una risorsa**, quindi su **Database** e infine in **Azure Cosmos DB** fare clic su **Crea**.
   
   ![Screenshot del portale di Azure in cui sono evidenziati Altri servizi e Azure Cosmos DB](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-1.png)

3. Nella pagina **Crea account Cosmos DB** immettere le impostazioni per il nuovo account Azure Cosmos DB. 
 
    Impostazione|Valore|DESCRIZIONE
    ---|---|---
    Sottoscrizione|Sottoscrizione in uso|Selezionare la sottoscrizione di Azure da usare per l'account Azure Cosmos DB. 
    Gruppo di risorse|Creare un nuovo gruppo di risorse<br><br>Immettere quindi lo stesso nome univoco specificato in ID|Selezionare **Crea nuovo**. Immettere quindi il nome di un nuovo gruppo di risorse per l'account. Per semplicità, usare lo stesso nome usato come ID. 
    Nome account|Immettere un nome univoco|Immettere un nome univoco per identificare l'account Azure Cosmos DB. Poiché all'ID fornito viene aggiunto *documents.azure.com* per creare l'URI, usare un ID univoco.<br><br>L'ID può contenere solo lettere minuscole, numeri e il carattere trattino (-). Deve avere una lunghezza compresa tra 3 e 31 caratteri.
    API|API Cosmos DB per MongoDB|L'API determina il tipo di account da creare. Azure Cosmos DB offre cinque API: Core (SQL) per i database di documenti, Gremlin per i database a grafo, API Azure Cosmos DB per MongoDB per i database di documenti, Tabella di Azure e Cassandra. Attualmente, è necessario creare un account separato per ogni API. <br><br>Selezionare **MondoDB** poiché in questa guida introduttiva si creerà una tabella che funziona con l'API di MongoDB.|
    Località|Selezionare l'area più vicina agli utenti|Selezionare una posizione geografica in cui ospitare l'account Azure Cosmos DB. Usare la località più vicina agli utenti per offrire loro la massima velocità di accesso ai dati.

    Selezionare **Rivedi+Crea**. È possibile ignorare le sezioni **Rete** e **Tag**. 

    ![Pagina del nuovo account per Azure Cosmos DB](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-create-new-account.png)

4. La creazione dell'account richiede alcuni minuti, Attendere che nel portale venga visualizzata la pagina che indica **Complimenti, L'account Cosmos con compatibilità del protocollo di collegamento per MongoDB è pronto**.

    ![Riquadro Notifiche del portale di Azure](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-account-created.png)
