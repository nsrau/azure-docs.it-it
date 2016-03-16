<properties
	pageTitle="Limiti dei servizi in Ricerca di Azure | Microsoft Azure | Servizio di ricerca cloud ospitato"
	description="Limiti del servizio usati per la pianificazione della capacità e limiti massimi per richieste e risposte in Ricerca di Azure."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="NA"
	ms.workload="search"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.date="02/28/2016"
	ms.author="heidist"/>

# Limiti dei servizi in Ricerca di Azure

I limiti massimi per archiviazione, carichi di lavoro e quantità di indici, documenti e altri oggetti dipendono dal piano tariffario scelto per Ricerca di Azure: **gratuito**, **Basic** o **Standard**.

- Il servizio **gratuito** è un servizio condiviso multi-tenant fornito con la sottoscrizione di Azure. È un'opzione senza costi aggiuntivi per i sottoscrittori che consente di provare il servizio prima di iscriversi per usare risorse dedicate. 
- Il piano **Basic (anteprima)** fornisce risorse di elaborazione dedicate per carichi di lavoro di produzione di dimensioni ridotte. Questo livello, attualmente in anteprima, viene offerto a una [tariffa ridotta](https://azure.microsoft.com/pricing/details/search/).
- Il piano **Standard** prevede computer dedicati usati solo dal servizio di un unico utente, con ulteriore capacità di elaborazione e archiviazione a ogni livello, inclusa la configurazione minima. Il servizio Standard è disponibile in due livelli (S1 ed S2). 

## Limiti dei livelli

[AZURE.INCLUDE [azure-search-limits](../../includes/azure-search-limits-all.md)]

> [AZURE.NOTE] Il numero di query al secondo è variabile, in particolare per il servizio condiviso, poiché la velocità effettiva è basata sulla larghezza di banda disponibile e sulla concorrenza per le risorse di sistema. Le risorse di calcolo e di archiviazione di Azure che supportano il servizio condiviso vengono condivise da più sottoscrittori, pertanto il numero di query al secondo per la soluzione scelta varia a seconda del numero degli altri carichi di lavoro in esecuzione nello stesso momento. Al livello Standard è possibile stimare in modo più preciso il numero di query al secondo, perché si ha il controllo di un numero maggiore di parametri. Per indicazioni su come calcolare il numero di query al secondo per i carichi di lavoro, vedere la sezione relativa alle procedure consigliate in [Gestire la soluzione di ricerca](search-manage.md).

## Limiti della chiave API

Le chiavi API vengono utilizzate per l'autenticazione del servizio. Sono disponibili due tipi. Le chiavi amministratore, specificate nell'intestazione della richiesta, consentono l'accesso completo in lettura e scrittura al servizio. Le chiavi di query sono di sola lettura, sono specificate nell'URL e in genere sono distribuite ad applicazioni client.

- 2 chiavi di amministrazione al massimo per ogni servizio
- 50 chiavi di query al massimo per ogni servizio

## Limiti di richiesta

- 16 MB al massimo per <sup>1</sup> richiesta
- 8 KB al massimo per la lunghezza dell'URL
- 1000 documenti al massimo per ogni batch di carichi, unioni o eliminazioni di indice
- 32 campi al massimo nella clausola $orderby
- 32766 byte (32 KB meno 2 byte) di testo codificato UTF-8 per la dimensione massima del termine di ricerca

## Limiti di risposta

- 1000 documenti al massimo restituiti per pagina di risultati della ricerca
- 100 suggerimenti al massimo restituiti per richiesta di API di suggerimento

<sup>1</sup> In Ricerca di Azure, il corpo di una richiesta è soggetto a un limite massimo di 16 MB, che impone un limite pratico ai contenuti di singoli campi o raccolte non vincolate dai limiti teorici. Per altre informazioni sulla composizione dei campi e sulle restrizioni, vedere [Tipi di dati supportati](https://msdn.microsoft.com/library/azure/dn798938.aspx).

<!------HONumber=AcomDC_0302_2016-->