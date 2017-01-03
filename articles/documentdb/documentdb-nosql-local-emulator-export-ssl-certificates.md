---
title: Esportare i certificati dell&quot;emulatore di DocumentDB | Documentazione Microsoft
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
ms.date: 11/29/2016
ms.author: tvoellm
translationtype: Human Translation
ms.sourcegitcommit: c47ff7045a7c69f2f4a0235fa591a6a8cc820192
ms.openlocfilehash: dc3d297f44fb99b6fad58810eb31f429fdb1bd25

---

# <a name="export-the-documentdb-emulator-certificates"></a>Esportare i certificati dell'emulatore di DocumentDB

[**Scaricare l'emulatore**](https://aka.ms/documentdb-emulator)

L'emulatore di Azure DocumentDB fornisce un ambiente locale che emula il servizio Azure DocumentDB a fini di sviluppo, incluso l'uso di connessioni SSL. Questo post illustra come esportare i certificati SSL da usare in linguaggi e runtime non integrati con l'archivio certificati Windows, ad esempio Java che usa il proprio [archivio certificati](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html) e Python che usa [wrapper per socket](https://docs.python.org/2/library/ssl.html). Per altre informazioni sull'emulatore, vedere il post "[Use the Azure DocumentDB Emulator for development and testing](./documentdb-nosql-local-emulator.md)" (Usare l'emulatore di Azure DocumentDB per lo sviluppo e il test).

# <a name="certification-rotation"></a>Rotazione della certificazione

I certificati nell'emulatore locale di DocumentDB vengono generati la prima volta che l'emulatore viene eseguito. Esistono due certificati, uno usato per la connessione all'emulatore locale e l'altro per la gestione dei segreti nell'emulatore. Il certificato che si vuole esportare è il certificato per la connessione con il nome descrittivo "DocumentDBEmulatorCertificate".

Entrambi i certificati possono essere rigenerati facendo clic su "Reset Data" (Reimposta dati), come illustrato sotto, dall'emulatore locale di DocumentDB in esecuzione nell'area di notifica di Windows. Se si rigenerano i certificati e questi sono stati installati nell'archivio certificati di Java o usati altrove, sarà necessario aggiornarli. In caso contrario l'applicazione non si connetterà più all'emulatore locale.

![Reimpostazione dei dati nell'emulatore locale di DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-reset-data.png)

# <a name="how-to-export-the-documentdb-ssl-certificate"></a>Come esportare i certificati SSL di DocumentDB

- Passaggio 1: Avviare Gestione certificati di Windows eseguendo certlm.msc, passare alla cartella Personale->Certificati e aprire il certificato con il nome descrittivo "DocumentDBEmulatorCertificate".

![Passaggio 1 dell'esportazione nell'emulatore locale di DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-1.png)

- Passaggio 2: Fare clic su "Dettagli", quindi su "OK".

![Passaggio 1 dell'esportazione nell'emulatore locale di DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-2.png)

- Passaggio 3: Fare clic su "Copia su file".

![Passaggio 1 dell'esportazione nell'emulatore locale di DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-3.png)

- Passaggio 4: Fare clic su "Avanti".

![Passaggio 1 dell'esportazione nell'emulatore locale di DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-4.png)

- Passaggio 5: Fare clic su "No, non esportare la chiave privata", quindi fare clic su "Avanti".

![Passaggio 1 dell'esportazione nell'emulatore locale di DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-5.png)

- Passaggio 6: Fare clic su "Codificato Base 64 X.509 (.CER)" e quindi su "Avanti".

![Passaggio 1 dell'esportazione nell'emulatore locale di DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-6.png)

- Passaggio 7: Assegnare un nome al certificato, in questo caso "documentdbemulatorcert", e quindi fare clic su "Avanti".

![Passaggio 1 dell'esportazione nell'emulatore locale di DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-7.png)

- Passaggio 8: Fare clic su "Fine".

![Passaggio 1 dell'esportazione nell'emulatore locale di DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-8.png)

# <a name="how-to-use-the-certificate-in-java"></a>Come usare il certificato in Java

Quando si eseguono applicazioni Java o applicazioni MongoDB che usano il client Java, è più facile installare il certificato nell'archivio certificati predefinito di Java che passare i flag "-Djavax.net.ssl.trustStore=<keystore> -Djavax.net.ssl.trustStorePassword="<password>". Ad esempio, l'[applicazione demo Java](https://localhost:8081/_explorer/index.html) inclusa dipende dall'archivio certificati predefinito.

Seguire le istruzioni nel post "[Aggiunta di un certificato all'archivio certificati CA Java](https://docs.microsoft.com/en-us/azure/java-add-certificate-ca-store)" per importare il certificato X.509 nell'archivio certificati Java predefinito. Tenere presente che si usa la directory %JAVA_HOME% quando si esegue keytool.

Dopo l'installazione del certificato SSL "DocumentDBEmulatorCertificate", l'applicazione potrà connettersi e usare l'emulatore di DocumentDB locale. In caso di problemi, vedere l'articolo "[Debugging SSL/TLS Connections](http://docs.oracle.com/javase/7/docs/technotes/guides/security/jsse/ReadDebug.html)" (Debug delle connessioni SSL/TLS). Molto probabilmente il certificato non è installato nell'archivio %JAVA_HOME%/jre/lib/security/cacerts. Se, ad esempio, sono installate più versioni di Java, è possibile che l'applicazione stia usando un archivio cacerts diverso da quello aggiornato.

# <a name="how-to-use-the-certificate-in-python"></a>Come usare il certificato in Python

Per impostazione predefinita, Python SDK per DocumentDB non proverà a usare il certificato SSL quando si connette all'emulatore locale. Se tuttavia si vuole usare la convalida SSL, è possibile seguire gli esempi della documentazione sui [wrapper per socket di Python](https://docs.python.org/2/library/ssl.html).



<!--HONumber=Nov16_HO5-->


