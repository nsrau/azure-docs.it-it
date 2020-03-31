---
title: Consigli sulle prestazioni - Database di Azure per PostgreSQL - Server singoloPerformance Recommendations - Azure Database for PostgreSQL - Single Server
description: Questo articolo descrive la funzionalità di raccomandazione delle prestazioni nel database di Azure per PostgreSQL - Server singolo.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: a30af0c8bef47a37fe3439e885d3895a2c826225
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74768470"
---
# <a name="performance-recommendations-in-azure-database-for-postgresql---single-server"></a>Consigli sulle prestazioni nel database di Azure per PostgreSQL - Server singoloPerformance Recommendations in Azure Database for PostgreSQL - Single Server

**Si applica a:** Database di Azure per PostgreSQL - Versioni a server singolo 9.6, 10, 11Azure Database for PostgreSQL - Single Server versions 9.6, 10, 11

La funzionalità Suggerimenti per le prestazioni analizza i database per creare suggerimenti personalizzati per migliorare le prestazioni. Per produrre le raccomandazioni, l'analisi esamina varie caratteristiche del database, tra cui lo schema. Abilitare [Query Store](concepts-query-store.md) nel server per utilizzare completamente la funzionalità Suggerimenti per le prestazioni. Dopo aver implementato qualsiasi raccomandazione di prestazioni, è necessario testare le prestazioni per valutare l'impatto di tali modifiche. 

## <a name="permissions"></a>Autorizzazioni
Autorizzazioni **Proprietario** oppure **Collaboratore** necessarie per eseguire l'analisi usando la funzionalità Raccomandazioni per gli elementi consigliati.

## <a name="performance-recommendations"></a>Raccomandazioni per le prestazioni
La funzionalità [Raccomandazioni per le prestazioni](concepts-performance-recommendations.md) analizza i carichi di lavoro sul server per identificare gli indici con la possibilità di migliorare le prestazioni.

Aprire **Consigli sulle prestazioni** dalla sezione Prestazioni **intelligenti** della barra dei menu nella pagina del portale di Azure per il server PostgreSQL.

![Pagina di destinazione Elementi consigliati per le prestazioni](./media/concepts-performance-recommendations/performance-recommendations-page.png)

Selezionare **Analizza** e scegliere un database per avviare l'analisi. A seconda del carico di lavoro, il completamento dell'analisi potrebbe richiedere alcuni minuti. Al termine dell'analisi, verrà inviata una notifica nel portale. L'analisi esegue un esame approfondito del database. Si consiglia di eseguire l'analisi durante i periodi di minore attività. 

Nella finestra **Raccomandazioni** verrà visualizzato un elenco di suggerimenti, se ne sono stati trovati.

![Nuova pagina Raccomandazioni per le prestazioni](./media/concepts-performance-recommendations/performance-recommendations-result.png)

Le raccomandazioni non vengono applicate automaticamente. Per applicare la raccomandazione, copiare il testo della query ed eseguirlo dal client desiderato. Ricordarsi di testare e monitorare per valutare la raccomandazione. 

## <a name="recommendation-types"></a>Tipi di raccomandazione

Attualmente sono supportati due tipi di consigli: *Crea indice* e Indice *di rilascio*.

### <a name="create-index-recommendations"></a>Raccomandazioni relative alla creazione di indici
*Creare* suggerimenti per l'indice suggerisci nuovi indici per velocizzare le query eseguite più di frequente o che richiedono molto tempo nel carico di lavoro. Questo tipo di raccomandazione richiede l'abilitazione di [Query Store.This](concepts-query-store.md) recommendation type requires Query Store to be enabled. Query Store raccoglie le informazioni sulle query e fornisce le statistiche dettagliate sul runtime delle query e sulla frequenza utilizzate dall'analisi per eseguire la raccomandazione.

### <a name="drop-index-recommendations"></a>Raccomandazioni relative all'eliminazione di indici
Oltre a rilevare gli indici mancanti, Database di Azure per PostgreSQL analizza le prestazioni degli indici esistenti. Se un indice viene utilizzato raramente o ridondante, l'analizzatore consiglia di rilasciarlo.

## <a name="considerations"></a>Considerazioni
* Consigli sulle prestazioni non è disponibile per le repliche di [lettura.](concepts-read-replicas.md)
## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni su [monitoraggio e ottimizzazione](concepts-monitoring.md) nel Database di Azure per PostgreSQL.

