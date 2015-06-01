<properties 
	pageTitle="Dashboard Power BI di Analisi dei flussi | Azure" 
	description="Informazioni su come popolare un dashboard Power BI dinamico con dati dal processo di Analisi dei flussi." 
	services="stream-analytics" 
	documentationCenter="" 
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="04/24/2015" 
	ms.author="jeffstok"/>
	
#Analisi dei flussi di Azure e Power BI: dashboard dinamico su analisi in tempo reale del flusso di dati

Uno dei casi di uso comune di Analisi dei flussi di Azure è quello di analizzare un flusso di dati di elevato volume in tempo reale e di ottenere una visione approfondita in un dashboard dinamico (un dashboard che si aggiorna in tempo reale senza che l’utente debba aggiornare il browser). [Microsoft Power BI](https://powerbi.com/) è perfetto per creare dashboard dinamici in poco tempo. [Di seguito è riportato un video di esempio per illustrare lo scenario](https://www.youtube.com/watch?v=SGUpT-a99MA). Nel presente articolo viene illustrato come utilizzare Power BI come output per il processo di analisi dei flussi di Azure. Nota- Analisi dei flussi di Azure solitamente è disponibile in ogni caso a questo punto, l’output di Power BI è una funzionalità di anteprima di Analisi dei flussi di Azure.

##Prerequisiti

* Account Microsoft Azure tramite ID organizzazione (Power BI funziona solo con ID organizzazione. L’ID organizzazione è l’indirizzo di posta elettronica aziendale o dell’ufficio, ad esempio xyz@mycompany.com. E-mail personali come xyz@hotmail.com non sono ID organizzazione. [Ulteriori informazioni sull’ID organizzazione sono disponibili qui](https://www.arin.net/resources/request/org.html)).
* Un flusso di input per processo ASA (Azure Stream Analytics, Analisi dei flussi di Azure) da cui utilizzare il flusso di dati. A questo punto, ASA accetta input da un hub eventi di Azure o Archivio BLOB di Azure.  

##Creare processi di Analisi dei flussi di Azure

Dal [portale di Azure](https://manage.windowsazure.com), fare clic su **Nuovo, Servizi dati, Analisi dei flussi, Creazione rapida**.

Specificare i valori seguenti, quindi fare clic su **Crea processo di analisi dei flussi**:

* **Nome processo**: immettere un nome per il processo. Ad esempio, **DeviceTemperatures**.
* **Area**: selezionare l’area in cui collocare il processo. Considerare la possibilità di collocare il processo e l’hub eventi nella stessa area per garantire prestazioni ottimali ed evitare di incorrere in costi di trasferimento dei dati tra le aree.
* **Account di archiviazione**: scegliere l'account di archiviazione da usare per archiviare i dati di monitoraggio per tutti i processi di Analisi dei flussi in esecuzione all'interno dell'area scelta. È possibile scegliere un account di archiviazione esistente o crearne uno nuovo.

Fare clic su **Analisi dei flussi** nel riquadro sinistro per visualizzare un elenco dei processi di Analisi dei flussi.

![graphic1][graphic1]

> [AZURE.TIP]Il nuovo processo verrà visualizzato nell'elenco con lo stato **Non avviato**. Si noti che il pulsante **Avvia** nella parte inferiore della pagina è disabilitato. Questo è un comportamento previsto, poiché è necessario configurare input, output, query del processo e così via, prima di avviare il processo.

##Specificare l'input del processo

Per questa esercitazione, si presuppone che l’utente usi EventHub come input con serializzazione JSON e codifica utf-8.

* Fare clic sul nome del processo.
* Fare clic su **Input** nella parte superiore della pagina, quindi scegliere **Aggiungi input**. La finestra di dialogo visualizzata presenterà un numero di passaggi per l'impostazione dell'input.
*	Selezionare **Flusso dati**, quindi fare clic sul pulsante a destra.
*	Selezionare **Hub eventi**, quindi fare clic sul pulsante a destra.
*	Digitare o selezionare i valori seguenti nella terza pagina:
  *	**Alias dell’input** - Immettere un nome descrittivo per l'input del processo. Si noti che il nome verrà usato nella query in un secondo momento.
  * **Hub eventi** - Se l'hub eventi creato si trova nella stessa sottoscrizione del processo di Analisi dei flussi, selezionare lo spazio dei nomi in cui si trova l'hub eventi.
*	Se l'hub eventi si trova in un'altra sottoscrizione, selezionare **Usa hub eventi da un'altra sottoscrizione**, quindi immettere manualmente i valori per **Spazio dei nomi del bus di servizio**, **Nome hub eventi**, **Nome criterio hub eventi**, **Chiave criterio hub eventi** e **Conteggio partizioni hub eventi**.

> [AZURE.NOTE]Questo esempio usa il numero predefinito di partizioni, che è pari a 16.

* **Nome hub eventi** - Selezionare il nome dell’hub eventi di Azure disponibile.
* **Nome criterio hub eventi** - Selezionare il criterio hub eventi per l’eventhub usato. Assicurarsi che il criterio disponga di autorizzazioni di gestione.
*	**Gruppo utenti hub eventi** – È possibile lasciare il campo vuoto o specificare un gruppo di utenti disponibile sull’hub eventi. Tenere presente che ciascun gruppo di utenti di un hub eventi può avere solo 5 lettori alla volta. Pertanto, è necessario scegliere il gruppo di utenti appropriato al processo. Se si lascia il campo vuoto, verrà utilizzato il gruppo di utenti predefinito.

*	Fare clic sul pulsante a destra.
*	Specificare i valori seguenti:
  *	**Formato serializzatore eventi** - JSON
  *	**Codifica** - UTF8
*	Fare clic sul pulsante con il segno di spunta per aggiungere l'origine e per verificare che Analisi dei flussi possa connettersi all'hub eventi.

##Aggiungere l’output Power BI

1.  Fare clic su **Output** nella parte superiore della pagina, quindi scegliere **Aggiungi output**. Verrà visualizzato Power BI come opzione di output nell’elenco.

![graphic2][graphic2]

> [AZURE.NOTE]Nota - L’output Power BI è disponibile solo per gli account Azure tramite ID organizzazione. Se non si usa un ID organizzazione per l’account Azure (ad esempio, un account Live ID o un account Microsoft personale), non verrà visualizzata l’opzione di output Power BI.

2.  Selezionare **Power BI**, quindi fare clic sul pulsante destro.
3.  Verrà visualizzata una schermata analoga a quella seguente:

![graphic3][graphic3]

4.  In questo passaggio è necessario prestare attenzione a usare lo stesso ID organizzazione usato per il processo ASA. A questo punto, l’output Power BI deve usare lo stesso ID organizzazione usato per il processo ASA. Se si dispone già di un account Power BI, selezionare “Autorizza ora” usando lo stesso ID organizzazione. Altrimenti, scegliere “Registrati ora” e usare lo stesso ID organizzazione dell’account Azure durante la registrazione a Power BI. [In questo blog sono riportati passo passo i dettagli di registrazione a Power BI](http://blogs.technet.com/b/powerbisupport/archive/2015/02/06/power-bi-sign-up-walkthrough.aspx).
5.  Successivamente verrà visualizzata una schermata analoga a quella seguente:

![graphic4][graphic4]

Fornire i valori come mostrato di seguito:

* **Alias dell’output** – È possibile usare qualsiasi alias dell’output purché sia di facile riferimento. L’alias dell’output risulta essere particolarmente utile se si decide di avere più output per il processo. In tal caso, è necessario fare riferimento a questo output nella query. Ad esempio, utilizziamo il valore dell’alias di output = “OutPbi”.
* **Nome set di dati** - Fornire un nome set di dati all’output Power BI. Ad esempio, “pbidemo”.
*	**Nome tabella** - Fornire un nome tabella nel set di dati dell’output Power BI. Ad esempio, “pbidemo”. Attualmente, l’output Power BI da processi ASA può avere solo una tabella in un set di dati.

>	[AZURE.NOTE] Note - You should not explicitly create this dataset and table in your Power BI account. They will be automatically created when you start your ASA job and the job starts pumping output into Power BI. If your ASA job query doesn’t return any results, the dataset and table will not be created.

*	Fare clic su **OK**, **Connessione di test**. A questo punto la configurazione dell’output è completa.

>	[AZURE.WARNING] Also be aware that if Power BI already had a dataset and table with the same name as the one you provided in this ASA job, the existing data will be overwritten.


##Scrivere query

Andare nella scheda **Query** del processo. Scrivere la query, l’output che si desidera avere in Power BI. Ad esempio, potrebbe essere simile alla query SQL seguente:

    SELECT
    	MAX(hmdt) AS hmdt,
    	MAX(temp) AS temp,
    	System.TimeStamp AS time,
    	dspl
    INTO
        OutPBI
    FROM
    	Input TIMESTAMP BY time
    GROUP BY
    	TUMBLINGWINDOW(ss,1),
    	dspl

    
    
Avviare il processo. Confermare che l’hub eventi riceve eventi e che la query genera i risultati previsti. Se la query produce 0 righe, il set di dati e le tabelle Power BI non verranno creati automaticamente.

##Creare il dashboard in Power BI

Andare al sito [Powerbi.com](https://powerbi.com) e accedere con l’ID organizzazione. Se la query del processo ASA produce risultati, si visualizzerà il set di dati già creato:

![graphic5][graphic5]

Per creare il dashboard, andare all’opzione Dashboard e creare un nuovo dashboard.

![graphic6][graphic6]

In questo esempio, verrà nominato "Demo Dashboard".

A questo punto, fare clic sul set di dati creato dal processo ASA (pbidemo nell’esempio corrente). Si verrà indirizzati a una pagina per creare un grafico nella parte superiore di questo set di dati. Il seguente è solo un esempio dei report che è possibile creare:

Selezionare i campi e Σ temp e ora. Questi saranno automaticamente utilizzati in Valore e Asse del grafico:

![graphic7][graphic7]

In questo modo, si otterrà automaticamente un grafico come riportato:

![graphic8][graphic8]

Nella sezione relativa, fare clic sull’elenco a discesa per il valore temp e scegliere **media** per la temperatura e sul grafico, fare clic su **visualizzazione** e scegliere **grafico a linee**:

![graphic9][graphic9]

A questo punto si otterrà un grafico a linee della media nel tempo. Usando l’opzione di aggiunta come riportato, è possibile aggiungere l’elemento al dashboard creato in precedenza:

![graphic10][graphic10]

A questo punto, quando si visualizza il dashboard con il report aggiunto, si vedrà l’aggiornamento dei report in tempo reale. Provare a modificare i dati negli eventi – aumentare il valore temp o analogo e si visualizzerà in tempo reale l’effetto riportato sul dashboard.

Tenere presente che in questa esercitazione è stato mostrato come creare solo uno dei tipi di grafici possibili per un set di dati. Con Power BI le possibilità sono illimitate. Per visualizzare un altro esempio di dashboard Power BI, guardare il video [Introduzione a Power BI](https://youtu.be/L-Z_6P56aas?t=1m58s).

Un’altra risorsa utile per ulteriori informazioni su come creare dashboard con Power BI è [Dashboard nell’anteprima Power BI](http://support.powerbi.com/knowledgebase/articles/424868-dashboards-in-power-bi-preview).

## Ottenere aiuto
Per ulteriore assistenza, provare il [Forum di Analisi dei flussi di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## Passaggi successivi

- [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
- [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-get-started.md)
- [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
- [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Informazioni di riferimento sulle API REST di gestione di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)


[graphic1]: ./media/stream-analytics-power-bi-dashboard/1-stream-analytics-power-bi-dashboard.png
[graphic2]: ./media/stream-analytics-power-bi-dashboard/2-stream-analytics-power-bi-dashboard.png
[graphic3]: ./media/stream-analytics-power-bi-dashboard/3-stream-analytics-power-bi-dashboard.png
[graphic4]: ./media/stream-analytics-power-bi-dashboard/4-stream-analytics-power-bi-dashboard.png
[graphic5]: ./media/stream-analytics-power-bi-dashboard/5-stream-analytics-power-bi-dashboard.png
[graphic6]: ./media/stream-analytics-power-bi-dashboard/6-stream-analytics-power-bi-dashboard.png
[graphic7]: ./media/stream-analytics-power-bi-dashboard/7-stream-analytics-power-bi-dashboard.png
[graphic8]: ./media/stream-analytics-power-bi-dashboard/8-stream-analytics-power-bi-dashboard.png
[graphic9]: ./media/stream-analytics-power-bi-dashboard/9-stream-analytics-power-bi-dashboard.png
[graphic10]: ./media/stream-analytics-power-bi-dashboard/10-stream-analytics-power-bi-dashboard.png

<!--HONumber=52-->
