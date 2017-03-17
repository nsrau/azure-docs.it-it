---
title: Esportare i certificati dell&quot;emulatore di DocumentDB | Microsoft Docs
description: "Quando si effettua lo sviluppo in linguaggi e runtime che non usano l&quot;archivio certificati Windows, sarà necessario esportare e gestire i certificati SSL. Questo post contiene istruzioni dettagliate."
services: documentdb
documentationcenter: 
keywords: Emulatore di DocumentDB
author: voellm
manager: jhubbard
editor: 
ms.assetid: ef43deda-c2e9-4193-99e2-7f6a88a0319f
ms.service: documentdb
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/22/2017
ms.author: tvoellm
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: 5f2fd8810041d47be2ef0b21e1487426a643d541
ms.lasthandoff: 03/07/2017


---

# <a name="export-the-documentdb-emulator-certificates-for-use-with-java-python-and-nodejs"></a>Esportare i certificati dell'emulatore di DocumentDB per l'uso con Java, Python e Node.js

[**Scaricare l'emulatore**](https://aka.ms/documentdb-emulator)

L'emulatore di Azure DocumentDB fornisce un ambiente locale che emula il servizio Azure DocumentDB a fini di sviluppo, incluso l'uso di connessioni SSL. Questo post illustra come esportare i certificati SSL da usare in linguaggi e runtime non integrati con l'archivio certificati Windows, ad esempio Java che usa il proprio [archivio certificati](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html), Python che usa [wrapper per socket](https://docs.python.org/2/library/ssl.html) e Node.js che usa [tlsSocket](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback). Per altre informazioni sull'emulatore. vedere [Usare l'emulatore di Azure DocumentDB per lo sviluppo e il test](./documentdb-nosql-local-emulator.md).

## <a name="certification-rotation"></a>Rotazione della certificazione

I certificati nell'emulatore locale di DocumentDB vengono generati la prima volta che l'emulatore viene eseguito. Esistono due certificati, uno usato per la connessione all'emulatore locale e l'altro per la gestione dei segreti nell'emulatore. Il certificato che si vuole esportare è il certificato per la connessione con il nome descrittivo "DocumentDBEmulatorCertificate".

Entrambi i certificati possono essere rigenerati facendo clic su **Reset Data** (Reimposta dati), come illustrato sotto, dall'emulatore di DocumentDB in esecuzione nell'area di notifica di Windows. Se si rigenerano i certificati e questi sono stati installati nell'archivio certificati di Java o usati altrove, sarà necessario aggiornarli. In caso contrario l'applicazione non si connetterà più all'emulatore locale.

![Reimpostazione dei dati nell'emulatore locale di DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-reset-data.png)

## <a name="how-to-export-the-documentdb-ssl-certificate"></a>Come esportare i certificati SSL di DocumentDB

1. Avviare Gestione certificati di Windows eseguendo certlm.msc, passare alla cartella Personale->Certificati e aprire il certificato con il nome descrittivo **DocumentDBEmulatorCertificate**.

    ![Passaggio 1 dell'esportazione nell'emulatore locale di DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-1.png)

2. Fare clic su **Details** (Dettagli) quindi su **OK**.

    ![Passaggio 2 dell'esportazione nell'emulatore locale di DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-2.png)

3. Fare clic su **Copy to File...** (Copia in file).

    ![Passaggio 3 dell'esportazione nell'emulatore locale di DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-3.png)

4. Fare clic su **Next** (Avanti).

    ![Passaggio 4 dell'esportazione nell'emulatore locale di DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-4.png)

5. Fare clic su **No, do not export private key** (No, non esportare la chiave privata) e quindi su **Next** (Avanti).

    ![Passaggio 5 dell'esportazione nell'emulatore locale di DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-5.png)

6. Fare clic su **Base-64 encoded X.509 (.CER)** (Codificato in base&64; X.509 (.CER)) e quindi su **Next** (Avanti).

    ![Passaggio 6 dell'esportazione nell'emulatore locale di DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-6.png)

7. Assegnare un nome al certificato. In questo caso, **documentdbemulatorcert**, quindi fare clic su **Next** (Avanti).

    ![Passaggio 7 dell'esportazione nell'emulatore locale di DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-7.png)

8. Fare clic su **Finish** (Fine).

    ![Passaggio 8 dell'esportazione nell'emulatore locale di DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-8.png)

## <a name="how-to-use-the-certificate-in-java"></a>Come usare il certificato in Java

Quando si eseguono applicazioni Java o applicazioni MongoDB che usano il client Java, è più facile installare il certificato nell'archivio certificati predefinito di Java che passare i flag "-Djavax.net.ssl.trustStore=<keystore> -Djavax.net.ssl.trustStorePassword="<password>". Ad esempio, l'[applicazione demo Java](https://localhost:8081/_explorer/index.html) inclusa dipende dall'archivio certificati predefinito.

Seguire le istruzioni indicate in [Aggiunta di un certificato all'archivio certificati CA Java](https://docs.microsoft.com/en-us/azure/java-add-certificate-ca-store) per importare il certificato X.509 nell'archivio certificati Java predefinito. Tenere presente che si usa la directory %JAVA_HOME% quando si esegue keytool.

Dopo l'installazione del certificato SSL "DocumentDBEmulatorCertificate", l'applicazione potrà connettersi e usare l'emulatore di DocumentDB locale. Se i problemi persistono, vedere l'articolo [Debug delle connessioni SSL/TLS](http://docs.oracle.com/javase/7/docs/technotes/guides/security/jsse/ReadDebug.html). Molto probabilmente il certificato non è installato nell'archivio %JAVA_HOME%/jre/lib/security/cacerts. Se, ad esempio, sono installate più versioni di Java, è possibile che l'applicazione stia usando un archivio cacerts diverso da quello aggiornato.

## <a name="how-to-use-the-certificate-in-python"></a>Come usare il certificato in Python

Per impostazione predefinita, [Python SDK (2.0.0 o versione successiva)](https://docs.microsoft.com/en-us/azure/documentdb/documentdb-sdk-python) per DocumentDB non proverà a usare il certificato SSL quando si connette all'emulatore locale. Se tuttavia si vuole usare la convalida SSL, è possibile seguire gli esempi della documentazione sui [wrapper per socket di Python](https://docs.python.org/2/library/ssl.html).

## <a name="how-to-use-the-certificate-in-nodejs"></a>Come usare il certificato in Node.js

Per impostazione predefinita, [Node.js SDK (1.10.1 o versione successiva)](https://docs.microsoft.com/en-us/azure/documentdb/documentdb-sdk-node) per DocumentDB non proverà a usare il certificato SSL quando si connette all'emulatore locale. Se tuttavia si vuole usare la convalida SSL, è possibile seguire gli esempi nella [documentazione di Node.js](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback).

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni su DocumentDB, vedere [Introduzione ad Azure DocumentDB](documentdb-introduction.md).
* Per avviare lo sviluppo nell'emulatore di DocumentDB, scaricare uno degli [SDK di DocumentDB supportati](documentdb-sdk-dotnet.md).

