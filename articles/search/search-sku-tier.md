<properties
	pageTitle="Scegliere uno SKU o un piano tariffario per Ricerca di Azure | Microsoft Azure"
	description="È possibile effettuare il provisioning di Ricerca di Azure per questi SKU: Gratuito, Basic e Standard; il piano Standard è disponibile in più configurazioni di risorse e livelli di capacità."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="jhubbard"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="NA"
	ms.workload="search"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.date="08/08/2016"
	ms.author="heidist"/>

# Scegliere uno SKU o un piano tariffario per Ricerca di Azure

Durante il [provisioning del servizio](search-create-service-portal.md) sarà necessario specificare lo SKU o il piano tariffario. Le opzioni disponibili includono **Gratuito**, **Basic** o **Standard**. Il piano **Standard** è disponibile in più configurazioni e capacità.

È consigliabile eseguire sempre il provisioning del servizio **Gratuito** (uno per sottoscrizione, senza scadenza), in modo che sia subito disponibile per progetti leggeri. Usare il servizio **Gratuito** per i test e per la valutazione e quindi creare un secondo servizio fatturabile con i piani **Basic** o **Standard** per carichi di lavoro di produzione o di test di dimensioni maggiori.

In Ricerca di Azure lo SKU determina la capacità, non la disponibilità delle funzionalità. Tutte le funzionalità sono disponibili su ogni piano tariffario, incluse le funzioni di anteprima.

## Come prendere una decisione relativa al piano tariffario

La capacità e i costi dell'esecuzione del servizio sono in stretta associazione. Le informazioni in questo articolo consentono di decidere quale SKU offra il giusto equilibrio, ma sarà comunque necessaria una stima almeno approssimativa degli aspetti seguenti:

- Numero e dimensioni degli indici che si prevede di creare
- Numero e dimensioni dei documenti da caricare
- Stima generica del volume di query in termini di query al secondo (QPS, Queries Per Second)

Il numero e le dimensioni sono importanti perché i limiti massimi vengono raggiunti tramite un limite rigido relativo al numero di indici o documenti in un servizio o alle risorse (di archiviazione o repliche) usate dal servizio. Il limite effettivo per il servizio corrisponderà agli elementi usati completamente per primi, ovvero risorse oppure oggetti.

Con le stime a disposizione, la procedura seguente semplificherà il processo:

- **Passaggio 1** Esaminare le descrizioni degli SKU seguenti per ottenere informazioni sulle opzioni disponibili.
- **Passaggio 2** Esaminare le domande per restringere la scelta.
- **Passaggio 3** Convalidare la decisione esaminando i limiti rigidi in termini di archiviazione e prezzi.

> [AZURE.NOTE] Se si sottostima la capacità, sarà necessario effettuare il provisioning di un nuovo servizio a un piano superiore e quindi ricaricare gli indici. Non è disponibile alcun aggiornamento sul posto dello stesso servizio da uno SKU a un altro.

## Descrizioni degli SKU

La tabella seguente fornisce le descrizioni di ogni piano.

Livello|Scenari principali
----|-----------------
**Free**|Servizio condiviso gratuito usato per la valutazione, l'analisi o piccoli carichi di lavoro. Essendo condiviso con altri sottoscrittori, la velocità effettiva e l'indicizzazione delle query varieranno in base agli altri utenti che usano il servizio. La capacità è ridotta (50 MB o 3 indici con un massimo di 10.000 documenti l'uno).
**Basic**|Piccoli carichi di lavoro di produzione su hardware dedicato. Disponibilità elevata. La capacità prevede al massimo di 3 e 1 partizione (2 GB).
**S1**|Lo Standard 1 supporta combinazioni flessibili di partizioni (12) e repliche (12). Viene usato per i carichi di lavoro di produzione medi su hardware dedicato. È possibile allocare partizioni e repliche in combinazioni supportate da un numero massimo di 36 unità di ricerca fatturabili. In questo piano le partizioni sono da 25 GB e il valore QPS è pari a circa 15 query al secondo.
**S2**|Lo Standard 2 esegue carichi di lavoro di produzione più elevati usando le stesse 36 unità di ricerca del piano S1 ma con partizioni e repliche di dimensioni maggiori. In questo piano le partizioni sono da 100 GB e il valore QPS è pari a circa 60 query al secondo.
**S3** (Anteprima)|Lo Standard 3 esegue carichi di lavoro di produzione in proporzione più elevati su sistemi di fascia superiore, in configurazioni fino a 12 partizioni o 12 repliche in meno di 36 unità di ricerca. In questo piano le partizioni sono da 200 GB e il valore QPS è pari a circa 60 query al secondo. Il piano S3 è in anteprima ed è disponibile a una tariffa promozionale.
**S3 HD** (Anteprima)|Lo Standard 3 ad alta densità è progettato per un numero elevato di indici più piccoli. È disponibile solo una partizione da 200 GB. Il valore QPS è pari a più di 60 query al secondo. Il piano S3 è in anteprima ed è disponibile a una tariffa promozionale.

> [AZURE.NOTE] I valori massimi per le repliche e le partizioni vengono fatturati come unità di servizio (al massimo 36 unità per servizio). Ciò impone un limite effettivo inferiore rispetto al valore massimo nominale. Per usare ad esempio il numero massimo di 12 repliche si possono avere non oltre 3 partizioni perché 12 * 3 = 36 unità. Analogamente, ridurre le repliche a 3 per usare il numero di partizioni massimo. Per un grafico delle combinazioni consentite, vedere [Ridimensionare i livelli di risorse per le query e l'indicizzazione dei carichi di lavoro in Ricerca di Azure](search-capacity-planning.md).

## Esaminare i limiti per ogni piano

Il grafico seguente è un subset dei limiti tratto da [Limiti dei servizi in Ricerca di Azure](search-limits-quotas-capacity.md). Elenca i fattori che probabilmente influiranno maggiormente sulla decisione relativa allo SKU. È possibile fare riferimento a questo grafico quando si esaminano le domande seguenti.

Risorsa|Free|Basic|S1|S2|S3 <br/>(Anteprima) |S3 HD <br/>(Anteprima) 
---|---|---|---|----|---|----
Contratto di servizio (SLA)|No <sup>1</sup> |Sì |Sì |Sì |No <sup>1</sup> |No <sup>1</sup> 
Indici consentiti per SKU|3|5|50|200|200|1000
Limiti per i documenti|10\.000 in totale|1 milione per servizio|15 milioni per partizione |60 milioni per partizione|120 milioni per partizione |1 milione per indice
Partizioni massime|N/D |1 |12 |12 |12|1
Dimensioni della partizione|50 MB totali|2 GB per servizio|25 GB per partizione |100 GB per partizione (con un massimo di 1,2 TB per ogni servizio)|200 GB per partizione, con un massimo di 2,4 TB per ogni servizio|200 GB per l'unica partizione
Repliche massime|N/D |3 |12 |12 |12|12
Query al secondo|N/D|~3 per replica|~15 per replica|~60 per replica|>60 per replica|>60 per replica

<sup>1</sup> Gli SKU Gratuito e Anteprima non includono contratti di servizio. I contratti di servizio vengono applicati quando uno SKU è disponibile a livello generale.


## Eliminare gli SKU che non soddisfano i requisiti 

Le domande seguenti consentono di prendere la decisione corretta per gli SKU per il carico di lavoro.

1. Si hanno requisiti in termini di **contratti di servizio**? Restringere la decisione sugli SKU alle tipologie Basic o Standard (non anteprima).
2. **Quanti indici** sono necessari? Una delle variabili principali che influiranno sulla decisione per gli SKU è il numero di indici supportato da ogni SKU. Il supporto degli indici è notevolmente diverso nei piani tariffari più bassi. Il requisito relativo al numero di indici può essere determinante nella scelta dello SKU.
3. **Quanti documenti** verranno caricati in ogni indice? Il numero e le dimensioni dei documenti determinano le dimensioni finali dell'indice. Supponendo che si possano stimare le dimensioni previste dell'indice, è possibile confrontare tale numero con le dimensioni della partizione per ogni SKU, estese per il numero di partizioni necessarie per archiviare un indice di tali dimensioni.
4. **Qual è il carico di query previsto**? Dopo aver determinato i requisiti di archiviazione, prendere in considerazione i carichi di lavoro di query. S2 ed entrambi gli SKU S3 offrono una velocità effettiva quasi equivalente, ma i requisiti in termini di contratto di servizio escludono gli SKU di anteprima.

La maggior parte dei clienti può prendere in considerazione o escludere uno SKU specifico rispondendo a queste quattro domande. Se non è ancora chiaro quale SKU scegliere, contattare il supporto di Azure per altre informazioni.

## Convalida della decisione: lo SKU offre una risorsa di archiviazione e un valore di query al secondo sufficienti?

Come ultimo passaggio, visitare di nuovo la [pagina relativa ai prezzi](https://azure.microsoft.com/pricing/details/search/) e le [sezioni per servizio e per indice nei limiti di servizio](search-limits-quotas-capacity.md) per verificare le stime rispetto ai limiti di sottoscrizione e di servizio.

Se il prezzo o i requisiti di archiviazione non rientrano nelle stime può essere opportuno effettuare il refactoring dei carichi di lavoro tra una serie di servizi più piccoli, ad esempio. A livello più granulare è possibile progettare nuovamente gli indici perché siano più piccoli oppure usare filtri per rendere le query più efficienti.

> [AZURE.NOTE] I requisiti di archiviazione possono apparire maggiori del necessario se i documenti contengono dati estranei. I documenti contengono idealmente solo da dati o metadati ricercabili. I dati binari non sono ricercabili e devono essere archiviati separatamente, ad esempio in una tabella o un archivio BLOB di Azure, con un campo nell'indice che contenga un riferimento URL ai dati esterni. Le dimensioni massime di un singolo documento sono pari a 16 MB o meno, se si caricano più documenti in una richiesta. Per altre informazioni, vedere [Limiti dei servizi in Ricerca di Azure](search-limits-quotas-capacity.md).

## Passaggio successivo

Dopo aver determinato lo SKU corretto, continuare con la procedura:

- [Creare un servizio di ricerca nel portale](search-create-service-portal.md)
- [Modificare l'allocazione di partizioni e repliche per la scalabilità del servizio](search-capacity-planning.md)

<!---HONumber=AcomDC_0914_2016-->