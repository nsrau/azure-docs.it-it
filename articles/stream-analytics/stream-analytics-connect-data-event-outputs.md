<properties 
	pageTitle="Creare output di analisi di flusso | Microsoft Azure" 
	description="Informazioni su come effettuare la connessione e configurare le destinazioni di output per le soluzioni di analisi di flusso." 
	documentationCenter="" 
	services="stream-analytics"
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="10/05/2015" 
	ms.author="jeffstok"/>

# Creare output di analisi di flusso

## Informazioni sugli output di analisi di flusso ##
---
Quando si crea un processo di analisi di flusso, una delle considerazioni da fare riguarda l'utilizzo dell'output del processo. In che modo i consumer della trasformazione dei dati visualizzeranno i risultati del processo di analisi di flusso? Quali strumenti useranno per analizzare l'output? La conservazione dei dati o il data warehouse è un requisito?

L'analisi di flusso di Azure offre sette metodi diversi per archiviare e visualizzare gli output del processo. Database SQL, archivio BLOB, Hub eventi, code del bus di servizio, argomenti del bus di servizio, Power BI e archivio tabelle. In questo modo è possibile visualizzare facilmente l'output del processo e si ottiene flessibilità nell'utilizzo e nell'archiviazione dell'output del processo per il data warehouse e altri scopi.

## Uso di un database SQL come output ##
---
Un database SQL può essere usato come output per i dati di natura relazionale o per applicazioni che dipendono dal contesto ospitato in un database relazionale. Per altre informazioni sui database SQL di Azure, vedere [Database SQL di Azure](http://azure.microsoft.com/services/sql-database/).

### Parametri ###

Per iniziare a usare un database SQL, saranno necessarie le informazioni seguenti sul database SQL:

1. Server Name
2. Nome database
3. Una combinazione nome utente/password valida
4. Nome tabella di output. Questa tabella deve esistere già perché non verrà creata dal processo.

### Aggiunta del database SQL come output ###

![graphic1][graphic1]

Passare alla scheda di output del processo, fare clic sul pulsante **AGGIUNGI OUTPUT** e fare clic su Avanti.

![graphic2][graphic2]

Scegliere **Database SQL** come output.

![graphic3][graphic3]

Immettere le informazioni del database nella pagina successiva. L'alias dell'output è un nome descrittivo usato nelle query per indirizzare l'output delle query a questo database. L'alias è predefinito solo se un unico output è presente come "output".

![graphic4][graphic4]

Se il database esiste nella stessa sottoscrizione del processo di analisi di flusso, è disponibile l'opzione "Usare il database SQL dalla sottoscrizione corrente". Selezionare quindi il database nell'elenco a discesa.

![graphic5][graphic5]

Fare clic su Avanti per aggiungere questo output. Vengono avviate due operazioni, di cui la prima consiste nell'aggiungere l'output.

![graphic6][graphic6]

La seconda operazione è il test della connessione. L'analisi di flusso di Azure tenterà di connettersi al database SQL e di verificare che le credenziali immesse siano corrette e che la tabella sia accessibile.

## Uso dell'archivio BLOB come output ##
---
Per un'introduzione all'archivio BLOB di Azure e al relativo utilizzo, vedere la documentazione in [Come usare i BLOB](./articles/storage/storage-dotnet-how-to-use-blobs.md).

### Parametri ###

Per iniziare a usare un output di archiviazione BLOB, saranno necessarie le informazioni seguenti:

1. Se l'account di archiviazione è in una sottoscrizione diversa da quella del processo di flusso, verranno visualizzati i campi in cui immettere il nome dell'account di archiviazione e la chiave dell'account di archiviazione.
2. Nome del contenitore.
3. Prefisso del nome file.
4. Formato di serializzazione utilizzato per i dati (Avro, CSV, JSON).

### Aggiunta dell'archivio BLOB come output ###

Selezionare l'output per l'archivio BLOB.

![graphic20][graphic20]

Immettere quindi i dettagli come mostrato di seguito:

![graphic21][graphic21]

## Uso di un Hub eventi come output ##
---
### Panoramica ###
 
Gli Hub eventi sono un ingestor di evento estremamente scalabile e sono in genere il modo più comune per l'ingresso dei dati nell'analisi flusso. Grazie alla gestione affidabile di grandi quantità di eventi, sono anche ideali per l'output dei processi. Un uso di un Hub eventi come output si verifica quando l'output di un processo di analisi di flusso rappresenta l'input di un altro processo di flusso. Per altri dettagli sugli Hub eventi, visitare il portale di [Hub eventi](https://azure.microsoft.com/services/event-hubs/ "Hub eventi").
 
### Parametri ###

Per configurare i flussi dei dati dell'Hub eventi, sono necessari alcuni parametri.

1. Spazio dei nomi Service Bus: Spazio dei nomi Service Bus dell'Hub eventi. Uno spazio dei nomi Service Bus è un contenitore per un set di entità di messaggistica. Quando si crea un nuovo Hub eventi, viene creato anche uno spazio dei nomi Service Bus. 
2. Nome Hub eventi: nome dell'Hub eventi. È il nome specificato al momento della creazione di un nuovo Hub eventi. 
3. Nome criterio Hub eventi: il nome del criterio di accesso condiviso per l'accesso all'Hub eventi. Per un Hub eventi nella scheda Configura è possibile configurare criteri di accesso condiviso. Ogni criterio di accesso condiviso dispone di un nome, delle autorizzazioni impostate, e di tasti di scelta generati.
4. Chiave di criterio Hub eventi: la chiave principale o secondaria del criterio di accesso condiviso per l'accesso all'Hub eventi.  
5. Colonna chiave di partizione: parametro facoltativo per gli output dell'Hub eventi. Questa colonna contiene la chiave di partizione per l'output dell'Hub eventi.

### Aggiunta di un Hub eventi come output ###

1. Fare clic su **Output** nella parte superiore della pagina, quindi scegliere **Aggiungi output**. Selezionare Hub eventi come opzione per l'output e fare clic sul pulsante a destra nella parte inferiore della finestra.

    ![graphic38][graphic38]

2. Immettere nei campi le informazioni pertinenti per l'output e al termine fare clic sul pulsante a destra nella parte inferiore della finestra per continuare.

    ![graphic36][graphic36]

3. Verificare che il formato di serializzazione degli eventi, il tipo di codifica e il formato siano impostati sui valori appropriati e fare clic sulla casella di controllo per completare l'azione.

    ![graphic37][graphic37]

## Uso di Power BI come output ##
---
### Panoramica ###
Power BI può essere utilizzato come output per un processo di analisi di flusso per offrire agli utenti dell'analisi di flusso un'esperienza di visualizzazione avanzata. Questa funzionalità può essere utilizzata per i dashboard operativi, la generazione di report e la creazione di report basati sulle metriche. Per altre informazioni su Power BI, visitare il sito [Power BI](https://powerbi.microsoft.com/).

### Parametri ###

Per configurare un output di Power BI, sono necessari alcuni parametri.

1. Alias di output: qualsiasi nome descrittivo di alias di output a cui fare riferimento facilmente. L'alias di output risulta particolarmente utile se si decide di avere più output per un processo. In questo caso, la query farà riferimento all'alias. Ad esempio, usare il valore dell'alias di output = "OutPbi".
2. Nome set di dati: immettere un nome per il set di dati che dovrà essere usato dall'output di Power BI. Ad esempio, "pbidemo".
2. Nome tabella: immettere un nome per la tabella nel set di dati dell'output di Power BI. Ad esempio, "pbidemo". **Attualmente, l’output di Power BI da processi di Analisi di flusso può avere solo una tabella in un set di dati.**

### Aggiunta di Power BI come output ###

1.  Fare clic su **Output** nella parte superiore della pagina, quindi scegliere **Aggiungi output**. Selezionare Power BI come opzione di output.

    ![graphic22][graphic22]

2.  Viene presentata una schermata simile a quella seguente:

    ![graphic23][graphic23]

3.  In questo passaggio immettere l'account aziendale o dell'istituto di istruzione per autorizzare l'output di Power BI. Se non si è già iscritti a Power BI, scegliere **Iscriviti ora**.
4.  Verrà quindi presentata una schermata simile a quella seguente:

    ![graphic24][graphic24]


>	[AZURE.NOTE] One should not explicitly create the dataset and table in the Power BI dashboard. The dataset and table will be automatically populated when the job is started and the job starts pumping output into Power BI. Note that if the job query doesn’t return any results, the dataset and table will not be created. Also be aware that if Power BI already had a dataset and table with the same name as the one provided in this Stream Analytics job, the existing data will be overwritten.

*	Fare clic su **OK**, **Connessione di test**. A questo punto la configurazione dell'output è completa.


## Uso dell'archivio tabelle di Azure per un output ##
---
Il servizio di archiviazione tabelle offre un archivio a disponibilità elevata e altamente scalabile, per consentire la scalabilità automatica di un'applicazione in base alle richieste degli utenti. L'archivio tabelle è l'archivio di chiavi/attributi NoSQL di Microsoft che è possibile sfruttare per i dati strutturati con meno vincoli allo schema. Archivio tabelle di Azure consente di archiviare i dati per il salvataggio permanente e il recupero efficiente. Per altre informazioni sull'archivio tabelle di Azure, vedere [Archiviazione tabelle di Azure](./articles/storage/storage-introduction.md).

### Parametri ###

Per iniziare a usare un archivio tabelle di Azure, sono necessarie le informazioni seguenti:

1. Nome dell'account di archiviazione (nel caso in cui l'archiviazione si trovi in una sottoscrizione diversa dal processo di flusso).
2. Chiave dell'account di archiviazione (nel caso in cui l'archiviazione si trovi in una sottoscrizione diversa dal processo di flusso).
3. Nome tabella di output (verrà creato se non esiste).
4. Chiave di partizione (obbligatoria).
5. Chiave di riga

Per una progettazione migliore della chiave di partizione e della chiave riga, consultare l'articolo riportato di seguito [Progettazione di una strategia di partizionamento scalabile per archivio tabelle di Azure](https://msdn.microsoft.com/library/azure/hh508997.aspx).

### Aggiunta dell'archivio tabelle di Azure come output ###

![graphic11][graphic11]

Passare alla scheda di output del processo, fare clic sul pulsante **AGGIUNGI OUTPUT** e fare clic su Avanti.

![graphic12][graphic12]

Scegliere **Archivio tabelle** come output.

![graphic13][graphic13]

Nella pagina successiva, immettere le informazioni della tabella di Azure. L'alias di output è il nome che è possibile usare nella query per indirizzare l'output della query a questa tabella.

![graphic14][graphic14]

![graphic15][graphic15]

Dimensioni del batch indica il numero di record per un'operazione batch. Il valore predefinito in genere è sufficiente per la maggior parte dei processi. Per altri dettagli sulla modifica di questa impostazione, vedere la [specifica relativa alle operazioni batch su tabella](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx).

Se esiste un account di archiviazione di Azure nella stessa sottoscrizione in uso per creare il processo, selezionare "Usare l'account di archiviazione dalla sottoscrizione corrente" e selezionare l'account di archiviazione nell'elenco a discesa.

Fare clic su Avanti per aggiungere questo output. Vengono avviate due operazioni, di cui la prima consiste nell'aggiungere l'output.

![graphic16][graphic16]

La seconda nel testare la connessione. L'analisi di flusso di Azure tenterà di connettersi all'account di archiviazione e di verificare che le credenziali immesse siano corrette.

## Code del bus di servizio ##
---
### Introduzione ai concetti relativi alle code del bus di servizio ###
Le code del bus di servizio consentono un recapito dei messaggi di tipo FIFO (First In, First Out) a uno o più consumer concorrenti. Si prevede in genere che i messaggi vengano ricevuti ed elaborati nell'ordine temporale in cui sono stati aggiunti alla coda e ogni messaggio viene ricevuto ed elaborato da un solo consumer.

Per altre informazioni sulle code del bus di servizio, vedere [Code, argomenti e sottoscrizioni del bus di servizio](https://msdn.microsoft.com/library/azure/hh367516.aspx "Code, argomenti e sottoscrizioni del bus di servizio") e [Introduzione alle code del bus di servizio](http://blogs.msdn.com/b/appfabric/archive/2011/05/17/an-introduction-to-service-bus-queues.aspx "Introduzione alle code del bus di servizio").

### Parametri ###

Per iniziare a usare un output delle code del bus di servizio, saranno necessarie le informazioni seguenti:

1. Alias di output: qualsiasi nome descrittivo di alias di output a cui fare riferimento facilmente. L'alias di output risulta particolarmente utile se si decide di avere più output per un processo. In questo caso, la query del processo farà riferimento all'alias.
2. Spazio dei nomi e nome del bus di servizio.
3. Nome coda: le code sono entità di messaggistica, simili agli Hub eventi e agli argomenti. Sono progettati per raccogliere i flussi di eventi da diversi dispositivi e servizi. Quando una coda viene creata, le viene assegnato un nome specifico.
4. Formato di serializzazione utilizzato per i dati (Avro, CSV, JSON).

### Aggiunta delle code del bus di servizio come output ###

![graphic31][graphic31]

Immettere quindi i dettagli come mostrato di seguito e selezionare Avanti.

![graphic32][graphic32]

Verificare che il formato e la serializzazione dei dati siano corretti.

![graphic33][graphic33]

## Argomenti del bus di servizio ##
---
### Introduzione ai concetti relativi agli argomenti del bus di servizio ###
Le code del bus di servizio forniscono un metodo di comunicazione uno-a-uno dal mittente al destinatario, invece gli argomenti del bus di servizio offrono una forma di comunicazione uno-a-molti.

Per altre informazioni sugli argomenti del bus di servizio, vedere [Code, argomenti e sottoscrizioni del bus di servizio](https://msdn.microsoft.com/library/azure/hh367516.aspx "Code, argomenti e sottoscrizioni del bus di servizio") e [Introduzione agli argomenti del bus di servizio](http://blogs.msdn.com/b/appfabric/archive/2011/05/25/an-introduction-to-service-bus-topics.aspx "Introduzione agli argomenti del bus di servizio").

### Parametri ###

Per iniziare a usare un output degli argomenti del bus di servizio, saranno necessarie le informazioni seguenti:

1. Alias di output: qualsiasi nome descrittivo di alias di output a cui fare riferimento facilmente. L'alias di output risulta particolarmente utile se si decide di avere più output per un processo. In questo caso, la query farà riferimento all'alias.
2. Spazio dei nomi e nome del bus di servizio.
3. Nome argomento: gli argomenti sono entità di messaggistica, simili agli Hub eventi e alle code. Sono progettati per raccogliere i flussi di eventi da diversi dispositivi e servizi. Quando un argomento viene creato, gli viene assegnato un nome specifico. Poiché i messaggi inviati a un argomento non saranno disponibili se non viene creata una sottoscrizione, assicurarsi che esistano una o più sottoscrizioni per l'argomento.
4. Formato di serializzazione utilizzato per i dati (Avro, CSV, JSON).

### Aggiunta degli argomenti del bus di servizio come output ###

Selezionare l'output per gli argomenti del bus di servizio.

![graphic34][graphic34]

Immettere quindi i dettagli come mostrato di seguito e selezionare Avanti.

![graphic35][graphic35]

Verificare che il formato e la serializzazione dei dati siano corretti.

![graphic33][graphic33]


## Ottenere aiuto
Per ulteriore assistenza, provare il [Forum di Analisi dei flussi di Azure](https://social.msdn.microsoft.com/Forums/IT-IT/home?forum=AzureStreamAnalytics)

## Passaggi successivi

- [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
- [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-get-started.md)
- [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
- [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)




[graphic1]: ./media/stream-analytics-connect-data-event-outputs/1-stream-analytics-connect-data-event-input-output.png
[graphic2]: ./media/stream-analytics-connect-data-event-outputs/2-stream-analytics-connect-data-event-input-output.png
[graphic3]: ./media/stream-analytics-connect-data-event-outputs/3-stream-analytics-connect-data-event-input-output.png
[graphic4]: ./media/stream-analytics-connect-data-event-outputs/4-stream-analytics-connect-data-event-input-output.png
[graphic5]: ./media/stream-analytics-connect-data-event-outputs/5-stream-analytics-connect-data-event-input-output.png
[graphic6]: ./media/stream-analytics-connect-data-event-outputs/6-stream-analytics-connect-data-event-input-output.png
[graphic7]: ./media/stream-analytics-connect-data-event-outputs/7-stream-analytics-connect-data-event-input-output.png
[graphic8]: ./media/stream-analytics-connect-data-event-outputs/8-stream-analytics-connect-data-event-input-output.png
[graphic9]: ./media/stream-analytics-connect-data-event-outputs/9-stream-analytics-connect-data-event-input-output.png
[graphic10]: ./media/stream-analytics-connect-data-event-outputs/10-stream-analytics-connect-data-event-input-output.png
[graphic11]: ./media/stream-analytics-connect-data-event-outputs/11-stream-analytics-connect-data-event-input-output.png
[graphic12]: ./media/stream-analytics-connect-data-event-outputs/12-stream-analytics-connect-data-event-input-output.png
[graphic13]: ./media/stream-analytics-connect-data-event-outputs/13-stream-analytics-connect-data-event-input-output.png
[graphic14]: ./media/stream-analytics-connect-data-event-outputs/14-stream-analytics-connect-data-event-input-output.png
[graphic15]: ./media/stream-analytics-connect-data-event-outputs/15-stream-analytics-connect-data-event-input-output.png
[graphic16]: ./media/stream-analytics-connect-data-event-outputs/16-stream-analytics-connect-data-event-input-output.png
[graphic17]: ./media/stream-analytics-connect-data-event-outputs/17-stream-analytics-connect-data-event-input-output.png
[graphic18]: ./media/stream-analytics-connect-data-event-outputs/18-stream-analytics-connect-data-event-input-output.png
[graphic19]: ./media/stream-analytics-connect-data-event-outputs/19-stream-analytics-connect-data-event-input-output.png
[graphic20]: ./media/stream-analytics-connect-data-event-outputs/20-stream-analytics-connect-data-event-input-output.png
[graphic21]: ./media/stream-analytics-connect-data-event-outputs/21-stream-analytics-connect-data-event-input-output.png
[graphic22]: ./media/stream-analytics-connect-data-event-outputs/22-stream-analytics-connect-data-event-input-output.png
[graphic23]: ./media/stream-analytics-connect-data-event-outputs/23-stream-analytics-connect-data-event-input-output.png
[graphic24]: ./media/stream-analytics-connect-data-event-outputs/24-stream-analytics-connect-data-event-input-output.png
[graphic25]: ./media/stream-analytics-connect-data-event-outputs/25-stream-analytics-connect-data-event-input-output.png
[graphic26]: ./media/stream-analytics-connect-data-event-outputs/26-stream-analytics-connect-data-event-input-output.png
[graphic27]: ./media/stream-analytics-connect-data-event-outputs/27-stream-analytics-connect-data-event-input-output.png
[graphic28]: ./media/stream-analytics-connect-data-event-outputs/28-stream-analytics-connect-data-event-input-output.png
[graphic29]: ./media/stream-analytics-connect-data-event-outputs/29-stream-analytics-connect-data-event-input-output.png
[graphic30]: ./media/stream-analytics-connect-data-event-outputs/30-stream-analytics-connect-data-event-input-output.png
[graphic31]: ./media/stream-analytics-connect-data-event-outputs/31-stream-analytics-connect-data-event-input-output.png
[graphic32]: ./media/stream-analytics-connect-data-event-outputs/32-stream-analytics-connect-data-event-input-output.png
[graphic33]: ./media/stream-analytics-connect-data-event-outputs/33-stream-analytics-connect-data-event-input-output.png
[graphic34]: ./media/stream-analytics-connect-data-event-outputs/34-stream-analytics-connect-data-event-input-output.png
[graphic35]: ./media/stream-analytics-connect-data-event-outputs/35-stream-analytics-connect-data-event-input-output.png
[graphic36]: ./media/stream-analytics-connect-data-event-outputs/36-stream-analytics-connect-data-event-input-output.png
[graphic37]: ./media/stream-analytics-connect-data-event-outputs/37-stream-analytics-connect-data-event-input-output.png
[graphic38]: ./media/stream-analytics-connect-data-event-outputs/38-stream-analytics-connect-data-event-input-output.png

<!---HONumber=Oct15_HO2-->