---
title: Raccomandazioni per le prestazioni-database di Azure per MySQL
description: Questo articolo descrive la funzionalità di raccomandazione per le prestazioni del database di Azure per MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: f957459cf20a018ae53ba6ec90fb478dd0c69044
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770901"
---
# <a name="performance-recommendations-in-azure-database-for-mysql"></a>Suggerimenti sulle prestazioni in database di Azure per MySQL

**Si applica a:** Database di Azure per MySQL 5,7

La funzionalità raccomandazioni per le prestazioni analizza i database per creare suggerimenti personalizzati per migliorare le prestazioni. Per produrre le raccomandazioni, l'analisi esamina le varie caratteristiche del database, incluso lo schema. Abilitare [query Store](concepts-query-store.md) sul server per usare completamente la funzionalità delle raccomandazioni per le prestazioni. Se lo schema delle prestazioni è disattivato, l'attivazione di Query Store Abilita performance_schema e un subset di strumenti dello schema di prestazioni necessari per la funzionalità. Una volta implementate le indicazioni sulle prestazioni, è consigliabile testare le prestazioni per valutare l'effetto di tali modifiche.

## <a name="permissions"></a>autorizzazioni

Autorizzazioni **Proprietario** oppure **Collaboratore** necessarie per eseguire l'analisi usando la funzionalità Raccomandazioni per gli elementi consigliati.

## <a name="performance-recommendations"></a>Raccomandazioni per le prestazioni

La funzionalità [Raccomandazioni per le prestazioni](concepts-performance-recommendations.md) analizza i carichi di lavoro sul server per identificare gli indici con la possibilità di migliorare le prestazioni.

Aprire **suggerimenti sulle prestazioni** dalla sezione **prestazioni intelligenti** della barra dei menu nella pagina portale di Azure per il server MySQL.

![Pagina di destinazione Elementi consigliati per le prestazioni](./media/concepts-performance-recommendations/performance-recommendations-page.png)

Selezionare **analizza** e scegliere un database che inizierà l'analisi. A seconda del carico di lavoro, l'analisi potrebbe richiedere alcuni minuti. Al termine dell'analisi, verrà inviata una notifica nel portale. L'analisi esegue un esame approfondito del database. Si consiglia di eseguire l'analisi durante i periodi di minore traffico.

Nella finestra **raccomandazioni** verrà visualizzato un elenco di raccomandazioni, se trovate, e l'ID di query correlato che ha generato questa raccomandazione. Con l'ID query è possibile usare la vista [MySQL. query_store](concepts-query-store.md#mysqlquery_store) per ottenere ulteriori informazioni sulla query.

![Nuova pagina Raccomandazioni per le prestazioni](./media/concepts-performance-recommendations/performance-recommendations-result.png)

Le raccomandazioni non vengono applicate automaticamente. Per applicare la raccomandazione, copiare il testo della query ed eseguirlo dal client preferito. Ricordarsi di eseguire il test e il monitoraggio per valutare la raccomandazione.

## <a name="recommendation-types"></a>Tipi di raccomandazione

Attualmente sono supportate solo le raccomandazioni *create index* .

### <a name="create-index-recommendations"></a>Raccomandazioni relative alla creazione di indici

I suggerimenti per la *creazione* di indici suggeriscono nuovi indici per velocizzare le query di esecuzione più frequenti o che richiedono molto tempo nel carico di lavoro. Questo tipo di raccomandazione richiede l'abilitazione di [query Store](concepts-query-store.md) . Query Store raccoglie informazioni sulle query e fornisce le statistiche dettagliate sul runtime di query e sulla frequenza utilizzate dall'analisi per apportare le indicazioni.

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sul [monitoraggio e l'ottimizzazione](concepts-monitoring.md) nel database di Azure per MySQL.