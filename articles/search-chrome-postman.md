<properties 
	pageTitle="Come utilizzare Chrome Postman con Ricerca di Azure" 
	description="Come utilizzare Chrome Postman con Ricerca di Azure" 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""
    tags="azure-portal"/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="04/27/2015" 
	ms.author="heidist"/>

# Come utilizzare Chrome Postman con Ricerca di Azure #

[Postman](https://chrome.google.com/webstore/detail/postman-rest-client/fdmmgilgnpjigdojojpjoooidkmcomcm "Chrome Postman") è uno strumento fornito con Google Chrome che consente agli sviluppatori di lavorare in modo efficiente con servizi API basati su REST, ad esempio Ricerca di Azure. È possibile utilizzare Postman per creare rapidamente ed eseguire query degli indici di ricerca mediante l'invio di chiamate API tramite Postman, senza dover scrivere righe di codice. Questo approccio è un modo efficiente per conoscere l’API e provarne le nuove funzionalità.

![][1]
 
## Requisiti ##

È necessario disporre di un servizio Ricerca di Azure. Come con qualsiasi applicazione personalizzata che utilizza Ricerca di Azure, sarà necessario disporre dell'URL del servizio, oltre a un amministratore `api-key` in grado di creare l'indice. Per istruzioni su come ottenere i valori per il servizio Ricerca, vedere [Creare un servizio nel portale di](search-create-service-portal.md).

## Installare Postman ##
Per scaricare Postman, visitare il [Google Web Store](https://chrome.google.com/webstore/detail/postman-rest-client/fdmmgilgnpjigdojojpjoooidkmcomcm). Il collegamento in questa pagina consente di scaricare e installare il client REST per Postman. Una volta installato, è possibile avviare Postman dall’applicazione Chrome Launcher.

![][2]
 
## Configurazione Postman per eseguire query di Ricerca di Azure ##
Per configurare Postman, seguire i passaggi:

1. Immettere l'URL del servizio di Ricerca di Azure dove viene indicato di inserirlo.  
2. Aggiungere all'URL: `?api-version=2015-02-28`. È inoltre possibile specificare una versione diversa di API. Per informazioni dettagliate, vedere [Controllo delle versioni di Ricerca di Azure](https://msdn.microsoft.com/library/azure/dn864560.aspx).
3. Assicurarsi che venga scelto `GET`.
4. Fare clic sul pulsante relativo alle **intestazioni**.
5. Immettere un valore per:
	- `api-key`: [Admin Key]
	- `Content-Type`: `application/json; charset=utf-8`
6. Fare clic sul pulsante di **invio** per inoltrare la chiamata REST a Ricerca di Azure e visualizzare la risposta JSON.

![][3]

## Creazione di un indice di Ricerca di Azure con Postman ##

Successivamente, si elaborerà ciò che è stato completato nell'ultimo passaggio eseguendo una chiamata REST per creare un nuovo indice di Ricerca di Azure. A differenza della chiamata precedente, la creazione dell'indice richiede un verbo HTTP PUT, nonché un documento JSON con la definizione dello schema di indice. Per questo esempio si intende creare un indice utilizzato per archiviare un elenco di sentieri escursionistici. A tale scopo, effettuare l'operazione seguente:

1. Modificare l'URL: `https://[SEARCH SERVICE].search.windows.net/indexes/trails?api-version=2015-02-28` utilizzando il nome del servizio di ricerca.
2. Modificare il tipo di richiesta da `GET` a `PUT`.
3. Nel contenuto del corpo RAW, immettere il seguente elemento JSON:

	    {
	    "name": "trails", 
	    "fields": [
	    {"name": "id", "type": "Edm.String", "key": true, "searchable": false}, 
	    {"name": "name", "type": "Edm.String"}, 
	    {"name": "county", "type": "Edm.String"}, 
	    {"name": "elevation", "type": "Edm.Int32"}, 
	    {"name": "location", "type": "Edm.GeographyPoint"} ]
	    }

4. Fare clic su **Invia**.

![][4]
 
## Pubblicazione di documenti in un indice di Ricerca di Azure con Postman ##
Dopo aver creato l'indice, caricare il documento al suo interno. A tale scopo, vengono pubblicati un gruppo di documenti in un batch utilizzando i dati di cinque sentieri della banca dati USGS \(United States Geological Survey\):

1. Modificare l'URL: `https://[SEARCH SERVICE].windows.net/indexes/trails/docs/index?api-version=2015-02-28` utilizzando il nome del servizio di ricerca. Si noti che l'URL include un percorso all’indice appena creato.
2. Modificare il tipo HTTP in `POST`.
3. Nel contenuto del corpo RAW, immettere il seguente elemento JSON:

	    {
	      "value": [
		    {"@search.action": "upload", "id": "233358", "name": "Pacific Crest National Scenic Trail", "county": "San Diego", "elevation":1294, "location": { "type": "Point", "coordinates": [-120.802102,49.00021] }},
		    {"@search.action": "upload", "id": "801970", "name": "Lewis and Clark National Historic Trail", "county": "Richland", "elevation":584, "location": { "type": "Point", "coordinates": [-104.8546903,48.1264084] }},
		    {"@search.action": "upload", "id": "1144102", "name": "Intake Trail", "county": "Umatilla", "elevation":1076, "location": { "type": "Point", "coordinates": [-118.0468873,45.9981939] }},
		    {"@search.action": "upload", "id": "1509897", "name": "Burke Gilman Trail", "county": "King", "elevation":10, "location": { "type": "Point", "coordinates": [-122.2754036,47.7059315] }},
		    {"@search.action": "upload", "id": "1517508", "name": "Cavanaugh-Oso Truck Trail", "county": "Skagit", "elevation":339, "location": { "type": "Point", "coordinates": [-121.9470829,48.2981608] }}
	      ]
	    }
    
4. Fare clic su **Invia**.

![][5]

## Esecuzione di query dell’indice con Postman ##
Il passaggio finale consiste nell’effettuare una query dell’indice e nell’inviare una richiesta di ricerca full-text semplice per la parola *sentieri*.

1. Immettere le informazioni seguenti nell'URL: `https://[SEARCH SERVICE].search.windows.net/indexes/trails/docs?api-version=2015-02-28&search=trail` utilizzando il nome del servizio di ricerca. Si noti che l'URL include il parametro di query `search` e un termine di ricerca di *sentiero*.
2. Modificare il tipo di richiesta HTTP in `GET`.
3. Fare clic su **Invia**.
 
Nella risposta verranno visualizzati i risultati della ricerca JSON restituiti da Ricerca di Azure.

![][6]

## Passaggi successivi ##
Ora che sono state esaminate tutte le nozioni fondamentali dell’utilizzo di Ricerca di Azure con Postman, esistono alcune operazioni per completare i passaggi successivi:

1. Postman supporta `Collections`, che rappresentano un modo pratico per salvare richieste comunemente emesse. È possibile condividere raccolte con altre persone, da eseguire nella propria copia di Postman.
2. Nella documentazione di Ricerca di Azure, assicurarsi di prendere nota del tipo di richiesta HTTP \(`GET`, `PUT` e così via\) associata a ogni chiamata e modificarla in maniera appropriata in Postman.

La documentazione per l'API REST è disponibile in [MSDN](https://msdn.microsoft.com/library/azure/dn798935.aspx).

È inoltre possibile visualizzare i collegamenti l’[elenco di video ed esercitazioni](https://msdn.microsoft.com/library/azure/dn818681.aspx) per ulteriori esempi.

<!-- Image References -->
[1]: ./media/search-chrome-postman/full_postman_client.png
[2]: ./media/search-chrome-postman/postman.png
[3]: ./media/search-chrome-postman/configure.png
[4]: ./media/search-chrome-postman/create_index.png
[5]: ./media/search-chrome-postman/upload_documents.png
[6]: ./media/search-chrome-postman/query.png

<!--HONumber=54-->