---
title: Raccomandazioni per le prestazioni-database di Azure per MariaDB
description: Questo articolo descrive la funzionalità di raccomandazione per le prestazioni del database di Azure per MariaDB
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 6/3/2020
ms.openlocfilehash: 05bc0f1ae50f74cc7c8ab2b236d73bdb4a6fe787
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84484684"
---
# <a name="performance-recommendations-in-azure-database-for-mariadb"></a>Raccomandazioni per le prestazioni nel Database di Azure per MariaDB

**Si applica a:** Database di Azure per MariaDB 10,2

La funzionalità Raccomandazioni per le prestazioni analizza i database per creare suggerimenti personalizzati per migliorare le prestazioni. Per produrre le raccomandazioni, l'analisi prende in considerazione varie caratteristiche dei database, tra cui lo schema. Attivare [Query Store](concepts-query-store.md) sul server per sfruttare al massimo le potenzialità di Raccomandazioni per le prestazioni. Se performance_schema è disattivato, l'attivazione di Query Store attiva performance_schema e un subset di strumenti dello schema di prestazioni necessari per questa funzionalità. Dopo avere implementato una raccomandazione per le prestazioni, è necessario testare le prestazioni per valutare l'impatto di tali modifiche.

## <a name="permissions"></a>Autorizzazioni

Autorizzazioni **Proprietario** oppure **Collaboratore** necessarie per eseguire l'analisi usando la funzionalità Raccomandazioni per gli elementi consigliati.

## <a name="performance-recommendations"></a>Raccomandazioni per le prestazioni

La funzionalità [Raccomandazioni per le prestazioni](concepts-performance-recommendations.md) analizza i carichi di lavoro sul server per identificare gli indici con la possibilità di migliorare le prestazioni.

Aprire **suggerimenti sulle prestazioni** dalla sezione **prestazioni intelligenti** della barra dei menu nella pagina portale di Azure per il server MariaDB.

:::image type="content" source="./media/concepts-performance-recommendations/performance-recommendations-page.png" alt-text="Pagina di destinazione Elementi consigliati per le prestazioni":::

Selezionare **Analizza** e scegliere un database per avviare l'analisi. A seconda del carico di lavoro, l'operazione potrebbe richiedere alcuni minuti. Al termine dell'analisi, verrà inviata una notifica nel portale. L'analisi esegue un esame approfondito del database. Si consiglia quindi di effettuarla durante i periodi di minore traffico.

Nella finestra **Raccomandazioni** viene visualizzato un elenco di raccomandazioni (se individuate) e l'ID di query da cui è stata generata ogni raccomandazione. Con l'ID di query è possibile usare la vista [mysql.query_store](concepts-query-store.md#mysqlquery_store) per altre informazioni sulla query.

:::image type="content" source="./media/concepts-performance-recommendations/performance-recommendations-result.png" alt-text="Nuova pagina Raccomandazioni per le prestazioni":::

Le raccomandazioni non vengono applicate automaticamente. Per applicare una raccomandazione, copiare il testo della query ed eseguirlo dal client preferito. Non dimenticare di eseguire le operazioni di test e monitoraggio per valutare la raccomandazione.

## <a name="recommendation-types"></a>Tipi di raccomandazione

### <a name="index-recommendations"></a>Indicazioni sugli indici

Le raccomandazioni di tipo *Crea indice* suggeriscono nuovi indici per velocizzare le query che, nell'ambito del carico di lavoro, vengono eseguite più di frequente o richiedono più tempo. Per questo tipo di raccomandazione è necessario che sia attivato [Query Store](concepts-query-store.md). Query Store raccoglie informazioni sulle query e fornisce le statistiche dettagliate di frequenza e runtime delle query usate dall'analisi per generare le raccomandazioni.

### <a name="query-recommendations"></a>Suggerimenti per le query

Le raccomandazioni per la query suggeriscono le ottimizzazioni e le riscritture per le query nel carico di lavoro. Identificando gli anti-pattern della query MariaDB e correggendo la sintassi, è possibile migliorare le prestazioni delle query che richiedono molto tempo. Per questo tipo di raccomandazione è necessario che sia attivato Query Store. Query Store raccoglie informazioni sulle query e fornisce le statistiche dettagliate di frequenza e runtime delle query usate dall'analisi per generare le raccomandazioni.
## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sul [monitoraggio e l'ottimizzazione](concepts-monitoring.md) nel database di Azure per MariaDB.