---
title: Raccomandazioni per le prestazioni nel Database di Azure per PostgreSQL
description: Questo articolo descrive la funzionalità di raccomandazione per le prestazioni nel Database di Azure per PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/28/2018
ms.openlocfilehash: c5324618eeda90b4ef1a512385fb2f14bf391215
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58620173"
---
# <a name="performance-recommendations-in-azure-database-for-postgresql"></a>Raccomandazioni per le prestazioni nel Database di Azure per PostgreSQL

**Si applica a:** Database di Azure per PostgreSQL 9.6 e 10

La funzionalità suggerimenti relativi alle prestazioni analizza i database per creare i suggerimenti personalizzati per migliorare le prestazioni. Per produrre i suggerimenti, l'analisi vengono esaminati vari dalle caratteristiche del database incluso lo schema. Abilitare [Query Store](concepts-query-store.md) sul server per sfruttare appieno la funzionalità di raccomandazioni per le prestazioni. Dopo avere implementato qualsiasi raccomandazione per le prestazioni, è consigliabile testare le prestazioni per valutare l'impatto di tali modifiche. 

## <a name="permissions"></a>Autorizzazioni
Autorizzazioni **Proprietario** oppure **Collaboratore** necessarie per eseguire l'analisi usando la funzionalità Raccomandazioni per gli elementi consigliati.

## <a name="performance-recommendations"></a>Raccomandazioni per le prestazioni
La funzionalità [Raccomandazioni per le prestazioni](concepts-performance-recommendations.md) analizza i carichi di lavoro sul server per identificare gli indici con la possibilità di migliorare le prestazioni.

Aprire **raccomandazioni per le prestazioni** dal **prestazioni intelligenti** sezione della barra dei menu nella pagina del portale di Azure per il server PostgreSQL.

![Pagina di destinazione Elementi consigliati per le prestazioni](./media/concepts-performance-recommendations/performance-recommendations-page.png)

Selezionare **Analyze** e scegliere un database, che verrà avviata l'analisi. A seconda del carico di lavoro, th analisi può richiedere alcuni minuti. Al termine dell'analisi, verrà inviata una notifica nel portale. Analisi consente di eseguire un esame approfondito del database. Si consiglia di che eseguire analisi durante i periodi non di punta. 

Il **raccomandazioni** finestra verrà visualizzato un elenco di raccomandazioni se sono stati trovati.

![Nuova pagina Raccomandazioni per le prestazioni](./media/concepts-performance-recommendations/performance-recommendations-result.png)

Raccomandazioni non vengono applicate automaticamente. Per applicare la raccomandazione, copiare il testo della query ed eseguirlo dal client preferito. Ricordarsi di test e monitoraggio per valutare la raccomandazione. 

## <a name="recommendation-types"></a>Tipi di consigli

Attualmente, sono supportati due tipi di suggerimenti: *Crea indice* e *Drop Index*.

### <a name="create-index-recommendations"></a>Raccomandazioni relative alla creazione di indici
*Crea indice* offrono suggerimenti per individuare gli indici nuovi per velocizzare le query nel carico di lavoro più di frequente esecuzione o che richiedono molto tempo. Questo tipo di raccomandazione richiede [Query Store](concepts-query-store.md) deve essere abilitata. Query Store raccoglie informazioni sulle query e fornisce le statistiche di runtime e la frequenza query dettagliate che usa l'analisi per verificare la raccomandazione.

### <a name="drop-index-recommendations"></a>Raccomandazioni relative all'eliminazione di indici
Oltre a rilevare indici mancanti, Database di Azure per PostgreSQL consente di analizzare le prestazioni degli indici esistenti. Se un indice viene raramente usato o ridondanti, l'analizzatore consiglia di eliminarlo.


## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni su [monitoraggio e ottimizzazione](concepts-monitoring.md) nel Database di Azure per PostgreSQL.

