---
title: Prestazioni automatizzate, costi, consigli sulla sicurezza per Azure Cosmos DB
description: Informazioni su come visualizzare prestazioni, costi, sicurezza e altre raccomandazioni personalizzate per Azure Cosmos DB in base ai modelli di carico di lavoro.
author: ThomasWeiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/28/2020
ms.reviewer: sngun
ms.openlocfilehash: 8fa2fdf23a0d71b854e043b66c0aed7e944c5f39
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87450838"
---
# <a name="automated-recommendations-for-azure-cosmos-db"></a>Suggerimenti automatici per Azure Cosmos DB

Tutti i servizi cloud, tra cui Azure Cosmos DB ricevere aggiornamenti frequenti con nuove funzionalità, funzionalità e miglioramenti. È importante che l'applicazione continui a essere aggiornata con le prestazioni e gli aggiornamenti della sicurezza più recenti. Il portale di Azure offre consigli personalizzati che consentono di ottimizzare le prestazioni dell'applicazione. Il motore di consulenza del Azure Cosmos DB analizza continuamente la cronologia di utilizzo delle risorse di Azure Cosmos DB e fornisce consigli basati sui modelli di carico di lavoro. Queste raccomandazioni corrispondono ad aree come il partizionamento, l'indicizzazione, la rete, la sicurezza e così via. Queste raccomandazioni personalizzate consentono di migliorare le prestazioni dell'applicazione.

## <a name="view-recommendations"></a>Visualizzare raccomandazioni

È possibile visualizzare le indicazioni per Azure Cosmos DB nei modi seguenti:

- Un modo per visualizzare le raccomandazioni è nella scheda notifiche. Se sono presenti nuove raccomandazioni, viene visualizzata una barra messaggi. Accedere al [portale di Azure](https://portal.azure.com) e passare all'account Azure Cosmos. Nell'account Azure Cosmos aprire il riquadro **notifiche** e quindi selezionare la scheda **raccomandazioni** . È possibile selezionare il messaggio e visualizzare le raccomandazioni.  

   :::image type="content" source="./media/automated-recommendations/cosmos-db-pane-recommendations.png" alt-text="Visualizzare le raccomandazioni dal riquadro Azure Cosmos DB":::

- È anche possibile trovare le raccomandazioni tramite [Azure Advisor](../advisor/advisor-overview.md) categorizzate in base a bucket diversi, ad esempio costi, sicurezza, affidabilità, prestazioni e eccellenza operativa. È possibile selezionare sottoscrizioni specifiche e filtrare in base al tipo di risorsa, che è **Azure Cosmos DB account**.  Quando si seleziona una raccomandazione specifica, vengono visualizzate le azioni che è possibile eseguire per trarre vantaggio dai carichi di lavoro.

   :::image type="content" source="./media/automated-recommendations/advisor-pane-recommendations.png" alt-text="Visualizzare le raccomandazioni dal riquadro Azure Cosmos DB":::

Non tutte le raccomandazioni visualizzate nel riquadro Azure Cosmos DB sono disponibili nell'Azure Advisor e viceversa. Ciò è dovuto al fatto che in base al tipo di raccomandazione rientrano nel riquadro Azure Advisor, Azure Cosmos DB riquadro o entrambi.

Attualmente Azure Cosmos DB supporta le raccomandazioni relative alle aree seguenti. Ognuna di queste raccomandazioni include un collegamento alla sezione pertinente della documentazione, quindi è facile eseguire i passaggi successivi.

## <a name="sdk-usage-recommendations"></a>Suggerimenti sull'utilizzo di SDK

In questa categoria l'Advisor rileva l'utilizzo di una versione precedente degli SDK e consiglia di eseguire l'aggiornamento a una versione più recente per sfruttare le correzioni di bug più recenti e i miglioramenti delle prestazioni. Attualmente sono disponibili le seguenti raccomandazioni specifiche dell'SDK:

|Nome  |Descrizione  |
|---------|---------|
| Connettore Spark precedente | Rileva l'utilizzo delle versioni precedenti del connettore Spark e consiglia di eseguire l'aggiornamento. |
| .NET SDK precedente | Rileva l'utilizzo di versioni precedenti di .NET SDK e consiglia di eseguire l'aggiornamento. |
| SDK Java precedente | Rileva l'utilizzo delle versioni precedenti del connettore Java e consiglia di eseguire l'aggiornamento. |

## <a name="indexing-recommendations"></a>Indicazioni sull'indicizzazione

In questa categoria, l'Advisor rileva la modalità di indicizzazione, i criteri di indicizzazione, i percorsi indicizzati e consiglia di cambiare se la configurazione corrente influisca sulle prestazioni di esecuzione delle query. Sono attualmente disponibili le seguenti raccomandazioni specifiche per l'indicizzazione:

|Nome  |Descrizione  |
|---------|---------|
| Indicizzazione differita | Rileva l'utilizzo della modalità di indicizzazione differita e consiglia di utilizzare la modalità di indicizzazione coerente. Lo scopo della modalità di indicizzazione differita di Azure Cosmos DB è limitato e può influisca sull'aggiornamento dei risultati di query in alcune situazioni, pertanto è consigliabile usare una modalità di indicizzazione coerente. |
| Indicizzazione composita| Rileva gli account in cui le query possono trarre vantaggio dagli indici compositi e consigliarne l'utilizzo. Gli indici compositi possono migliorare notevolmente le prestazioni e l'utilizzo della velocità effettiva di alcune query.|
| Criteri di indicizzazione predefiniti con molti percorsi indicizzati | Rileva i contenitori in esecuzione nell'indicizzazione predefinita con molti percorsi indicizzati e consiglia di personalizzare i criteri di indicizzazione.|
| Ordina per query con costi elevati di Ur/s| Rileva i contenitori che emettono l'ordine in base alle query con costi elevati di Ur/s e consiglia di esplorare indici compositi.|
| Account MongoDB 3,6 senza indice e consumo di Ur/s elevato| Rileva l'API di Azure Cosmos DB per MongoDB con la versione 3,6 dei contenitori che inviano query con costi elevati di Ur/s e consiglia di aggiungere indici.|

## <a name="cost-optimization-recommendations"></a>Consigli sull'ottimizzazione dei costi

In questa categoria l'Advisor rileva l'utilizzo di Ur/s e stabilisce che è possibile ottimizzare il prezzo apportando alcune modifiche alle risorse o sfruttando un modello di determinazione prezzi diverso. Sono attualmente disponibili le seguenti raccomandazioni specifiche per l'ottimizzazione dei costi:

|Nome  |Descrizione  |
|---------|---------|
| Capacità riservata | Rileva l'utilizzo di Ur/s e consiglia le istanze riservate agli utenti che ne traggono vantaggio. |
| Contenitori inattivi | Rileva i contenitori che non sono stati usati per più di 30 giorni e consiglia di ridurre la velocità effettiva per tali contenitori o di eliminarli.|
| Nuove sottoscrizioni con velocità effettiva elevata | Rileva le nuove sottoscrizioni con account che passano insolitamente le UR/sec al giorno e fornisce una notifica. Questa notifica si riferisce in modo specifico ai nuovi clienti che Azure Cosmos DB opera su un modello basato sulla velocità effettiva con provisioning e non su un modello basato sul consumo. |

## <a name="migration-recommendations"></a>Raccomandazioni per la migrazione

In questa categoria l'Advisor rileva che si sta usando le funzionalità legacy consiglia di eseguire la migrazione, in modo da poter sfruttare la scalabilità e altri vantaggi Azure Cosmos DB. Sono attualmente disponibili le seguenti raccomandazioni specifiche per la migrazione:

|Nome  |Descrizione  |
|---------|---------|
| Contenitori non partizionati | Rileva i contenitori di dimensioni fisse che si avvicinano al limite massimo di archiviazione e consiglia di eseguirne la migrazione a contenitori partizionati.|

## <a name="query-usage-recommendations"></a>Suggerimenti sull'utilizzo delle query

In questa categoria, Advisor rileva l'esecuzione della query e indica che è possibile ottimizzare le prestazioni delle query con alcune modifiche. Attualmente sono disponibili le seguenti raccomandazioni sull'utilizzo delle query:

|Nome  |Descrizione  |
|---------|---------|
| Query con dimensioni di pagina fisse | Rileva le query rilasciate con una dimensione di pagina fissa e consiglia di usare-1 (nessun limite per le dimensioni della pagina) anziché definire un valore specifico. Questa opzione riduce il numero di round trip di rete necessari per recuperare tutti i risultati. |

## <a name="next-steps"></a>Passaggi successivi

* [Ottimizzazione delle prestazioni delle query in Azure Cosmos DB](sql-api-query-metrics.md)
* [Risolvere i problemi di query](troubleshoot-query-performance.md) quando si usa Azure Cosmos DB
