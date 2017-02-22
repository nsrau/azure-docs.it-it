---
title: Usare Robomongo per MongoDB con Azure DocumentDB | Documentazione Microsoft
description: Informazioni su come usare Robomongo con un account DocumentDB con supporto del protocollo per MongoDB, ora disponibile in anteprima.
keywords: robomongo
services: documentdb
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 352c5fb9-8772-4c5f-87ac-74885e63ecac
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2016
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: a6aadaae2a9400dc62ab277d89d9a9657833b1b7
ms.openlocfilehash: 141154f9e8236e595f77bd4880c4f63d480da445


---
# <a name="use-robomongo-with-a-documentdb-account-with-protocol-support-for-mongodb"></a>Usare Robomongo con un account DocumentDB con supporto del protocollo per MongoDB
Per connettersi a un account Azure DocumentDB con il supporto del protocollo per MongoDB usando Robomongo, seguire questa procedura:

* Scaricare e installare [Robomongo](https://robomongo.org/)
* Creare l'account DocumentDB con supporto del protocollo per le informazioni sulla [stringa di connessione](documentdb-connect-mongodb-account.md) di MongoDB

## <a name="connect-using-robomongo"></a>Connettersi tramite Robomongo
Per aggiungere l'account DocumentDB con supporto del protocollo per MongoDB alle connessioni Robomongo MongoDB, eseguire questa procedura.

1. Recuperare le informazioni su DocumentDB con supporto del protocollo per MongoDB usando [queste](documentdb-connect-mongodb-account.md)istruzioni.

    ![Screenshot del pannello Stringa di connessione](./media/documentdb-mongodb-robomongo/connectionstringblade.png)
2. Eseguire *Robomongo.exe*

3. Fare clic sul pulsante di connessione in **File** per gestire le connessioni. Fare clic su **Create** (Crea) nella finestra **MongoDB Connections** (Connessioni MongoDB) per aprire la finestra **Connection Settings** (Impostazioni di connessione).

4. Nella finestra **Impostazioni di connessione** scegliere un nome. Individuare l'**host** e la **porta** dalle informazioni di connessione nel Passaggio 1 e immettere tali valori nei campi **Address** (Indirizzo) e **Port** (Porta), rispettivamente.

    ![Screenshot della gestione connessioni di Robomongo](./media/documentdb-mongodb-robomongo/manageconnections.png)
5. Nella scheda **Authentication** (Autenticazione) fare clic su **Perform authentication** (Esegui autenticazione). Immettere il valore nel campo **User Name** (Nome utente) (il valore predefinito è *Admin*) e la **password**.
I valori per i campi **User Name** (Nome utente) e **Password** possono essere trovati nelle informazioni di connessione nel Passaggio 1.

    ![Screenshot della scheda di autenticazione Robomongo](./media/documentdb-mongodb-robomongo/authentication.png)
6. Nella scheda **SSL** selezionare **Use SSL protocol** (Usa protocollo SSL), quindi modificare il valore di **Authentication Method** (Metodo di autenticazione) in **Self-signed Certificate** (Certificato autofirmato).

    ![Screenshot della scheda SSL di Robomongo](./media/documentdb-mongodb-robomongo/SSL.png)
7. Infine, fare clic su **Test** per verificare che sia possibile connettersi, quindi su **Save** (Salva).

## <a name="next-steps"></a>Passaggi successivi
* Esplorare DocumentDB con supporto del protocollo per trovare [esempi](documentdb-mongodb-samples.md)di MongoDB.



<!--HONumber=Jan17_HO4-->


