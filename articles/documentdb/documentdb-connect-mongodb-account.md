---
title: Stringa di connessione MongoDB per un account DocumentDB | Documentazione Microsoft
description: Informazioni su come connettere l&quot;app MongoDB a un account Azure DocumentDB usando una stringa di connessione MongoDB.
keywords: stringa di connessione mongodb
services: documentdb
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: e36f7375-9329-403b-afd1-4ab49894f75e
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2016
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: 218d41ac352f01a7e03b5b8a1c45e1bf6d5ace28
ms.openlocfilehash: a3b1289d52207448cebc628466492371d1bf6519


---

# <a name="connect-a-mongodb-app-to-a-documentdb-account-using-a-mongodb-connection-string"></a>Connettere un'app MongoDB a un account DocumentDB usando una stringa di connessione MongoDB
Informazioni su come connettere l'app MongoDB a un account Azure DocumentDB usando una stringa di connessione MongoDB. Connettendo l'app MongoDB a un database Azure DocumentDB, è possibile usare un database DocumentDB come archivio dati per l'app MongoDB. 

Questa esercitazione illustra due modi per recuperare le informazioni della stringa di connessione:

- [Metodo di avvio rapido](#QuickStartConnection), da usare con driver Python, Java, MongoDB Shell, Node.js e .NET.
- [Metodo basato sulla stringa di connessione personalizzata](#GetCustomConnection), da usare con gli altri driver.

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure. Se non si ha un account Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) ora. 
- Un account DocumentDB. Per istruzioni, vedere [Creare un account DocumentDB da usare con le app MongoDB](documentdb-create-mongodb-account.md).

## <a name="a-idquickstartconnectionaget-the-mongodb-connection-string-using-the-quick-start"></a><a id="QuickStartConnection"></a>Ottenere la stringa di connessione MongoDB usando l'avvio rapido
1. In un browser Internet accedere al [portale di Azure](https://portal.azure.com).
2. Nel pannello **NoSQL (DocumentDB)** selezionare l'account DocumentDB con il supporto del protocollo per MongoDB. 
3. Nella barra di **spostamento a sinistra** del pannello Account fare clic su **Avvio rapido**. 
4. Scegliere la piattaforma (*driver .NET*, *driver Node.js*, *MongoDB Shell*, *driver Java*, *driver Python*). Se il driver o lo strumento non è visualizzato nell'elenco, tenere presente che altri frammenti di codice di connessione vengono continuamente documentati. Aggiungere un commento di seguito sull'elemento desiderato e vedere [Ottenere informazioni sulla stringa di connessione dell'account](#GetCustomConnection) per istruzioni su come creare la propria connessione.
5. Copiare e incollare il frammento di codice nell'app MongoDB per iniziare.

    ![Screenshot del pannello Avvio rapido](./media/documentdb-connect-mongodb-account/QuickStartBlade.png)

## <a name="a-idgetcustomconnectiona-get-the-mongodb-connection-string-to-customize"></a><a id="GetCustomConnection"></a> Ottenere la stringa di connessione MongoDB da personalizzare
1. In un browser Internet accedere al [portale di Azure](https://portal.azure.com).
2. Nel pannello **NoSQL (DocumentDB)** selezionare l'account DocumentDB con il supporto del protocollo per MongoDB. 
3. Nella barra di **spostamento a sinistra** del pannello Account fare clic su **Stringa di connessione**. 
4. Verrà visualizzato il pannello **Informazioni sulla stringa di connessione** con tutte le informazioni necessarie per connettersi all'account usando un driver per MongoDB, inclusa una stringa di connessione predefinita.

    ![Screenshot del pannello Stringa di connessione](./media/documentdb-connect-mongodb-account/ConnectionStringBlade.png)

## <a name="connection-string-requirements"></a>Requisiti della stringa di connessione
> [!Important]
> DocumentDB ha standard e requisiti di sicurezza restrittivi. Gli account DocumentDB richiedono l'autenticazione e la comunicazione sicura tramite **SSL**.
>
>

È importante notare che DocumentDB supporta il formato URI della stringa di connessione standard di MongoDB, con un paio di requisiti specifici: gli account DocumentDB richiedono l'autenticazione e la comunicazione sicura con SSL.  Il formato della stringa di connessione è quindi:

    mongodb://username:password@host:port/[database]?ssl=true

Dove i valori di questa stringa sono disponibili nel pannello Stringa di connessione illustrato in precedenza.

* Nome utente (obbligatorio)
  * Nome dell'account DocumentDB
* Password (obbligatorio)
  * Password dell'account DocumentDB
* Host (obbligatorio)
  * FQDN dell'account DocumentDB
* Porta (obbligatorio)
  * 10250
* Database (facoltativo)
  * Database predefinito usato dalla connessione (se non viene specificato alcun database, il database predefinito è "test")
* ssl=true (obbligatorio)

Si consideri ad esempio l'account indicato sopra in Informazioni sulla stringa di connessione.  Una stringa di connessione valida è la seguente:

    mongodb://contoso123:0Fc3IolnL12312asdfawejunASDF@asdfYXX2t8a97kghVcUzcDv98hawelufhawefafnoQRGwNj2nMPL1Y9qsIr9Srdw==@anhohmongo.documents.azure.com:10250/mydatabase?ssl=true

## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come [usare MongoChef](documentdb-mongodb-mongochef.md) con un account DocumentDB con supporto del protocollo per MongoDB.
* Esplorare DocumentDB con supporto del protocollo per trovare [esempi](documentdb-mongodb-samples.md)di MongoDB.



<!--HONumber=Jan17_HO2-->


