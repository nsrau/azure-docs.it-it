---
title: Informazioni sulla fattura di Azure Cosmos DB
description: Questo articolo aiuta a comprendere la fattura di Azure Cosmos DB con alcuni esempi.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: a097539e51aa2a2130dead236d553d60f2ebb89d
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65965629"
---
# <a name="understand-your-azure-cosmos-db-bill"></a>Informazioni sulla fattura di Azure Cosmos DB

In quanto servizio di database cloud nativo completamente gestito, Azure Cosmos DB semplifica la fatturazione, addebitando solo la velocità effettiva di cui è stato effettuato il provisioning e le risorse di archiviazione utilizzate. Non esistono Nessun spese di licenza aggiuntive, hardware, i costi di utilità o i costi di funzionalità rispetto a un'istanza locale o basato su IaaS alternative. Quando si considera il più funzionalità area di Azure Cosmos DB, il servizio di database offre una riduzione sostanziale dei costi rispetto ai valori esistenti in locale o soluzioni IaaS.

Con Azure Cosmos DB i costi vengono addebitati su base oraria a seconda della velocità effettiva di cui viene effettuato il provisioning e a seconda delle risorse di archiviazione utilizzate. Per la velocità effettiva di cui è stato effettuato il provisioning, l'unità per la fatturazione è 100 UR al secondo all'ora, l'importo addebitato è di 0,008 dollari all'ora, in base al prezzo pubblico standard. Vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/cosmos-db/). Per le risorse di archiviazione utilizzate, vengono fatturati 0,25 dollari al mese per ogni GB di spazio di archiviazione. Vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/cosmos-db/). 

Questo articolo presenta alcuni esempi che consentono di comprendere i dettagli della fattura mensile. I numeri visualizzati negli esempi possono essere diversi se per i contenitori di Azure Cosmos in uso è stato effettuato il provisioning di una quantità diversa di velocità effettiva, se i contenitori si estendono su più aree o vengono eseguiti per un periodo diverso nel corso di un mese.

## <a name="billing-examples"></a>Esempi di fatturazione

### <a name="billing-example---throughput-on-a-container-full-month"></a>Esempio di fatturazione: velocità effettiva in un contenitore (mese completo)

* Si supponga di configurare una velocità effettiva di 1.000 UR/sec per un contenitore esistente 24 ore * 30 giorni al mese = 720 ore totali.  

* 1.000 UR/sec corrisponde a 10 unità di 100 UR/sec all'ora per ogni ora di esistenza dei contenitori (1.000/100 = 10). 

* Moltiplicando 10 unità all'ora per il costo di 0,008 dollari (per 100 UR/sec all'ora) si ottiene 0,08 dollari all'ora. 

* Moltiplicando 0,08 dollari all'ora per il numero di ore del mese, si ottiene 0,08 dollari * 24 ore * 30 giorni = 57,60 dollari al mese.  

* Il totale della fattura mensile indicherà 7.200 unità di 100 UR, con un costo di 57,60 dollari.

### <a name="billing-example---throughput-on-a-container-partial-month"></a>Esempio di fatturazione: velocità effettiva in un contenitore (mese parziale)

* Si supponga di creare un contenitore con una velocità effettiva di cui è stato effettuato il provisioning di 2.500 UR/sec. Il contenitore esiste per 24 ore durante il mese (ad esempio, viene eliminato 24 ore dopo la sua creazione).  

* La fattura quindi riporterà 600 unità (2.500 UR/sec / 100 UR/sec/unità * 24 ore). Il costo sarà di 4,80 dollari (600 unità * 0,008 dollari/unità).

* L'importo totale della fattura per il mese sarà di 4,80 dollari.

### <a name="billing-rate-if-storage-size-changes"></a>Tariffa di fatturazione in caso di modifica delle dimensioni di archiviazione

La capacità di archiviazione viene fatturata in unità della quantità oraria massima di dati archiviati, in GB, in un periodo mensile. Se, ad esempio, sono stati utilizzati 100 GB di spazio di archiviazione nella prima metà del mese e 50 GB nella seconda, verrà addebitato l'equivalente di 75 GB di spazio di archiviazione per tale mese.

### <a name="billing-rate-when-container-or-a-set-of-containers-are-active-for-less-than-an-hour"></a>Tariffa di fatturazione se un contenitore o un set di contenitori è attivo per meno di un'ora

Verrà addebitata una tariffa fissa per ogni ora di esistenza del contenitore o del database, indipendentemente dall'utilizzo o dal fatto che il contenitore o il database sia attivo per più o meno di un'ora. Se, ad esempio, si crea un contenitore o un database e lo si elimina dopo 5 minuti, la fattura riporterà un'ora.

### <a name="billing-rate-when-throughput-on-a-container-or-database-scales-updown"></a>Tariffa di fatturazione in caso di aumento o riduzione della velocità effettiva di un contenitore o di un database

Se alle 9:30 si aumenta la velocità effettiva di cui è stato effettuato il provisioning da 400 UR/sec a 1.000 UR/sec e quindi si torna a 400 UR/sec alle 10:45, verranno addebitate due ore di 1.000 UR/sec. 

Se alle 9:30 si aumenta la velocità effettiva di cui è stato effettuato il provisioning per un contenitore o un set di contenitori da 100 K UR/sec a 200 K UR/sec e quindi si torna a 100 K UR/sec alle 10:45, verranno addebitate due ore di 200 K UR/sec.

### <a name="billing-example-multiple-containers-each-with-dedicated-provisioned-throughput-mode"></a>Esempio di fatturazione: più contenitori, ognuno in modalità di velocità effettiva di cui è stato effettuato il provisioning dedicata

* Se si crea un account Azure Cosmos nell'area Stati Uniti orientali 2 con due contenitori con velocità effettiva di cui è stato effettuato il provisioning pari rispettivamente a 500 UR/sec e a 700 UR/sec, si ottiene una velocità effettiva totale di cui è stato effettuato il provisioning pari a 1.200 UR/sec.  

* L'addebito sarà di 1.200/100 * 0,008 dollari = 0,096 dollari/ora. 

* Se è necessario cambiare la velocità effettiva e si aumenta la capacità di ogni contenitore di 500 UR/sec creando al contempo un nuovo contenitore senza limiti con 20.000 UR/sec, la capacità complessiva di cui è stato effettuato il provisioning sarà pari a 22.200 UR/sec (1.000 UR/sec + 1.200 UR/sec + 20.000 UR/sec).  

* La fattura sarà quindi di 0,008 x 222 = 1,776 dollari/ora. 

* In un mese di 720 ore (24 ore * 30 giorni), se per 500 ore la velocità effettiva di cui è stato effettuato il provisioning è stata pari a 1.200 UR/sec e per le restanti 220 ore è stata pari a 22.200 UR/sec, la fattura mensile indicherà: 500 x 0,096 dollari/ora + 220 x 1,776 dollari/ora =  438,72 dollari/mese.

![Esempio di fattura con velocità effettiva dedicata](./media/understand-your-bill/bill-example1.png)

### <a name="billing-example-containers-with-shared-throughput-mode"></a>Esempio di fatturazione: contenitori con velocità effettiva condivisa

* Se si crea un account Azure Cosmos negli Stati Uniti orientali 2 con due database Azure Cosmos (con un set di contenitori che condividono la velocità effettiva a livello di database) con velocità effettiva di cui è stato effettuato il provisioning pari a 50 K UR/sec e 70 K UR/sec rispettivamente, la velocità effettiva di cui è stato effettuato il provisioning totale è di 120 K UR/sec.  

* L'addebito sarà di 1200 x 0,008 dollari = 9,60 dollari/ora. 

* Se è necessario cambiare la velocità effettiva e per ogni database si aumenta la velocità effettiva di cui è stato effettuato il provisioning di 10 K UR/sec e si aggiunge un nuovo contenitore al primo database con modalità di velocità effettiva dedicata di 15 K UR/sec al database di velocità effettiva condiviso, la capacità di cui è stato effettuato il provisioning complessiva è di 155 K UR/sec (60 K UR/sec + 80 K UR/sec + 15 K UR/sec).  

* La fattura passerà quindi a: 1.550 * 0,008 dollari = 12,40 dollari/ora.  

* In un mese di 720 ore, se per 300 ore la velocità effettiva di cui è stato effettuato il provisioning è stata pari a 120 K UR/sec e per le restanti 420 ore è stata pari a 155 K UR/sec, la fattura mensile indicherà: 300 x 9,60 dollari/ora + 420 x 12,40 dollari/ora =  2.880 dollari + 5.208 dollari = 8.088 dollari/mese. 

![Esempio di fattura con velocità effettiva condivisa](./media/understand-your-bill/bill-example2.png)

## <a name="billing-examples-with-geo-replication-and-multi-master"></a>Esempi di fatturazione con replica geografica e multimaster  

Nell'account di database di Azure Cosmos DB è possibile aggiungere o rimuovere in qualsiasi momento aree di Azure dislocate in qualsiasi parte del mondo. La velocità effettiva configurata per i vari database e contenitori di Azure Cosmos DB viene riservata in ogni area di Azure associata all'account di database di Azure Cosmos DB. Se la somma della velocità effettiva di cui è stato effettuato il provisioning (UR/sec) configurata in tutti i database e in tutti i contenitori all'interno dell'account del database di Azure Cosmos account (con provisioning all'ora) è T e il numero di aree di Azure associate all'account di database è N, la velocità effettiva di cui è stato effettuato il provisioning totale per un'ora specifica, (a) per un account di database di Azure Cosmos configurato con un'area di scrittura singola è uguale a T x N UR/sec e (b) per un account di database di Azure Cosmos configurato con tutte le aree in grado di elaborare operazioni di scrittura è uguale a T x (N + 1) UR/sec, rispettivamente. La velocità effettiva di cui è stato effettuato il provisioning (area di scrittura singola) costa 0,008 dollari/ora per 100 UR/sec e la velocità effettiva di cui è stato effettuato il provisioning con più aree scrivibili (configurazione multimaster) costa 0,016 dollari/ora per 100 UR/sec (vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/cosmos-db/)). Indipendentemente dal numero di aree di scrittura, Azure Cosmos DB consente la lettura dei dati da qualsiasi area.

### <a name="billing-example-multi-region-azure-cosmos-account-single-region-writes"></a>Esempio di fatturazione: account di Azure Cosmos multiarea, operazioni di scrittura in un'area singola

Si supponga di avere un contenitore Azure Cosmos negli Stati Uniti occidentali. Il contenitore viene creato con una velocità effettiva 10 K UR/sec e questo mese viene archiviato 1 TB di dati. Si supponga di aggiungere tre aree (Stati Uniti orientali, Europa settentrionale e Asia orientale) all'account di Azure Cosmos, ognuna con le stesse risorse di archiviazione e con la stessa velocità effettiva. La fattura mensile totale sarà la seguente (presupponendo un mese di 30 giorni). La fattura sarà la seguente: 

|**Elemento** |**Utilizzo (mese)** |**Tariffa** |**Costo mensile** |
|---------|---------|---------|-------|
|Fattura per la velocità effettiva per un contenitore negli Stati Uniti occidentali      | 10 K UR/sec * 24 * 30    |$ 0,008 ogni 100 UR/sec all'ora   |$ 576|
|Fattura per la velocità effettiva per 3 aree aggiuntive: Stati Uniti orientali, Europa settentrionale e Asia orientale       | 3 * 10 K UR/sec * 24 * 30    |$ 0,008 ogni 100 UR/sec all'ora  |$ 1.728|
|Fattura per le risorse di archiviazione per un contenitore negli Stati Uniti occidentali      | 250 GB    |$ 0,25/GB  |$ 62,50|
|Fattura per le risorse di archiviazione per 3 aree aggiuntive: Stati Uniti orientali, Europa settentrionale e Asia orientale      | 3 * 250 GB    |$ 0,25/GB  |$ 187,50|
|**Totale**     |     |  |**$ 2.554**|

*Si supponga anche che vengano trasmessi 100 GB di dati in uscita ogni mese dal contenitore negli Stati Uniti occidentali verso gli Stati Uniti orientali, l'Europa settentrionale e l'Asia orientale. Viene fatturato il traffico in uscita in base alle tariffe per il trasferimento di dati.*

### <a name="billing-example-multi-region-azure-cosmos-account-multi-region-writes"></a>Esempio di fatturazione: account di Azure Cosmos multiarea, operazioni di scrittura in più aree

Si supponga di creare un contenitore Azure Cosmos negli Stati Uniti occidentali. Il contenitore viene creato con una velocità effettiva 10 K UR/sec e questo mese viene archiviato 1 TB di dati. Si supponga di aggiungere tre aree (Stati Uniti orientali, Europa settentrionale e Asia orientale), ognuna con le stesse risorse di archiviazione e con la stessa velocità effettiva e che si voglia avere la possibilità di scrivere nei contenitori di tutte le aree associate all'account di Azure Cosmos. La fattura mensile totale sarà la seguente (presupponendo un mese di 30 giorni):

|**Elemento** |**Utilizzo (mese)**|**Tariffa** |**Costo mensile** |
|---------|---------|---------|-------|
|Fattura per la velocità effettiva per un contenitore negli Stati Uniti occidentali (operazioni di scrittura in tutte le aree)       | 10 K UR/sec * 24 * 30    |$ 0,016 ogni 100 UR/sec all'ora    |$ 1.152 |
|Fattura per la velocità effettiva per 3 aree aggiuntive: Stati Uniti orientali, Europa settentrionale e Asia orientale (operazioni di scrittura in tutte le aree)        | (3+1) * 10 K UR/sec * 24 * 30    |$ 0,016 ogni 100 UR/sec all'ora   |$ 4.608 |
|Fattura per le risorse di archiviazione per un contenitore negli Stati Uniti occidentali      | 250 GB    |$ 0,25/GB  |$ 62,50|
|Fattura per le risorse di archiviazione per 3 aree aggiuntive: Stati Uniti orientali, Europa settentrionale e Asia orientale      | 3 * 250 GB    |$ 0,25/GB  |$ 187,50|
|**Totale**     |     |  |**$ 6.010**|

*Si supponga anche che vengano trasmessi 100 GB di dati in uscita ogni mese dal contenitore negli Stati Uniti occidentali verso gli Stati Uniti orientali, l'Europa settentrionale e l'Asia orientale. Viene fatturato il traffico in uscita in base alle tariffe per il trasferimento di dati.*

### <a name="billing-example-azure-cosmos-account-with-multi-master-database-level-throughput-including-dedicated-throughput-mode-for-some-containers"></a>Esempio di fatturazione: Account Azure Cosmos con velocità effettiva multimaster a livello di database, inclusa la modalità di velocità effettiva dedicata per alcuni contenitori

Si consideri l'esempio seguente, un account di Azure Cosmos multiarea in cui tutte le aree sono scrivibili (configurazione multimaster). Per semplicità, si suppone che le dimensioni delle risorse di archiviazione rimangano costanti e non cambino. Nel corso del mese, la velocità effettiva di cui è stato effettuato il provisioning è variata nel modo seguente (presupponendo 30 giorni o 720 ore): 

[0-100 ore]  

* È stato creato un account di Azure Cosmos per tre aree (Stati Uniti occidentali, Stati Uniti orientali, Europa settentrionale), in cui tutte le aree sono scrivibili 

* È stato creato un database (D1) con 10 K UR/sec di velocità effettiva condivisa 

* È stato creato un database (D2) con 30 K UR/sec di velocità effettiva condivisa  

* È stato creato un contenitore (C1) con 20 K UR/sec di velocità effettiva dedicata 

[101-200 ore]  

* Il database (D1) è stato aumentato a 50 K UR/sec 

* Il database (D2) è stato aumentato a 70 K UR/sec  

* Il contenitore (C1) è stato eliminato  

[201-300 ore]  

* È stato creato di nuovo il contenitore (C1) con 20 K UR/sec di velocità effettiva dedicata 

[301-400 ore]  

* È stata rimossa una delle aree dall'account di Azure Cosmos (il numero di aree scrivibili ora è 2) 

* Il database (D1) è stato ridotto a 10 K UR/sec 

* Il database (D2) è stato aumentato a 80 K UR/sec  

* Il contenitore (C1) è stato eliminato di nuovo 

[401-500 ore]  

* Il database (D2) è stato ridotto a 10 K UR/sec  

* È stato creato di nuovo il contenitore (C1) con 20 K UR/sec di velocità effettiva dedicata 

[501-700 ore]  

* Il database (D1) è stato aumentato a 20 K UR/sec  

* Il database (D2) è stato aumentato a 100 K UR/sec  

* Il contenitore (C1) è stato eliminato di nuovo  

[701-720 ore]  

* Il database (D2) è stato ridotto a 50 K UR/sec  

Le modifiche della velocità effettiva totale durante le 720 ore del mese sono illustrate nella figura seguente: 

![Esempio reale](./media/understand-your-bill/bill-example3.png)

La fattura mensile totale (presupponendo 30 giorni/720 ore al mese) verrà calcolata come segue:

|**Ore**  |**UR/sec** |**Elemento** |**Utilizzo (orario)** |**Costii** |
|---------|---------|---------|-------|-------|
|[0-100] |D1: 10 K <br/>D2: 30 K <br/>C1: 20 K |Fattura per la velocità effettiva per un contenitore negli Stati Uniti occidentali (operazioni di scrittura in tutte le aree)  | `D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br/>`D2: 30 K RU/sec/100 * $0.016 * 100 hours = $480` <br/>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |$ 960  |
| | |Fattura per la velocità effettiva per 2 aree aggiuntive: Stati Uniti orientali, Europa settentrionale (tutte le aree sono scrivibili)  |`(2 + 1) * (60 K RU/sec /100 * $0.016) * 100 hours = $2,880`  |$ 2.880  |
|[101-200] |D1: 50 K <br/>D2: 70 K <br/>C1: -- |Fattura per la velocità effettiva per un contenitore negli Stati Uniti occidentali (operazioni di scrittura in tutte le aree)  |`D1: 50 K RU/sec/100 * $0.016 * 100 hours = $800` <br/>`D2: 70 K RU/sec/100 * $0.016 * 100 hours = $1,120` |$ 1920  |
| | |Fattura per la velocità effettiva per 2 aree aggiuntive: Stati Uniti orientali, Europa settentrionale (tutte le aree sono scrivibili)  |`(2 + 1) * (120 K RU/sec /100 * $0.016) * 100 hours = $5,760`  |$ 5.760  |
|[201-300]  |D1: 50 K <br/>D2: 70 K <br/>C1: 20 K |Fattura per la velocità effettiva per un contenitore negli Stati Uniti occidentali (operazioni di scrittura in tutte le aree)  |`D1: 50 K RU/sec/100 * $0.016 * 100 hours = $800` <br/>`D2: 70 K RU/sec/100 * $0.016 * 100 hours = $1,120` <br/>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |$2,240  |
| | |Fattura per la velocità effettiva per 2 aree aggiuntive: Stati Uniti orientali, Europa settentrionale (tutte le aree sono scrivibili)  |`(2 + 1) * (140 K RU/sec /100 * $0.016-) * 100 hours = $6,720` |$ 6.720 |
|[301-400] |D1: 10 K <br/>D2: 80 K <br/>C1: -- |Fattura per la velocità effettiva per un contenitore negli Stati Uniti occidentali (operazioni di scrittura in tutte le aree)  |`D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br/>`D2: 80 K RU/sec/100 * $0.016 * 100 hours = $1,280`  |$ 1.440   |
| | |Fattura per la velocità effettiva per 2 aree aggiuntive: Stati Uniti orientali, Europa settentrionale (tutte le aree sono scrivibili)  |`(1 + 1) * (90 K RU/sec /100 * $0.016) * 100 hours = $2,880`  |$ 2.880  |
|[401-500] |D1: 10 K <br>D2: 10 K <br>C1: 20 K |Fattura per la velocità effettiva per un contenitore negli Stati Uniti occidentali (operazioni di scrittura in tutte le aree)  |`D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br>`D2: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |$ 640  |
| | |Fattura per la velocità effettiva per 2 aree aggiuntive: Stati Uniti orientali, Europa settentrionale (tutte le aree sono scrivibili)  |`(1 + 1) * (40 K RU/sec /100 * $0.016) * 100 hours = $1,280`  |$ 1.280  |
|[501-700] |D1: 20 K <br>D2: 100 K <br>C1: -- |Fattura per la velocità effettiva per un contenitore negli Stati Uniti occidentali (operazioni di scrittura in tutte le aree)  |`D1: 20 K RU/sec/100 * $0.016 * 200 hours = $640` <br>`D2: 100 K RU/sec/100 * $0.016 * 200 hours = $3,200` |$ 3.840  |
| | |Fattura per la velocità effettiva per 2 aree aggiuntive: Stati Uniti orientali, Europa settentrionale (tutte le aree sono scrivibili)  |`(1 + 1) * (120 K RU/sec /100 * $0.016) * 200 hours = $1,280`  |$ 7.680  |
|[701-720] |D1: 20 K <br/>D2: 50 K <br/>C1: -- |Fattura per la velocità effettiva per un contenitore negli Stati Uniti occidentali (operazioni di scrittura in tutte le aree)  |`D1: 20 K RU/sec/100 *$0.016 * 20 hours = $64` <br/>`D2: 50 K RU/sec/100 *$0.016 * 20 hours = $160` |$ 224  |
| | |Fattura per la velocità effettiva per 2 aree aggiuntive: Stati Uniti orientali, Europa settentrionale (tutte le aree sono scrivibili)  |`(1 + 1) * (70 K RU/sec /100 * $0.016) * 20 hours = $448`  |$ 224  |
|| |**Costo mensile totale**  | |**$38.688**   |

## <a name="proactively-estimating-your-monthly-bill"></a>Stima proattiva della fattura mensile  

Si consideri un altro esempio, in cui si vuole stimare in modo proattivo la fattura prima della fine del mese. È possibile stimare la fattura nel modo seguente:

|**Costi di archiviazione** | |
|----|----|
|Dimensioni medie dei record (KB) |1 |
|Numero di record  |100.000.000  |
|Spazio di archiviazione totale (GB)  |100 |
|Costo mensile per GB  |$ 0,25  |
|Costo mensile previsto per l'archiviazione   |$ 25,00  |

<br>

|**Costi della velocità effettiva** | | | |
|----|----|----|----|
|Tipo di operazione| Richieste/sec| Avg. UR/richiesta| UR necessarie|
|Scrittura| 100 | 5 | 500|
|Lettura| 400| 1| 400|

Totale UR/sec: 500 + 400 = 900 Costo orario: 900/100 * $ 0,008 = $ 0,072 Costo mensile previsto per la velocità effettiva (presupponendo 31 giorni): $ 0,072 * 24 * 31 = $ 53,57

**Costo mensile totale**

Costo mensile totale = Costo mensile per l'archiviazione + Costo mensile per la velocità effettiva Costo mensile totale = $ 25,00 + $ 53,57 = $ 78,57

*I prezzi possono variare a seconda dell'area. Per i prezzi aggiornati, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/cosmos-db/).*

## <a name="billing-with-azure-cosmos-db-reserved-capacity"></a>Fatturazione con capacità riservata di Azure Cosmos DB

La capacità riservata di Azure Cosmos DB consente di acquistare in anticipo velocità effettiva di cui è stato effettuato il provisioning (capacità riservata o prenotazione) che può essere applicata a tutti i database e a tutti i contenitori di Azure Cosmos DB (per qualsiasi modello di dati o API) in tutte le aree di Azure. Poiché il prezzo della velocità effettiva di cui è stato effettuato il provisioning varia in base all'area, è possibile considerare la capacità riservata come un credito monetario acquistato a prezzo scontato che può essere dedotto dal prezzo della velocità effettiva di cui è stato effettuato il provisioning in ogni area. Si supponga, ad esempio, che un account di Azure Cosmos abbia un singolo contenitore di cui sia stato effettuato il provisioning con 50 K UR/sec e due aree replicate globalmente: Stati Uniti orientali e Giappone orientale. Se si sceglie l'opzione con pagamento in base al consumo, si paga:  

* Nell'area Stati Uniti orientali: 50 K UR/sec alla tariffa di $ 0,008 per 100 UR/sec 

* Nell'area Giappone orientale: 50 K UR/sec alla tariffa di $ 0,009 per 100 UR/sec

La fattura totale (senza capacità riservata) è (presupponendo 30 giorni o 720 ore): 

|**Area**| **Prezzo all'ora per 100 UR/s**|**Unità (UR/s)**|**Importo fatturato (orario)**| **Importo fatturato (mensile)**|
|----|----|----|----|----|
|Stati Uniti orientali|$ 0,008 |50 K|$ 4|$ 2.880 |
|Giappone orientale|$ 0,009 |50 K| $ 4,50 |$ 3.240 |
|Totale|||$ 8,50|$ 6.120 |

Si supponga invece di aver acquistato capacità riservata. È possibile acquistare capacità riservata per 100 K UR/sec al prezzo di 56,064 dollari per un anno (con lo sconto del 20%) o di 6,40 dollari all'ora. Vedere i prezzi della capacità riservata nella [pagina dei prezzi](https://azure.microsoft.com/pricing/details/cosmos-db/)).  

* Costo della velocità effettiva (con pagamento in base al consumo): 100.000 UR/sec/100 * $ 0,008/ora * 8760 ore in un anno = $ 70,080 

* Costo della velocità effettiva (con capacità riservata) $ 70,080, con lo sconto del 20% = $ 56,064 

Ciò che si è effettivamente acquistato è un credito di 8 dollari all'ora, per 100 K UR/sec in base al prezzo di listino degli Stati Uniti orientali, a 6,40 dollari all'ora. È quindi possibile utilizzare questa prenotazione prepagata su base oraria per il provisioning della capacità di velocità effettiva in qualsiasi area di Azure globale al prezzo di listino impostato per ciascuna area per la sottoscrizione. In questo esempio, effettuando il provisioning di 50 UR/sec sia nell'area Stati Uniti orientali sia nell'area Giappone orientale, è possibile dedurre un valore di 8,00 dollari all'ora di velocità effettiva di cui è stato effettuato il provisioning e verrà fatturata un'eccedenza di 0,50 dollari all'ora (o 360 dollari al mese). 

|**Area**| **Prezzo all'ora per 100 UR/s**|**Unità (UR/s)**| **Importo fatturato (orario)**| **Importo fatturato (mensile)**|
|----|----|----|----|----|
|Stati Uniti orientali|$ 0,008 |50 K|$ 4|$ 2.880 |
|Giappone orientale|$ 0,009 |50 K| $ 4,50 |$ 3.240 |
|||Pagamento a consumo|$ 8,50|$ 6120|
|Capacità riservata acquistata|$ 0,0064 (20% di sconto) |100 UR/sec o $ 8 di capacità preacquistata |-$ 8|-$ 5.760 |
|Fattura netta|||$ 0.50 |$ 360 |

## <a name="next-steps"></a>Fasi successive

È ora possibile passare alle informazioni sull'ottimizzazione dei costi in Azure Cosmos DB con gli articoli seguenti:

* Altre informazioni su [come il modello di determinazione dei prezzi di Cosmos DB sia conveniente per i clienti](total-cost-ownership.md)
* Altre informazioni sull'[ottimizzazione di sviluppo e test](optimize-dev-test.md)
* Altre informazioni sull'[ottimizzazione dei costi della velocità effettiva](optimize-cost-throughput.md)
* Altre informazioni sull'[Ottimizzazione dei costi di archiviazione](optimize-cost-storage.md)
* Altre informazioni sull'[ottimizzazione del costo delle operazioni di lettura e scrittura](optimize-cost-reads-writes.md)
* Altre informazioni sull'[ottimizzazione del costo delle query](optimize-cost-queries.md)
* Altre informazioni sull'[ottimizzazione dei costi degli account Azure Cosmos multiarea](optimize-cost-regions.md)
