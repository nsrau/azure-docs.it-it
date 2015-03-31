<properties 
	pageTitle="Informazioni sulle metriche di Analisi archiviazione" 
	description="Informazioni su come utilizzare l'analisi archiviazione, incluse le metriche di transazione e quelle di capacità, su come vengono archiviate le metriche e su come accedere ai dati delle metriche." 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor="cgronlun"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="tamram"/>

# Informazioni sulle metriche di Analisi archiviazione

## Panoramica

Analisi archiviazione è in grado di archiviare le metriche che includono le statistiche delle transazioni aggregate e i dati di capacità relativi alle richieste in un servizio di archiviazione. Le transazioni vengono segnalate sia a livello di operazione API, sia a livello di servizio di archiviazione, mentre la capacità viene segnalata a livello di servizio di archiviazione. I dati delle metriche possono essere utilizzati per analizzare l'uso del servizio di archiviazione, diagnosticare i problemi relativi alle richieste effettuate al servizio di archiviazione e per migliorare le prestazioni delle applicazioni che usano un servizio.

Per utilizzare Analisi archiviazione, è necessario abilitarla singolarmente per ciascun servizio che si desidera monitorare. Tale operazione può essere eseguita dal [portale di gestione di Azure](https://manage.windowsazure.com/). Per i dettagli, vedere [Come monitorare un account di archiviazione](../how-to-monitor-a-storage-account). È inoltre possibile abilitare Analisi archiviazione a livello di codice tramite l'API REST o la libreria client. [Utilizzare le operazioni Get Blob Service Properties, Get Queue Service Properties](https://msdn.microsoft.com/library/hh452239.aspx) e [Get Table Service Properties per abilitare Analisi archiviazione](https://msdn.microsoft.com/library/hh452238.aspx) per ciascun servizio.

##Metriche di transazione

A intervalli di ore o minuti viene registrato un set di dati consistente per ciascun servizio di archiviazione e operazione API richiesta, inclusi ingresso/uscita, disponibilità, errori e vengono suddivise in categorie le percentuali di richieste. È possibile vedere un elenco completo dei dettagli delle transazioni nell'argomento [Schema di tabella della metrica di Analisi di archiviazione](https://msdn.microsoft.com/library/hh343264.aspx).

I dati delle transazioni vengono registrati a due livelli: a livello di servizio e di operazione API. A livello di servizio, le statistiche che riepilogano tutte le operazioni API richieste vengono scritte in un'entità di tabella ogni ora, persino se non sono state eseguite richieste al servizio. A livello di operazione API, le statistiche vengono scritte in un'entità solo se l'operazione è stata richiesta entro l'ora.

Se, ad esempio, si esegue un'operazione **GetBlob** nel servizio BLOB, la metrica Analisi archiviazione registrerà la richiesta e la includerà nei dati aggregati sia per il servizio BLOB, sia per l'operazione **GetBlob**. Tuttavia, se nel corso di quell'ora non vengono richieste operazioni **GetBlob**, non verrà scritta un'entità in `$MetricsTransactionsBlob` per tale operazione.

Le metriche delle transazioni vengono registrate sia per le richieste utente, sia per quelle eseguite dalla stessa Analisi archiviazione. Ad esempio, le richieste di Analisi archiviazione di scrivere entità di log e di tabella vengono registrate. Per altre informazioni su come vengono fatturate queste richieste, vedere [Analisi archiviazione e fatturazione](https://msdn.microsoft.com/library/hh360997.aspx)

##Metriche della capacità

>[AZURE.NOTE] Al momento, le metriche per la capacità sono disponibili solo per il servizio BLOB. Le metriche della capacità per il servizio di tabelle e di accodamento saranno disponibili nelle versioni future di Analisi archiviazione.

I dati relativi alla capacità vengono registrati quotidianamente per il servizio BLOB di un account di archiviazione e vengono scritte due entità di tabella. Un'entità fornisce le statistiche per i dati utente e l'altra le statistiche sul contenitore BLOB `$logs` utilizzato da Analisi archiviazione. Nella tabella `$MetricsCapacityBlob` sono incluse le seguenti statistiche:

- **Capacità**: quantità di archiviazione utilizzata dal servizio BLOB dell'account di archiviazione, in byte.

- **ContainerCount**: numero di contenitori BLOB del servizio BLOB dell'account di archiviazione.

- **ObjectCount**: numero di BLOB di pagine o blocchi inviati nel servizio BLOB dell'account di archiviazione.

Per altre informazioni sulle metriche della capacità, vedere Schema di tabella della metrica di Analisi di archiviazione.

##Come vengono archiviate le metriche

Tutti i dati delle metriche per ciascun servizio di archiviazione vengono archiviati in tre tabelle riservate per tale servizio: una tabella per le informazioni sulle transazioni, una per le informazioni sulle transazioni al minuto e un'altra per le informazioni relative alla capacità. Le informazioni sulla transazione e sulle transazioni al minuto consistono nei dati di richiesta e di risposta, mentre le informazioni sulla capacità consistono nei dati d'uso dell'archiviazione. Metriche per ora, minuto e capacità del servizio BLOB di un account di archiviazione sono accessibili nelle tabelle denominate nel modo descritto nella tabella seguente.

| Livello metrica  	| Nomi tabella        	| Supportato per le versioni        	|
|------------------------------------	|-----------------------------------------------------------------------------------------------------------------------------	|----------------------------------------------------------------------------------------------------------------------------------------------	|
| Metriche orarie, posizione principale 	| $MetricsTransactionsBlob <br/>$MetricsTransactionsTable <br/> $MetricsTransactionsQueue   	| Solo le versioni precedenti al 15-08-2013. Sebbene tali nomi siano supportati, è consigliabile passare all'utilizzo delle tabelle elencate di seguito. 	|
| Metriche orarie, posizione principale 	| $MetricsHourPrimaryTransactionsBlob <br/>$MetricsHourPrimaryTransactionsTable <br/>$MetricsHourPrimaryTransactionsQueue 	| Tutte le versioni, inclusa quella del 15-08-2013       	|
| Metriche per minuto, posizione principale 	| $MetricsMinutePrimaryTransactionsBlob <br/>$MetricsMinutePrimaryTransactionsTable <br/>$MetricsMinutePrimaryTransactionsQueue 	| Tutte le versioni, inclusa quella del 15-08-2013       	|
| Metriche orarie, posizione secondaria 	| $MetricsHourSecondaryTransactionsBlob <br/>$MetricsHourSecondaryTransactionsTable <br/>$MetricsHourSecondaryTransactionsQueue 	| Tutte le versioni, inclusa quella del 15-08-2013. Deve essere abilitata la replica con accesso in lettura con ridondanza geografica.    	|
| Metriche al minuto, posizione secondaria 	| $MetricsMinuteSecondaryTransactionsBlob <br/>$MetricsMinuteSecondaryTransactionsTable <br/>$MetricsMinuteSecondaryTransactionsQueue 	| Tutte le versioni, inclusa quella del 15-08-2013. Deve essere abilitata la replica con accesso in lettura con ridondanza geografica.    	|
| Capacità (solo servizio BLOB) 	| $MetricsCapacityBlob       	| Tutte le versioni, inclusa quella del 15-08-2013       	|



Tali tabelle vengono create automaticamente quando viene abilitato Analisi archiviazione per un account di archiviazione. L'accesso alle tabelle viene eseguito tramite lo spazio dei nomi dell'account di archiviazione, ad esempio: `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`


##Accesso ai dati delle metriche

Tutti i dati delle tabelle delle metriche sono accessibili mediante le API del servizio di tabelle, incluse le API .NET fornite dalla libreria gestita di Azure. L'amministratore dell'account di archiviazione può leggere ed eliminare le entità di tabella, ma non può crearle né aggiornarle.

##Passaggi successivi
###Concetti
[Abilitazione e configurazione di Analisi archiviazione](https://msdn.microsoft.com/library/hh360996.aspx) 

[Schema di tabella della metrica di Analisi di archiviazione](https://msdn.microsoft.com/library/hh343264.aspx) 

[Operazioni registrate di Analisi archiviazione e messaggi di stato](https://msdn.microsoft.com/library/hh343260.aspx) 

[Informazioni sulla registrazione di Analisi archiviazione](https://msdn.microsoft.com/library/hh343262.aspx) 

###Altre risorse

[Come monitorare un account di archiviazione](../how-to-monitor-a-storage-account) 
<!--HONumber=47-->
