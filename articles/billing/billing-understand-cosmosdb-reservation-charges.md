---
title: Informazioni su come viene applicato lo sconto per la prenotazione ad Azure Cosmos DB | Microsoft Docs
description: Informazioni su come viene applicato lo sconto per la prenotazione alla velocità effettiva con provisioning (UR/sec) in Azure Cosmos DB.
services: cosmos-db
author: rimman
manager: kfile
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/13/2019
ms.author: banders
ms.reviewer: sngun
ms.openlocfilehash: d5a13e4466234d73bafe8dbe76cae92955cf64bd
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "60370748"
---
# <a name="understand-how-the-reservation-discount-is-applied-to-azure-cosmos-db"></a>Informazioni su come viene applicato lo sconto per la prenotazione ad Azure Cosmos DB

Dopo avere acquistato capacità riservata per Azure Cosmos DB, lo sconto per la prenotazione viene automaticamente applicato alle risorse di Azure Cosmos DB che corrispondono agli attributi e alla quantità della prenotazione. Una prenotazione copre la velocità effettiva con provisioning relativa alle risorse di Azure Cosmos DB, e non copre addebiti per contenitori predefiniti, software, rete o archiviazione.

## <a name="how-reservation-discount-is-applied"></a>Come viene applicato lo sconto per la prenotazione

Lo sconto per la prenotazione si basa sul principio "*use-it-or-lose-it*", ossia se non si usa si perde. Quindi, se non si hanno risorse corrispondenti per un'ora, si perderà la quantità di prenotazione per quell'ora. Non è possibile riportare le ore prenotate inutilizzate.

Quando si arresta una risorsa, lo sconto per la prenotazione si applica automaticamente a un'altra risorsa corrispondente nell'ambito specificato. Se non si trovano risorse corrispondenti nell'ambito specificato, le ore prenotate vanno *perse*.

## <a name="reservation-discount-applied-to-azure-cosmos-db-accounts"></a>Sconto per la prenotazione applicato agli account Azure Cosmos DB

Uno sconto per la prenotazione viene applicato alla [velocità effettiva con provisioning](../cosmos-db/request-units.md) in termini di unità richiesta al secondo (UR/sec) su base oraria. Per le risorse di Azure Cosmos DB che non vengono eseguite per l'intera ora, lo sconto per la prenotazione viene automaticamente applicato ad altre risorse di Cosmos DB che corrispondono agli attributi della prenotazione. Lo sconto può essere applicato a risorse di Azure Cosmos DB in esecuzione simultaneamente. Se non ci sono risorse di Azure Cosmos DB in esecuzione per l'ora intera e che corrispondono agli attributi della prenotazione, non è possibile ottenere il vantaggio completo dello sconto sulla prenotazione per questa ora.

Gli sconti sono suddivisi in livelli. Le prenotazioni con più unità richiesta offrono quindi sconti più elevati.

Con l'acquisto della prenotazione saranno applicati gli sconti a tutte le aree con il tasso corrispondente al piano tariffario su richiesta a livello di area. Per i tassi di sconto per le prenotazioni, vedere la sezione [Sconto per la prenotazione per area](#reservation-discount-per-region) di questo articolo.

## <a name="reservation-discount-per-region"></a>Sconto per la prenotazione per area
Lo sconto per la prenotazione viene applicato ai costi di velocità effettiva di Azure Cosmos DB su base oraria, a livello di sottoscrizione singola o di ambito di registrazione/account. Lo sconto per la prenotazione si applica all'utilizzo del contatore in aree diverse, con i tassi seguenti:

|Descrizione del contatore  |Region |Rapporto  |
|---------|---------|---------|
|Azure Cosmos DB - 100 UR/sec/ora - Asia Pacifico sud-orientale  |  Asia Pacifico sud-orientale    |   1      |
|Azure Cosmos DB - 100 UR/sec/ora - Asia Pacifico orientale |   Asia Pacifico orientale   |    1     |
|Azure Cosmos DB - 100 UR/sec/ora - Europa settentrionale|  Europa settentrionale       |    1     |
|Azure Cosmos DB - 100 UR/sec/ora - Corea meridionale|    Corea meridionale     |     1    |
|Azure Cosmos DB - 100 UR/sec/ora - Europa occidentale|    Europa occidentale     |      1   |
|Azure Cosmos DB - 100 UR/sec/ora - Corea centrale|   Corea centrale    |       1  |
|Azure Cosmos DB - 100 UR/sec/ora - Regno Unito meridionale|   Regno Unito meridionale      |     1    |
|Azure Cosmos DB - 100 UR/sec/ora - Regno Unito occidentale|   Regno Unito occidentale      |    1     |
|Azure Cosmos DB - 100 UR/sec/ora - Regno Unito settentrionale |   Regno Unito settentrionale    |     1    |
|Azure Cosmos DB - 100 UR/sec/ora - Regno Unito meridionale 2|   Regno Unito meridionale 2      |     1    |
|Azure Cosmos DB - 100 UR/sec/ora - Stati Uniti orientali 2|  Stati Uniti orientali 2     |     1    |
|Azure Cosmos DB - 100 UR/sec/ora - Stati Uniti centro-settentrionali|   Stati Uniti centro-settentrionali      |     1    |
|Azure Cosmos DB - 100 UR/sec/ora - Stati Uniti occidentali|   Stati Uniti occidentali      |     1    |
|Azure Cosmos DB - 100 UR/sec/ora - Stati Uniti centrali| Stati Uniti centrali        |     1    |
|Azure Cosmos DB - 100 UR/sec/ora - Stati Uniti occidentali 2|   Stati Uniti occidentali 2      |      1   |
|Azure Cosmos DB - 100 UR/sec/ora - Stati Uniti centro-occidentali|   Stati Uniti centro-occidentali      |       1  |
|Azure Cosmos DB - 100 UR/sec/ora - Stati Uniti orientali|   Stati Uniti orientali      |  1       |
|Azure Cosmos DB - 100 UR/sec/ora - Sudafrica settentrionale|     Sudafrica settentrionale    |   1      |
|Azure Cosmos DB - 100 UR/sec/ora - Sudafrica occidentale |    Sudafrica occidentale      |    1     |
|Azure Cosmos DB - 100 UR/sec/ora - India meridionale|    India meridionale     |    1,0375    |
|Azure Cosmos DB - 100 UR/sec/ora - Canada orientale|   Canada orientale      |    1.1      |
|Azure Cosmos DB - 100 UR/sec/ora - Giappone orientale|   Giappone orientale      |    1,125     |
|Azure Cosmos DB - 100 UR/sec/ora - Giappone occidentale|     Giappone occidentale    |   1,125       |
|Azure Cosmos DB - 100 UR/sec/ora - India occidentale|     India occidentale    |    1,1375     |
|Azure Cosmos DB - 100 UR/sec/ora - India centrale|    India centrale     |  1,1375       |
|Azure Cosmos DB - 100 UR/sec/ora - Australia orientale|     Australia orientale    |   1,15       |
|Azure Cosmos DB - 100 UR/sec/ora - Canada centrale|  Canada centrale       |   1.2       |
|Azure Cosmos DB - 100 UR/sec/ora - Francia centrale|   Francia centrale      |    1,25      |
|Azure Cosmos DB - 100 UR/sec/ora - Brasile meridionale|  Brasile meridionale       |   1,5      |
|Azure Cosmos DB - 100 UR/sec/ora - Australia centrale|   Australia centrale      |   1,5      |
|Azure Cosmos DB - 100 UR/sec/ora - Australia centrale 2| Australia centrale 2        |    1,5     |
|Azure Cosmos DB - 100 UR/sec/ora - Francia meridionale|    Francia meridionale     |    1,625     |

## <a name="scenarios-that-show-how-the-reservation-discount-is-applied"></a>Scenari che mostrano come viene applicato lo sconto per la prenotazione

Prendere in considerazione i seguenti requisiti per una prenotazione:

* Velocità effettiva richiesta: 50.000 UR/s  
* Aree geografiche usate: 2

In questo caso gli addebiti su richiesta totali sono per una quantità pari a 500 del contatore 100 UR/sec in queste due aree, per un consumo totale di UR/sec di 100.000 ogni ora.

**Scenario 1**

Si supponga, ad esempio, che siano necessarie distribuzioni di Azure Cosmos DB nelle aree geografiche "Stati Uniti centro-settentrionali" e "Stati Uniti occidentali" e che ogni area abbia un consumo di velocità effettiva pari a 50.000 UR/sec. un acquisto di prenotazioni di 100.000 UR/sec bilancerà completamente gli addebiti su richiesta.

Lo sconto coperto da una prenotazione viene calcolato come: consumo_di_velocità_effettiva * tasso_di_sconto_per_la_prenotazione_per_quella_area. Per le aree "Stati Uniti centro-settentrionali" e "Stati Uniti occidentali", il tasso di sconto per la prenotazione è 1. Pertanto, le UR/sec scontate totali sono pari a 100.000 UR/sec. Questo valore viene calcolato come: 50.000 * 1 + 50.000 * 1 = 100.000 UR/s. e non sono previsti addebiti aggiuntivi alle normali tariffe con pagamento in base al consumo.

|Descrizione del contatore | Region |Consumo di velocità effettiva (UR/sec) |Sconto per la prenotazione applicato al valore UR/sec |
|---------|---------|---------|---------|
|Azure Cosmos DB - 100 UR/sec/ora - Stati Uniti centro-settentrionali  |   Stati Uniti centro-settentrionali  | 50.000  | 50.000  |
|Azure Cosmos DB - 100 UR/sec/ora - Stati Uniti occidentali  |  Stati Uniti occidentali   |  50.000  |  50.000 |

**Scenario 2**

Si supponga, ad esempio, che siano necessarie distribuzioni di Azure Cosmos DB nelle aree geografiche "Australia centrale 2" e "Francia meridionale" e che ogni area abbia un consumo di velocità effettiva pari a 50.000 UR/sec. un acquisto di prenotazioni di 100.000 UR/sec sarà applicabile nel modo seguente, presupponendo che l'utilizzo dell'area Australia centrale 2 sia già stato scontato:

|Descrizione del contatore | Region |Consumo di velocità effettiva (UR/sec) |Sconto per la prenotazione applicato al valore UR/sec |
|---------|---------|---------|---------|
|Azure Cosmos DB - 100 UR/sec/ora - Australia centrale 2  |  Australia centrale 2   |  50.000  |  50.000   |
|Azure Cosmos DB - 100 UR/sec/ora - Francia meridionale  |  Francia meridionale   |  50.000 |  15.384  |

Un utilizzo di 50.000 unità nell'area "Australia centrale 2" corrisponde a 75.000 UR/sec di utilizzo fatturabile o utilizzo normalizzato. Questo valore viene calcolato come consumo_di_velocità_effettiva * tasso_di_sconto_per_la_prenotazione_per_quella_area, che corrisponde a 75.000 UR/sec di utilizzo fatturabile o normalizzato. Questo valore viene calcolato come: 50.000 * 1,5 = 75.000 UR/s.

100.000 UR/sec di acquisto di prenotazioni farebbe variare il valore 75.000 UR/sec per l'area "Australia centrale 2" e lascerebbe il valore di 25.000 UR/sec per l'area "Francia meridionale". Dalle rimanenti 25.000 UR/sec, uno sconto per la prenotazione di 15.384 UR/sec viene applicato all'area "Francia meridionale". Il valore dello sconto viene calcolato come: 25.000 / 1,625 = 15.384 UR/s. Le rimanenti 34.616 UR/sec dell'area "Francia meridionale" vengono addebitate alle normali tariffe con pagamento in base al consumo.

Il sistema di fatturazione di Azure assegnerà i vantaggi di fatturazione della prenotazione alla prima istanza elaborata che corrisponde alla configurazione di prenotazione, che in questo caso è "Australia centrale 2".

Per informazioni sull'applicazione delle prenotazioni di Azure nei report sull'utilizzo per la fatturazione, vedere [Informazioni sull'utilizzo delle prenotazioni di Azure](../billing/billing-understand-reserved-instance-usage-ea.md).

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:

* [Informazioni sulle prenotazioni di Azure](../billing/billing-save-compute-costs-reservations.md)  
* [Pagare in anticipo le risorse di Azure Cosmos DB con capacità riservata di Azure Cosmos DB](../cosmos-db/cosmos-db-reserved-capacity.md)  
* [Pagare in anticipo le risorse di calcolo del database SQL con capacità riservata del database SQL di Azure](../sql-database/sql-database-reserved-capacity.md)  
* [Gestire le prenotazioni per Azure](../billing/billing-manage-reserved-vm-instance.md)  
* [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](../billing/billing-understand-reserved-instance-usage.md)  
* [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](../billing/billing-understand-reserved-instance-usage-ea.md)
* [Informazioni sull'utilizzo della prenotazione per sottoscrizioni CSP](https://docs.microsoft.com/partner-center/azure-reservations)
