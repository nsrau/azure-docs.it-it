---
title: Usare Studio 3T per connettersi all'API di Azure Cosmos DB per MongoDBUse Studio 3T to connect to Azure Cosmos DB's API for MongoDB
description: Informazioni su come connettersi all'API di un database cosmos di Azure per MongoDB usando Studio 3T.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/20/2020
author: timsander1
ms.author: tisande
ms.custom: seodec18
ms.openlocfilehash: 84b703cceeb130b177b8ab32281ef616b1ec632b
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548828"
---
# <a name="connect-to-an-azure-cosmos-account-using-studio-3t"></a>Connettersi a un account Cosmos di Azure usando Studio 3TConnect to an Azure Cosmos account using Studio 3T

Per connettersi all'API di un database Cosmos di Azure per MongoDB usando Studio 3T, è necessario:To connect to an Azure Cosmos DB's API for MongoDB using Studio 3T, you must:

* Scaricare e installare [Studio 3T](https://studio3t.com/).
* Disporre delle informazioni sulla [stringa](connect-mongodb-account.md) di connessione dell'account Azure Cosmos.Have your Azure Cosmos account's connection string information.

## <a name="create-the-connection-in-studio-3t"></a>Creare la connessione in Studio 3T

Per aggiungere l'account Azure Cosmos alla gestione connessione di Studio 3T, eseguire la procedura seguente:To add your Azure Cosmos account to the Studio 3T connection manager, use the following steps:

1. Recuperare le informazioni di connessione per l'API di Azure Cosmos DB per l'account MongoDB usando le istruzioni nell'articolo [Connettere un'applicazione MongoDB al](connect-mongodb-account.md) database Cosmos di Azure.Retrieve the connection information for your Azure Cosmos DB's API for MongoDB account using the instructions in the Connect a MongoDB application to Azure Cosmos DB article.

    ![Screenshot della pagina della stringa di connessione](./media/mongodb-mongochef/ConnectionStringBlade.png)

2. Fare clic su **Connect** (Connetti) per aprire la gestione connessioni, quindi fare clic su **New Connection** (Nuova connessione)

    ![Screenshot della gestione connessione di Studio 3T](./media/mongodb-mongochef/ConnectionManager.png)
3. Nella scheda **Server** della finestra **Nuova connessione** immettere l'HOST (FQDN) dell'account Azure Cosmos e la porta.

    ![Screenshot della scheda del server di gestione connessione di Studio 3T](./media/mongodb-mongochef/ConnectionManagerServerTab.png)
4. Nella finestra **New Connection** (Nuova connessione) della scheda **Authentication** (Autenticazione) scegliere la modalità di autenticazione **Basic (MONGODB-CR or SCARM-SHA-1)** (Base - MONGODB-CR o SCARM-SHA-1) e immettere il nome utente e la password.  Accettare il database di autenticazione predefinito (admin) o specificare un valore personalizzato.

    ![Screenshot della scheda di autenticazione della gestione connessione di Studio 3T](./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png)
5. Nella finestra **New Connection** (Nuova connessione) nella scheda **SSL** selezionare la casella di controllo **Use SSL protocol to connect** (Usa protocollo SSL per la connessione) e quindi selezionare il pulsante di opzione **Accept server self-signed SSL certificates** (Accetta certificati SSL autofirmati server).

    ![Screenshot della scheda SSL di Gestione connessione di Studio 3T](./media/mongodb-mongochef/ConnectionManagerSSLTab.png)
6. Fare clic sul pulsante **Test Connection** (Test connessione) per convalidare le informazioni di connessione, quindi fare clic su **OK** per tornare alla finestra New Connection (Nuova connessione) e infine su **Save** (Salva).

    ![Screenshot della finestra di connessione di prova di Studio 3T](./media/mongodb-mongochef/TestConnectionResults.png)

## <a name="use-studio-3t-to-create-a-database-collection-and-documents"></a>Usare Studio 3T per creare un database, una raccolta e documenti
Per creare un database, una raccolta e documenti usando Studio 3T, seguire la procedura seguente:

1. In **Connection Manager** (Gestione connessioni) evidenziare la connessione e fare clic su **Connect** (Connetti).

    ![Screenshot della gestione connessione di Studio 3T](./media/mongodb-mongochef/ConnectToAccount.png)
2. Fare clic con il pulsante destro del mouse sull'host, quindi scegliere **Add Database** (Aggiungi database).  Specificare un nome per il database e fare clic su **OK**.

    ![Screenshot dell'opzione Aggiungi database di Studio 3T](./media/mongodb-mongochef/AddDatabase1.png)
3. Fare clic con il pulsante destro del mouse sul database, quindi scegliere **Add Collection** (Aggiungi raccolta).  Specificare un nome per la raccolta, quindi fare clic su **Create**(Crea).

    ![Screenshot dell'opzione Aggiungi raccolta di Studio 3T](./media/mongodb-mongochef/AddCollection.png)
4. Selezionare la voce di menu **Collection** (Raccolta), quindi fare clic su **Add Document** (Aggiungi documento).

    ![Screenshot della voce di menu Aggiungi documento di Studio 3T](./media/mongodb-mongochef/AddDocument1.png)
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

    ![Screenshot dei risultati della query Mongo Chef](./media/mongodb-mongochef/QueryDocument1.png)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [usare Robo 3T](mongodb-robomongo.md) con l'API di Azure Cosmos DB per MongoDB.
- Esplorare gli [esempi](mongodb-samples.md) di MongoDB con l'API di Azure Cosmos DB per MongoDB.
