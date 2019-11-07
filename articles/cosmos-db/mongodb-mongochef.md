---
title: Connettersi all'API di Azure Cosmos DB per MongoDB tramite Studio 3T
description: Informazioni su come connettersi all'API di un Azure Cosmos DB per MongoDB usando Studio 3T.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 11/01/2019
author: sivethe
ms.author: sivethe
ms.custom: seodec18
ms.openlocfilehash: c9be9893d28439fdcff9f33f0303a816bd94c7b8
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73584431"
---
# <a name="connect-to-an-azure-cosmos-account-using-studio-3t"></a>Connettersi a un account Azure Cosmos usando Studio 3T

Per connettersi all'API di un Azure Cosmos DB per MongoDB usando Studio 3T, è necessario:

* Scaricare e installare [Studio 3T](https://studio3t.com/).
* Ottenere le informazioni sulla stringa di [connessione](connect-mongodb-account.md) dell'account Azure Cosmos.

> [!NOTE]
> Attualmente, le versioni di Robo 3T v 1.2 e versioni precedenti sono supportate con l'API di Cosmos DB per MongoDB.

## <a name="create-the-connection-in-studio-3t"></a>Creare la connessione in Studio 3T

Per aggiungere l'account Azure Cosmos alla gestione connessione di studio 3T, seguire questa procedura:

1. Recuperare le informazioni di connessione per l'account dell'API Azure Cosmos DB per MongoDB usando le istruzioni nell'articolo [connettere un'applicazione MongoDB a Azure Cosmos DB](connect-mongodb-account.md) .

    ![Screenshot della pagina della stringa di connessione](./media/mongodb-mongochef/ConnectionStringBlade.png)

2. Fare clic su **Connect** (Connetti) per aprire la gestione connessioni, quindi fare clic su **New Connection** (Nuova connessione)

    ![Screenshot della gestione connessione di studio 3T](./media/mongodb-mongochef/ConnectionManager.png)
3. Nella finestra **nuova connessione** , nella scheda **Server** , immettere l'host (FQDN) dell'account Azure Cosmos e la porta.

    ![Screenshot della scheda server di gestione connessione di studio 3T](./media/mongodb-mongochef/ConnectionManagerServerTab.png)
4. Nella finestra **New Connection** (Nuova connessione) della scheda **Authentication** (Autenticazione) scegliere la modalità di autenticazione **Basic (MONGODB-CR or SCARM-SHA-1)** (Base - MONGODB-CR o SCARM-SHA-1) e immettere il nome utente e la password.  Accettare il database di autenticazione predefinito (admin) o specificare un valore personalizzato.

    ![Screenshot della scheda autenticazione della gestione connessione di studio 3T](./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png)
5. Nella finestra **New Connection** (Nuova connessione) nella scheda **SSL** selezionare la casella di controllo **Use SSL protocol to connect** (Usa protocollo SSL per la connessione) e quindi selezionare il pulsante di opzione **Accept server self-signed SSL certificates** (Accetta certificati SSL autofirmati server).

    ![Screenshot della scheda SSL di gestione connessione di studio 3T](./media/mongodb-mongochef/ConnectionManagerSSLTab.png)
6. Fare clic sul pulsante **Test Connection** (Test connessione) per convalidare le informazioni di connessione, quindi fare clic su **OK** per tornare alla finestra New Connection (Nuova connessione) e infine su **Save** (Salva).

    ![Screenshot della finestra di connessione di test di studio 3T](./media/mongodb-mongochef/TestConnectionResults.png)

## <a name="use-studio-3t-to-create-a-database-collection-and-documents"></a>Usare Studio 3T per creare un database, una raccolta e documenti
Per creare un database, una raccolta e documenti usando Studio 3T, seguire la procedura seguente:

1. In **Connection Manager** (Gestione connessioni) evidenziare la connessione e fare clic su **Connect** (Connetti).

    ![Screenshot della gestione connessione di studio 3T](./media/mongodb-mongochef/ConnectToAccount.png)
2. Fare clic con il pulsante destro del mouse sull'host, quindi scegliere **Add Database** (Aggiungi database).  Specificare un nome per il database e fare clic su **OK**.

    ![Screenshot dell'opzione Add database di studio 3T](./media/mongodb-mongochef/AddDatabase1.png)
3. Fare clic con il pulsante destro del mouse sul database, quindi scegliere **Add Collection** (Aggiungi raccolta).  Specificare un nome per la raccolta, quindi fare clic su **Create**(Crea).

    ![Screenshot dell'opzione di aggiunta della raccolta di studio 3T](./media/mongodb-mongochef/AddCollection.png)
4. Selezionare la voce di menu **Collection** (Raccolta), quindi fare clic su **Add Document** (Aggiungi documento).

    ![Screenshot della voce di menu Aggiungi documento di studio 3T](./media/mongodb-mongochef/AddDocument1.png)
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

    ![Screenshot dei risultati delle query di Mongo chef](./media/mongodb-mongochef/QueryDocument1.png)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [usare Robo 3T](mongodb-robomongo.md) con l'API di Azure Cosmos DB per MongoDB.
- Esplorare gli [esempi](mongodb-samples.md) di MongoDB con l'API di Azure Cosmos DB per MongoDB.
