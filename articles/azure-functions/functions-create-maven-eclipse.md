---
title: Creare un'app per le funzioni di Azure con Java ed Eclipse | Microsoft Docs
description: Guida pratica per creare e pubblicare un'app semplice senza server attivata tramite HTTP usando Java ed Eclipse in Funzioni di Azure.
services: functions
documentationcenter: na
author: jeffhollan
manager: jpconnock
keywords: funzioni di Azure, funzioni, elaborazione eventi, calcolo, architettura senza server, java
ms.service: azure-functions
ms.devlang: java
ms.topic: conceptual
ms.date: 07/01/2018
ms.author: jehollan
ms.custom: mvc, devcenter
ms.openlocfilehash: 123a24eb13de584d8e3b70d0d8b1173f583867c1
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58881427"
---
# <a name="create-your-first-function-with-java-and-eclipse"></a>Creare la prima funzione con Java ed Eclipse 

Questo articolo illustra come creare un progetto per funzioni [senza server](https://azure.microsoft.com/solutions/serverless/) con l'IDE Eclipse e Apache Maven, testarlo ed eseguirne il debug e infine distribuirlo in Funzioni di Azure. 

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Configurazione dell'ambiente di sviluppo

Per sviluppare un'app per le funzioni con Java ed Eclipse, è necessario che siano installati gli elementi seguenti:

-  [Java Developer Kit](https://www.azul.com/downloads/zulu/), versione 8.
-  [Apache Maven](https://maven.apache.org), versione 3.0 o successiva.
-  [Eclipse](https://www.eclipse.org/downloads/packages/), con supporto per Java e Maven.
-  [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure)

> [!IMPORTANT] 
> Per completare questa guida introduttiva, è necessario impostare la variabile di ambiente JAVA_HOME sul percorso di installazione di JDK.

È altamente consigliabile installare anche gli [strumenti di base di Funzioni di Azure, versione 2](functions-run-local.md#v2) che offrono un ambiente locale per l'esecuzione e il debug di Funzioni di Azure. 

## <a name="create-a-functions-project"></a>Creare un progetto per le funzioni

1. In Eclipse scegliere **Project** (Progetto) dal menu **File**. 
1. Nella finestra **New Project** (Nuovo progetto) aprire la cartella **Java Project** (Progetto Java) e selezionare **Maven Project** (Progetto Maven) e quindi scegliere **Next** (Avanti).
1. Nella finestra di dialogo **New Maven Project** (Nuovo progetto Maven) accettare i valori predefiniti e scegliere **Next** (Avanti).
1. Selezionare **Add Archetype** (Aggiungi archetipo) e aggiungere le voci per [azure-functions-archetype](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype).
    - Archetype Group ID (ID gruppo archetipo): com.microsoft.azure
    - Archetype Artifact ID (ID artefatto archetipo): azure-functions-archetype
    - Versione: Usare la versione più recente dal [il repository centrale](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype)
    ![Maven Eclipse creare](media/functions-create-first-java-eclipse/functions-create-eclipse.png)  
1. Fare clic su **OK**, immettere i dettagli per il progetto corrente e quindi fare clic su **Finish** (Fine).

Maven crea i file di progetto in una nuova cartella denominata _artifactId_. Il codice generato nel progetto è una semplice funzione [attivata tramite HTTP](/azure/azure-functions/functions-bindings-http-webhook) che restituisce il corpo della richiesta di attivazione dell'HTTP.

## <a name="run-functions-locally-in-the-ide"></a>Eseguire le funzioni in locale nell'IDE

> [!NOTE]
> Per l'esecuzione e il debug delle funzioni in locale, è necessario che siano installati gli [strumenti di base di Funzioni di Azure, versione 2](functions-run-local.md#v2).

1. Fare clic con il pulsante destro del mouse sul progetto generato e quindi scegliere **RunAs** e **Maven build** (Compilazione di Maven).
1. Nella finestra di dialogo **Edit Configuration** (Modifica configurazione) specificare `package` nei campi **Goals** (Obiettivi) e **Name** (Nome) e quindi selezionare **Run** (Esegui). In questo modo verrà compilato e creato il pacchetto del codice funzione.
1. Una volta completata la compilazione, creare un'altra configurazione di esecuzione usando `azure-functions:run` come obiettivo e nome. Selezionare **Run** (Esegui) per eseguire la funzione nell'ambiente IDE.

Dopo aver completato la procedura di test della funzione, terminare il runtime nella finestra della console. Può essere attivo e in esecuzione in locale un solo host di funzioni alla volta.

### <a name="debug-the-function-in-eclipse"></a>Eseguire il debug della funzione in Eclipse

Nella procedura di configurazione **Run As** descritta nel passaggio precedente, sostituire `azure-functions:run` con `mvn azure-functions:run -DenableDebug` ed eseguire la configurazione aggiornata per avviare l'app per le funzioni in modalità di debug.

Selezionare il menu **Run** (Esegui) e aprire **Debug Configurations** (Configurazioni di debug). Scegliere **Remote Java Application** (Applicazione Java remota) e crearne una nuova. Assegnare un nome alla configurazione e specificare le impostazioni. La porta deve essere coerente con la porta di debug aperta dall'host di funzioni, che per impostazione predefinita è `5005`. Al termine dell'installazione, fare clic su `Debug` per avviare il debug.

![Funzioni di debug in Eclipse](media/functions-create-first-java-eclipse/debug-configuration-eclipse.PNG)

Impostare i punti di interruzione ed esaminare gli oggetti presenti nella funzione usando l'IDE. Al termine, arrestare il debugger e l'host di funzione in esecuzione. Può essere attivo e in esecuzione in locale un solo host di funzioni alla volta.

## <a name="deploy-the-function-to-azure"></a>Distribuire la funzione in Azure

Il processo di distribuzione in Funzioni di Azure usa le credenziali dell'account dell'interfaccia della riga di comando di Azure. Prima di continuare, [accedere all'interfaccia della riga di comando di Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) usando il prompt dei comandi del computer.

```azurecli
az login
```

Distribuire il codice in una nuova app per le funzioni usando la destinazione Maven `azure-functions:deploy` in una nuova configurazione **RunAs**.

Quando la distribuzione è stata completata, viene visualizzato l'URL da usare per accedere all'app per le funzioni di Azure:

```output
[INFO] Successfully deployed Function App with package.
[INFO] Deleting deployment package from Azure Storage...
[INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
[INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
[INFO] ------------------------------------------------------------------------
```

## <a name="next-steps"></a>Passaggi successivi

- Vedere il [manuale dello sviluppatore di funzioni Java](functions-reference-java.md) per altre informazioni sullo sviluppo di funzioni Java.
- Al progetto aggiungere altre funzioni con trigger diversi usando la destinazione Maven `azure-functions:add`.
