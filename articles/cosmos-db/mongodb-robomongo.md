---
title: Usare Robomongo per Azure Cosmos DB | Microsoft Docs
description: 'Informazioni su come usare Robomongo con un account Azure Cosmos DB: API per MongoDB'
keywords: robomongo
services: cosmos-db
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 352c5fb9-8772-4c5f-87ac-74885e63ecac
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: anhoh
ms.openlocfilehash: 8983594776a1bbe413a6d7cf2cd518f0e327648a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="use-robomongo-with-an-azure-cosmos-db-api-for-mongodb-account"></a>Usare Robomongo con un account Azure Cosmos DB: API per MongoDB
Per connettersi a un account Azure Cosmos DB: API per MongoDB usando Robomongo, seguire questa procedura:

* Scaricare e installare [Robomongo](https://robomongo.org/)
* Verificare che siano disponibili le informazioni sulla [stringa di connessione](connect-mongodb-account.md) dell'account Azure Cosmos DB: API per MongoDB

## <a name="connect-using-robomongo"></a>Connettersi tramite Robomongo
Per aggiungere l'account Azure Cosmos DB: API per MongoDB alle connessioni Robomongo MongoDB, seguire questa procedura.

1. Recuperare le informazioni sulla connessione dell'account Azure Cosmos DB: API per MongoDB usando le istruzioni riportate [qui](connect-mongodb-account.md).

    ![Screenshot del pannello Stringa di connessione](./media/mongodb-robomongo/connectionstringblade.png)
2. Eseguire *Robomongo.exe*

3. Fare clic sul pulsante di connessione in **File** per gestire le connessioni. Fare clic su **Create** (Crea) nella finestra **MongoDB Connections** (Connessioni MongoDB) per aprire la finestra **Connection Settings** (Impostazioni di connessione).

4. Nella finestra **Impostazioni di connessione** scegliere un nome. Individuare l'**host** e la **porta** dalle informazioni di connessione nel Passaggio 1 e immettere tali valori nei campi **Address** (Indirizzo) e **Port** (Porta), rispettivamente.

    ![Screenshot della gestione connessioni di Robomongo](./media/mongodb-robomongo/manageconnections.png)
5. Nella scheda **Authentication** (Autenticazione) fare clic su **Perform authentication** (Esegui autenticazione). Immettere il valore nel campo **User Name** (Nome utente) (il valore predefinito è *Admin*) e la **password**.
I valori per i campi **User Name** (Nome utente) e **Password** possono essere trovati nelle informazioni di connessione nel Passaggio 1.

    ![Screenshot della scheda di autenticazione Robomongo](./media/mongodb-robomongo/authentication.png)
6. Nella scheda **SSL** selezionare **Use SSL protocol** (Usa protocollo SSL), quindi modificare il valore di **Authentication Method** (Metodo di autenticazione) in **Self-signed Certificate** (Certificato autofirmato).

    ![Screenshot della scheda SSL di Robomongo](./media/mongodb-robomongo/SSL.png)
7. Infine, fare clic su **Test** per verificare che sia possibile connettersi, quindi su **Save** (Salva).

## <a name="next-steps"></a>Passaggi successivi
* Esaminare gli [esempi](mongodb-samples.md) di Azure Cosmos DB: API per MongoDB.
