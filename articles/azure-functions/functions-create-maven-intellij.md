---
title: Creare una funzione di Azure con Java e IntelliJ
description: Informazioni su come creare e pubblicare in Azure un'app semplice senza server attivata tramite HTTP con Java e IntelliJ.
author: jeffhollan
ms.topic: how-to
ms.date: 07/01/2018
ms.author: jehollan
ms.custom: mvc, devcenter
ms.openlocfilehash: 7ced455e8124abed75dc7b2bbf7f92eb13613347
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85556762"
---
# <a name="create-your-first-azure-function-with-java-and-intellij"></a>Creare la prima funzione di Azure con Java e IntelliJ

Questo articolo illustra:
- Come creare un progetto di funzione senza [Server](https://azure.microsoft.com/overview/serverless-computing/) con IntelliJ IDEA
- Come eseguire il test e il debug della funzione nell'ambiente di sviluppo integrato (IDE) sul proprio computer
- Come distribuire il progetto di funzione in Funzioni di Azure

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Configurazione dell'ambiente di sviluppo

Per sviluppare una funzione con Java e IntelliJ, installare il software seguente:

+ Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
+ [Java Development Kit (JDK) supportato da Azure](https://aka.ms/azure-jdks) per Java 8
+ [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) Ultimate Edition o Community Edition installato
+ [Maven 3.5.0+](https://maven.apache.org/download.cgi)
+ Ultima versione di [Function Core Tools](https://github.com/Azure/azure-functions-core-tools)


## <a name="installation-and-sign-in"></a>Installazione e accesso

1. Nella finestra di dialogo Settings/Preferences (Impostazioni/Preferenze) (CTRL+ALT+S) di IntelliJ IDEA selezionare **Plugins**. Quindi individuare **Azure Toolkit for IntelliJ** nel **Marketplace** e fare clic su **Installa**. Dopo l'installazione, fare clic su **Riavvia** per attivare il plug-in. 

    ![Plug-in Azure Toolkit for IntelliJ in Marketplace][marketplace]

2. Per accedere all'account Azure, aprire **Azure Explorer** sulla barra laterale e quindi fare clic su **Azure Sign In** (Accesso ad Azure) sulla barra superiore. Oppure scegliere **Tools/Azure/Azure Sign in** (Strumenti/Azure/Accesso ad Azure) dal menu IDEA.
    ![Comando di accesso ad Azure in IntelliJ][intellij-azure-login]

3. Nella finestra **Azure Sign In** (Accesso ad Azure) selezionare **Device Login** (Accesso dispositivo) e quindi fare clic su **Sign in** (Accedi) ([altre opzioni di accesso](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/sign-in-instructions)).

   ![Finestra di accesso ad Azure con l'accesso dispositivo selezionato][intellij-azure-popup]

4. Nella finestra di dialogo **Azure Device Login** (Accesso dispositivo Azure) fare clic su **Copy&Open** (Copia e apri).

   ![Finestra di dialogo di accesso ad Azure][intellij-azure-copycode]

5. Nel browser incollare il codice dispositivo (copiato facendo clic su **Copy&Open** nell'ultimo passaggio) e quindi fare clic su **Avanti**.

   ![Accesso al dispositivo nel browser][intellij-azure-link-ms-account]

6. Nella finestra di dialogo **Select Subscriptions** (Seleziona sottoscrizioni) selezionare le sottoscrizioni da usare e quindi fare clic su **OK**.

   ![Finestra di dialogo Seleziona sottoscrizioni][intellij-azure-login-select-subs]
   
## <a name="create-your-local-project"></a>Creare il progetto locale

In questa sezione si userà Azure Toolkit for IntelliJ per creare un progetto locale di Funzioni di Azure. Più avanti in questo articolo verrà pubblicato il codice della funzione in Azure. 

1. Aprire la finestra di dialogo iniziale di IntelliJ, selezionare *Create New Project* (Crea nuovo progetto) per aprire la creazione guidata di un nuovo progetto, quindi selezionare *Funzioni di Azure*.

    ![Creare un progetto di funzioni](media/functions-create-first-java-intellij/create-functions-project.png)

1. Selezionare *Http Trigger* (Trigger HTTP), quindi fare clic su *Avanti* e seguire la procedura guidata per esaminare tutte le configurazioni nelle pagine seguenti. Confermare il percorso del progetto, quindi fare clic su *Fine*. Intellj IDEA apre quindi il nuovo progetto.

    ![Completamento della creazione di un progetto di funzioni](media/functions-create-first-java-intellij/create-functions-project-finish.png)

## <a name="run-the-function-app-locally"></a>Eseguire l'app per le funzioni in locale

1. Passare a `src/main/java/org/example/functions/HttpTriggerFunction.java` per visualizzare il codice generato. Accanto alla riga *17* si noterà un pulsante verde *Run* (Esegui). Fare clic e quindi selezionare *Run 'azure-function-exam'* . Si vedrà l'app per le funzioni in esecuzione in locale con alcuni log.

    ![Progetto di funzioni in esecuzione in locale](media/functions-create-first-java-intellij/local-run-functions-project.png)

    ![Output delle funzioni in esecuzione in locale](media/functions-create-first-java-intellij/local-run-functions-output.png)

1. È possibile provare la funzione accedendo all'endpoint stampato dal browser, ad esempio `http://localhost:7071/api/HttpTrigger-Java?name=Azure`.

    ![Risultato del test delle funzioni in esecuzione in locale](media/functions-create-first-java-intellij/local-run-functions-test.png)

1. Il log viene anche stampato nella propria istanza di IDEA. A questo punto, arrestare la funzione facendo clic sul pulsante *stop*.

    ![Log di test delle funzioni in esecuzione in locale](media/functions-create-first-java-intellij/local-run-functions-log.png)

## <a name="debug-the-function-app-locally"></a>Eseguire il debug dell'app per le funzioni in locale

1. A questo punto, provare a eseguire il debug dell'app per le funzioni in locale. Fare clic sul pulsante *Debug* sulla barra degli strumenti. Se non viene visualizzato, fare clic su *View -> Appearance-> Toolbar* (Visualizza -> Aspetto -> Barra degli strumenti) per abilitare la barra degli strumenti.

    ![Pulsante debug per le funzioni in locale](media/functions-create-first-java-intellij/local-debug-functions-button.png)

1. Fare clic su riga *20* del file `src/main/java/org/example/functions/HttpTriggerFunction.java` per aggiungere un punto di interruzione, accedere nuovamente all'endpoint `http://localhost:7071/api/HttpTrigger-Java?name=Azure`. Si noterà che il punto di interruzione viene raggiunto. È possibile provare altre funzionalità di debug, ad esempio *step*, *watch*, *evaluation*. Arrestare la sessione di debug facendo clic sul pulsante stop.

    ![Interruzione delle funzioni con il debug in locale](media/functions-create-first-java-intellij/local-debug-functions-break.png)

## <a name="deploy-your-function-app-to-azure"></a>Distribuire l'app per le funzioni in Azure

1. Fare clic con il pulsante destro del mouse sul progetto nella finestra di esplorazione progetti IntelliJ, quindi scegliere *Azure -> Deploy to Azure Functions* (Azure -> Distribuisci in Funzioni di Azure)

    ![Distribuire le funzioni in Azure](media/functions-create-first-java-intellij/deploy-functions-to-azure.png)

1. Se non sono ancora disponibili app per le funzioni, fare clic su *No available function, click to create a new one* (Nessuna funzione disponibile, fare clic per crearne una).

    ![Creare un'app in cui distribuire le funzioni in Azure](media/functions-create-first-java-intellij/deploy-functions-create-app.png)

1. Digitare il nome dell'app per le funzioni e scegliere i valori appropriati per sottoscrizione, piattaforma, gruppo di risorse, piano di servizio app. È anche possibile creare qui un gruppo di risorse e/o un piano di servizio app. Quindi, mantenere invariate le impostazioni dell'app, fare clic su *OK* e attendere alcuni minuti che venga creata la nuova funzione. Al termine, l'indicatore di stato Dopo *Creating New Function App* (Creazione della nuova app per le funzioni in corso) scompare.

    ![Creazione guidata dell'app in cui distribuire le funzioni in Azure](media/functions-create-first-java-intellij/deploy-functions-create-app-wizard.png)

1. Selezionare l'app per le funzioni in cui eseguire la distribuzione. La nuova app per le funzioni appena creata verrà selezionata automaticamente. Fare clic su *Run* (Esegui) per distribuire le funzioni.

    ![Esecuzione della distribuzione di funzioni in Azure](media/functions-create-first-java-intellij/deploy-functions-run.png)

    ![Log della distribuzione di funzioni in Azure](media/functions-create-first-java-intellij/deploy-functions-log.png)

## <a name="manage-azure-functions-from-idea"></a>Gestire Funzioni di Azure da IDEA

1. È possibile gestire le funzioni con *Azure Explorer* nella propria istanza di IDEA. Fare clic su *App per le funzioni*. Verranno visualizzate tutte le proprie funzioni.

    ![Visualizzare le funzioni in Explorer](media/functions-create-first-java-intellij/explorer-view-functions.png)

1. Fare clic per selezionare una delle funzioni, quindi fare clic con il pulsante destro del mouse e scegliere *Mostra proprietà* per aprire la pagina dei dettagli. 

    ![Visualizzare le proprietà della funzione](media/functions-create-first-java-intellij/explorer-functions-show-properties.png)

1. Fare clic con il pulsante destro del mouse sulla funzione *HttpTrigger-Java* e scegliere *Funzione di trigger*. Il browser viene aperto all'URL del trigger.

    ![Esecuzione della distribuzione di funzioni in Azure](media/functions-create-first-java-intellij/explorer-trigger-functions.png)

## <a name="add-more-functions-to-the-project"></a>Aggiungere altre funzioni al progetto

1. Fare clic con il pulsante destro del mouse sul pacchetto *org.example.functions* e Scegliere *New -> Azure Function Class* (Nuovo -> Classe di funzioni di Azure). 

    ![Aggiungere funzioni al progetto](media/functions-create-first-java-intellij/add-functions-entry.png)

1. Immettere il nome della classe *HttpTest* e selezionare *HttpTrigger* nella creazione guidata classe di funzione, fare clic su *OK* per avviare la creazione. In questo modo è possibile creare le nuove funzioni come si preferisce.

    ![Aggiungere funzioni al progetto, selezionare trigger](media/functions-create-first-java-intellij/add-functions-trigger.png)
    
    ![Output dell'aggiunta di funzioni al progetto](media/functions-create-first-java-intellij/add-functions-output.png)

## <a name="cleaning-up-functions"></a>Pulizia delle funzioni

1. Eliminazione di Funzioni di Azure in Azure Explorer
      
      ![Aggiungere funzioni al progetto, selezionare trigger](media/functions-create-first-java-intellij/delete-function.png)
      

## <a name="next-steps"></a>Passaggi successivi

È stato creato un progetto di funzioni Java con una funzione attivata tramite HTTP. Il progetto è stato quindi eseguito in un computer locale e distribuito in Azure. A questo punto, estendere la funzione effettuando l'operazione seguente.

> [!div class="nextstepaction"]
> [Aggiungere un'associazione di output della coda di Archiviazione di Azure](/azure/azure-functions/functions-add-output-binding-storage-queue-java)


[marketplace]:./media/functions-create-first-java-intellij/marketplace.png
[intellij-azure-login]: media/functions-create-first-java-intellij/intellij-azure-login.png
[intellij-azure-popup]: media/functions-create-first-java-intellij/intellij-azure-login-popup.png
[intellij-azure-copycode]: media/functions-create-first-java-intellij/intellij-azure-login-copyopen.png
[intellij-azure-link-ms-account]: media/functions-create-first-java-intellij/intellij-azure-login-linkms-account.png
[intellij-azure-login-select-subs]: media/functions-create-first-java-intellij/intellij-azure-login-selectsubs.png
