<properties pageTitle="Raccomandazioni "Spesso acquistati insieme" di Ricerca di Azure | Microsoft Azure | Apache Mahout | Azure Machine Learning" description="Codice di esempio per l'aggiunta delle raccomandazioni Spesso acquistati insieme, Novità o Chi ha acquistato questo articolo ha acquistato anche a un'applicazione di Ricerca di Azure tramite Apache Mahout o Azure Machine Learning" services="search" documentationCenter="" authors="liamca" manager="pablocas" editor="" tags="machine learning"/>

<tags
   ms.service="search"
   ms.devlang="dotnet"
   ms.workload="search"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.date="11/12/2015"
   ms.author="liamca"/>

# Raccomandazioni "Spesso acquistati insieme" di Ricerca di Azure

Se si effettuano acquisti online, è possibile notare le raccomandazioni "Spesso acquistati insieme" o "Chi ha acquistato questo articolo ha acquistato anche" o l'elenco "Novità" per proporre al cliente articoli o servizi aggiuntivi. In genere questi dati vengono compilati da rivenditori online in base alla cronologia di acquisto di numerosi clienti o in base ai dati relativi alle specifiche preferenze dei consumer. La capacità di offrire raccomandazioni rilevanti e indirizzate ai consumer tramite la ricerca è una potente funzionalità non solo per le applicazioni commerciali, ma anche per i siti che forniscono informazioni o contenuti multimediali.

Nelle applicazioni di Ricerca di Azure è possibile implementare raccomandazioni scrivendo un codice aggiuntivo che importa i dati e servizi usati per identificare modelli e relazioni nei dati e quindi trasformarli in raccomandazioni rilevanti per gli utenti.

[Questo esempio illustra come](https://github.com/liamca/azure-search-recommendations) usare [Apache Mahout]() come motore di raccomandazione.

Nella parte restante di questo articolo verrà illustrato il codice di esempio che consente di aggiungere raccomandazioni "Spesso noleggiati insieme" usando dati di esempio sul noleggio di film.

![1](./media/search-fbt-recommendations/product_recommendations.png)

## Che cos'è una raccomandazione?

Le *raccomandazioni* costituiscono una tecnica per visualizzare più elementi di un catalogo in base all'attività di ricerca esistente dell'utente (ad esempio i log Web) al fine di raccomandare elementi e migliorare la conversione.

I motori di raccomandazione sono spesso sottoposti a training usando l'attività precedente del cliente o raccogliendo i dati direttamente da un archivio digitale. In questo esempio viene usato Apache Mahout per compilare i dati relativi alle raccomandazioni.

Di seguito sono riportate alcune raccomandazioni comuni: - Spesso acquistati insieme: i clienti che hanno acquistato questo articolo hanno acquistato anche - Raccomandazioni Cliente-elemento: i clienti con preferenze simili hanno acquistato anche

## Creazione di un indice di Ricerca di Azure

- Aprire la soluzione AzureSearchMovieRecommendations.sln e impostare ImportAzureSearchIndexData come progetto predefinito.  
- Aprire Program.cs all'interno di ImportAzureSearchIndexData e modificare SearchServiceName e SearchApiKey in modo che puntino al servizio di Ricerca di Azure
- Scaricare hetrec2011-movielens-2k.zip da http://grouplens.org/datasets/hetrec-2011/ e copiare il file Movies.dat e user\_ratedmovies.dat in \\ImportAzureSearchIndexData\\data
- Eseguire il progetto per creare un indice e caricare i dati relativi ai film 
- Al termine, l'applicazione eseguirà una ricerca di prova

## Creare una semplice applicazione HTML per la ricerca di film

Un'applicazione Web JavaScript completa che consente di eseguire una query dell'indice di Ricerca di Azure è disponibile all'indirizzo \\WebSite\\starter-template-complete

Se si desidera esaminare la demo dall'inizio, il file CSS originale è disponibile all'indirizzo \\WebSite\\starter-template

Apre il file search.js all'interno di \\WebSite\\starter-template-complete e aggiornare apiKey e azureSearchService con i dettagli del servizio Ricerca di Azure.

Dovrebbe essere possibile aprire il file in un browser, ad esempio Chrome, per visualizzare i film digitando nella casella di ricerca.

## Comando per l'esecuzione di Creazione di raccomandazioni tramite Mahout

- Caricare il file data\\movie\_usage.txt nell'Archivio BLOB di Azure 
- Creare un'istanza di HDInsight abilitando Desktop remoto e connettersi al computer tramite Desktop remoto, disponibile nel portale di Azure classico
- Dal computer di HDInsight aprire la "riga di comando di Hadoop"
- Passare alla directory bin di Mahout in c:\\apps\\dist. In questo caso la directory è C:\\apps\\dist\\mahout-1.0.0.2.3.3.0-2992\\bin, ma è possibile ottenere una versione di Mahout più recente
- Eseguire la riga di comando riportata di seguito in cui è possibile sostituire gli elementi [CONTAINER] e [STORAGEACT] con i dettagli di Archiviazione di Azure in cui si trova il file movie\_usage.txt:

    mahout itemsimilarity -s SIMILARITY\_COSINE --input "wasb://[CONTAINER]@[STORAGEACT].blob.core.windows.net/movie\_usage.txt" --output "wasb://[CONTAINER]@[STORAGEACT].blob.core.windows.net/output/" --tempDir "wasb://[CONTAINER]@[STORAGEACT].blob.core.windows.net/temp" -m 5

Il completamento dell'operazione dovrebbe richiedere alcuni minuti, ma al termine il contenitore di archiviazione dovrebbe contenere il file seguente che includerà le raccomandazioni sui film: /movies/output/part-r-00000

Questo file include 3 colonne: [ID dell'elemento film] [ID dell'elemento Raccomandazioni relative a questo film], [Percentuale di somiglianza]

## Importazione di dati da Mahout a Ricerca di Azure

Il programma che ha creato l'indice di Ricerca di Azure ha anche creato un campo denominato `Recommendations` di tipo Raccolta, simile a un set di stringhe delimitato da virgole. I dati creati nel passaggio precedente verranno uniti con l'indice di Ricerca di Azure.

- Dalla soluzione di Visual Studio AzureSearchMovieRecommendations.sln aprire Program.cs all'interno di MahoutOutputLoader.
- Aggiornare SearchServiceName e SearchApiKey con i dettagli del servizio Ricerca di Azure
- Aggiornare StorageApiKey e StorageAccountName con i dettagli dell'account di Archiviazione di Azure per il quale è stato archiviato il file delle raccomandazioni dei prodotti di Mahout
- Eseguire l'applicazione per unire i dati
 
## Visualizzazione delle raccomandazioni
A questo punto dovrebbe essere possibile tornare all'applicazione Web e fare clic su uno dei film per visualizzare le raccomandazioni.

Se si desidera visualizzare la modalità di restituzione delle raccomandazioni quando è stata selezionata l'immagine, aprire Search.js ed esaminare la funzione openMovieDetails().

## Riconoscimenti

I dati sono stati forniti da GroupLens (http://grouplens.org/datasets/hetrec-2011/)

Per informazioni dettagliate sulle licenze dei dati, fare riferimento a questa pagina: http://files.grouplens.org/datasets/hetrec2011/hetrec2011-movielens-readme.txt

<!---HONumber=AcomDC_1203_2015-->