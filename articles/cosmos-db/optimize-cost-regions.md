---
title: Ottimizzare i costi per le distribuzioni in più aree in Azure Cosmos DB
description: Questo articolo illustra come gestire i costi delle distribuzioni in più aree in Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2020
ms.openlocfilehash: 010ca40f4f3aacd6353aecd150e944672cc09066
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097510"
---
# <a name="optimize-multi-region-cost-in-azure-cosmos-db"></a>Ottimizzare i costi per più aree in Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

È possibile aggiungere e rimuovere aree nell'account Azure Cosmos in qualsiasi momento. La velocità effettiva che si configura per vari database e contenitori di Azure Cosmos è riservata in ogni area associata al proprio account. Se la velocità effettiva di cui è stato effettuato il provisioning all'ora, ovvero la somma di Ur/s configurata in tutti i database e i contenitori per l'account Azure Cosmos è `T` e il numero di aree di Azure associate all'account del database è `N` , la velocità effettiva totale con provisioning per l'account Cosmos, per un'ora specifica è uguale a `T x N RU/s` .

La velocità effettiva di cui è stato effettuato il provisioning con un'area di scrittura singola costa $0,008/l'ora per 100 UR/s, mentre la velocità effettiva di cui è stato effettuato il provisioning con più aree scrivibili costa $0,016/l'ora per 100 UR/s. Per altre informazioni, vedere la [Pagina dei prezzi](https://azure.microsoft.com/pricing/details/cosmos-db/) di Azure Cosmos DB.

## <a name="costs-for-multiple-write-regions"></a>Costi per più aree di scrittura

In un sistema di scrittura in più aree, le unità richiesta NET disponibili per le operazioni di scrittura aumentano `N` i tempi in cui `N` è il numero di aree di scrittura. Diversamente dalle Scritture a singola area, ogni area è ora scrivibile e supporta la risoluzione dei conflitti. Dal punto di vista della pianificazione dei costi, per eseguire le `M` ur/s di scritture in tutto il mondo, sarà necessario effettuare il provisioning di M `RUs` a livello di contenitore o di database. È quindi possibile aggiungere tutte le aree desiderate e usarle per operazioni di scrittura per eseguire `M` UR di scritture in tutto il mondo.

### <a name="example"></a>Esempio

Si supponga di avere un contenitore negli Stati Uniti occidentali configurato per le Scritture in una singola area, con provisioning con velocità effettiva di 10.000 UR/s e archivia 1 TB di dati in questo mese. Si supponga di aggiungere un'area, Stati Uniti orientali, con lo stesso spazio di archiviazione e velocità effettiva e di voler scrivere nei contenitori di entrambe le aree dall'app. La fattura mensile totale sarà la seguente (presupponendo un mese di 31 giorni):

|**Item**|**Uso (mensile)**|**Tariffa**|**Costo mensile**|
|----|----|----|----|
|Fatturazione della velocità effettiva per il contenitore negli Stati Uniti occidentali (singole aree di scrittura) |10.000 UR/sec * 24 ore * 31 giorni |$0,008 per 100 ur/sec all'ora |$584,06 |
|Fatturazione della velocità effettiva per il contenitore in due aree: Stati Uniti occidentali & Stati Uniti orientali (più aree di scrittura) |2 * 10.000 UR/sec * 24 ore * 31 giorni|$0,016 per 100 RU/s all'ora |$2.336,26 |
|Fattura per le risorse di archiviazione per un contenitore negli Stati Uniti occidentali |1 TB (o 1.024 GB) |$0,25/GB |$256 |
|Fattura di archiviazione per 2 aree: Stati Uniti occidentali & Stati Uniti orientali |2 * 1 TB (o 3.072 GB) |$0,25/GB |$768 |

## <a name="improve-throughput-utilization-on-a-per-region-basis"></a>Migliorare l'uso della velocità effettiva per singola area

Se si dispone di un uso inefficiente, ad esempio una o più aree di lettura sottoutilizzate, è possibile eseguire le operazioni per sfruttare al massimo le UR nelle aree di lettura utilizzando il feed delle modifiche dall'area di lettura o spostarlo in un altro database secondario se utilizzato in modo eccessivo. Per prima cosa è necessario assicurarsi di ottimizzare la velocità effettiva con provisioning (UR) nell'area di scrittura. Scrive un costo superiore a quello delle letture, a meno che le query di grandi dimensioni in modo da mantenere anche l'utilizzo possa risultare complesso. In generale, monitora la velocità effettiva utilizzata nelle tue aree e Aggiungi o Rimuovi aree su richiesta per ridimensionare la velocità effettiva di lettura e scrittura, assicurandosi di comprendere l'effetto della latenza per le app distribuite nella stessa area.

## <a name="next-steps"></a>Passaggi successivi

È ora possibile passare ad altre informazioni sull'ottimizzazione dei costi in Azure Cosmos DB con gli articoli seguenti:

* Altre informazioni sull'[Ottimizzazione di sviluppo e test](optimize-dev-test.md)
* Altre informazioni su [come comprendere la fatturazione di Azure Cosmos DB](understand-your-bill.md)
* Altre informazioni sull'[Ottimizzazione dei costi della velocità effettiva](optimize-cost-throughput.md)
* Altre informazioni sull'[ottimizzazione dei costi di archiviazione](optimize-cost-storage.md)
* Altre informazioni sull'[ottimizzazione del costo delle operazioni di lettura e scrittura](optimize-cost-reads-writes.md)
* Altre informazioni sull'[ottimizzazione del costo delle query](./optimize-cost-reads-writes.md)