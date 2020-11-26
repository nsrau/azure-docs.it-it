---
title: Come scegliere tra la velocità effettiva con provisioning e senza server in Azure Cosmos DB
description: Informazioni su come scegliere tra la velocità effettiva con provisioning e senza server per il carico di lavoro.
author: ThomasWeiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/25/2020
ms.openlocfilehash: 1943aae3a2b01490dca687bcdea99d76da238d51
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96187256"
---
# <a name="how-to-choose-between-provisioned-throughput-and-serverless"></a>Come scegliere tra la velocità effettiva con provisioning e senza server
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB è disponibile in due modalità di capacità diverse: [velocità effettiva con provisioning](set-throughput.md) e senza [Server](serverless.md). È possibile eseguire esattamente le stesse operazioni di database in entrambe le modalità, ma il modo in cui vengono fatturate queste operazioni è radicalmente diverso. Il video seguente illustra le differenze principali tra queste modalità e il modo in cui si adattano a diversi tipi di carichi di lavoro:

> [!VIDEO https://www.youtube.com/embed/CgYQo6uHyt0]

## <a name="detailed-comparison"></a>Confronto dettagliato

| Criteri | Velocità effettiva con provisioning | Senza server |
| --- | --- | --- |
| Stato | Disponibile a livello generale | In anteprima |
| Ideale per | Carichi di lavoro cruciali che richiedono prestazioni prevedibili | Carichi di lavoro non critici da piccoli a medi con traffico leggero e intermittente |
| Funzionamento | Per ogni contenitore viene effettuato il provisioning di una quantità di velocità effettiva espressa in [unità richiesta](request-units.md) al secondo. Ogni secondo, questa quantità di unità richiesta è disponibile per le operazioni del database. La velocità effettiva con provisioning può essere aggiornata manualmente o modificata automaticamente con la [scalabilità](provision-throughput-autoscale.md)automatica. | È possibile eseguire le operazioni di database sui contenitori senza dover effettuare il provisioning di alcuna capacità. |
| Distribuzione geografica | Disponibile (numero illimitato di aree di Azure) | Non disponibile (gli account senza server possono essere eseguiti solo in un'area di Azure) |
| Dimensione massima di archiviazione per ogni contenitore | Nessuna limitazione | 50 GB |
| Prestazioni | disponibilità da 99,99% a 99,999% coperta dal contratto di contratto<br>Latenza < 10 ms per letture di punti e scritture coperte dal contratto di contratto<br>velocità effettiva garantita del 99,99% coperta dal contratto di contratto | disponibilità da 99,9% a 99,99% coperta dal contratto di contratto<br>Latenza < 10 ms per letture di punti e < 30 ms per scritture coperte da SLO<br>ingrandimento del 95% incluso in SLO |
| Modello di fatturazione | Per le UR/s con provisioning, la fatturazione viene eseguita su base oraria, indipendentemente dal numero di UR utilizzate. | La fatturazione viene eseguita su base oraria per la quantità di ur utilizzate dalle operazioni del database. |

> [!IMPORTANT]
> Alcune limitazioni senza server possono essere facilitate o rimosse quando il server diventa disponibile a livello generale e **i commenti e suggerimenti** ci aiuteranno a decidere. Scopri di più sull'esperienza senza server: [azurecosmosdbserverless@service.microsoft.com](mailto:azurecosmosdbserverless@service.microsoft.com) .

## <a name="estimating-your-expected-consumption"></a>Stima del consumo previsto

In alcune situazioni, potrebbe non essere chiaro se la velocità effettiva con provisioning o senza server deve essere scelta per un determinato carico di lavoro. Per semplificare questa decisione, è possibile stimare il **consumo complessivo previsto**, ovvero il numero totale di UR che è possibile utilizzare più di un mese (è possibile stimarlo con la tabella riportata di [seguito](plan-manage-costs.md#estimating-serverless-costs)).

**Esempio 1**: si prevede che un carico di lavoro aumenti fino a un massimo di 500 UR/sec e utilizzerà un totale di 20 milioni ur per un mese.

- In modalità di velocità effettiva con provisioning, è necessario effettuare il provisioning di un contenitore con 500 UR/sec per un costo mensile di: $0,008 * 5 * 730 = **$29,20**
- In modalità senza server è necessario pagare per le UR utilizzate: $0,25 * 20 = **$5,00**

**Esempio 2**: si prevede che un carico di lavoro aumenti fino a un massimo di 500 UR/sec e utilizzerà un totale di 250 milioni ur per un mese.

- In modalità di velocità effettiva con provisioning, è necessario effettuare il provisioning di un contenitore con 500 UR/sec per un costo mensile di: $0,008 * 5 * 730 = **$29,20**
- In modalità senza server è necessario pagare per le UR utilizzate: $0,25 * 250 = **$62,50**

(questi esempi non sono conteggiati per il costo di archiviazione, che è lo stesso tra le due modalità)

> [!NOTE]
> I costi indicati nell'esempio precedente sono solo a scopo dimostrativo. Per le informazioni più aggiornate sui prezzi, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/cosmos-db/) .

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulla [velocità effettiva di provisioning in Azure Cosmos DB](set-throughput.md)
- Scopri di più su [Azure Cosmos DB senza server](serverless.md)
- Acquisire familiarità con il concetto di [unità richiesta](request-units.md)
