---
title: Visualizzazione e analisi dei dati di log in Monitoraggio di Azure | Microsoft Docs
description: Questo articolo illustra l'uso di Log Analytics nel portale di Azure per creare e modificare le query di log in Monitoraggio di Azure.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: bwren
ms.openlocfilehash: 0e5b9b43e528b37fd994f9131f145abadb33c53b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59798477"
---
# <a name="viewing-and-analyzing-log-data-in-azure-monitor"></a>Visualizzazione e analisi dei dati di log in Monitoraggio di Azure
Log Analytics rappresenta l'esperienza principale per la gestione dei dati di log e la creazione delle query in Monitoraggio di Azure. Aprire Log Analytics tramite la voce **Log** del menu di **Monitoraggio di Azure**. È possibile ottenere un'introduzione al portale e iniziare a conoscere le relative funzionalità in [Introduzione a Log Analytics nel portale di Azure](get-started-portal.md).

Log Analytics offre le funzionalità seguenti per la gestione delle query di log.

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
> Log Analytics ha la stessa funzionalità del portale di Analisi avanzata, uno strumento esterno al portale di Azure. Il portale di Analisi avanzata è ancora disponibile, ma i collegamenti e gli altri riferimenti a esso nel portale di Azure vengono sostituiti con questa nuova pagina.

![Log Analytics](media/portals/log-analytics.png)

## <a name="resource-logs"></a>Log risorse
Log Analytics si integra con varie risorse di Azure, ad esempio le macchine virtuali. Ciò significa che è possibile aprire Log Analytics direttamente tramite il menu di monitoraggio della risorsa senza passare a Monitoraggio di Azure e perdere il contesto della risorsa. La funzionalità **Log** non è ancora stata abilitata per tutte le risorse di Azure, ma sarà presente nel menu del portale per vari tipi di risorse.

Quando si apre Log Analytics da una risorsa specifica, l'ambito viene automaticamente impostato per registrare i record solo di tale risorsa.   Per scrivere una query che includa altri record, è necessario aprirla dal menu di Monitoraggio di Azure.

Le opzioni seguenti non sono ancora disponibili dalla visualizzazione della risorsa di Log Analytics:

- Salva
- imposta avviso
- Esplora query
- Passaggio a diverse aree di lavoro/risorse (attualmente non pianificato)


## <a name="firewall-requirements"></a>Requisiti del firewall
Il browser richiede l'accesso agli indirizzi seguenti per accedere a Log Analytics.  Se il browser accede al portale di Azure attraverso un firewall, è necessario abilitare l'accesso a questi indirizzi.

| Uri | IP | Porte |
|:---|:---|:---|
| portal.loganalytics.io | Dinamico | 80,443 |
| api.loganalytics.io    | Dinamico | 80,443 |
| docs.loganalytics.io   | Dinamico | 80,443 |


## <a name="next-steps"></a>Passaggi successivi

- Esaminare un'[esercitazione sull'uso di Log Analytics](../../azure-monitor/log-query/get-started-portal.md).
- Esaminare un'[esercitazione sull'uso di Ricerca log](../../azure-monitor/learn/tutorial-viewdata.md).

