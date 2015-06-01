<properties 
	pageTitle="Connessione a input e output | Azure" 
	description="Informazioni su come effettuare la connessione e configurare le origini di input e gli output di destinazione per le soluzioni di analisi del flusso." 
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
	ms.date="04/24/2015" 
	ms.author="jeffstok"/>

#Connessione a input e output
In questo documento è possibile osservare i diversi metodi di configurazione delle origini di input e output di destinazione per le soluzioni di analisi del flusso.

##Utilizzo di SQL come output

È possibile utilizzare i database SQL di Azure come output per i dati di natura relazionale o per applicazioni che dipendono dal contesto ospitato in un database.

[http://azure.microsoft.com/services/sql-database/](http://azure.microsoft.com/services/sql-database/)

Per iniziare a utilizzare un database SQL di Azure, sono necessarie le seguenti informazioni sul database:

1. Nome server.
2. Nome database.
3. Una combinazione nome utente/password valida.
4. Nome tabella di output.

![graphic1][graphic1]

Passare alla scheda di output del processo, fare clic sul pulsante "AGGIUNGI OUTPUT" e fare clic su Avanti.

![graphic2][graphic2]


Scegliere "Database SQL" come output.

![graphic3][graphic3]

Immettere le informazioni del database nella pagina successiva. L'alias dell’output è il nome che è possibile utilizzare nella query per indirizzare la query di output su questo database. Se si dispone di un output, l'alias predefinito è "output".

![graphic4][graphic4]

Se si utilizza un database esistente nella stessa sottoscrizione in uso, è possibile selezionare "Utilizza SQL Database da sottoscrizione corrente" e selezionare il database dall'elenco a discesa.

![graphic5][graphic5]

Fare clic su Avanti per aggiungere questo output. Vengono avviate due operazioni, di cui la prima consiste nell'aggiungere l'output.

![graphic6][graphic6]

La seconda nel testare la connessione. Analisi del flusso di Azure tenterà di connettersi al Database SQL e verificare che le credenziali immesse siano corrette e che la tabella sia accessibile. Al completamento, viene visualizzato uno dei due messaggi.

![graphic7][graphic7]

![graphic8][graphic8]



Se viene visualizzato il secondo, fare clic su "DETTAGLI" per visualizzare i dettagli esatti dell'errore.

![graphic9][graphic9]

In questo esempio, le credenziali fornite non sono corrette. È possibile correggere le credenziali e provare a ripetere il test facendo clic sul pulsante "CONNESSIONE TEST".

![graphic10][graphic10]

##Utilizzo di Hub eventi

###Panoramica
Gli Hub eventi sono un ingestor di evento estremamente scalabile e sono in genere il modo più comune per l'ingresso dei dati nell'analisi flusso. Sono progettati per raccogliere i flussi di eventi da diversi dispositivi e servizi. Gli Hub eventi, insieme ad Analisi dei flussi, forniscono ai clienti una soluzione end-to-end per l'analisi in tempo reale. Gli Hub eventi rendono disponibili i feed degli eventi ai clienti su Azure in tempo reale. Inoltre, i processi di analisi del flusso possono essere elaborati in tempo reale. Ad esempio, i clienti possono pubblicare le selezioni effettuate nel Web, le letture dei sensori, gli eventi del registro online per gli Hub eventi e creare processi di analisi del flusso per utilizzare gli Hub eventi come flussi di dati di input per disporre di filtri in tempo reale, aggregazione e join. Gli Hub eventi possono essere utilizzati anche per dati in uscita. L'utilizzo più comune degli Hub eventi si verifica quando l'output di un processo di analisi del flusso rappresenta l'input di un altro processo di flusso.

###Gruppi di utenti
Ogni input del processo di analisi del flusso deve essere configurato in modo tale da disporre del proprio gruppo di utenti dell'Hub eventi. Quando un processo contiene self-join o più output, alcuni input potrebbero essere letti da più di un lettore. Ciò determina il superamento del limite di 5 lettori per gruppo di utenti in un singolo gruppo di utenti. In questo caso, la query dovrà essere suddivisa in più query e i risultati intermedi devono essere instradati tramite gli Hub eventi aggiuntivi. Si noti che è presente anche un limite di 20 gruppi di utenti per Hub eventi. Per dettagli, vedere Guida per gli sviluppatori degli Hub eventi.

 
###Parametri
Sono presenti alcuni parametri per i clienti devono configurare per i flussi di dati dell'Hub eventi. Tali parametri vengono applicati all'input Hub eventi e ai flussi di dati di output, se non diversamente specificato.

1. Spazio dei nomi Service Bus: Spazio dei nomi Service Bus dell'Hub eventi. Uno spazio dei nomi Service Bus è un contenitore per un set di entità di messaggistica. Quando si crea un nuovo Hub eventi, viene inoltre creato uno spazio dei nomi Service Bus. 
2. Nome Hub eventi: nome dell'Hub eventi. È il nome specificato al momento della creazione di un nuovo Hub eventi. 
3. Nome criterio Hub eventi: il nome del criterio di accesso condiviso per l'accesso all'Hub eventi. Per un Hub eventi nella scheda Configura è possibile configurare criteri di accesso condiviso. Ogni criterio di accesso condiviso dispone di un nome, delle autorizzazioni impostate, e di tasti di scelta.
4. Chiave di criterio Hub eventi: la chiave principale o secondaria del criterio di accesso condiviso per l'accesso all'Hub eventi. 
5. Gruppo di utenti dell'Hub eventi: parametro facoltativo per gli input di Hub eventi. Il gruppo di utenti per acquisire dati dall'Hub eventi. Se non specificato, i processi di analisi del flusso utilizzano il gruppo di utenti predefinito per acquisire dati dall'Hub eventi. È consigliabile utilizzare un gruppo di utenti distinto per ogni processo di analisi del flusso.

Colonna chiave di partizione: parametro facoltativo per gli output dell'Hub eventi. La colonna di attributo di dati utilizzata come chiave di partizione per l'output dell'Hub eventi.

##Utilizzo dell'output della tabelle di Azure

È possibile utilizzare le tabelle di Azure per dati strutturati con meno vincoli sullo schema. Archivio tabelle di Azure consente di archiviare i dati per il salvataggio permanente e il recupero efficiente. Per altre informazioni, vedere [Introduzione a Archiviazione di Azure](http://azure.microsoft.com/storage-introduction/)
 
Per iniziare a utilizzare un archivio tabelle di Azure, sono necessarie le seguenti informazioni sulla tabella:

1. Nome dell'account di archiviazione (nel caso in cui l'archiviazione si trovi in una sottoscrizione diversa dal processo di flusso).
2. Chiave account di archiviazione (nel caso in cui l'archiviazione si trovi in una sottoscrizione diversa dal processo di flusso).
3. Nome tabella di output (verrà creato se non esiste).
4. Chiave di partizione (obbligatoria).
5.   Chiave riga (attualmente è obbligatoria, in base ai commenti e suggerimenti di clienti, vogliamo renderla facoltativa)

Per una progettazione migliore della chiave di partizione e della chiave riga, consultare l'articolo riportato di seguito [Progettazione di una strategia di partizionamento scalabile per archivio tabelle di Azure](https://msdn.microsoft.com/library/azure/hh508997.aspx).


![graphic11][graphic11]


Passare alla scheda di output del processo, fare clic sul pulsante "AGGIUNGI OUTPUT" e fare clic su Avanti.


![graphic12][graphic12]


Scegliere "Archivio tabelle" come output.


![graphic13][graphic13]


Nella pagina successiva, immettere le informazioni della tabella di Azure. L'alias di output è il nome che è possibile utilizzare nella query per indirizzare la query di output su questa tabella.


![graphic14][graphic14] ![graphic15][graphic15]

La dimensione del batch rappresenta il numero di record per un'operazione batch. Se non si dispone di sufficiente familiarità, lasciare il valore invariato oppure fare riferimento a [https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx) per ulteriori dettagli


Se si utilizza un'archiviazione di Azure esistente nella stessa sottoscrizione in uso, è possibile selezionare "Utilizza account di archiviazione dalla sottoscrizione corrente" e selezionare il database dall'elenco a discesa.

Fare clic su Avanti per aggiungere questo output. Vengono avviate due operazioni, di cui la prima consiste nell'aggiungere l'output.

![graphic16][graphic16]

La seconda nel testare la connessione. Analisi del flusso di Azure tenterà di connettersi all'account di archiviazione e di verificare che le credenziali immesse siano corrette. Al completamento, viene visualizzato uno dei due messaggi.

![graphic17][graphic17]

Se viene visualizzato il secondo, fare clic su "DETTAGLI" per visualizzare i dettagli esatti dell'errore.

![graphic18][graphic18]

In questo esempio, le credenziali fornite non sono corrette. È possibile correggere le credenziali e provare a ripetere il test facendo clic sul pulsante "CONNESSIONE TEST".

![graphic19][graphic19]

## Ottenere aiuto
Per ulteriore assistenza, provare il [Forum di Analisi dei flussi di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## Passaggi successivi

- [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
- [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-get-started.md)
- [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
- [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Informazioni di riferimento sulle API REST di gestione di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)




[graphic1]: ./media/stream-analytics-connect-data-event-input-output/1-stream-analytics-connect-data-event-input-output.png
[graphic2]: ./media/stream-analytics-connect-data-event-input-output/2-stream-analytics-connect-data-event-input-output.png
[graphic3]: ./media/stream-analytics-connect-data-event-input-output/3-stream-analytics-connect-data-event-input-output.png
[graphic4]: ./media/stream-analytics-connect-data-event-input-output/4-stream-analytics-connect-data-event-input-output.png
[graphic5]: ./media/stream-analytics-connect-data-event-input-output/5-stream-analytics-connect-data-event-input-output.png
[graphic6]: ./media/stream-analytics-connect-data-event-input-output/6-stream-analytics-connect-data-event-input-output.png
[graphic7]: ./media/stream-analytics-connect-data-event-input-output/7-stream-analytics-connect-data-event-input-output.png
[graphic8]: ./media/stream-analytics-connect-data-event-input-output/8-stream-analytics-connect-data-event-input-output.png
[graphic9]: ./media/stream-analytics-connect-data-event-input-output/9-stream-analytics-connect-data-event-input-output.png
[graphic10]: ./media/stream-analytics-connect-data-event-input-output/10-stream-analytics-connect-data-event-input-output.png
[graphic11]: ./media/stream-analytics-connect-data-event-input-output/11-stream-analytics-connect-data-event-input-output.png
[graphic12]: ./media/stream-analytics-connect-data-event-input-output/12-stream-analytics-connect-data-event-input-output.png
[graphic13]: ./media/stream-analytics-connect-data-event-input-output/13-stream-analytics-connect-data-event-input-output.png
[graphic14]: ./media/stream-analytics-connect-data-event-input-output/14-stream-analytics-connect-data-event-input-output.png
[graphic15]: ./media/stream-analytics-connect-data-event-input-output/15-stream-analytics-connect-data-event-input-output.png
[graphic16]: ./media/stream-analytics-connect-data-event-input-output/16-stream-analytics-connect-data-event-input-output.png
[graphic17]: ./media/stream-analytics-connect-data-event-input-output/17-stream-analytics-connect-data-event-input-output.png
[graphic18]: ./media/stream-analytics-connect-data-event-input-output/18-stream-analytics-connect-data-event-input-output.png
[graphic19]: ./media/stream-analytics-connect-data-event-input-output/19-stream-analytics-connect-data-event-input-output.png

<!--HONumber=52-->
