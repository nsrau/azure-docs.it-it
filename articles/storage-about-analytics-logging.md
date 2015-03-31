<properties 
	pageTitle="Informazioni sulla registrazione di Analisi archiviazione" 
	description="Informazioni su come utilizzare il log dell'analisi dell'archiviazione, sulle richieste autenticate e non e su come vengono archiviati i log " 
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

# Informazioni sulla registrazione di Analisi archiviazione

## Panoramica
Analisi archiviazione registra informazioni dettagliate sulle richieste riuscite e non a un servizio di archiviazione. Queste informazioni possono essere utilizzate per monitorare le singole richieste e per diagnosticare problemi relativi a un servizio di archiviazione. Le richieste vengono registrate in base al massimo sforzo.

Per utilizzare Analisi archiviazione, è necessario abilitarla singolarmente per ciascun servizio che si desidera monitorare. Tale operazione può essere eseguita dal [portale di gestione di Azure](https://manage.windowsazure.com/). Per i dettagli, vedere [Come monitorare un account di archiviazione](../how-to-monitor-a-storage-account). È inoltre possibile abilitare Analisi archiviazione a livello di codice tramite l'API REST o la libreria client. Utilizzare le operazioni Get Blob Service Properties, Get Queue Service Properties e Get Table Service Properties per abilitare Analisi archiviazione per ciascun servizio.

Le voci di log vengono create solo in presenza di attività del servizio di archiviazione. Se, ad esempio, un account di archiviazione presenta un'attività nel servizio BLOB, ma non nei servizi di tabelle o di accodamento, saranno creati solo log relativi al servizio BLOB.

##Registrazione delle richieste autenticate
Vengono registrati i seguenti tipi di richieste autenticate:

- Richieste riuscite

- Richieste non riuscite, tra cui timeout, limitazione, rete, autorizzazione e altri errori

- Richieste tramite una firma di accesso condiviso, incluse le richieste riuscite e non riuscite

- Richieste ai dati di analisi

Le richieste eseguite dalla stessa Analisi archiviazione, ad esempio, la creazione oppure l'eliminazione di log, non vengono registrate. Un elenco completo dei dati registrati è documentato negli argomenti [Operazioni registrate in Analisi archiviazione e messaggi di stato](https://msdn.microsoft.com/library/hh343260.aspx) e [Formato log Analisi archiviazione](https://msdn.microsoft.com/library/hh343259.aspx).

##Registrazione di richieste anonime
Vengono registrati i seguenti tipi di richieste anonime:

- Richieste riuscite

- Errori server

- Errori di timeout per client e server

- Richieste GET non riuscite con codice di errore 304 (non modificate)

Tutte le altre richieste anonime non riuscite non vengono registrate. Un elenco completo dei dati registrati è documentato negli argomenti [Operazioni registrate in Analisi archiviazione e messaggi di stato](https://msdn.microsoft.com/library/hh343260.aspx) e [Formato log Analisi archiviazione](](https://msdn.microsoft.com/library/hh343259.aspx)).

##Come vengono archiviati i log
Tutti i log vengono archiviati in BLOB di blocchi in un contenitore denominato $logs, che viene creato automaticamente quando viene abilitata Analisi archiviazione per un account di archiviazione. Il contenitore $logs si trova nello spazio dei nomi BLOB dell'account di archiviazione, ad esempio: `http://<accountname>.blob.core.windows.net/$logs`. Questo contenitore non può essere eliminato una volta abilitata Analisi di archiviazione, sebbene sia possibile eliminarne il contenuto.

>[Azure.NOTE] Il contenitore $logs non viene visualizzato quando viene eseguita un'operazione di inclusione nell'elenco del contenitore, ad esempio, il metodo [ListContainers](https://msdn.microsoft.com/library/ee758348.aspx). È necessario effettuare l'accesso diretto. Ad esempio, è possibile utilizzare il metodo [ListBlobs](https://msdn.microsoft.com/library/ee772878.aspx) per accedere ai BLOB nel contenitore `$logs`.
Nel momento in cui vengono registrate le richieste, Analisi archiviazione carica i risultati intermedi come blocchi. Analisi archiviazione invierà periodicamente questi blocchi e li renderà disponibili come BLOB.

Per i log creati nella stessa ora possono esistere record duplicati. È possibile determinare se un record è un duplicato controllandone il numero **RequestId** e **Operation**.

##Convenzioni di denominazione dei log
Ciascun log verrà scritto nel seguente formato:

 <service-name>/YYYY/MM/DD/hhmm/<counter>.log 

Nella tabella seguente vengono descritti i singoli attributi del nome del log:

| Attributo 	| Descrizione           	|
|----------------	|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------	|
| <service-name> 	| Nome del servizio di archiviazione. Ad esempio: BLOB, tabella o coda        	|
| AAAA 	| Quattro cifre dell'anno del log. Ad esempio: 2011         	|
| MM 	| Due cifre del mese del log. Ad esempio: 07         	|
| GG 	| Due cifre del mese del log. Ad esempio: 07         	|
| hh 	| Due cifre dell'ora che indica l'ora di inizio dei log, nel formato UTC 24 ore. Ad esempio: 18      	|
| mm 	| Numero di due cifre che indica il minuto di inizio per i log. >[AZURE.NOTE]Questo valore non è supportato nella versione corrente di Analisi archiviazione, e il relativo valore sarà sempre 00. 	|
| <counter> 	| Contatore base zero con sei cifre che indica il numero di BLOB di log generati per il servizio di archiviazione in un'ora. Questo contatore parte da 000000. Ad esempio: 000001 	|

Di seguito viene riportato un nome di log di esempio completo che combina gli esempi precedenti:

 blob/2011/07/31/1800/000001.log

L'URI di esempio riportato di seguito può essere utilizzato per accedere al log precedente:

 https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log 

Quando viene registrata una richiesta di archiviazione, il nome log risultante è correlato all'ora in cui è stata completata l'operazione richiesta. Ad esempio, se una richiesta GetBlob è stata completata alle 18.30 del 31/07/2011, il log avrà il seguente prefisso: `blob/2011/07/31/1800/`

##Metadati dei log
Tutti i BLOB dei log vengono archiviati con i metadati che possono essere utilizzati per identificare i dati di registrazione contenuti nei BLOB. Nella tabella seguente sono descritti i singoli attributi dei metadati:

| Attributo 	| Descrizione               	|
|------------	|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------	|
| LogType 	| Descrive se il log contiene informazioni relative alle operazioni di lettura, scrittura o eliminazione. Questo valore può includere un solo tipo o una combinazione di tutti e tre, separati da virgola. Esempio 1: scrittura Esempio 2: lettura, scrittura Esempio 3: lettura, scrittura, eliminazione 	|
| StartTime 	| L'ora in cui è stata registrata la prima voce nel log, nel formato AAAA-MM-GGThh:mm:ssZ. Ad esempio: 2011-07-31T18:21:46Z         	|
| EndTime 	| L'ora in cui è stata registrata l'ultima voce nel log, nel formato AAAA-MM-GGThh:mm:ssZ. Ad esempio: 2011-07-31T18:22:09Z         	|
| LogVersion 	| Versione del formato del log. Al momento, l'unico valore supportato è: 1,0            	|

Nell'elenco seguente sono visualizzati i metadati di esempio completi tramite gli esempi precedenti:

- LogType=write 

- StartTime=2011-07-31T18:21:46Z 

- EndTime=2011-07-31T18:22:09Z 

- LogVersion=1.0 

##Accesso ai dati di registrazione

Tutti i dati del contenitore `$logs` sono accessibili tramite le API del servizio BLOB, incluse le API .NET fornite dalla libreria gestita di Azure. L'amministratore dell'account di archiviazione può leggere ed eliminare i log, ma non può crearli o aggiornarli. Quando si esegue una query per un log è possibile utilizzare sia i metadati del log, sia il nome del log. È possibile che i log di una data ora siano visualizzati come fuori servizio, ma i metadati specificano sempre l'intervallo temporale delle voci di un log. Pertanto, nella ricerca di un particolare log, è possibile utilizzare una combinazione di nomi log e metadati.

##Passaggi successivi

[Formato log Analisi archiviazione](https://msdn.microsoft.com/library/hh343259.aspx) 

[Operazioni registrate di Analisi archiviazione e messaggi di stato](https://msdn.microsoft.com/library/hh343260.aspx) 

[Informazioni sulle metriche di Analisi archiviazione](https://msdn.microsoft.com/library/hh343258.aspx) 
<!--HONumber=47-->
