---
title: Visualizzazione e analisi dei dati in Azure Log Analytics | Microsoft Docs
description: Assistenza per gli utenti della ricerca log di Log Analytics relativamente all'esperienza di query dei log di Monitoraggio di Azure.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/16/2019
ms.author: bwren
ms.openlocfilehash: dc3a754bd1a9f689f4b24b9ee2c1e4cbc0611684
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/06/2019
ms.locfileid: "55751362"
---
# <a name="transition-from-log-analytics-log-search-to-azure-monitor-logs"></a>Transizione dalla ricerca log di Log Analytics ai log di Monitoraggio di Azure
La ricerca log in Log Analytics di recente è stata sostituita da una nuova esperienza per l'analisi dei log di Monitoraggio di Azure. La pagina Ricerca log attualmente è ancora accessibile mediante la voce di menu **Log (versione classica)** nella pagina **Aree di lavoro di Log Analytics** del portale di Azure, ma verrà rimossa il 15 febbraio 2019. Questo articolo descrive le differenze tra le due esperienze allo scopo di agevolare la transizione dalla ricerca log. 

## <a name="filter-results-of-a-query"></a>Filtrare i risultati di una query
In Ricerca log viene visualizzato un elenco di filtri non appena vengono restituiti i risultati della ricerca. Selezionare un filtro e fare clic su **Applica** per eseguire la query con il filtro selezionato.

![Filtro in Ricerca log](media/log-search-transition/filter-log-search.png)

Nei log di Monitoraggio di Azure selezionare *Filter (preview)* (Filtro (anteprima)) per visualizzare i filtri. Fare clic sull'icona del filtro per visualizzare filtri aggiuntivi. Selezionare un filtro e fare clic su **Apply & Run** (Applica ed esegui) per eseguire la query con il filtro selezionato.

![Filtro dei log](media/log-search-transition/filter-logs.png)

## <a name="extract-custom-fields"></a>Estrarre i campi personalizzati 
In Ricerca log è possibile estrarre i [campi personalizzati](../platform/custom-fields.md) dalla vista elenco, dove il menu di un campo include l'azione _Extract fields from Table_ (Estrai campi dalla tabella).

![Estrazione dei campi in Ricerca log](media/log-search-transition/extract-fields-log-search.png)

Nei log di Monitoraggio di Azure è possibile estrarre i campi personalizzati dalla vista tabella. A tale scopo, espandere un record facendo clic sulla freccia a sinistra e quindi fare clic sui puntini di sospensione per accedere all'azione _Extract fields_ (Estrai campi).

![Estrazione dei campi nei log](media/log-search-transition/extract-fields-logs.png)

## <a name="functions-and-computer-groups"></a>Funzioni e gruppi di computer
Per salvare una ricerca in Ricerca log, selezionare **Ricerche salvate** e **Aggiungi** per fornire un nome, una categoria e il testo di una query. Creare un [gruppo di computer](../platform/computer-groups.md) aggiungendo un alias di funzione.

![Salvataggio della ricerca log](media/log-search-transition/save-search-log-search.png)

Per salvare la query corrente nei log di Monitoraggio di Azure, selezionare **Salva**. Impostare **Salva con nome** su _Funzione_ e specificare un valore nel campo **Alias della funzione** per creare una [funzione](functions.md). Selezionare _Salva questa query come gruppo di computer_ per usare l'alias della funzione come [gruppo di computer](../platform/computer-groups.md).

![Salvataggio della query di log](media/log-search-transition/save-query-logs.png)

## <a name="saved-queries"></a>Query salvate
In Ricerca log le query salvate sono disponibili mediante la voce **Ricerche salvate** della barra delle azioni. Nei log di Monitoraggio di Azure è possibile accedere alle query salvate da [Esplora query](../log-query/get-started-portal.md#save-queries).

![Esplora query](media/log-search-transition/query-explorer.png)

## <a name="drill-down-on-summarized-rows"></a>Eseguire il drill-down nelle righe di riepilogo
In Ricerca log è possibile fare clic su una riga in una query di riepilogo per avviare un'altra query che elenca i record dettagliati di tale riga.

![Drill-down Ricerca log](media/log-search-transition/drilldown-search.png)

Nei log di Monitoraggio di Azure è necessario modificare la query per restituire questi record. Espandere una delle righe nei risultati e fare clic su **+** accanto al valore per aggiungerlo alla query. Impostare quindi come commento il comando **summarize** ed eseguire nuovamente la query.

![Drill-down dei log di Monitoraggio di Azure](media/log-search-transition/drilldown-logs.png)

## <a name="take-action"></a>Intervieni
In Ricerca log è possibile [avviare un runbook](take-action.md) dal risultato di una ricerca selezionando **Intervieni**.

![Intervieni](media/log-search-transition/take-action-log-search.png)

Nei log di Monitoraggio di Azure [creare un avviso dalla query di log](../platform/alerts-log.md). Configurare un gruppo di azioni con una o più azioni da eseguire in risposta all'avviso.

![Gruppo di azioni](media/log-search-transition/action-group.png)

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulla nuova [esperienza dei log di Monitoraggio di Azure](get-started-portal.md).