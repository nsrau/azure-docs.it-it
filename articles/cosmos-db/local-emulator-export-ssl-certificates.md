---
title: Esportare i certificati dell'emulatore di Azure Cosmos DB
description: Informazioni su come esportare il certificato dell'emulatore Azure Cosmos DB per l'uso con app Java, Python e Node.js. I certificati devono essere esportati e usati per i linguaggi e gli ambienti di runtime che non usano l'archivio certificati di Windows.
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/17/2020
author: deborahc
ms.author: dech
ms.custom: devx-track-python, devx-track-java
ms.openlocfilehash: 8f2714c7c49aa5e02747ce726da29a98485b5fbd
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90988225"
---
# <a name="export-the-azure-cosmos-db-emulator-certificates-for-use-with-java-python-and-nodejs-apps"></a>Esportare i certificati dell'emulatore Azure Cosmos DB per l'uso con app Java, Python e Node.js

L'emulatore di Azure Cosmos DB fornisce un ambiente locale che emula il servizio Azure Cosmos DB a scopo di sviluppo. Azure Cosmos Emulator supporta solo la comunicazione sicura tramite connessioni TLS.

I certificati nell'emulatore locale Azure Cosmos DB vengono generati la prima volta che si esegue l'emulatore. Esistono due certificati, Uno di essi viene usato per connettersi all'emulatore locale e l'altro viene usato per gestire la crittografia predefinita dei dati dell'emulatore all'interno dell'emulatore. Il certificato che si vuole esportare è il certificato per la connessione con il nome descrittivo "DocumentDBEmulatorCertificate".

Quando si usa l'emulatore per sviluppare app in linguaggi diversi, ad esempio Java, Python o Node.js, è necessario esportare il certificato dell'emulatore e importarlo nell'archivio certificati richiesto.

Il linguaggio e il Runtime .NET usano l'archivio certificati Windows per connettersi in modo sicuro al Azure Cosmos DB emulatore locale quando l'applicazione viene eseguita in un host del sistema operativo Windows. Gli altri linguaggi hanno il proprio metodo di gestione e uso dei certificati. Ad esempio, Java usa il proprio [archivio certificati](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html), Python usa i [wrapper socket](https://docs.python.org/2/library/ssl.html)e Node.js USA [tlsSocket](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback).

Questo articolo illustra come esportare i certificati TLS/SSL da usare in linguaggi e ambienti di runtime diversi che non si integrano con l'archivio certificati di Windows. Per altre informazioni sull'emulatore, vedere [Usare l'emulatore di Azure Cosmos DB per sviluppo e test](./local-emulator.md).

## <a name="export-the-azure-cosmos-db-tlsssl-certificate"></a><a id="export-emulator-certificate"></a>Esportare il certificato TLS/SSL Azure Cosmos DB

È necessario esportare il certificato dell'emulatore per usare correttamente l'endpoint dell'emulatore da linguaggi e ambienti di runtime che non si integrano con l'archivio certificati di Windows. È possibile esportare il certificato utilizzando Gestione certificati Windows. Usare le istruzioni dettagliate seguenti per esportare il certificato "DocumentDBEmulatorCertificate" come file con estensione cer X. 509 con codifica BASE 64:

1. Avviare Gestione certificati di Windows eseguendo certlm.msc, passare alla cartella Personale->Certificati e aprire il certificato con il nome descrittivo **DocumentDbEmulatorCertificate**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-1.png" alt-text="Passaggio 1 dell'esportazione nell'emulatore locale di Azure Cosmos DB":::

1. Fare clic su **Details** (Dettagli) quindi su **OK**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-2.png" alt-text="Passaggio 2 dell'esportazione nell'emulatore locale di Azure Cosmos DB":::

1. Fare clic su **Copy to File...** (Copia in file).

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-3.png" alt-text="Passaggio 3 dell'esportazione nell'emulatore locale di Azure Cosmos DB":::

1. Fare clic su **Avanti**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-4.png" alt-text="Passaggio 4 dell'esportazione nell'emulatore locale di Azure Cosmos DB":::

1. Fare clic su **No, do not export private key** (No, non esportare la chiave privata) e quindi su **Next** (Avanti).

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-5.png" alt-text="Passaggio 5 dell'esportazione nell'emulatore locale di Azure Cosmos DB":::

1. Fare clic su **Base-64 encoded X.509 (.CER)** (Codificato in base 64 X.509 (.CER)) e quindi su **Next** (Avanti).

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-6.png" alt-text="Passaggio 6 dell'esportazione nell'emulatore locale di Azure Cosmos DB":::

1. Assegnare un nome al certificato. In questo caso, **documentdbemulatorcert**, quindi fare clic su **Next** (Avanti).

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-7.png" alt-text="Passaggio 7 dell'esportazione nell'emulatore locale di Azure Cosmos DB":::

1. Fare clic su **Fine**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-8.png" alt-text="Passaggio 8 dell'esportazione nell'emulatore locale di Azure Cosmos DB":::

## <a name="use-the-certificate-with-java-apps"></a>Usare il certificato con le app java

Quando si eseguono applicazioni Java o applicazioni MongoDB che usano un client basato su Java, è più semplice installare il certificato nell'archivio certificati predefinito Java rispetto al passaggio dei `-Djavax.net.ssl.trustStore=<keystore> -Djavax.net.ssl.trustStorePassword="<password>"` flag. Ad esempio, l'applicazione demo Java inclusa ( `https://localhost:8081/_explorer/index.html` ) dipende dall'archivio certificati predefinito.

Per importare il certificato X. 509 nell'archivio certificati Java predefinito, seguire le istruzioni riportate nell' [Archivio dei certificati per l'aggiunta di un certificato all'archivio Java](https://docs.microsoft.com/azure/java-add-certificate-ca-store) . Tenere presente che si lavorerà nella directory *% JAVA_HOME%* quando si esegue lo strumento. Dopo aver importato il certificato nell'archivio certificati, i client per l'API SQL e Azure Cosmos DB per MongoDB saranno in grado di connettersi all'emulatore di Azure Cosmos.

In alternativa, è possibile eseguire lo script bash seguente per importare il certificato:

```bash
#!/bin/bash

# If emulator was started with /AllowNetworkAccess, replace the below with the actual IP address of it:
EMULATOR_HOST=localhost
EMULATOR_PORT=8081
EMULATOR_CERT_PATH=/tmp/cosmos_emulator.cert
openssl s_client -connect ${EMULATOR_HOST}:${EMULATOR_PORT} </dev/null | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > $EMULATOR_CERT_PATH
# delete the cert if already exists
sudo $JAVA_HOME/bin/keytool -cacerts -delete -alias cosmos_emulator
# import the cert
sudo $JAVA_HOME/bin/keytool -cacerts -importcert -alias cosmos_emulator -file $EMULATOR_CERT_PATH
```

Una volta installato il certificato TLS/SSL "CosmosDBEmulatorCertificate", l'applicazione deve essere in grado di connettersi e usare l'emulatore Azure Cosmos DB locale. In caso di problemi, è possibile seguire l'articolo [debug delle connessioni SSL/TLS](https://docs.oracle.com/javase/7/docs/technotes/guides/security/jsse/ReadDebug.html) . Nella maggior parte dei casi, il certificato potrebbe non essere installato nell'archivio *% JAVA_HOME%/jre/lib/security/cacerts* . Ad esempio, se si dispone di più versioni installate di Java, è possibile che l'applicazione stia usando un archivio cacerts diverso da quello aggiornato.

## <a name="use-the-certificate-with-python-apps"></a>Usare il certificato con le app Python

Quando ci si connette all'emulatore dalle app Python, la verifica TLS è disabilitata. Per impostazione predefinita, [Python SDK (versione 2.0.0 o successiva)](sql-api-sdk-python.md) per l'API SQL non tenterà di usare il certificato TLS/SSL per la connessione all'emulatore locale. Se tuttavia si vuole usare la convalida TLS, è possibile seguire gli esempi nella documentazione relativa ai [wrapper dei socket Python](https://docs.python.org/2/library/ssl.html) .

## <a name="how-to-use-the-certificate-in-nodejs"></a>Come usare il certificato in Node.js

Quando ci si connette all'emulatore da Node.js SDK, la verifica TLS è disabilitata. Per impostazione predefinita, l' [ SDKNode.js (versione 1.10.1 o successiva)](sql-api-sdk-node.md) per l'API SQL non tenterà di usare il certificato TLS/SSL per la connessione all'emulatore locale. Se tuttavia si vuole usare la convalida TLS, è possibile seguire gli esempi nella [ documentazione diNode.js](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback).

## <a name="rotate-emulator-certificates"></a>Ruotare i certificati dell'emulatore

È possibile forzare la rigenerazione dei certificati dell'emulatore selezionando **Reimposta i dati** dal Azure Cosmos DB emulatore in esecuzione nel vassoio di Windows. Si noti che questa azione eliminerà anche tutti i dati archiviati localmente dall'emulatore.

:::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-reset-data.png" alt-text="Reimpostazione dei dati nell'emulatore locale di Azure Cosmos DB":::

Se il certificato è stato installato nell'archivio certificati Java o è stato usato altrove, è necessario importarli nuovamente usando i certificati correnti. L'applicazione non è in grado di connettersi all'emulatore locale fino a quando non si aggiornano i certificati.

## <a name="next-steps"></a>Passaggi successivi

* [Usare i parametri della riga di comando e i comandi di PowerShell per controllare l'emulatore](emulator-command-line-parameters.md)
* [Problemi di debug con l'emulatore](troubleshoot-local-emulator.md)

