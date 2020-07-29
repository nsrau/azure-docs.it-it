---
title: Raccomandazioni per le prestazioni-database di Azure per PostgreSQL-server singolo
description: Questo articolo descrive la funzionalità di raccomandazione per le prestazioni del database di Azure per PostgreSQL-server singolo.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: a30af0c8bef47a37fe3439e885d3895a2c826225
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "74768470"
---
# <a name="performance-recommendations-in-azure-database-for-postgresql---single-server"></a>Suggerimenti sulle prestazioni in database di Azure per PostgreSQL-server singolo

**Si applica a:** Database di Azure per PostgreSQL: versioni a server singolo 9,6, 10, 11

La funzionalità raccomandazioni per le prestazioni analizza i database per creare suggerimenti personalizzati per migliorare le prestazioni. Per produrre le raccomandazioni, l'analisi prende in considerazione varie caratteristiche dei database, tra cui lo schema. Attivare [Query Store](concepts-query-store.md) sul server per sfruttare al massimo le potenzialità di Raccomandazioni per le prestazioni. Dopo avere implementato una raccomandazione per le prestazioni, è necessario testare le prestazioni per valutare l'impatto di tali modifiche. 

## <a name="permissions"></a>Autorizzazioni
Autorizzazioni **Proprietario** oppure **Collaboratore** necessarie per eseguire l'analisi usando la funzionalità Raccomandazioni per gli elementi consigliati.

## <a name="performance-recommendations"></a>Raccomandazioni per le prestazioni
La funzionalità [Raccomandazioni per le prestazioni](concepts-performance-recommendations.md) analizza i carichi di lavoro sul server per identificare gli indici con la possibilità di migliorare le prestazioni.

Aprire **suggerimenti sulle prestazioni** dalla sezione **prestazioni intelligenti** della barra dei menu nella pagina portale di Azure per il server PostgreSQL.

![Pagina di destinazione Elementi consigliati per le prestazioni](./media/concepts-performance-recommendations/performance-recommendations-page.png)

Selezionare **Analizza** e scegliere un database per avviare l'analisi. A seconda del carico di lavoro, il completamento dell'analisi potrebbe richiedere alcuni minuti. Al termine dell'analisi, verrà inviata una notifica nel portale. L'analisi esegue un esame approfondito del database. Si consiglia quindi di effettuarla durante i periodi di minore traffico. 

Nella finestra **raccomandazioni** viene visualizzato un elenco di raccomandazioni, se trovate.

![Nuova pagina Raccomandazioni per le prestazioni](./media/concepts-performance-recommendations/performance-recommendations-result.png)

Le raccomandazioni non vengono applicate automaticamente. Per applicare una raccomandazione, copiare il testo della query ed eseguirlo dal client preferito. Non dimenticare di eseguire le operazioni di test e monitoraggio per valutare la raccomandazione. 

## <a name="recommendation-types"></a>Tipi di raccomandazione

Attualmente sono supportati due tipi di consigli: *create index* e *Drop index*.

### <a name="create-index-recommendations"></a>Raccomandazioni relative alla creazione di indici
Le raccomandazioni di tipo *Crea indice* suggeriscono nuovi indici per velocizzare le query che, nell'ambito del carico di lavoro, vengono eseguite più di frequente o richiedono più tempo. Per questo tipo di raccomandazione è necessario che sia attivato [Query Store](concepts-query-store.md). Query Store raccoglie informazioni sulle query e fornisce le statistiche dettagliate di frequenza e runtime delle query usate dall'analisi per generare le raccomandazioni.

### <a name="drop-index-recommendations"></a>Raccomandazioni relative all'eliminazione di indici
Oltre a rilevare gli indici mancanti, database di Azure per PostgreSQL analizza le prestazioni degli indici esistenti. Se un indice viene usato raramente o ridondante, l'analizzatore consiglia di eliminarlo.

## <a name="considerations"></a>Considerazioni
* Le raccomandazioni sulle prestazioni non sono disponibili per le [repliche di lettura](concepts-read-replicas.md).
## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni su [monitoraggio e ottimizzazione](concepts-monitoring.md) nel Database di Azure per PostgreSQL.

