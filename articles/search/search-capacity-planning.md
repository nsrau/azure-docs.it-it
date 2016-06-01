<properties
	pageTitle="Pianificazione della capacità in Ricerca di Azure | Microsoft Azure | Servizio di ricerca cloud ospitato"
	description="In Ricerca di Azure la capacità viene generata da partizioni e repliche, ognuna delle quali costituisce un'unità di ricerca fatturabile."
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

# Pianificazione della capacità in Ricerca di Azure

In Ricerca di Azure è possibile modificare in modo incrementale la capacità di risorse di calcolo specifiche. È possibile aumentare la dimensione delle partizioni, se serve altro spazio di archiviazione e di I/O, o aumentare le repliche per migliorare le prestazioni delle query e dell'indicizzazione.

La scalabilità è disponibile se si effettua il provisioning di un servizio al [livello Basic](http://aka.ms/azuresearchbasic) o a uno dei [livelli Standard](search-limits-quotas-capacity.md).

Per entrambi i livelli, è possibile acquistare capacità a incrementi di *unità di ricerca* (SU, Search Unit). Ogni partizione e ogni replica vengono considerate rispettivamente come una SU.

- Il livello Basic fornisce fino a tre SU per servizio.
- Il livello Standard fornisce fino a 36 SU per servizio.

È necessario scegliere una combinazione di partizioni e repliche che non superi il limite del livello. Ad esempio, non è possibile aumentare le prestazioni del servizio Standard fino a 12 partizioni e 6 repliche, poiché tale operazione richiederebbe 72 unità di ricerca (12 x 6), superando il limite di 36 unità di ricerca per servizio.

In genere, la maggior parte delle applicazioni di ricerca tendono a richiedere più repliche che partizioni. Per i dettagli vedere la sezione relativa alla [disponibilità elevata](#HA).

**Calcolare le unità di ricerca per combinazioni di ricerca specifiche: R X P = SU**

La formula per calcolare il numero di SU necessarie prevede la moltiplicazione del numero di repliche per il numero di partizioni. Ad esempio, 3 repliche moltiplicate per 3 partizioni vengono fatturate come 9 unità di ricerca.

Entrambi i livelli partono da una replica e da una partizione, conteggiate come una unità di ricerca (SU, Search Unit). Questo è l'unico caso in cui una replica e una partizione vengono considerate come una sola unità di ricerca. Ogni risorsa aggiuntiva, che si tratti di una replica o di una partizione, viene conteggiata come SU specifica.

Il costo per SU è determinato dal livello. Il costo per SU è più basso al livello Basic rispetto al livello Standard. Le tariffe per ogni livello sono disponibili in [Prezzi di Ricerca](https://azure.microsoft.com/pricing/details/search/).

## Livello Basic: combinazioni di partizioni e repliche

Il servizio Basic prevede una partizione e fino a tre repliche, per un massimo di 3 SU.

<table cellspacing="0" border="1">
<tr><td><b>3 repliche</b></td><td>3 unità di ricerca</td></tr>
<tr><td><b>2 repliche</b></td><td>2 unità di ricerca</td></tr>
<tr><td><b>1 replica.</b></td><td>1 unità di ricerca</td></tr>
<tr><td></td><td><b>1 partizione</b></td></tr>
</table>

<a id="chart"></a>
## Livello Standard: combinazioni di partizioni e repliche

Questa tabella mostra le unità di ricerca necessarie per supportare le combinazioni di repliche e di partizioni entro il limite di 36 unità di ricerca (SU).

<table cellspacing="0" border="1">
<tr><td><b>12 repliche</b></td><td>12 unità di ricerca</td><td>24 unità di ricerca</td><td>36 unità di ricerca</td><td>N/D</td><td>N/D</td><td>N/D</td></tr>
<tr><td><b>6 repliche</b></td><td>6 unità di ricerca</td><td>12 unità di ricerca</td><td>18 unità di ricerca</td><td>24 unità di ricerca</td><td>36 unità di ricerca</td><td>N/D</td></tr>
<tr><td><b>5 repliche</b></td><td>5 unità di ricerca</td><td>10 unità di ricerca</td><td>15 unità di ricerca</td><td>20 unità di ricerca</td><td>30 unità di ricerca</td><td>N/D</td></tr>
<tr><td><b>4 repliche</b></td><td>4 unità di ricerca</td><td>8 unità di ricerca</td><td>12 unità di ricerca</td><td>16 unità di ricerca</td><td>24 unità di ricerca</td><td>N/D </td></tr>
<tr><td><b>3 repliche</b></td><td>3 unità di ricerca</td><td>6 unità di ricerca</td><td>9 unità di ricerca</td><td>12 unità di ricerca</td><td>18 unità di ricerca</td><td>36 unità di ricerca</td></tr>
<tr><td><b>2 repliche</b></td><td>2 unità di ricerca</td><td>4 unità di ricerca</td><td>6 unità di ricerca</td><td>8 unità di ricerca</td><td>12 unità di ricerca</td><td>24 unità di ricerca</td></tr>
<tr><td><b>1 replica.</b></td><td>1 unità di ricerca</td><td>2 unità di ricerca</td><td>3 unità di ricerca</td><td>4 unità di ricerca</td><td>6 unità di ricerca</td><td>12 unità di ricerca</td></tr>
<tr><td>N/D</td><td><b>1 partizione</b></td><td><b>2 partizioni</b></td><td><b>3 partizioni</b></td><td><b>4 partizioni</b></td><td><b>6 partizioni</b></td><td><b>12 partizioni</b></td></tr>
</table>

Le unità di ricerca, i prezzi e le capacità sono illustrati in dettaglio nel sito web di Azure. Per altre informazioni, vedere [Dettagli prezzi](https://azure.microsoft.com/pricing/details/search/).

> [AZURE.NOTE] Il numero di repliche e partizioni si deve dividere equamente per 12 (in particolare 1, 2, 3, 4, 6, 12). In questo modo Ricerca di Azure divide preventivamente ogni indice in 12 partizioni in modo che possa essere distribuito tra le partizioni. Ad esempio, se il servizio dispone di tre partizioni e si crea un nuovo indice, ogni partizione conterrà 4 partizioni dell'indice. Come viene suddiviso in partizioni un indice in Ricerca di Azure è un dettaglio di implementazione, soggetto a modifiche nella prossima versione. Sebbene il numero attualmente sia 12, tale numero potrebbe non essere 12 in futuro.

<a id="HA"></a>
## Scegliere una combinazione di partizioni e repliche per la disponibilità elevata

Poiché è facile e relativamente veloce eseguire la scalabilità verticale, è consigliabile in genere iniziare con una partizione e una o due repliche e quindi eseguire la scalabilità verticale come vengono compilati i volumi di query. Per molte distribuzioni, una partizione fornisce archiviazione e I/O sufficienti (15 milioni di documenti per partizione).

I carichi di lavoro delle query, tuttavia, si basano sulle repliche. Se è necessaria maggiore velocità effettiva o la disponibilità elevata, è possibile richiedere repliche aggiuntive.

Le indicazioni generali per la disponibilità elevata sono:

- 2 repliche per la disponibilità elevata dei carichi di lavoro di sola lettura (query)
- 3 o più repliche per la disponibilità elevata dei carichi di lavoro di lettura/scrittura (query e indicizzazione)

Attualmente, non esiste alcun meccanismo incorporato per il ripristino di emergenza. L'aggiunta di partizioni o repliche sarebbe una strategia errata per soddisfare gli obiettivi di ripristino di emergenza. In alternativa, è possibile aggiungere la ridondanza a livello di servizio. Per una discussione più approfondita delle soluzioni alternative, vedere [questo post di forum](https://social.msdn.microsoft.com/Forums/ee108a26-00c5-49f6-b1ff-64c66c8b828a/dr-and-high-availability-for-azure-search?forum=azuresearch).

> [AZURE.NOTE] Tenere presente che la scalabilità e i contratti di servizio sono funzionalità del servizio standard. Il servizio gratuito è disponibile a un livello di risorse fisso, con le repliche e le partizioni condivise da più sottoscrittori. Se si è partiti dal servizio gratuito e ora si desidera aggiornare, è necessario creare un nuovo servizio Ricerca di Azure a livello di standard e quindi ricaricare gli indici e i dati nel nuovo servizio. Per istruzioni sul provisioning del servizio, vedere [Creare un servizio Ricerca di Azure nel portale](search-create-service-portal.md).

## Informazioni sulle partizioni e sulle repliche

Le **partizioni** forniscono archiviazione e I/O. Un singolo servizio di ricerca può avere un massimo di 12 partizioni. Ogni partizione viene fornita con un limite di 15 milioni di documenti o 25 GB di spazio di archiviazione, a seconda di quale valore si verifica per primo. Se si aggiungono le partizioni, il servizio di ricerca può caricare più documenti. Un servizio con una sola partizione che archivia inizialmente fino a 25 GB di dati, ad esempio, può archiviare a 50 GB quando si aggiunge una seconda partizione al servizio.

Le **repliche** sono copie del motore di ricerca. Ogni singolo servizio di ricerca può avere un massimo di 12 repliche. Sono necessarie almeno 2 repliche per la disponibilità di lettura (query) e almeno 3 repliche per la disponibilità di lettura e scrittura (query, indicizzazione).

In ogni replica viene eseguita una copia di ogni indice. Quando si aggiungono le repliche, copie aggiuntive dell'indice vengono portate online per supportare i carichi di lavoro di query maggiori e per bilanciare il carico delle richieste su più repliche. Se si dispone di più indici, ad esempio 6 indici e 3 repliche, ogni replica avrà una copia di tutti i 6 indici.

Si noti che non vengono fornite le stime per query al secondo (QPS), in quanto l'esecuzione di query può variare notevolmente a seconda della complessità della query e dei carichi di lavoro concorrenti. In media, una replica può soddisfare circa 15 QPS, ma la velocità effettiva sarà leggermente superiore o inferiore a seconda della complessità della query (le query con facet sono più complesse) e della latenza di rete. Inoltre, è importante riconoscere che mentre l'aggiunta delle repliche aggiunge in modo definito scalabilità e prestazioni, il risultato finale non è strettamente lineare: l'aggiunta di 3 repliche non garantisce di triplicare la velocità effettiva. La latenza delle query è un indicatore che potrebbe essere necessario per le repliche aggiuntive. Per altre informazioni sulle query al secondo, inclusi gli approcci per la stima delle query al secondo per i carichi di lavoro, vedere [Gestire il servizio di ricerca](search-manage.md).

<!---HONumber=AcomDC_0518_2016-->