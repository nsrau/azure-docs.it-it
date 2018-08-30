---
title: Visualizzazione e analisi dei dati in Azure Log Analytics | Microsoft Docs
description: Questo articolo descrive i portali che è possibile usare in Azure Log Analytics per la creazione e la modifica di ricerche log.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: magoedte; bwren
ms.component: na
ms.openlocfilehash: 386aad94461fa3f2ceafb7564342797eefa2f086
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2018
ms.locfileid: "40246707"
---
# <a name="viewing-and-analyzing-data-in-log-analytics"></a>Visualizzazione e analisi dei dati in Log Analytics
Sono disponibili due opzioni nel portale di Azure per l'analisi dei dati archiviati in Log Analytics e per la creazione di query ad hoc per l'analisi. Le query create con questi portali sono usabili per altre funzionalità, come ad esempio dashboard e avvisi.

## <a name="log-analytics-page-preview"></a>Pagina di Log Analytics (anteprima)
Aprire la pagina di Log Analytics da **Log (anteprima)** nel menu di Log Analytics. Si tratta di una nuova esperienza per lavorare con i dati di log e creare query. È possibile ottenere un'introduzione al portale e controllare le funzionalità su [Introduzione alla pagina di Log Analytics nel portale di Azure](query-language/get-started-analytics-portal.md).

Tramite l'esperienza di [ricerca log](#log-search), la pagina di Log Analytics offre i miglioramenti seguenti.

* Schede multiple, ovvero schede separate create per lavorare con più query.
* Visualizzazioni avanzate: varietà di opzioni di grafico.
* Funzionalità Intellisense e di completamento automatico del linguaggio migliorata.
* Evidenziazione della sintassi: migliore leggibilità delle query. 
* Esplora query: query e funzioni salvate durante l'accesso.
* Vista schema: consente di esaminare la struttura dei dati per facilitare la scrittura di query.
* Condivisione: consente di creare collegamenti alle query o aggiungere le query a qualsiasi dashboard di Azure condiviso.
* Analisi intelligente: identifica i picchi nei grafici e svolge una rapida analisi della causa.
* Selezione delle colonne: consente di ordinare e raggruppare le colonne nei risultati della query.

> [!NOTE]
> La pagina di Log Analytics ha la stessa funzionalità del portale di Analisi avanzata, uno strumento esterno al portale di Azure. Il portale di Analisi avanzata è ancora disponibile, ma i collegamenti e gli altri riferimenti a esso nel portale di Azure vengono sostituiti con questa nuova pagina.

![Portale Advanced Analytics](media/log-analytics-log-search-portals/advanced-analytics-portal.png)


### <a name="firewall-requirements"></a>Requisiti del firewall
Il browser richiede l'accesso agli indirizzi seguenti per accedere alla pagina di Log Analytics e al portale di Analisi avanzata.  Se il browser accede al portale di Azure attraverso un firewall, è necessario abilitare l'accesso a questi indirizzi.

| Uri | IP | Porte |
|:---|:---|:---|
| portal.loganalytics.io | Dinamico | 80,443 |
| api.loganalytics.io    | Dinamico | 80,443 |
| docs.loganalytics.io   | Dinamico | 80,443 |


## <a name="log-search"></a>Ricerca log
Aprire la pagina di ricerca log da **Log** nel menu di Log Analytics o da **Log Analytics** nel menu di Monitoraggio di Azure. Tale operazione è adatta per l'analisi dei dati di log tramite query di base. Essa offre molte funzionalità per la modifica delle query senza che sia necessaria una conoscenza completa del linguaggio di query.  Per ottenere un riepilogo di queste funzionalità, vedere [Creare ricerche log in Azure Log Analytics tramite Ricerca log](log-analytics-log-search-log-search-portal.md). 


![Pagina Ricerca log](media/log-analytics-log-search-portals/log-search-portal.png)


## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sulla creazione di query tramite il linguaggio di query, vedere l'[esercitazione dettagliata sull'uso di Ricerca log](log-analytics-tutorial-viewdata.md)
- Assistere a una [lezione usando il portale di Analisi avanzata](query-language/get-started-analytics-portal.md), che fornisce la stessa esperienza della pagina di Log Analytics.

