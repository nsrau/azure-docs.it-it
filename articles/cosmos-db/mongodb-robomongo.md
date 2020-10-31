---
title: Usare Robo 3T per connettersi ad Azure Cosmos DB
description: Informazioni su come connettersi ad Azure Cosmos DB usando Robo 3T e l'API di Azure Cosmos DB per MongoDB
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/23/2020
author: timsander1
ms.author: tisande
ms.openlocfilehash: e096de5eea9bf5bbd47b976976192e62eb6aa33e
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93096411"
---
# <a name="use-robo-3t-with-azure-cosmos-dbs-api-for-mongodb"></a>Usare Robo 3T con l'API di Azure Cosmos DB per MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Per connettersi all'account Cosmos usando Robo 3T, è necessario:

* Scaricare e installare [Robo 3T](https://robomongo.org/)
* Avere le informazioni relative alla [stringa di connessione](connect-mongodb-account.md) di Cosmos DB

> [!NOTE]
> Attualmente, le versioni di Robo 3T v 1.2 e versioni precedenti sono supportate con l'API di Cosmos DB per MongoDB.

## <a name="connect-using-robo-3t"></a>Connettersi tramite Robo 3T

Per aggiungere l'account Cosmos alla gestione connessione di Robo 3T, seguire questa procedura:

1. Recuperare le informazioni di connessione per l'account Cosmos configurato con l'API di Azure Cosmos DB per MongoDB usando le istruzioni riportate [qui](connect-mongodb-account.md).

    :::image type="content" source="./media/mongodb-robomongo/connectionstringblade.png" alt-text="Screenshot del pannello Stringa di connessione":::
2. Eseguire l'applicazione *Robomongo* .

3. Fare clic sul pulsante di connessione in **File** per gestire le connessioni. Fare clic su **Create** (Crea) nella finestra **MongoDB Connections** (Connessioni MongoDB) per aprire la finestra **Connection Settings** (Impostazioni di connessione).

4. Nella finestra **Impostazioni di connessione** scegliere un nome. Individuare l' **host** e la **porta** dalle informazioni di connessione nel Passaggio 1 e immettere tali valori nei campi **Address** (Indirizzo) e **Port** (Porta), rispettivamente.

    :::image type="content" source="./media/mongodb-robomongo/manageconnections.png" alt-text="Screenshot del pannello Stringa di connessione":::
5. Nella scheda **Authentication** (Autenticazione) fare clic su **Perform authentication** (Esegui autenticazione). Immettere il valore nel campo **User Name** (Nome utente) (il valore predefinito è *Admin* ) e la **password** .
I valori per i campi **User Name** (Nome utente) e **Password** possono essere trovati nelle informazioni di connessione nel Passaggio 1.

    :::image type="content" source="./media/mongodb-robomongo/authentication.png" alt-text="Screenshot del pannello Stringa di connessione":::
6. Nella scheda **SSL** selezionare **Use SSL protocol** (Usa protocollo SSL), quindi modificare il valore di **Authentication Method** (Metodo di autenticazione) in **Self-signed Certificate** (Certificato autofirmato).

    :::image type="content" source="./media/mongodb-robomongo/SSL.png" alt-text="Screenshot del pannello Stringa di connessione":::
7. Infine, fare clic su **Test** per verificare che sia possibile connettersi, quindi su **Save** (Salva).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [usare Studio 3T](mongodb-mongochef.md) con l'API di Azure Cosmos DB per MongoDB.
- Esplorare gli [esempi](mongodb-samples.md) di MongoDB con l'API di Azure Cosmos DB per MongoDB.
