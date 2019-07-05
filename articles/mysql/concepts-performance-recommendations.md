---
title: Raccomandazioni sulle prestazioni nel Database di Azure per MySQL
description: Questo articolo descrive la funzionalità di raccomandazione per le prestazioni nel Database di Azure per MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: af3c4482b1ce9e521d14a0e0c63de40625c25c73
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67461786"
---
# <a name="performance-recommendations-in-azure-database-for-mysql"></a>Raccomandazioni sulle prestazioni nel Database di Azure per MySQL

**Si applica a:** Database di Azure per MySQL 5.7

> [!NOTE]
> Raccomandazioni sulle prestazioni è disponibile in anteprima.

La funzionalità suggerimenti relativi alle prestazioni analizza i database per creare i suggerimenti personalizzati per migliorare le prestazioni. Per produrre i suggerimenti, l'analisi vengono esaminati vari dalle caratteristiche del database incluso lo schema. Abilitare [Query Store](concepts-query-store.md) sul server per sfruttare appieno la funzionalità di raccomandazioni per le prestazioni. Se lo schema di prestazioni è impostata su OFF, l'attivazione in Query Store Abilita performance_schema e un subset di strumenti di prestazioni dello schema necessario per la funzionalità. Dopo avere implementato qualsiasi raccomandazione per le prestazioni, è consigliabile testare le prestazioni per valutare l'impatto di tali modifiche.

## <a name="permissions"></a>Autorizzazioni

Autorizzazioni **Proprietario** oppure **Collaboratore** necessarie per eseguire l'analisi usando la funzionalità Raccomandazioni per gli elementi consigliati.

## <a name="performance-recommendations"></a>Raccomandazioni per le prestazioni

La funzionalità [Raccomandazioni per le prestazioni](concepts-performance-recommendations.md) analizza i carichi di lavoro sul server per identificare gli indici con la possibilità di migliorare le prestazioni.

Aprire **raccomandazioni per le prestazioni** dal **prestazioni intelligenti** sezione della barra dei menu nella pagina del portale di Azure per il server MySQL.

![Pagina di destinazione Elementi consigliati per le prestazioni](./media/concepts-performance-recommendations/performance-recommendations-page.png)

Selezionare **Analyze** e scegliere un database, che verrà avviata l'analisi. A seconda del carico di lavoro, l'analisi potrebbe richiedere alcuni minuti. Al termine dell'analisi, verrà inviata una notifica nel portale. Analisi consente di eseguire un esame approfondito del database. Si consiglia di che eseguire analisi durante i periodi non di punta.

Il **raccomandazioni** finestra verrà visualizzato un elenco di raccomandazioni se sono stati trovati e l'ID di query correlata che ha generato questa raccomandazione. Con l'ID di query, è possibile usare la [mysql.query_store](concepts-query-store.md#mysqlquery_store) vista per altre informazioni sulla query.

![Nuova pagina Raccomandazioni per le prestazioni](./media/concepts-performance-recommendations/performance-recommendations-result.png)

Raccomandazioni non vengono applicate automaticamente. Per applicare la raccomandazione, copiare il testo della query ed eseguirlo dal client preferito. Ricordarsi di test e monitoraggio per valutare la raccomandazione.

## <a name="recommendation-types"></a>Tipi di consigli

Attualmente, solo *Create Index* consigli sono supportati.

### <a name="create-index-recommendations"></a>Raccomandazioni relative alla creazione di indici

*Crea indice* offrono suggerimenti per individuare gli indici nuovi per velocizzare le query nel carico di lavoro più di frequente esecuzione o che richiedono molto tempo. Questo tipo di raccomandazione richiede [Query Store](concepts-query-store.md) deve essere abilitata. Query Store raccoglie informazioni sulle query e fornisce le statistiche di runtime e la frequenza query dettagliate che usa l'analisi per verificare la raccomandazione.

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sulle [di monitoraggio e ottimizzazione](concepts-monitoring.md) nel Database di Azure per MySQL.