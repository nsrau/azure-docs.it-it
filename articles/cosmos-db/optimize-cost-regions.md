---
title: Ottimizzare i costi per le distribuzioni in più aree in Azure Cosmos DB
description: Questo articolo illustra come gestire i costi delle distribuzioni in più aree in Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: rimman
ms.openlocfilehash: 233eab1fc49d7ce4cbb1e5b98b67eda9a64aa195
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667602"
---
# <a name="optimize-multi-region-cost-in-azure-cosmos-db"></a>Ottimizzare i costi per più aree in Azure Cosmos DB

È possibile aggiungere e rimuovere aree nell'account Azure Cosmos in qualsiasi momento. La velocità effettiva che si configura per vari database e contenitori di Azure Cosmos è riservata in ogni area associata al proprio account. Se la velocità effettiva di cui è stato effettuato il provisioning ogni ora, data dalla somma di UR/s configurate in tutti i database e i contenitori per l'account Azure Cosmos, è `T` e il numero di aree di Azure associate all'account del database è `N`, la velocità effettiva totale con provisioning per l'account Cosmos per una determinata ora è uguale a:

1. `T x N RU/s`, se nell'account Azure Cosmos è configurata una singola area di scrittura. 

1. `T x (N+1) RU/s`, se nell'account Azure Cosmos sono configurate tutte le aree in grado di elaborare operazioni di scrittura. 

La velocità effettiva di cui è stato effettuato il provisioning con un'area di scrittura singola costa $0,008/l'ora per 100 UR/s, mentre la velocità effettiva di cui è stato effettuato il provisioning con più aree scrivibili costa $0,016/l'ora per 100 UR/s. Per altre informazioni, vedere la [Pagina dei prezzi](https://azure.microsoft.com/pricing/details/cosmos-db/) di Azure Cosmos DB.

## <a name="costs-for-multiple-write-regions"></a>Costi per più aree di scrittura

In un sistema multimaster, le UR nette disponibili per le operazioni di scrittura aumentano di `N` volte, dove `N` è il numero di aree di scrittura. A differenza delle operazioni di scrittura in una singola area, ogni area è ora accessibile in scrittura e deve supportare la risoluzione dei conflitti. La quantità di carico di lavoro per i writer è aumentata. Dal punto di vista della pianificazione dei costi, per `M` eseguire le UR/s di scritture in tutto il mondo, sarà `RUs` necessario effettuare il provisioning di M a livello di contenitore o di database. È quindi possibile aggiungere tutte le aree desiderate e usarle per operazioni di scrittura per eseguire `M` UR di scritture in tutto il mondo. 

### <a name="example"></a>Esempio

Si supponga di avere un contenitore nell'area Stati Uniti occidentali con provisioning con unità elaborate pari a 10.000 UR/s che sia stato archiviato 1 TB di dati questo mese. Si supponga quindi di aggiungere tre aree, Stati Uniti orientali, Europa settentrionale e Asia orientale, ciascuna con le stesse risorse di archiviazione e con la stessa velocità effettiva e che si desideri scrivere nei contenitori di tutte e quattro le aree dall'app distribuita a livello globale. La fattura mensile totale sarà la seguente (presupponendo un mese di 31 giorni):

|**Elemento**|**Uso (mensile)**|**Tariffa**|**Costo mensile**|
|----|----|----|----|
|Fattura per la velocità effettiva per contenitore negli Stati Uniti occidentali (operazioni di scrittura in più aree) |10K RU/s * 24 * 31 |$0,016 per 100 RU/s all'ora |$1.190,40 |
|Fattura per la velocità effettiva per 3 aree aggiuntive: Stati Uniti orientali, Europa settentrionale e Asia orientale (operazioni di scrittura in più aree) |(3 + 1) * 10K RU/s * 24 * 31 |$0,016 per 100 RU/s all'ora |$4.761,60 |
|Fattura per le risorse di archiviazione per un contenitore negli Stati Uniti occidentali |1 TB (o 1.024 GB) |$0,25/GB |$256 |
|Fattura per le risorse di archiviazione per 3 aree aggiuntive: Stati Uniti orientali, Europa settentrionale e Asia orientale |3 * 1 TB (o 3.072 GB) |$0,25/GB |$768 |
|**Totale**|||**$6.976** |

## <a name="improve-throughput-utilization-on-a-per-region-basis"></a>Migliorare l'uso della velocità effettiva per singola area

Nel caso di un uso inefficiente, ad esempio per una o più aree sottoutilizzate o sovrautilizzate, è possibile eseguire i passaggi seguenti per migliorare l'uso della velocità effettiva:  

1. Assicurarsi innanzitutto di ottimizzare la velocità effettiva con provisioning (UR) nell'area di scrittura e quindi usare al massimo le UR nelle aree di lettura tramite feed di modifiche dall'area di lettura e così via. 

2. Più operazioni di scrittura e lettura nelle aree di scrittura possono essere ridimensionate in tutte le aree associate con account Azure Cosmos. 

3. Monitorare l'attività nelle aree ed eventualmente aggiungere e rimuovere aree su richiesta per ridimensionare la velocità effettiva di lettura e scrittura.

## <a name="next-steps"></a>Passaggi successivi

È ora possibile passare ad altre informazioni sull'ottimizzazione dei costi in Azure Cosmos DB con gli articoli seguenti:

* Altre informazioni sull'[Ottimizzazione di sviluppo e test](optimize-dev-test.md)
* Altre informazioni sulla [Fatturazione in Azure Cosmos DB](understand-your-bill.md)
* Altre informazioni sull'[Ottimizzazione dei costi della velocità effettiva](optimize-cost-throughput.md)
* Altre informazioni sull'[Ottimizzazione dei costi di archiviazione](optimize-cost-storage.md)
* Altre informazioni sull'[ottimizzazione del costo delle operazioni di lettura e scrittura](optimize-cost-reads-writes.md)
* Altre informazioni sull'[Ottimizzazione dei costi delle query](optimize-cost-queries.md)

