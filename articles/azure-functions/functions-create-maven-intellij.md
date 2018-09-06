---
title: Creare un'app per le funzioni di Azure con Java e IntelliJ | Microsoft Docs
description: Guida pratica per creare e pubblicare un'app semplice senza server attivata tramite HTTP usando Java e IntelliJ in Funzioni di Azure.
services: functions
documentationcenter: na
author: jeffhollan
manager: jpconnock
keywords: funzioni di Azure, funzioni, elaborazione eventi, calcolo, architettura senza server, java
ms.service: functions
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 07/01/2018
ms.author: jehollan
ms.custom: mvc, devcenter
ms.openlocfilehash: 317d8bf40ef152e5b7dae6406be29330feaaa8d3
ms.sourcegitcommit: 3d0295a939c07bf9f0b38ebd37ac8461af8d461f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2018
ms.locfileid: "43842479"
---
# <a name="create-your-first-function-with-java-and-intellij-preview"></a>Creare la prima funzione con Java e IntelliJ (anteprima)

> [!NOTE] 
> Java per Funzioni di Azure è attualmente in versione di anteprima.

Questo articolo illustra come creare un progetto per funzioni [senza server](https://azure.microsoft.com/overview/serverless-computing/) con IntelliJ IDEA e Apache Maven, testarlo ed eseguirne il debug nel proprio computer dall'IDE e infine distribuirlo in Funzioni di Azure. 

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Configurazione dell'ambiente di sviluppo

Per sviluppare un'app per le funzioni con Java e IntelliJ, è necessario che siano installati gli elementi seguenti:

-  [Java Developer Kit](https://www.azul.com/downloads/zulu/), versione 8.
-  [Apache Maven](https://maven.apache.org), versione 3.0 o successiva.
-  [IntelliJ IDEA](https://www.jetbrains.com/idea/download), Community o Ultimate con gli strumenti di Maven.
-  [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure)

> [!IMPORTANT] 
> Per completare questa guida introduttiva, è necessario impostare la variabile di ambiente JAVA_HOME sul percorso di installazione di JDK.

È altamente consigliabile installare anche gli [strumenti di base di Funzioni di Azure, versione 2](functions-run-local.md#v2) che offrono un ambiente di sviluppo locale per la scrittura, l'esecuzione e il debug di Funzioni di Azure. 


## <a name="create-a-functions-project"></a>Creare un progetto per le funzioni

1. In IntelliJ IDEA fare clic su **Create New Project** (Crea nuovo progetto).  
1. Scegliere di creare il progetto da **Maven**.
1. Selezionare la casella di controllo **Create from archetype** (Crea da archetipo) e fare clic su **Add Archetype** (Aggiungi archetipo) per [azure-functions-archetype](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype).
    - GroupId (ID gruppo): com.microsoft.azure
    - ArtifactId (ID artefatto): azure-functions-archetype
    - Version (Versione): usare la versione più recente dal [repository centrale](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype)
    ![Creare da IntelliJ Maven](media/functions-create-first-java-intellij/functions-create-intellij.png)  
1. Fare clic su **Next** (Avanti), immettere i dettagli per il progetto corrente e quindi fare clic su **Finish** (Fine).

Maven crea i file di progetto in una nuova cartella denominata _artifactId_. Il codice generato nel progetto è una semplice funzione [attivata tramite HTTP](/azure/azure-functions/functions-bindings-http-webhook) che restituisce il corpo della richiesta di attivazione dell'HTTP.

## <a name="run-functions-locally-in-the-ide"></a>Eseguire le funzioni in locale nell'IDE

> [!NOTE]
> Per l'esecuzione e il debug delle funzioni in locale, è necessario che siano installati gli [strumenti di base di Funzioni di Azure, versione 2](functions-run-local.md#v2).

1. Scegliere di importare le modifiche o assicurarsi che l'[importazione automatica](https://www.jetbrains.com/help/idea/creating-and-optimizing-imports.html) sia abilitata.
1. Aprire la barra degli strumenti **Maven Projects** (Progetti Maven).
1. In Lifecycle (Ciclo di vita) fare doppio clic su **package** (pacchetto) per creare il pacchetto della soluzione, compilarla e creare una directory di destinazione.
1. In Plugins (Plug-in) -> azure-functions fare doppio clic su **azure-functions:run** per avviare il runtime locale di Funzioni di Azure.  
  ![Barra degli strumenti Maven per Funzioni di Azure](media/functions-create-first-java-intellij/functions-intellij-java-maven-toolbar.png)  

Dopo avere testato la funzione, chiudere la finestra di dialogo per l'esecuzione. Può essere attivo e in esecuzione in locale un solo host di funzioni alla volta.

### <a name="debug-the-function-in-intellij"></a>Eseguire il debug della funzione in IntelliJ
Per avviare l'host di funzioni in modalità di debug, aggiungere **- DenableDebug** come argomento quando si esegue la funzione. È possibile eseguire la seguente riga di comando nel terminale o configurarla negli [obiettivi Maven](https://www.jetbrains.com/help/idea/maven-support.html#run_goal). Quindi l'host di funzioni aprirà una porta di debug 5005. 

```
mvn azure-functions:run -DenableDebug
```

Per eseguire il debug in IntelliJ, nel menu **Run** (Esegui) selezionare **Edit Configurations** (Modifica configurazioni). Fare clic su **+** per aggiungere **Remote** (Remoto). Compilare **Name** (Nome) e **Settings** (Impostazioni), quindi fare clic su **OK** per salvare la configurazione. Al termine dell'installazione, fare clic su **Debug** "Your Remote Configuration Name" (Esegui il debug del nome della configurazione remota) o premere **MAIUSC+F9** per avviare il debug.

![Funzioni di debug in IntelliJ](media/functions-create-first-java-intellij/debug-configuration-intellij.PNG)

Al termine, arrestare il debugger e il processo in esecuzione. Può essere attivo e in esecuzione in locale un solo host di funzioni alla volta.

## <a name="deploy-the-function-to-azure"></a>Distribuire la funzione in Azure

Il processo di distribuzione in Funzioni di Azure usa le credenziali dell'account dell'interfaccia della riga di comando di Azure. Prima di continuare, [accedere all'interfaccia della riga di comando di Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) usando il prompt dei comandi del computer.

```azurecli
az login
```

Distribuire il codice in una nuova app per le funzioni usando la destinazione Maven `azure-functions:deploy` oppure selezionare l'opzione azure-functions:deploy nella finestra dei progetti Maven.

```
mvn azure-functions:deploy
```

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
