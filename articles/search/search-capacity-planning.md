<properties
	pageTitle="Ridimensionare i livelli di risorse per le query e l'indicizzazione dei carichi di lavoro in Ricerca di Azure | Microsoft Azure"
	description="La pianificazione della capacità in Ricerca di Azure si basa su combinazioni di risorse di calcolo costituite da partizioni e repliche, in cui ogni risorsa è quotata in unità di ricerca fatturabili."
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
	ms.date="05/25/2016"
	ms.author="heidist"/>

# Ridimensionare i livelli di risorse per le query e l'indicizzazione dei carichi di lavoro in Ricerca di Azure

In Ricerca di Azure è possibile modificare in modo incrementale la capacità di risorse di calcolo specifiche. È possibile aumentare le partizioni, se serve altro spazio di archiviazione e di I/O, o aumentare le repliche per migliorare le prestazioni delle query e dell'indicizzazione.

La scalabilità è disponibile se si effettua il provisioning di un servizio al [livello Basic](http://aka.ms/azuresearchbasic) o a uno dei [livelli Standard](search-limits-quotas-capacity.md).

Per tutti i livelli fatturabili è possibile acquistare capacità a incrementi di *unità di ricerca* (SU, Search Unit). Ogni partizione e ogni replica vengono considerate rispettivamente come una SU.

- Il livello Basic fornisce fino a tre SU per servizio.
- Il livello Standard fornisce fino a 36 SU per servizio.

Scegliere una combinazione di partizioni e repliche che non superi il limite del livello. Se si usa il portale per aumentare le prestazioni, verranno imposti limiti alle combinazioni consentite.

In genere, le applicazioni di ricerca richiedono più repliche che partizioni. Per i dettagli, vedere la sezione successiva relativa alla [disponibilità elevata](#HA).

<a id="HA"></a>
## Assegnazione di risorse per la disponibilità elevata

Poiché è facile e relativamente veloce eseguire la scalabilità verticale, è consigliabile in genere iniziare con una partizione e una o due repliche e quindi eseguire la scalabilità verticale come vengono compilati i volumi di query. Per molte distribuzioni, una partizione fornisce archiviazione e I/O sufficienti (15 milioni di documenti per partizione).

I carichi di lavoro delle query, tuttavia, vengono eseguiti principalmente sulle repliche. Se è necessaria maggiore velocità effettiva o la disponibilità elevata, è possibile richiedere repliche aggiuntive.

Le indicazioni generali per la disponibilità elevata sono:

- 2 repliche per la disponibilità elevata dei carichi di lavoro di sola lettura (query)
- 3 o più repliche per la disponibilità elevata dei carichi di lavoro di lettura/scrittura (query e indicizzazione)

## Ripristino di emergenza

Attualmente, non esiste alcun meccanismo incorporato per il ripristino di emergenza. L'aggiunta di partizioni o repliche sarebbe una strategia errata per soddisfare gli obiettivi di ripristino di emergenza. Il metodo più comune consiste nell'aggiungere ridondanza a livello di servizio con il provisioning di un secondo servizio di ricerca in un'altra area.

> [AZURE.NOTE] Tenere presente che la scalabilità e i contratti di servizio sono funzionalità dei livelli di servizio Basic e Standard. Il servizio gratuito è disponibile a un livello di risorse fisso, con le repliche e le partizioni condivise da più sottoscrittori. Se si è iniziato con il servizio gratuito e ora si desidera eseguire un aggiornamento, è necessario creare un nuovo servizio Ricerca di Azure al livello Basic o Standard e quindi ricaricare gli indici e i dati nel nuovo servizio. Per istruzioni sul provisioning del servizio, vedere [Creare un servizio Ricerca di Azure nel portale](search-create-service-portal.md).

## Terminologia: partizioni e repliche

Le **partizioni** forniscono archiviazione e I/O. Un singolo servizio di ricerca può avere un massimo di 12 partizioni. Ogni partizione viene fornita con un limite di 15 milioni di documenti o 25 GB di spazio di archiviazione, a seconda di quale valore si verifica per primo. Se si aggiungono le partizioni, il servizio di ricerca può caricare più documenti. Un servizio con una sola partizione che archivia inizialmente fino a 25 GB di dati, ad esempio, può archiviare a 50 GB quando si aggiunge una seconda partizione al servizio.

Le **repliche** sono copie del motore di ricerca. Ogni singolo servizio di ricerca può avere un massimo di 12 repliche. Sono necessarie almeno 2 repliche per la disponibilità di lettura (query) e almeno 3 repliche per la disponibilità di lettura e scrittura (query, indicizzazione).

## Aumentare le prestazioni delle query con le repliche

La latenza delle query è un indicatore che potrebbe essere necessario per le repliche aggiuntive. In genere, un primo passo per migliorare le prestazioni delle query consiste nell'aggiungere altre repliche.

In ogni replica viene eseguita una copia di ogni indice. Quando si aggiungono le repliche, copie aggiuntive dell'indice vengono portate online per supportare i carichi di lavoro di query maggiori e per bilanciare il carico delle richieste su più repliche. Se si dispone di più indici, ad esempio 6 indici e 3 repliche, ogni replica avrà una copia di tutti i 6 indici.

Si noti che non vengono fornite stime precise per query al secondo. Le prestazioni, infatti, dipendono dalla complessità della query e dai carichi di lavoro concorrenti. In media, una replica può soddisfare circa 15 QPS, ma la velocità effettiva sarà leggermente superiore o inferiore a seconda della complessità della query (le query con facet sono più complesse) e della latenza di rete. Inoltre, è importante riconoscere che mentre l'aggiunta delle repliche aggiunge in modo definito scalabilità e prestazioni, il risultato finale non è strettamente lineare: l'aggiunta di 3 repliche non garantisce di triplicare la velocità effettiva.

Per altre informazioni su query al secondo, inclusi i metodi per la stima di tale valore in funzione dei carichi di lavoro, vedere [Gestire il servizio di ricerca](search-manage.md).

## Aumentare le prestazioni dell'indicizzazione con le partizioni

Le applicazioni di ricerca che richiedono l'aggiornamento dei dati quasi in tempo reale avranno bisogno in proporzione di più partizioni che repliche. L'aggiunta di partizioni distribuisce le operazioni di lettura/scrittura su un numero più ampio di risorse di calcolo. Offre inoltre più spazio su disco per l'archiviazione di documenti e indici aggiuntivi.

L'esecuzione di query su indici di dimensioni maggiori può richiedere tempi più lunghi. Di conseguenza, si noterà che ogni aumento incrementale delle partizioni richiede un aumento delle repliche proporzionale ma più limitato. Come accennato in precedenza, la complessità delle query e il volume di query influiranno sulla rapidità di esecuzione delle query.

## Livello Basic: combinazioni di partizioni e repliche

Il servizio Basic prevede una partizione e fino a tre repliche, per un massimo di 3 SU.

<a id="chart"></a>
## Livello Standard: combinazioni di partizioni e repliche

Questa tabella mostra le unità di ricerca necessarie per supportare le combinazioni di repliche e di partizioni entro il limite di 36 unità di ricerca (SU).

- |- |- |- |- |- |- |
---|----|---|---|---|---|---|
**12 repliche**|12 unità di ricerca|24 unità di ricerca|36 unità di ricerca|N/D|N/D|N/D|
**6 repliche**|6 unità di ricerca|12 unità di ricerca|18 unità di ricerca|24 unità di ricerca|36 unità di ricerca|N/D|
**5 repliche**|5 unità di ricerca|10 unità di ricerca|15 unità di ricerca|20 unità di ricerca|30 unità di ricerca|N/D|
**4 repliche**|4 unità di ricerca|8 unità di ricerca <|12 unità di ricerca|16 unità di ricerca|24 unità di ricerca|N/D|
**3 repliche**|3 unità di ricerca|6 unità di ricerca|9 unità di ricerca|12 unità di ricerca|18 unità di ricerca|36 unità di ricerca|
**2 repliche**|2 unità di ricerca|4 unità di ricerca|6 unità di ricerca|8 unità di ricerca|12 unità di ricerca|24 unità di ricerca|
**1 replica.**|1 unità di ricerca|2 unità di ricerca|3 unità di ricerca|4 unità di ricerca|6 unità di ricerca|12 unità di ricerca|
N/D|**1 partizione**|**2 partizioni**|**3 partizioni** <|**4 partizioni**|**6 partizioni**|**12 partizioni**|


Le unità di ricerca, i prezzi e le capacità sono illustrati in dettaglio nel sito web di Azure. Per altre informazioni, vedere [Dettagli prezzi](https://azure.microsoft.com/pricing/details/search/).

> [AZURE.NOTE] Il numero di repliche e partizioni si deve dividere equamente per 12 (in particolare 1, 2, 3, 4, 6, 12). In questo modo Ricerca di Azure divide preventivamente ogni indice in 12 partizioni in modo che possa essere distribuito tra le partizioni. Ad esempio, se il servizio dispone di tre partizioni e si crea un nuovo indice, ogni partizione conterrà 4 partizioni dell'indice. Come viene suddiviso in partizioni un indice in Ricerca di Azure è un dettaglio di implementazione, soggetto a modifiche nella prossima versione. Sebbene il numero attualmente sia 12, tale numero potrebbe non essere 12 in futuro.

## Calcolare le unità di ricerca per combinazioni di ricerca specifiche: R X P = SU

La formula per calcolare il numero di SU necessarie prevede la moltiplicazione del numero di repliche per il numero di partizioni. Ad esempio, 3 repliche moltiplicate per 3 partizioni vengono fatturate come 9 unità di ricerca.

Entrambi i livelli partono da una replica e da una partizione, conteggiate come una unità di ricerca (SU, Search Unit). Questo è l'unico caso in cui una replica e una partizione vengono considerate come una sola unità di ricerca. Ogni risorsa aggiuntiva, che si tratti di una replica o di una partizione, viene conteggiata come SU specifica.

Il costo per SU è determinato dal livello. Il costo per SU è più basso al livello Basic rispetto al livello Standard. Le tariffe per ogni livello sono disponibili in [Prezzi di Ricerca](https://azure.microsoft.com/pricing/details/search/).

<!---HONumber=AcomDC_0601_2016-->