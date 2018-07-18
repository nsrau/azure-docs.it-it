---
title: Aggiungere dati di LUIS ad Application Insights usando C#| Microsoft Docs
titleSuffix: Azure
description: Creare un bot integrato con un'applicazione LUIS e Application Insights usando C#.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/07/2018
ms.author: v-geberr
ms.openlocfilehash: 52b6ae224b0e8da12eb4903f5100a6e5cc39704d
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "35378839"
---
# <a name="add-luis-results-to-application-insights-from-a-web-app-bot"></a>Aggiungere i risultati LUIS ad Application Insights da un bot app Web
Questa esercitazione aggiunge le informazioni relative a risposte LUIS all'archivio dei dati di telemetria di [Application Insights](https://azure.microsoft.com/services/application-insights/). Quando si dispone dei dati, è possibile eseguire query su di essi con il linguaggio Kusto o Power BI per analizzare, aggregare e registrare le finalità e le entità delle espressioni in tempo reale. Questa analisi consente di determinare se è necessario aggiungere o modificare le finalità e le entità dell'app LUIS.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
* Aggiungere Application Insights a un bot app Web
* Acquisire i risultati di query LUIS e inviarli ad Application Insights
* Eseguire query su Application Insights per finalità principale, punteggio ed espressione

## <a name="prerequisites"></a>prerequisiti

* Il bot app Web LUIS dell'**[esercitazione precedente](luis-csharp-tutorial-build-bot-framework-sample.md)** con Application Insights è attivato. 
* [Visual Studio 2017](https://www.visualstudio.com/downloads/) è installato localmente nel computer.

> [!Tip]
> Se non si ha già una sottoscrizione, è possibile registrarsi per ottenere un [account gratuito](https://azure.microsoft.com/free/).

Tutto il codice di questa esercitazione è disponibile nel [repository GitHub degli esempi LUIS](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/csharp) e ogni riga associata a questa esercitazione ha il commento `//LUIS Tutorial:`. 

## <a name="review-luis-web-app-bot"></a>Revisione di un bot di app Web LUIS
Questa esercitazione presuppone che si disponga di codice simile a quello riportato di seguito o che sia stata completata l'[altra esercitazione](luis-csharp-tutorial-build-bot-framework-sample.md): 

   [!code-csharp[Web app bot with LUIS](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs "Web app bot with LUIS")]

## <a name="application-insights-in-web-app-bot"></a>Application Insights nel bot dell’app Web
Attualmente, il servizio Application Insights, aggiunto come parte della creazione del servizio bot dell’app Web, raccoglie dati generali di telemetria relativa allo stato per il bot. Non raccoglie informazioni relative alla risposta LUIS. Per analizzare e migliorare LUIS, è necessario ottenere le informazioni sulla risposta LUIS.  

Per acquisire le risposte LUIS, il bot app Web richiede che **[Application Insights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/)** sia installato e configurato per il progetto. 

## <a name="download-web-app-bot"></a>Scarica bot dell’app Web
Utilizzare [Visual Studio 2017](https://www.visualstudio.com/downloads/) per aggiungere e configurare Application Insights per bot dell’app Web. Per utilizzare il bot dell’app Web in Visual Studio, scaricare il codice bot dell’app Web.

1. Nel portale Azure, per il bot dell’app Web, selezionare **Compila**.

    ![Selezionare Compila nel portale](./media/luis-tutorial-bot-csharp-appinsights/download-build-menu.png)

2. Selezionare **file zip di Download** e attendere che il file sia pronto.

    ![Scaricare il file zip](./media/luis-tutorial-bot-csharp-appinsights/download-link.png)

3. Selezionare **Scarica file ZIP** nella finestra popup. Ricordare il percorso sul computer di cui si avrà bisogno nella sezione successiva.

    ![Popup Scarica il file ZIP](./media/luis-tutorial-bot-csharp-appinsights/download-popup.png)

## <a name="open-solution-in-visual-studio-2017"></a>Aprire la soluzione in Visual Studio 2017

1. Estrarre il file in una cartella. 

2. Aprire Visual Studio 2017 e il file della soluzione, `Microsoft.Bot.Sample.LuisBot.sln`. Se viene visualizzato l'avviso di sicurezza, scegliere "OK".

    ![Aprire la soluzione in Visual Studio 2017](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-security-warning.png)

3. Visual Studio necessita dell’aggiunta di dipendenze alla soluzione. In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **Riferimenti** e scegliere **Gestisci pacchetti NuGet**. 

    ![Manage NuGet packages](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-manage-nuget-packages.png)

4. Gestione pacchetti NuGet mostra un elenco dei pacchetti installati. Selezionare **Ripristina** nella barra gialla. Attendere il completamento del processo di ripristino.

    ![Ripristino dei pacchetti NuGet](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-restore-packages.png)

## <a name="add-application-insights-to-the-project"></a>Aggiungere Application Insights al progetto
Installare e configurare Application Insights in Visual Studio. 

1. In Visual Studio 2017, nel menu principale, selezionare **Progetto**, quindi selezionare **Aggiungi Application Insights Telemetry**.

2. Nella finestra **Configurazione Application Insights**, selezionare **Inizia gratis**

    ![Avviare la configurazione di Application Insights](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-configure-app-insights.png)

3. Registra l'app con Application Insights. È possibile immettere le credenziali del portale di Azure. 

4. Visual Studio aggiunge Application Insights al progetto, visualizzandone lo stato. 

    ![Stato di Application Insights](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-adding-application-insights-to-project.png)

    Al termine del processo, **Configurazione di Application Insights** mostra lo stato di avanzamento. 

    ![Stato di avanzamento di Application Insights](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-configured-application-insights-to-project.png)

    **Abilita la raccolta delle analisi** è di colore rosso, vale a dire che non è abilitato. Questa esercitazione non usa la funzionalità. 

## <a name="build-and-resolve-errors"></a>Compilazione e risoluzione di errori

1. Compilare la soluzione selezionando il menu **Compila**, quindi selezionare **Ricompila soluzione**. Attendere il completamento del processo di compilazione. 

2. Se la compilazione non riesce con `CS0104` errori, è necessario correggerli. Nella cartella `Controllers`, all’interno di `MessagesController.cs file`, correggere l'uso ambiguo del tipo `Activity` aggiungendo il tipo di attività al tipo di connettore. A tale scopo, modificare il nome `Activity` sulle linee 22 e 36 da `Activity` a `Connector.Activity`. Compilare nuovamente la soluzione. Non dovrebbero più esserci errori di compilazione.

    Il codice sorgente completo del file è:

    [!code-csharp[MessagesController.cs file](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/csharp/MessagesController.cs "MessagesController.cs file")]

## <a name="publish-project-to-azure"></a>Pubblicare il progetto in Azure
Il pacchetto **Application Insights** è ora nel progetto e configurato correttamente per le credenziali nel portale Azure. Le modifiche al progetto devono essere pubblicate di nuovo in Azure.

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul nome del progetto e scegliere **Pubblica**.

    ![Pubblicazione del progetto sul portale](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-publish.png)

2. Nella finestra **Pubblica**, selezionare **Crea nuovo profilo**.

    ![Pubblicazione del progetto sul portale](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-publish-1.png)

3. Selezionare **Importa profilo**e selezionare **OK**.

    ![Pubblicazione del progetto sul portale](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-publish-2.png)

4. Nelle finestre **Importa file di impostazioni di pubblicazione**, accedere alla cartella del progetto, quindi alla cartella `PostDeployScripts`, selezionare il file che termina in `.PublishSettings` e selezionare `Open`. Ora è stata configurata la pubblicazione per questo progetto. 

5. Pubblicare il codice sorgente locale per il servizio bot selezionando il pulsante **Pubblica**. Nella finestra **Output** viene visualizzato lo stato. Il resto dell'esercitazione viene completato nel portale di Azure. Chiudere Visual Studio 2017. 

## <a name="open-three-browser-tabs"></a>Aprire tre schede del browser
Nel portale Azure, cercare il bot dell’app Web e aprirlo. Il passaggio seguente utilizza tre viste diverse per i bot dell’app Web. Potrebbe essere più facile avere tre schede separate aperte nel browser: 
  
>  * Test nella chat Web
>  * Compila/Apri editor di codici online -> Editor del servizio app
>  * Editor del servizio app/Apri console Kudu -> Console diagnostica

## <a name="modify-basicluisdialogcs-code"></a>Modificare il codice BasicLuisDialog.cs

1. Nella scheda del browser dell'**Editor del servizio app** aprire il file `BasicLuisDialog.cs`.

2. Aggiungere le seguenti dipendenze NuGet sotto le righe `using` esistenti:

   [!code-csharp[Add using statement](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/csharp/BasicLuisDialog.cs?range=11-12 "Add using statement")]

3. Aggiungere la funzione `LogToApplicationInsights`:

   [!code-csharp[Add the LogToApplicationInsights function](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/csharp/BasicLuisDialog.cs?range=61-92 "Add the LogToApplicationInsights function")]

    La chiave di strumentazione di Application Insights è già nell’impostazione applicazione del bot dell’app Web denominata `BotDevInsightsKey`. 

    L'ultima riga della funzione aggiunge ad Application Insights. Il nome della traccia è `LUIS`, un nome univoco in aggiunta a tutti gli altri dati di telemetria raccolti da questo bot app Web. Tutte le proprietà dispongono anche di `LUIS_`, in modo da poter vedere quali dati questo tutorial aggiunge rispetto alle informazioni fornite dal bot dell’app Web.

4. Richiamare la funzione `LogToApplicationInsights` nella parte superiore della funzione `ShowLuisResult`:

   [!code-csharp[Use the LogToApplicationInsights function](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/csharp/BasicLuisDialog.cs?range=114-115 "Use the LogToApplicationInsights function")]

## <a name="build-web-app-bot"></a>Compilare il bot dell’app Web
1. Il bot dell’app Web può essere compilato in uno dei due modi seguenti. Il primo metodo consiste nel fare clic con il pulsante destro del mouse su `build.cmd` in **Editor del servizio app**, selezionando quindi **Esegui dalla console**. Viene visualizzato l'output della console, che termina con `Finished successfully.`

2. Se questa operazione non viene completata correttamente, è necessario aprire la console, cercare lo script ed eseguirlo seguendo i seguenti passaggi. In **Editor del servizio app**, nella barra blu in alto, selezionare il nome del bot, quindi selezionare **Apri console Kudu** nell'elenco a discesa.

    ![Aprire la console Kudu](./media/luis-tutorial-bot-csharp-appinsights/open-kudu-console.png)

3. Nella finestra della console inserire il comando seguente:

    ```
    cd site\wwwroot && build.cmd
    ```

    Attendere il completamento del processo di compilazione con `Finished successfully.`

## <a name="test-the-web-app-bot"></a>Testare il bot dell'app Web

1. Per testare il bot app Web, aprire la funzionalità **Testa nella chat Web** nel portale. 

2. Immettere la frase `Coffee bar on please`.  

    ![Testare un bot dell’app Web in una chat](./media/luis-tutorial-bot-csharp-appinsights/test-in-web-chat.png)

3. Non sarà visibile alcuna differenza nella risposta del chatbot. La modifica consiste nell'invio di dati a Application Insights, non nelle risposte bot. Immettere alcune espressioni in più in modo da avere più di dati in Application Insights:

```
Please deliver a pizza
Turn off all the lights
Turn on the hall light
```

## <a name="view-luis-entries-in-application-insights"></a>Visualizzare le voci LUIS in Application Insights
Aprire Application Insights per visualizzare le voci LUIS. 

1. Nel portale selezionare **Tutte le risorse** e quindi filtrare il base al nome del bot app Web. Fare clic sulla risorsa con il tipo **Application Insights**. L'icona di Application Insights è una lampadina. 

    ![Cercare Application Insights](./media/luis-tutorial-bot-csharp-appinsights/portal-service-list-app-insights.png)

2. Quando la risorsa viene aperta, fare clic sull'icona **Cerca** a forma di lente di ingrandimento nel pannello di destra. Verrà visualizzato un altro pannello a destra. A seconda della quantità di dati di telemetria trovati, la visualizzazione del pannello potrebbe richiedere un attimo. Cercare `LUIS`. L'elenco viene ristretto ai soli risultati delle query LUIS aggiunti in questa esercitazione.

    ![Cerca tracce](./media/luis-tutorial-bot-csharp-appinsights/portal-service-list-app-insights-search-luis-trace.png)

3. Selezionare la voce principale. Verrà visualizzata a nuova finestra con dati più dettagliati, tra cui i dati personalizzati per la query LUIS all'estrema destra. I dati includono la finalità principale e il relativo punteggio.

    ![Revisionare l’elemento di traccia](./media/luis-tutorial-bot-csharp-appinsights/portal-service-list-app-insights-search-luis-trace-item.png)

    Al termine, fare clic su **X** in alto a destra per tornare all'elenco di elementi di dipendenza. 


> [!Tip]
> Se si desidera salvare l'elenco di dipendenze e tornarvi in un secondo momento, fare clic su **...Altro** e quindi su **Salva preferito**.

## <a name="query-application-insights-for-intent-score-and-utterance"></a>Eseguire query su Application Insights per finalità, punteggio ed espressione
Application Insights consente di eseguire query sui dati con il linguaggio [Kusto](https://docs.microsoft.com/azure/application-insights/app-insights-analytics#query-data-in-analytics), oltre che esportare i dati in [PowerBI](https://powerbi.microsoft.com). 

1. Fare clic su **Analisi** nella parte superiore dell'elenco di dipendenze, sopra la casella di filtro. 

    ![Pulsante Analytics](./media/luis-tutorial-bot-csharp-appinsights/portal-service-list-app-insights-search-luis-analytics-button.png)

2. Verrà aperta una nuova finestra con una finestra di query nella parte superiore e una finestra di tabella di dati al di sotto. Per chi ha già usato database in precedenza, questa disposizione risulta familiare. La query include tutti gli elementi delle ultime 24 ore che iniziano con il nome `LUIS`. La colonna **CustomDimensions** contiene i risultati delle query LUIS sotto forma di coppie nome/valore.

    ![Report analisi predefinito](./media/luis-tutorial-bot-csharp-appinsights/analytics-query-1.png)

3. Per estrarre finalità principale, punteggio ed espressione, aggiungere quanto segue sopra l'ultima riga nella finestra di query:

    ```SQL
    | extend topIntent = tostring(customDimensions.LUIS_topScoringIntent)
    | extend score = todouble(customDimensions.LUIS_topScoringIntentScore)
    | extend utterance = tostring(customDimensions.LUIS_query)
    ```

4. Eseguire la query. Scorrere verso destra nella tabella di dati. Saranno disponibili le nuove colonne relative a finalità principale, punteggio ed espressione. Fare clic sulla colonna relativa alla finalità principale per applicare l'ordinamento.

    ![Report analisi personalizzato](./media/luis-tutorial-bot-csharp-appinsights/analytics-query-2.png)


Leggere altre informazioni sul [linguaggio di query Kusto](https://docs.loganalytics.io/docs/Learn/Getting-Started/Getting-started-with-queries) o su come [esportare i dati in PowerBi](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi). 


## <a name="learn-more-about-bot-framework"></a>Ulteriori informazioni su Bot Framework
Ulteriori informazioni su [Bot Framework](https://dev.botframework.com/).

## <a name="next-steps"></a>Passaggi successivi

Tra le altre informazioni che è possibile aggiungere ai dati di Application Insights ci sono ID app, ID versione, data dell'ultima modifica del modello, data dell'ultimo training, data dell'ultima pubblicazione. Questi valori possono essere recuperati dall'URL endpoint (ID app e ID versione) o da una chiamata a un'[API di creazione](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3d) e quindi impostati nelle impostazioni del bot app Web e ricavati da lì.  

Se si sta usando la stessa sottoscrizione di endpoint per più app LUIS, è anche necessario includere l'ID sottoscrizione e una proprietà che indica che si tratta di una chiave condivisa. 

> [!div class="nextstepaction"]
> [Altre informazioni sulle espressioni di esempio](luis-how-to-add-example-utterances.md)
