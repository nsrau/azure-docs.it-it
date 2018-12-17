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
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: bwren
ms.openlocfilehash: 9a5472a6dfc944eb793e863704897c92b1a7572e
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2018
ms.locfileid: "53183353"
---
# <a name="viewing-and-analyzing-data-in-log-analytics"></a>Visualizzazione e analisi dei dati in Log Analytics
Sono disponibili due opzioni nel portale di Azure per l'analisi dei dati archiviati in Log Analytics e per la creazione di query ad hoc per l'analisi. Le query create con questi portali sono usabili per altre funzionalità, come ad esempio dashboard e avvisi.

## <a name="log-analytics-page"></a>Pagina Log Analytics
Aprire la pagina Log Analytics da **Log** nel menu di Log Analytics. Si tratta di una nuova esperienza per lavorare con i dati di log e creare query. È possibile ottenere un'introduzione al portale e controllare le funzionalità su [Introduzione alla pagina di Log Analytics nel portale di Azure](../../azure-monitor/log-query/get-started-portal.md).

Tramite l'esperienza di [ricerca log (classica)](#log-search-classic), la pagina di Log Analytics offre i miglioramenti seguenti.

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

![Portale Advanced Analytics](media/portals/advanced-analytics-portal.png)

### <a name="resource-logs"></a>Log risorse
La nuova esperienza di Log Analytics si integra con varie risorse di Azure, ad esempio le macchine virtuali. Ciò significa che è possibile aprire la pagina di Log Analytics direttamente tramite il menu di monitoraggio della risorsa senza passare a Monitoraggio di Azure o Log Analytics e perdere il contesto della risorsa. La funzionalità **Log** non è ancora stata abilitata per tutte le risorse di Azure, ma sarà presente nel menu del portale per vari tipi di risorse.

Quando si apre Log Analytics da una risorsa specifica, l'ambito viene automaticamente impostato per registrare i record solo di tale risorsa.   Per scrivere una query che includa altri record, è necessario aprirla dal menu di Log Analytics o di Monitoraggio di Azure.

Le opzioni seguenti non sono ancora disponibili dalla visualizzazione della risorsa di Log Analytics:

- Salva
- imposta avviso
- Esplora query
- Passaggio a diverse aree di lavoro/risorse (attualmente non pianificato)


### <a name="firewall-requirements"></a>Requisiti del firewall
Il browser richiede l'accesso agli indirizzi seguenti per accedere alla pagina di Log Analytics e al portale di Analisi avanzata.  Se il browser accede al portale di Azure attraverso un firewall, è necessario abilitare l'accesso a questi indirizzi.

| Uri | IP | Porte |
|:---|:---|:---|
| portal.loganalytics.io | Dinamico | 80,443 |
| api.loganalytics.io    | Dinamico | 80,443 |
| docs.loganalytics.io   | Dinamico | 80,443 |


## <a name="log-search-classic"></a>Ricerca log (classica)
Aprire la pagina Ricerca log da **Log (pagina classica)** nel menu di Log Analytics o da **Log Analytics** nel menu di Monitoraggio di Azure. È la pagina classica utilizzata per lavorare con le query di Log Analytics in cui non sono disponibili le funzionalità aggiuntive della pagina [Log Analytics](#log-analytics-page) elencate sopra.



![Pagina Ricerca log](media/portals/log-search-portal.png)


## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sulla creazione di query tramite il linguaggio di query, vedere l'[esercitazione dettagliata sull'uso di Ricerca log](../../azure-monitor/learn/tutorial-viewdata.md)
- Assistere a una [lezione usando il portale di Analisi avanzata](../../azure-monitor/log-query/get-started-portal.md), che fornisce la stessa esperienza della pagina di Log Analytics.

