---
title: Raccomandazioni per le prestazioni nel Database di Azure per PostgreSQL
description: Questo articolo descrive i consigli sulle prestazioni che si possono ottenere nel Database di Azure per PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/26/2018
ms.openlocfilehash: 1dedc08f27d1a483290dc61cce879290ca66592d
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2018
ms.locfileid: "53548103"
---
# <a name="performance-recommendations-in-azure-database-for-postgresql"></a>Raccomandazioni per le prestazioni nel Database di Azure per PostgreSQL

**Si applica a:** Database di Azure per PostgreSQL 9.6 e 10

> [!IMPORTANT]
> Raccomandazioni per le prestazioni è in anteprima pubblica.

La funzione Raccomandazioni per le prestazioni identifica gli indici principali che possono essere creati nel Database di Azure per il server PostgreSQL per migliorare le prestazioni. Per generare le raccomandazioni sugli indici, la funzionalità prende in considerazione diverse caratteristiche del database, tra cui lo schema e il carico di lavoro segnalati da Query Store. Dopo avere implementato le raccomandazioni per le prestazioni, i clienti devono testare le prestazioni per valutare l'impatto di tali modifiche. 

## <a name="permissions"></a>Autorizzazioni
Autorizzazioni **Proprietario** oppure **Collaboratore** necessarie per eseguire l'analisi usando la funzionalità Raccomandazioni per gli elementi consigliati.

## <a name="performance-recommendations"></a>Raccomandazioni per le prestazioni
La funzionalità [Raccomandazioni per le prestazioni](concepts-performance-recommendations.md) analizza i carichi di lavoro sul server per identificare gli indici con la possibilità di migliorare le prestazioni.

Aprire **Raccomandazioni per le prestazioni** dalla sezione **Supporto + risoluzione dei problemi** della barra dei menu nella pagina del portale di Azure per il server PostgreSQL.

![Pagina di destinazione Elementi consigliati per le prestazioni](./media/concepts-performance-recommendations/performance-recommendations-landing-page.png)

Selezionare **Analizza** e scegliere un database. Viene avviata l'analisi. A seconda del carico di lavoro, l'operazione potrebbe richiedere alcuni minuti. Al termine dell'analisi, verrà inviata una notifica nel portale.

La finestra **Elementi consigliati per le prestazioni** illustrerà un elenco di raccomandazioni se sono state trovate. Un elemento consigliato illustrerà informazioni su **Database**, **Tabella**, **Colonna**, e **Dimensioni indice** pertinenti.

![Nuova pagina Raccomandazioni per le prestazioni](./media/concepts-performance-recommendations/performance-recommendations-result.png)

Per implementare l'elemento consigliato, copiare il testo della query ed eseguirlo dal client preferito.

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni su [monitoraggio e ottimizzazione](concepts-monitoring.md) nel Database di Azure per PostgreSQL.

