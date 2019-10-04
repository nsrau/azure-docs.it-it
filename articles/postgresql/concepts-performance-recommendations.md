---
title: Suggerimenti sulle prestazioni in database di Azure per PostgreSQL-server singolo
description: Questo articolo descrive la funzionalità di raccomandazione per le prestazioni del database di Azure per PostgreSQL-server singolo.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: 5f1b64753d19158b17d4de1b3fbbe50d30ea0254
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70764676"
---
# <a name="performance-recommendations-in-azure-database-for-postgresql---single-server"></a>Suggerimenti sulle prestazioni in database di Azure per PostgreSQL-server singolo

**Si applica a:** Database di Azure per PostgreSQL: versioni a server singolo 9,6, 10, 11

La funzionalità raccomandazioni per le prestazioni analizza i database per creare suggerimenti personalizzati per migliorare le prestazioni. Per produrre le raccomandazioni, l'analisi esamina le varie caratteristiche del database, incluso lo schema. Abilitare [query Store](concepts-query-store.md) sul server per usare completamente la funzionalità delle raccomandazioni per le prestazioni. Una volta implementate le indicazioni sulle prestazioni, è consigliabile testare le prestazioni per valutare l'effetto di tali modifiche. 

## <a name="permissions"></a>Autorizzazioni
Autorizzazioni **Proprietario** oppure **Collaboratore** necessarie per eseguire l'analisi usando la funzionalità Raccomandazioni per gli elementi consigliati.

## <a name="performance-recommendations"></a>Raccomandazioni per le prestazioni
La funzionalità [Raccomandazioni per le prestazioni](concepts-performance-recommendations.md) analizza i carichi di lavoro sul server per identificare gli indici con la possibilità di migliorare le prestazioni.

Aprire **suggerimenti sulle prestazioni** dalla sezione **prestazioni intelligenti** della barra dei menu nella pagina portale di Azure per il server PostgreSQL.

![Pagina di destinazione Elementi consigliati per le prestazioni](./media/concepts-performance-recommendations/performance-recommendations-page.png)

Selezionare **analizza** e scegliere un database che inizierà l'analisi. A seconda del carico di lavoro, il completamento dell'analisi potrebbe richiedere alcuni minuti. Al termine dell'analisi, verrà inviata una notifica nel portale. L'analisi esegue un esame approfondito del database. Si consiglia di eseguire l'analisi durante i periodi di minore traffico. 

Nella finestra **raccomandazioni** viene visualizzato un elenco di raccomandazioni, se trovate.

![Nuova pagina Raccomandazioni per le prestazioni](./media/concepts-performance-recommendations/performance-recommendations-result.png)

Le raccomandazioni non vengono applicate automaticamente. Per applicare la raccomandazione, copiare il testo della query ed eseguirlo dal client preferito. Ricordarsi di eseguire il test e il monitoraggio per valutare la raccomandazione. 

## <a name="recommendation-types"></a>Tipi di raccomandazione

Attualmente sono supportati due tipi di consigli: *Create index* e *Drop index*.

### <a name="create-index-recommendations"></a>Raccomandazioni relative alla creazione di indici
I suggerimenti per la *creazione* di indici suggeriscono nuovi indici per velocizzare le query di esecuzione più frequenti o che richiedono molto tempo nel carico di lavoro. Questo tipo di raccomandazione richiede l'abilitazione di [query Store](concepts-query-store.md) . Query Store raccoglie informazioni sulle query e fornisce le statistiche dettagliate sul runtime di query e sulla frequenza utilizzate dall'analisi per apportare le indicazioni.

### <a name="drop-index-recommendations"></a>Raccomandazioni relative all'eliminazione di indici
Oltre a rilevare gli indici mancanti, database di Azure per PostgreSQL analizza le prestazioni degli indici esistenti. Se un indice viene usato raramente o ridondante, l'analizzatore consiglia di eliminarlo.

## <a name="considerations"></a>Considerazioni
* Le raccomandazioni sulle prestazioni non sono disponibili per le [repliche di lettura](concepts-read-replicas.md).
## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni su [monitoraggio e ottimizzazione](concepts-monitoring.md) nel Database di Azure per PostgreSQL.

