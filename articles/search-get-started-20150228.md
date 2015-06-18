<properties 
	pageTitle="Creare la prima applicazione Ricerca di Azure in .NET" 
	description="Esercitazione sulla compilazione di una soluzione utilizzando la libreria client .NET da .NET SDK di Ricerca di Azure." 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="03/05/2015" 
	ms.author="heidist"/>

#Creare la prima applicazione Ricerca di Azure in .NET#

In questa esercitazione viene compilata un'applicazione di ricerca nel Web personalizzata in Visual Studio 2013 o versione successiva che utilizza Ricerca di Azure per l’esperienza di ricerca. Nell'esercitazione viene utilizzato [.NET SDK di Ricerca di Azure](https://msdn.microsoft.com/library/azure/dn951165.aspx) per compilare le classi per gli oggetti e le operazioni utilizzate nell'esempio.

È possibile scaricare l'esempio dalla pagina [Azure Search Demo Using USGS Data](https://azsearchdemos.codeplex.com/SourceControl/latest) di CodePlex per seguire le istruzioni di questa esercitazione. L’applicazione di esempio usa i dati dei [servizi geologici degli Stati Uniti (USGS)](http://geonames.usgs.gov/domestic/download_data.htm), con il filtro dello stato di Washington. Tali dati saranno utilizzati per compilare un’applicazione di ricerca in base ai dati relativi agli edifici di riferimento quali ospedali e scuole nonché alle caratteristiche geologiche come fiumi, laghi e vette.

Per eseguire questo esempio, è necessario disporre di un servizio Ricerca di Azure, a cui è possibile iscriversi nel [portale di Azure](https://portal.azure.com).

Per assistenza nel provisioning e nella verifica della disponibilità del servizio, è possibile iniziare con [Creare un servizio nel portale](../search-create-service-portal/). Nell'articolo viene inoltre spiegato come individuare il nome del servizio e le chiavi di amministrazione, utilizzati in ogni esercitazione e soluzione che include Ricerca di Azure.

> [AZURE.TIP]Per evitare errori di compilazione, si consiglia di aggiornare i pacchetti NuGet prima di compilare i progetti. Fare clic con il pulsante destro del mouse sulla soluzione e scegliere **Gestisci pacchetti NuGet**. Aggiornare tutti i pacchetti utilizzati in questa soluzione.

##Compilare l'indice##

1. Copiare il nome del servizio e la chiave di amministrazione dal [portale di Azure](https://portal.azure.com) e incollarli in **DataIndexer** | **App.config**.
1. Fare clic con il pulsante destro del mouse sul progetto  **DataIndexer** per impostarlo come progetto di avvio.
1. Compilare ed eseguire il progetto.

Si noterà una finestra della console con questi messaggi.

![][1]

Nel portale, verrà visualizzato un nuovo indice di funzionalità con xx e xx. L’aggiornamento del portale può richiedere molto tempo, pertanto è consigliabile attendere alcuni minuti prima di aggiornare lo schermo per visualizzare i risultati.

![][2]

##Compilare l'applicazione.##


1. Copiare il nome del servizio e la chiave di amministrazione dal [portale di Azure](https://portal.azure.com) e incollarli in **SimpleSearchMVCApp** | **Web.config**.
1. Fare clic con il pulsante destro del mouse sul progetto  **SimpleSearchMVCApp** per impostarlo come progetto di avvio.
1. Compilare ed eseguire il progetto.

Nel browser predefinito apparirà una pagina Web con una casella di ricerca per l'accesso ai dati dei servizi geologici degli Stati Uniti archiviati nell'indice nel servizio Ricerca di Azure.

![][3]

##Eseguire ricerche sui dati dei servizi geologici degli Stati Uniti##

Il set di dati dei servizi geologici degli Stati Uniti include i record relativi allo stato di Washington. Se si fa clic su **Ricerca** su una casella di ricerca vuota, si otterranno le prime 50 voci, ossia l'impostazione predefinita.

L’immissione di un termine di ricerca fornirà al motore di ricerca un elemento con cui continuare. Provare a immettere un nome locale. "Snoqualmie" è una città dello stato di Washington. È inoltre il nome di un fiume, di una cascata, di un passaggio montano e di parchi nazionali.

![][4]

È inoltre possibile tentare con uno dei termini seguenti:

- Seattle
- Rainier
- Seattle e Rainier
- Seattle + Rainier -Mount (ottiene risultati per punti di riferimento su viale Rainier o sul club Rainier, tutto entro i limiti della città di Seattle).

##Esplorare il codice##

Per apprendere le nozioni fondamentali di .NET SDK, consultare [Come utilizzare Ricerca di Azure Search in .NET](../search-howto-dotnet-sdk/) per una spiegazione delle classi più comunemente utilizzate nella libreria client.

Nella parte rimanente di questa sezione vengono illustrati alcuni punti relativi a ogni progetto. Laddove appropriato saranno indicati alcuni approcci alternativi che utilizzano funzionalità più avanzate.

**Progetto DataIndexer**

Per semplificare le operazioni, i dati sono incorporati all'interno della soluzione in un file di testo generato dai dati del [sito Web dei servizi geologici degli Stati Uniti (USGS)](http://geonames.usgs.gov/domestic/download_data.htm).

Le alternative all'incorporamento dei dati includono [indicizzatori per DocumentDB](documentdb/documentdb-search-indexer.md) o [indicizzatori per SQL Azure Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md). Gli indicizzatori estraggono i dati nell’indice di Ricerca di Azure, semplificando notevolmente il codice da scrivere e gestire.

È inoltre possibile caricare dati da un database SQL Server locale. [Questa esercitazione ](http://azure.microsoft.com/blog/2014/11/10/how-to-sync-sql-server-data-with-azure-search/) spiega come eseguire questa operazione.

Il programma **DataIndexer** include un metodo **SearchDocuments** per la ricerca e il filtraggio dei dati. Questo metodo è disponibile come passaggio di verifica, supportando l'output di messaggi di stato nella finestra della console, ossia quelli che mostrano i risultati della ricerca e il funzionamento dei filtri. È molto probabile che non sia necessario un metodo come questo nel codice scritto per la creazione e il caricamento di un indice.

**Progetto SimpleSearchMVCApp**

Il progetto MVC utilizza una visualizzazione e controller per instradare input e output al livello di presentazione. **Index.cshtml** fornisce il codice HTML utilizzato per il rendering dei risultati della ricerca. Attualmente si tratta di una semplice tabella in cui sono organizzati i dati del set di dati. Anche se utile per la creazione di prototipi e l’esecuzione di test, è facilmente migliorabile in fase di presentazione. Per suggerimenti su come raggruppare i risultati e inserire i conteggi in una pagina, vedere [Risultati della pagina e impaginazione in Ricerca di Azure](search-pagination-page-layout.md).

Le connessioni a Ricerca di Azure e l'esecuzione di una richiesta di query sono definite nel file **FeatureSearch.cs**.

Come nota finale, se non si è ancora convinti del valore e della semplicità di .NET SDK, confrontare i file di origine di questo esempio con la [demo Adventure Works di Ricerca di Azure](https://azuresearchadventureworksdemo.codeplex.com/), basata sull'API REST. La versione di .NET SDK descritta in questa esercitazione è molto più semplice, con meno righe di codice.

##Passaggi successivi##

Questa è la prima esercitazione di Ricerca di Azure basata sul set di dati dei servizi geologici degli Stati Uniti. Nel corso del tempo, l’esercitazione sarà ampliata e ne saranno create altre per illustrare le funzionalità di ricerca che potrebbero essere utili nelle soluzioni personalizzate.

Se si dispone già delle nozioni di base di Ricerca di Azure, è possibile utilizzare questo esempio come base di prova per i suggerimenti di alternative (query di suggerimento per la digitazione e completamento automatico), filtri ed esplorazione basata su facet. È inoltre possibile migliorare la pagina dei risultati della ricerca aggiungendo conteggi e raggruppando i documenti in modo che gli utenti possano sfogliare i risultati.

Novità in Ricerca di Azure È consigliabile provare altre esercitazioni per acquisire consapevolezza di ciò che è possibile creare. Visitare la [pagina della documentazione](http://azure.microsoft.com/documentation/services/search/) per trovare ulteriori risorse. È inoltre possibile visualizzare i collegamenti nell'[elenco di video ed esercitazioni](https://msdn.microsoft.com/library/azure/dn798933.aspx) per accedere a ulteriori informazioni.

<!--Image references-->
[1]: ./media/search-get-started-20150228/consolemessages.png
[2]: ./media/search-get-started-20150228/portalindexstatus.png
[3]: ./media/search-get-started-20150228/usgssearchbox.png
[4]: ./media/search-get-started-20150228/snoqualmie.png
<!--HONumber=54-->