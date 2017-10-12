---
title: Esportare i certificati dell'emulatore di Azure Cosmos DB | Microsoft Docs
description: "Quando si effettua lo sviluppo in linguaggi e runtime che non usano l'archivio certificati Windows, sarà necessario esportare e gestire i certificati SSL. Questo post contiene istruzioni dettagliate."
services: cosmos-db
documentationcenter: 
keywords: Emulatore di Azure Cosmos DB
author: voellm
manager: jhubbard
editor: 
ms.assetid: ef43deda-c2e9-4193-99e2-7f6a88a0319f
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2017
ms.author: tvoellm
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bafbd43d65a0f46f87a13cea4a2a6299d3bf43ef
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="export-the-azure-cosmos-db-emulator-certificates-for-use-with-java-python-and-nodejs"></a>Esportare i certificati dell'emulatore di Azure Cosmos DB per l'uso con Java, Python e Node.js

[**Scaricare l'emulatore**](https://aka.ms/cosmosdb-emulator)

L'emulatore di Azure Cosmos DB fornisce un ambiente locale che emula il servizio Azure Cosmos DB per obiettivi di sviluppo, incluso l'uso di connessioni SSL. Questo post illustra come esportare i certificati SSL da usare in linguaggi e runtime non integrati con l'archivio certificati Windows, ad esempio Java che usa il proprio [archivio certificati](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html), Python che usa [wrapper per socket](https://docs.python.org/2/library/ssl.html) e Node.js che usa [tlsSocket](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback). Per altre informazioni sull'emulatore, vedere [Usare l'emulatore di Azure Cosmos DB per sviluppo e test](./local-emulator.md).

Questa esercitazione illustra le attività seguenti:

> [!div class="checklist"]
> * Rotazione dei certificati
> * Esportazione di un certificato SSL
> * Come usare il certificato in Java, Python e Node.js

## <a name="certification-rotation"></a>Rotazione della certificazione

I certificati nell'emulatore locale di Azure Cosmos DB vengono generati la prima volta che l'emulatore viene eseguito. Esistono due certificati, uno usato per la connessione all'emulatore locale e l'altro per la gestione dei segreti nell'emulatore. Il certificato che si vuole esportare è il certificato per la connessione con il nome descrittivo "DocumentDBEmulatorCertificate".

Entrambi i certificati possono essere rigenerati facendo clic su **Reset Data** (Reimposta dati), come illustrato di seguito, dall'emulatore di Azure Cosmos DB in esecuzione nell'area di notifica di Windows. Se si rigenerano i certificati e questi sono stati installati nell'archivio certificati di Java o usati altrove, sarà necessario aggiornarli. In caso contrario l'applicazione non si connetterà più all'emulatore locale.

![Reimpostazione dei dati nell'emulatore locale di Azure Cosmos DB](./media/local-emulator-export-ssl-certificates/database-local-emulator-reset-data.png)

## <a name="how-to-export-the-azure-cosmos-db-ssl-certificate"></a>Come esportare il certificato SSL dell'emulatore di Azure Cosmos DB

1. Avviare Gestione certificati di Windows eseguendo certlm.msc, passare alla cartella Personale->Certificati e aprire il certificato con il nome descrittivo **DocumentDbEmulatorCertificate**.

    ![Passaggio 1 dell'esportazione nell'emulatore locale di Azure Cosmos DB](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-1.png)

2. Fare clic su **Details** (Dettagli) quindi su **OK**.

    ![Passaggio 2 dell'esportazione nell'emulatore locale di Azure Cosmos DB](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-2.png)

3. Fare clic su **Copy to File...** (Copia in file).

    ![Passaggio 3 dell'esportazione nell'emulatore locale di Azure Cosmos DB](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-3.png)

4. Fare clic su **Avanti**.

    ![Passaggio 4 dell'esportazione nell'emulatore locale di Azure Cosmos DB](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-4.png)

5. Fare clic su **No, do not export private key** (No, non esportare la chiave privata) e quindi su **Next** (Avanti).

    ![Passaggio 5 dell'esportazione nell'emulatore locale di Azure Cosmos DB](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-5.png)

6. Fare clic su **Base-64 encoded X.509 (.CER)** (Codificato in base 64 X.509 (.CER)) e quindi su **Next** (Avanti).

    ![Passaggio 6 dell'esportazione nell'emulatore locale di Azure Cosmos DB](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-6.png)

7. Assegnare un nome al certificato. In questo caso, **documentdbemulatorcert**, quindi fare clic su **Next** (Avanti).

    ![Passaggio 7 dell'esportazione nell'emulatore locale di Azure Cosmos DB](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-7.png)

8. Fare clic su **Finish** (Fine).

    ![Passaggio 8 dell'esportazione nell'emulatore locale di Azure Cosmos DB](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-8.png)

## <a name="how-to-use-the-certificate-in-java"></a>Come usare il certificato in Java

Quando si eseguono applicazioni Java o applicazioni MongoDB che usano il client Java, è più facile installare il certificato nell'archivio certificati predefinito di Java che passare i flag "-Djavax.net.ssl.trustStore=<keystore> -Djavax.net.ssl.trustStorePassword="<password>". Ad esempio, l'[applicazione demo Java](https://localhost:8081/_explorer/index.html) inclusa dipende dall'archivio certificati predefinito.

Seguire le istruzioni indicate in [Aggiunta di un certificato all'archivio certificati CA Java](https://docs.microsoft.com/azure/java-add-certificate-ca-store) per importare il certificato X.509 nell'archivio certificati Java predefinito. Tenere presente che si usa la directory %JAVA_HOME% quando si esegue keytool.

Dopo l'installazione del certificato SSL "CosmosDBEmulatorCertificate", l'applicazione potrà connettersi e usare l'emulatore di Azure Cosmos DB locale. Se i problemi persistono, vedere l'articolo [Debug delle connessioni SSL/TLS](http://docs.oracle.com/javase/7/docs/technotes/guides/security/jsse/ReadDebug.html). Molto probabilmente il certificato non è installato nell'archivio %JAVA_HOME%/jre/lib/security/cacerts. Se, ad esempio, sono installate più versioni di Java, è possibile che l'applicazione stia usando un archivio cacerts diverso da quello aggiornato.

## <a name="how-to-use-the-certificate-in-python"></a>Come usare il certificato in Python

Per impostazione predefinita, [Python SDK (2.0.0 o versione successiva)](documentdb-sdk-python.md) per l'API di DocumentDB non prova a usare il certificato SSL quando si connette all'emulatore locale. Se tuttavia si vuole usare la convalida SSL, è possibile seguire gli esempi della documentazione sui [wrapper per socket di Python](https://docs.python.org/2/library/ssl.html).

## <a name="how-to-use-the-certificate-in-nodejs"></a>Come usare il certificato in Node.js

Per impostazione predefinita, [Node.js SDK (1.10.1 o versione successiva)](documentdb-sdk-node.md) per l'API di DocumentDB non prova a usare il certificato SSL quando si connette all'emulatore locale. Se tuttavia si vuole usare la convalida SSL, è possibile seguire gli esempi nella [documentazione di Node.js](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state eseguite le operazioni seguenti:

> [!div class="checklist"]
> * Rotazione dei certificati
> * Esportazione del certificato SSL
> * Uso del certificato in Java, Python e Node.js

È ora possibile passare all'esercitazione Creare un trigger HTTP di Funzioni di Azure con un'associazione di input di Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Creare una funzione di Azure con input da Azure Cosmos DB](tutorial-functions-http-trigger.md) 
