<properties
	pageTitle="Scegliere uno SKU o un livello per Ricerca di Azure | Microsoft Azure"
	description="È possibile effettuare il provisioning di Ricerca di Azure per questi SKU: Gratuito, Basic e Standard; il piano Standard è disponibile in più configurazioni di risorse e livelli di capacità."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="paulettm"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="NA"
	ms.workload="search"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.date="06/05/2016"
	ms.author="heidist"/>

# Scegliere uno SKU o un livello per Ricerca di Azure

Prima di poter [creare un servizio di ricerca](search-create-service-portal.md) in Ricerca di Azure è necessario sapere per quale livello o SKU effettuare il provisioning del servizio. Gli SKU includono: Gratuito, Basic o Standard; Standard è disponibile in più configurazioni di risorse e capacità.

La capacità e i costi dell'esecuzione del servizio sono in stretta associazione. Le informazioni in questo articolo consentono di decidere quale SKU offra il giusto equilibrio, ma sarà comunque necessaria una stima almeno approssimativa del numero e delle dimensioni degli indici che si intende creare e del volume di query. Con le stime a disposizione, la procedura seguente semplificherà il processo:

- **Passaggio 1** Esaminare le descrizioni degli SKU di seguito per conoscere le opzioni a disposizione.
- **Passaggio 2** Rispondere a una serie di domande per restringere la scelta.
- **Passaggio 3** Convalidare la decisione esaminando i limiti rigidi in termini di archiviazione e prezzi. 

## Descrizioni degli SKU

La tabella seguente illustra una descrizione e un semplice confronto di ogni SKU in termini di partizioni e repliche, nonché le query al secondo (QPS), se applicabile. Per un'introduzione a partizioni e repliche, vedere [Limiti dei servizi in Ricerca di Azure](search-limits-quotas-capacity.md).

Livello|Capacità|Progettato per
----|-----------|-----------
Free|50 MB o 3 indici e 10.000 documenti|Servizio condiviso gratuito usato per la valutazione, l'analisi o piccoli carichi di lavoro. Essendo condiviso con altri sottoscrittori, la velocità effettiva e l'indicizzazione delle query varieranno in base agli altri utenti che usano il servizio.
Basic|3 repliche e 1 partizione|Piccoli carichi di lavoro di produzione su hardware dedicato. Disponibilità elevata.
Standard 1 (S1)|Le partizioni sono di 25 GB ognuna. <br/><br/>Il valore QPS nelle repliche è di circa 15 query al secondo.|Supporta combinazioni flessibili di partizioni (12) e repliche (12); usato per i carichi di lavoro di produzione medi su hardware dedicato. È possibile allocare partizioni e repliche in combinazioni supportate da un numero massimo di 36 unità di ricerca fatturabili.
Standard 2 (S2)|Le partizioni sono di 100 GB ognuna. <br/><br/>Il valore QPS nelle repliche è di circa 60 query al secondo.|Esegue carichi di lavoro di produzione più elevati usando le stesse configurazioni di S1, ma con repliche e partizioni di dimensioni maggiori.
Standard 3 (S3) - Anteprima|Le partizioni sono di 200 GB ognuna. <br/><br/>Il valore QPS nelle repliche è di oltre 60 query al secondo.|Esegue carichi di lavoro di produzione in proporzione più elevati su sistemi di fascia superiore, in configurazioni fino a 12 partizioni o 12 repliche. 
Standard 3 ad alta densità (S3 HD) - Anteprima|Singola partizione da 200 GB. <br/><br/>Il valore QPS nelle repliche è di oltre 60 query al secondo.|Destinato ai clienti con un numero elevato di indici più piccoli.

## Percorso decisionale per la scelta di uno SKU

Il grafico seguente è un subset dei limiti tratto da [Limiti dei servizi in Ricerca di Azure](search-limits-quotas-capacity.md). Elenca i fattori che più probabilmente influenzano la decisione sugli SKU, inclusi i limiti di capacità degli SKU indicati nella tabella precedente, con altri limiti relativi ai tipi di contenuto, ovvero indici e documenti. È possibile fare riferimento a questo grafico

Risorsa|Free|Basic|S1|S2|S3 <br/>(Anteprima) |S3 HD <br/>(Anteprima) 
---|---|---|---|----|---|----
Contratto di servizio (SLA)|No <sup>1</sup> |Sì |Sì |Sì |No <sup>1</sup> |No <sup>1</sup> 
Partizioni massime|N/D |1 |12 |12 |12|1
Indici consentiti per SKU|3|5|50|200|200|1000
Limiti per i documenti|10\.000 in totale|1 milione per servizio|15 milioni per partizione |60 milioni per partizione|120 milioni per partizione |1 milione per indice
Dimensioni della partizione|50 MB totali|2 GB per servizio|25 GB per partizione |100 GB per partizione (con un massimo di 1,2 TB per ogni servizio)|200 GB per partizione, con un massimo di 2,4 TB per ogni servizio|200 GB per l'unica partizione
Repliche massime|N/D |3 |12 |12 |12|12
Query al secondo|N/D|~3 per replica|~15 per replica|~60 per replica|>60 per replica|>60 per replica

<sup>1</sup> Gli SKU Gratuito e Anteprima non includono contratti di servizio. I contratti di servizio vengono applicati quando uno SKU è disponibile a livello generale.

> [AZURE.NOTE] I valori massimi di replica e partizione sono soggetti a una configurazione di fatturazione massima combinata di 36 unità; ciò impone un limite effettivo inferiore rispetto a ciò che implica il valore massimo al valore nominale. Per usare ad esempio il numero massimo di 12 repliche si possono avere non oltre 3 partizioni perché 12 * 3 = 36 unità. Analogamente, ridurre le repliche a 3 per usare il numero di partizioni massimo. Vedere [Scalabilità dei livelli di risorse per carichi di lavoro di query e indicizzazione in Ricerca di Azure](search-capacity-planning.md) per un grafico delle combinazioni consentite.

### Domande frequenti nella scelta di uno SKU

Le domande seguenti consentono di prendere la decisione corretta per gli SKU per il carico di lavoro.

1. Si hanno requisiti in termini di **contratti di servizio**? Restringere la decisione sugli SKU alle tipologie Basic o Standard (non anteprima).
2. **Quanti indici** sono necessari? Una delle variabili principali che influiranno sulla decisione per gli SKU è il numero di indici supportato da ogni SKU. Il supporto degli indici è notevolmente diverso nei piani tariffari più bassi. Il requisito del numero di indici supportato potrebbe essere un fattore determinante nella scelta dello SKU.
3. **Quanti documenti** verranno caricati in ogni indice? Il numero e le dimensioni dei documenti determinano le dimensioni finali dell'indice. Supponendo che si possano stimare le dimensioni previste dell'indice, è possibile confrontare tale numero con le dimensioni della partizione per ogni SKU, estese per il numero di partizioni necessarie per archiviare un indice di tali dimensioni. 
4. **Qual è il carico di query previsto**? Dopo aver determinato i requisiti di archiviazione, prendere in considerazione i carichi di lavoro di query. S2 ed entrambi gli SKU S3 offrono una velocità effettiva quasi equivalente, ma i requisiti in termini di contratto di servizio escludono gli SKU di anteprima. 

La maggior parte dei clienti può prendere in considerazione o escludere uno SKU specifico rispondendo a queste quattro domande. Se non è ancora chiaro quale SKU scegliere, contattare il supporto di Azure per altre informazioni.

## Convalida della decisione: lo SKU offre una risorsa di archiviazione e un valore di query al secondo sufficienti?

Come ultimo passaggio, visitare di nuovo la [pagina relativa ai prezzi](https://azure.microsoft.com/pricing/details/search/) e le [sezioni per servizio e per indice nei limiti di servizio](search-limits-quotas-capacity.md) per verificare le stime rispetto ai limiti di sottoscrizione e di servizio.

Se il prezzo o i requisiti di archiviazione non rientrano nelle stime può essere opportuno effettuare il refactoring dei carichi di lavoro tra una serie di servizi più piccoli, ad esempio. A livello più granulare è possibile progettare nuovamente gli indici perché siano più piccoli oppure usare filtri per rendere le query più efficienti.

> [AZURE.NOTE] I requisiti di archiviazione possono apparire maggiori del necessario se i documenti contengono dati estranei. I documenti sono costituiti idealmente solo da dati o metadati ricercabili. I dati binari che possono aumentare le dimensioni di un documento devono essere archiviati separatamente, ad esempio in una tabella o un archivio BLOB di Azure, con un campo nell'indice che contenga un riferimento URL ai dati esterni. Le dimensioni massime di un singolo documento sono pari a 16 MB o meno, se si caricano più documenti in una richiesta. Per altre informazioni, vedere [Limiti dei servizi in Ricerca di Azure](search-limits-quotas-capacity.md).

## Passaggio successivo

Dopo aver determinato lo SKU corretto, continuare con la procedura:
- [Creare un servizio di ricerca nel portale](search-create-service-portal.md)
- [Modificare l'allocazione di partizioni e repliche per la scalabilità del servizio](search-capacity-planning.md)

<!---HONumber=AcomDC_0608_2016-->