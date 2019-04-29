---
title: Connettersi all'API di Azure Cosmos DB per MongoDB tramite Studio 3T
titleSuffix: Azure Cosmos DB
description: Informazioni su come connettersi a Cosmos DB usando Studio 3T e l'API di Azure Cosmos DB per MongoDB
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
origin.date: 12/26/2018
ms.date: 03/18/2019
author: rockboyfor
ms.author: v-yeche
ms.custom: seodec18
ms.openlocfilehash: 9b0a6393d2372c831fdc964dee18acbccfd39a77
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61332050"
---
# <a name="connect-to-cosmos-account-using-studio-3t"></a>Connettersi a un account Cosmos usando Studio 3T

Per connettersi a un account Cosmos usando l'API di Azure Cosmos DB per MongoDB, è necessario:

* Scaricare e installare [Studio 3T](https://studio3t.com/)
* Avere le informazioni relative alla [stringa di connessione](connect-mongodb-account.md) di Cosmos DB

## <a name="create-the-connection-in-studio-3t"></a>Creare la connessione in Studio 3T
Per aggiungere l'account Cosmos alla gestione connessione di Studio 3T, seguire questa procedura:

1. Recuperare le informazioni di connessione per l'account Cosmos configurato con l'API di Azure Cosmos DB per MongoDB usando le istruzioni nell'articolo [Connettere un'applicazione MongoDB ad Azure Cosmos DB](connect-mongodb-account.md).

    ![Screenshot della pagina della stringa di connessione](./media/mongodb-mongochef/ConnectionStringBlade.png)
2. Fare clic su **Connect** (Connetti) per aprire la gestione connessioni, quindi fare clic su **New Connection** (Nuova connessione)

    ![Screenshot della gestione connessione di Studio 3T](./media/mongodb-mongochef/ConnectionManager.png)
3. Nella finestra **New Connection** (Nuova connessione) della scheda **Server**, specificare l'host, ovvero l'FQDN, dell'account Azure Cosmos DB e la porta.

    ![Schermata della scheda server Studio 3T connection manager](./media/mongodb-mongochef/ConnectionManagerServerTab.png)
4. Nella finestra **New Connection** (Nuova connessione) della scheda **Authentication** (Autenticazione) scegliere la modalità di autenticazione **Basic (MONGODB-CR or SCARM-SHA-1)** (Base - MONGODB-CR o SCARM-SHA-1) e immettere il nome utente e la password.  Accettare il database di autenticazione predefinito (admin) o specificare un valore personalizzato.

    ![Schermata della scheda autenticazione Studio 3T connection manager](./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png)
5. Nella finestra **New Connection** (Nuova connessione) nella scheda **SSL** selezionare la casella di controllo **Use SSL protocol to connect** (Usa protocollo SSL per la connessione) e quindi selezionare il pulsante di opzione **Accept server self-signed SSL certificates** (Accetta certificati SSL autofirmati server).

    ![Schermata della scheda SSL Studio 3T connection manager](./media/mongodb-mongochef/ConnectionManagerSSLTab.png)
6. Fare clic sul pulsante **Test Connection** (Test connessione) per convalidare le informazioni di connessione, quindi fare clic su **OK** per tornare alla finestra New Connection (Nuova connessione) e infine su **Save** (Salva).

    ![Finestra di connessione di test di screenshot di Studio 3T](./media/mongodb-mongochef/TestConnectionResults.png)

## <a name="use-studio-3t-to-create-a-database-collection-and-documents"></a>Usare Studio 3T per creare un database, una raccolta e documenti
Per creare un database, una raccolta e documenti usando Studio 3T, seguire la procedura seguente:

1. In **Connection Manager** (Gestione connessioni) evidenziare la connessione e fare clic su **Connect** (Connetti).

    ![Screenshot della gestione connessione di Studio 3T](./media/mongodb-mongochef/ConnectToAccount.png)
2. Fare clic con il pulsante destro del mouse sull'host, quindi scegliere **Add Database** (Aggiungi database).  Specificare un nome per il database e fare clic su **OK**.

    ![Screenshot dell'opzione di Database di aggiunta di Studio 3T](./media/mongodb-mongochef/AddDatabase1.png)
3. Fare clic con il pulsante destro del mouse sul database, quindi scegliere **Add Collection** (Aggiungi raccolta).  Specificare un nome per la raccolta, quindi fare clic su **Create**(Crea).

    ![Screenshot dell'opzione di Studio 3T Aggiungi raccolta](./media/mongodb-mongochef/AddCollection.png)
4. Selezionare la voce di menu **Collection** (Raccolta), quindi fare clic su **Add Document** (Aggiungi documento).

    ![Screenshot della voce di menu Aggiungi documento Studio 3T](./media/mongodb-mongochef/AddDocument1.png)
5. Nella finestra di dialogo Add Document (Aggiungi documento) incollare quanto segue, quindi fare clic su **Add Document**(Aggiungi documento).

        {
        "_id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
               { "firstName": "Thomas" },
               { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "isRegistered": true
        }
6. Aggiungere un altro documento, questa volta con il contenuto seguente:

        {
        "_id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam",
                 "givenName": "Jesse",
                "gender": "female", "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            {
                "familyName": "Miller",
                 "givenName": "Lisa",
                 "gender": "female",
                 "grade": 8 }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
        }
7. Eseguire una query di esempio. Cercare ad esempio famiglie con cognome 'Andersen' e restituire i campi relativi a genitori e stato.

    ![Risultati della query di schermata di Mongochef](./media/mongodb-mongochef/QueryDocument1.png)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [usare Robo 3T](mongodb-robomongo.md) con l'API di Azure Cosmos DB per MongoDB.
- Esplorare gli [esempi](mongodb-samples.md) di MongoDB con l'API di Azure Cosmos DB per MongoDB.

<!-- Update_Description: update meta properties, wording update -->