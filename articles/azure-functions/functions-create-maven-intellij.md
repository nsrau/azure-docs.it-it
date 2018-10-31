---
title: Creare una funzione di Azure con Java e IntelliJ | Microsoft Docs
description: Informazioni su come creare e pubblicare in Azure un'app semplice senza server attivata tramite HTTP con Java e IntelliJ.
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
ms.openlocfilehash: eb8499ef6c0f872a0761f7be606e058387947b2b
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2018
ms.locfileid: "49319881"
---
# <a name="create-your-first-azure-function-with-java-and-intellij-preview"></a>Creare la prima funzione di Azure con Java e IntelliJ (anteprima)

> [!NOTE]
> Java per Funzioni di Azure è attualmente in versione di anteprima.

Questo articolo illustra:
- Come creare un progetto di funzione [senza server](https://azure.microsoft.com/overview/serverless-computing/) con IntelliJ IDEA e Apache Maven
- Come eseguire il test e il debug della funzione nell'ambiente di sviluppo integrato (IDE) sul proprio computer
- Come distribuire il progetto di funzione in Funzioni di Azure

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Configurazione dell'ambiente di sviluppo

Per sviluppare una funzione con Java e IntelliJ, installare il software seguente:

- [Java Developer Kit](https://www.azul.com/downloads/zulu/) (JDK), versione 8
- [Apache Maven](https://maven.apache.org), versione 3.0 o successiva
- [IntelliJ IDEA](https://www.jetbrains.com/idea/download), versione Community o Ultimate con Maven
- [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure)

> [!IMPORTANT]
> Per completare la procedura descritta in questo articolo, è necessario impostare la variabile di ambiente JAVA_HOME sul percorso di installazione di JDK.

 Si consiglia di installare [Azure Functions Core Tools, versione 2](functions-run-local.md#v2), che offre un ambiente di sviluppo locale per la scrittura, l'esecuzione e il debug di Funzioni di Azure.

## <a name="create-a-functions-project"></a>Creare un progetto per le funzioni

1. In IntelliJ IDEA selezionare **Create New Project** (Crea nuovo progetto).  
1. Nella finestra **New Project** (Nuovo progetto) selezionare **Maven** nel riquadro sinistro.
1. Selezionare la casella di controllo **Create from archetype** (Crea da archetipo) e quindi selezionare **Add Archetype** (Aggiungi archetipo) per [azure-functions-archetype](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype).
1. Nella finestra **Add Archetype** (Aggiungi archetipo) completare i campi come indicato di seguito:
    - _GroupId_: com.microsoft.azure
    - _ArtifactId_: azure-functions-archetype
    - _Versione_: usare la versione più recente disponibile nel [repository centrale](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype)
    ![Creare un progetto Maven dall'archetipo in IntelliJ IDEA](media/functions-create-first-java-intellij/functions-create-intellij.png)  
1. Selezionare **OK**, quindi **Avanti**.
1. Immettere i dettagli relativi al progetto corrente e selezionare **Finish** (Fine).

Maven crea i file di progetto in una nuova cartella a cui viene assegnato lo stesso nome del valore _artifactId_. Il codice generato del progetto è una semplice funzione [attivata tramite HTTP](/azure/azure-functions/functions-bindings-http-webhook) che restituisce il corpo della richiesta di attivazione dell'HTTP.

## <a name="run-functions-locally-in-the-ide"></a>Eseguire le funzioni in locale nell'IDE

> [!NOTE]
> Per l'esecuzione e il debug delle funzioni in locale, è necessario che sia installato [Azure Functions Core Tools, versione 2](functions-run-local.md#v2).

1. Importare le modifiche manualmente oppure abilitare l'[importazione automatica](https://www.jetbrains.com/help/idea/creating-and-optimizing-imports.html).
1. Aprire la barra degli strumenti **Maven Projects** (Progetti Maven).
1. Espandere **Lifecycle** (Ciclo di vita) e quindi aprire **package** (pacchetto). La soluzione viene compilata e inserita in un pacchetto all'interno di una directory di destinazione appena creata.
1. Espandere **Plugins** (Plugin)  > **azure-functions** e aprire **azure-functions:run** per avviare il runtime locale di Funzioni di Azure.  
  ![Barra degli strumenti Maven per Funzioni di Azure](media/functions-create-first-java-intellij/functions-intellij-java-maven-toolbar.png)  

1. Dopo avere testato la funzione, chiudere la finestra di dialogo di esecuzione. Può essere attivo e in esecuzione in locale un solo host di funzioni alla volta.

## <a name="debug-the-function-in-intellij"></a>Eseguire il debug della funzione in IntelliJ

1. Per avviare l'host di funzioni in modalità di debug, aggiungere **- DenableDebug** come argomento quando si esegue la funzione. È possibile modificare la configurazione negli [obiettivi di Maven](https://www.jetbrains.com/help/idea/maven-support.html#run_goal) o eseguire il comando seguente in una finestra del terminale:  

   ```
   mvn azure-functions:run -DenableDebug
   ```

   Con questo comando l'host di funzioni apre una porta di debug 5005.

1. Nel menu **Run** (Esegui) selezionare **Edit Configurations** (Modifica configurazioni).
1. Selezionare **(+)** per aggiungere una **configurazione remota**.
1. Compilare i campi _Name_ (Nome) e _Settings_ (Impostazioni) e quindi selezionare **OK** per salvare la configurazione.
1. Al termine dell'installazione, selezionare **Debug <nome configurazione remota>** oppure premere MAIUSC+F9 sulla tastiera per avviare il debug.

   ![Funzioni di debug in IntelliJ](media/functions-create-first-java-intellij/debug-configuration-intellij.PNG)

1. Al termine, arrestare il debugger e il processo in esecuzione. Può essere attivo e in esecuzione in locale un solo host di funzioni alla volta.

## <a name="deploy-the-function-to-azure"></a>Distribuire la funzione in Azure

1. Prima di poter distribuire la funzione in Azure, è necessario [accedere usando l'interfaccia della riga di comando di Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

   ``` azurecli
   az login
   ```

1. Distribuire il codice in una nuova funzione usando la destinazione Maven `azure-functions:deploy`. È anche possibile selezionare l'opzione **azure-functions:deploy** nella finestra dei progetti Maven.

   ```
   mvn azure-functions:deploy
   ```

1. Al termine della distribuzione della funzione, trovare l'URL della funzione nell'output dell'interfaccia della riga di comando di Azure.

   ``` output
   [INFO] Successfully deployed Function App with package.
   [INFO] Deleting deployment package from Azure Storage...
   [INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
   [INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
   [INFO] ------------------------------------------------------------------------
   ```

## <a name="next-steps"></a>Passaggi successivi

- Vedere il [manuale dello sviluppatore di funzioni Java](functions-reference-java.md) per altre informazioni sullo sviluppo di funzioni Java.
- Al progetto aggiungere altre funzioni con trigger diversi usando la destinazione Maven `azure-functions:add`.
