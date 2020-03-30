---
title: Consigli sulle prestazioni - Database di Azure per MySQLPerformance recommendations - Azure Database for MySQL
description: Questo articolo descrive la funzionalità di raccomandazione delle prestazioni nel database di Azure per MySQLThis article describes the Performance Recommendation feature in Azure Database for MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: c7779d82ddd6e5fd1bf7fcd983937ea6c10dab1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537075"
---
# <a name="performance-recommendations-in-azure-database-for-mysql"></a>Raccomandazioni per le prestazioni nel Database di Azure per MySQL

**Si applica a:** Database di Azure per MySQL 5.7Azure Database for MySQL 5.7

La funzionalità Suggerimenti per le prestazioni analizza i database per creare suggerimenti personalizzati per migliorare le prestazioni. Per produrre le raccomandazioni, l'analisi esamina varie caratteristiche del database, tra cui lo schema. Abilitare [Query Store](concepts-query-store.md) nel server per utilizzare completamente la funzionalità Suggerimenti per le prestazioni. Se lo schema delle prestazioni è OFF, l'attivazione dell'archivio query consente performance_schema e un sottoinsieme degli strumenti dello schema delle prestazioni necessari per la funzionalità. Dopo aver implementato qualsiasi raccomandazione di prestazioni, è necessario testare le prestazioni per valutare l'impatto di tali modifiche.

## <a name="permissions"></a>Autorizzazioni

Autorizzazioni **Proprietario** oppure **Collaboratore** necessarie per eseguire l'analisi usando la funzionalità Raccomandazioni per gli elementi consigliati.

## <a name="performance-recommendations"></a>Raccomandazioni per le prestazioni

La funzionalità [Raccomandazioni per le prestazioni](concepts-performance-recommendations.md) analizza i carichi di lavoro sul server per identificare gli indici con la possibilità di migliorare le prestazioni.

Aprire **Consigli sulle prestazioni** dalla sezione Prestazioni intelligenti della barra dei menu nella pagina del portale di Azure per il server MySQL.Open Performance Recommendations from the Intelligent **Performance** section of the menu bar on the Azure portal page for your MySQL server.

![Pagina di destinazione Elementi consigliati per le prestazioni](./media/concepts-performance-recommendations/performance-recommendations-page.png)

Selezionare **Analizza** e scegliere un database per avviare l'analisi. A seconda del carico di lavoro, il completamento dell'analisi potrebbe richiedere alcuni minuti. Al termine dell'analisi, verrà inviata una notifica nel portale. L'analisi esegue un esame approfondito del database. Si consiglia di eseguire l'analisi durante i periodi di minore attività.

Nella finestra **Suggerimenti** verrà visualizzato un elenco di suggerimenti se ne sono stati trovati e l'ID query correlato che ha generato questa raccomandazione. Con l'ID query, è possibile utilizzare la visualizzazione [mysql.query_store](concepts-query-store.md#mysqlquery_store) per ulteriori informazioni sulla query.

![Nuova pagina Raccomandazioni per le prestazioni](./media/concepts-performance-recommendations/performance-recommendations-result.png)

Le raccomandazioni non vengono applicate automaticamente. Per applicare la raccomandazione, copiare il testo della query ed eseguirlo dal client desiderato. Ricordarsi di testare e monitorare per valutare la raccomandazione.

## <a name="recommendation-types"></a>Tipi di raccomandazione

Attualmente sono supportate solo le raccomandazioni *Crea indice.*

### <a name="create-index-recommendations"></a>Raccomandazioni relative alla creazione di indici

*Creare* suggerimenti per l'indice suggerisci nuovi indici per velocizzare le query eseguite più di frequente o che richiedono molto tempo nel carico di lavoro. Questo tipo di raccomandazione richiede l'abilitazione di [Query Store.This](concepts-query-store.md) recommendation type requires Query Store to be enabled. Query Store raccoglie le informazioni sulle query e fornisce le statistiche dettagliate sul runtime delle query e sulla frequenza utilizzate dall'analisi per eseguire la raccomandazione.

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sul [monitoraggio e l'ottimizzazione](concepts-monitoring.md) in Azure Database for MySQL.Learn more about monitoring and tuning in Azure Database for MySQL.