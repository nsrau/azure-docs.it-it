---
title: Introduzione a Ricerca di Azure in NodeJS | Microsoft Docs
description: Illustra la creazione di un'applicazione di ricerca in un servizio di ricerca cloud ospitato in Azure usando NodeJS come linguaggio di programmazione.
services: search
documentationcenter: ''
author: EvanBoyle
manager: pablocas
editor: v-lincan

ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.date: 07/14/2016
ms.author: evboyle

---
# Introduzione a Ricerca di Azure in NodeJS
> [!div class="op_single_selector"]
> * [Portale](search-get-started-portal.md)
> * [.NET](search-howto-dotnet-sdk.md)
> 
> 

Informazioni su come compilare un'applicazione di ricerca NodeJS personalizzata che utilizza Ricerca di Azure per l’esperienza di ricerca. L'esercitazione utilizza l’[API REST del servizio Ricerca di Azure](https://msdn.microsoft.com/library/dn798935.aspx) per costruire gli oggetti e le operazioni utilizzati in questo esercizio.

Sono stati utilizzati [NodeJS](https://nodejs.org) e NPM, [Sublime Text 3](http://www.sublimetext.com/3) e Windows PowerShell in Windows 8.1 per sviluppare e testare il codice.

Per eseguire questo esempio, è necessario un servizio di Ricerca di Azure, a cui è possibile iscriversi nel [portale di Azure](https://portal.azure.com). Per istruzioni dettagliate, vedere [Creare un servizio di Ricerca di Azure nel portale](search-create-service-portal.md).

## Informazioni sui dati
L’applicazione di esempio usa i dati dei [servizi geologici degli Stati Uniti (USGS)](http://geonames.usgs.gov/domestic/download_data.htm) con il filtro dello stato del Rhode Island per ridurre la dimensione del set di dati. Tali dati saranno usati per compilare un'applicazione di ricerca che restituisce gli edifici di riferimento quali ospedali e scuole nonché caratteristiche geologiche come fiumi, laghi e vette.

In questa applicazione, il programma **DataIndexer** compila e carica l'indice utilizzando un costrutto [Indexer](https://msdn.microsoft.com/library/azure/dn798918.aspx), recuperando il set di dati filtrato dei servizi geologici degli Stati Uniti da un database SQL di Azure pubblico. Nel codice del programma vengono fornite credenziali e connessioni all'origine dati online. Non è necessaria ulteriore configurazione.

> [!NOTE]
> A questo set di dati è stato applicato un filtro per restare sotto il limite di 10.000 documenti del livello di prezzo gratuito. Se si usa il livello standard, questo limite non si applica. Per altre informazioni sulla capacità per ogni piano tariffario, vedere [Limiti del servizio di ricerca](search-limits-quotas-capacity.md).
> 
> 

<a id="sub-2"></a>

## Individuare il nome del servizio e la chiave API del servizio Ricerca di Azure
Dopo aver creato il servizio, tornare al portale per ottenere l'URL o `api-key`. Per le connessioni al servizio Ricerca è necessario disporre sia dell'URL che di una `api-key` per l'autenticazione della chiamata.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nella barra di spostamento fare clic su **Servizio di ricerca** per elencare tutti i servizi di Ricerca di Azure di cui è stato effettuato il provisioning per la sottoscrizione.
3. Selezionare il servizio che si vuole usare.
4. Nel dashboard del servizio, saranno riportate sezioni per informazioni essenziali, nonché l'icona della chiave per l'accesso alle chiavi di amministrazione.
   
      ![][3]
5. Copiare l'URL del servizio, una chiave di amministrazione e una chiave di query. Saranno necessari in seguito, quando verranno aggiunti al file config.js.

## Scaricare i file di esempio
Utilizzare uno degli approcci seguenti per scaricare l'esempio.

1. Andare a [AzureSearchNodeJSIndexerDemo](https://github.com/AzureSearch/AzureSearchNodeJSIndexerDemo).
2. Fare clic su **Download ZIP**, salvare il file con estensione zip su disco e quindi estrarre tutti i file in esso contenuti.

Tutte le successive modifiche e le istruzioni di esecuzione verranno effettuate sui file in questa cartella.

## Aggiornare config.js. con l'URL del servizio di ricerca e la chiave api
Utilizzando l'URL e la chiave API copiati in precedenza, specificare l'URL, la chiave di amministrazione e la chiave di query nel file di configurazione.

Le chiavi di amministrazione forniscono il controllo completo sulle operazioni del servizio, incluse creazione ed eliminazione di un indice e caricamento di documenti. Le chiavi di query, invece, sono per le operazioni di sola lettura, in genere utilizzate dalle applicazioni client che si connettono a Ricerca di Azure.

In questo esempio, è necessario includere la chiave di query per rafforzare la procedura consigliata di utilizzo della chiave di query nelle applicazioni client.

La seguente schermata mostra **config.js** aperto in un editor di testo con le voci pertinenti delimitate in modo che sia possibile sapere dove aggiornare il file con i valori validi per il servizio di ricerca.

![][5]

## Ospitare un ambiente di runtime per l'esempio
L'esempio richiede un server HTTP, che è possibile installare a livello globale tramite npm.

Eseguire i comandi seguenti da una finestra di PowerShell:

1. Passare alla cartella che contiene il file **package.json**.
2. Digitare `npm install`.
3. Digitare `npm install -g http-server`.

## Compilare l’indice ed eseguire l'applicazione.
1. Digitare `npm run indexDocuments`.
2. Digitare `npm run build`.
3. Digitare `npm run start_server`.
4. Inserire nel browser l'indirizzo `http://localhost:8080/index.html`

## Eseguire ricerche sui dati dei servizi geologici degli Stati Uniti
Il set di dati dei servizi geologici degli Stati Uniti include i dati relativi allo stato del Rhode Island. Se si fa clic su **Ricerca** su una casella di ricerca vuota, si otterranno le prime 50 voci, ossia l'impostazione predefinita.

L’immissione di un termine di ricerca fornirà al motore di ricerca un elemento con cui continuare. Provare a immettere un nome locale. "Roger Williams" è stato il primo governatore del Rhode Island. Numerosi parchi, edifici e scuole prendono il suo nome.

![][9]

È inoltre possibile tentare con uno dei termini seguenti:

* Pawtucket
* Pembroke
* goose +cape

## Passaggi successivi
Questa è la prima esercitazione di Ricerca di Azure basata su NodeJS e sul set di dati dei servizi geologici degli Stati Uniti. Nel corso del tempo, l’esercitazione sarà ampliata per illustrare le funzionalità di ricerca aggiuntive che potrebbero essere utili nelle soluzioni personalizzate.

Se si dispone già delle nozioni di base di Ricerca di Azure, è possibile usare questo esempio come base di prova per i suggerimenti di alternative (query di suggerimento per la digitazione e completamento automatico), filtri ed esplorazione basata su facet. È inoltre possibile migliorare la pagina dei risultati della ricerca aggiungendo conteggi e raggruppando i documenti in modo che gli utenti possano sfogliare i risultati.

Novità in Ricerca di Azure È consigliabile provare altre esercitazioni per acquisire consapevolezza di ciò che è possibile creare. Visitare la [pagina della documentazione](https://azure.microsoft.com/documentation/services/search/) per trovare ulteriori risorse. È inoltre possibile visualizzare i collegamenti nell'[elenco di video ed esercitazioni](search-video-demo-tutorial-list.md) per accedere a ulteriori informazioni.

<!--Image references-->
[1]: ./media/search-get-started-nodejs/create-search-portal-1.PNG
[2]: ./media/search-get-started-nodejs/create-search-portal-2.PNG
[3]: ./media/search-get-started-nodejs/create-search-portal-3.PNG
[5]: ./media/search-get-started-nodejs/AzSearch-NodeJS-configjs.png
[9]: ./media/search-get-started-nodejs/rogerwilliamsschool.png

<!---HONumber=AcomDC_0720_2016-->