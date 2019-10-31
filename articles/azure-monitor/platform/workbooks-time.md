---
title: Creare report interattivi con le cartelle di lavoro di monitoraggio di Azure | Parametri time | Microsoft docs
description: Semplificare la creazione di report complessi con cartelle di lavoro con parametri predefinite e personalizzate
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.service: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 906ebcc49eca72744abf5854a1b320407d0384dc
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73165511"
---
# <a name="workbook-time-parameters"></a>Parametri ora cartella di lavoro

I parametri temporali consentono agli utenti di impostare il contesto di analisi dell'ora e vengono utilizzati da quasi tutti i report. È relativamente semplice da configurare e usare, consentendo agli autori di specificare gli intervalli di tempo da visualizzare nell'elenco a discesa, inclusa l'opzione per gli intervalli di tempo personalizzati. 

## <a name="creating-a-time-parameter"></a>Creazione di un parametro time
1. Iniziare con una cartella di lavoro vuota in modalità di modifica.
2. Scegliere _Aggiungi parametri_ dai collegamenti all'interno della cartella di lavoro.
3. Fare clic sul pulsante _Aggiungi parametro_ blu.
4. Nel riquadro nuovo parametro visualizzato immettere:
    1. Nome parametro: `TimeRange`
    2. Tipo di parametro: `Time range picker`
    3. Obbligatorio: `checked`
    4. Intervalli di tempo disponibili: ultima ora, ultime 12 ore, ultime 24 ore, ultime 48 ore, ultimi 3 giorni, ultimi 7 giorni e Consenti selezione dell'intervallo di tempo personalizzato
5. Scegliere ' Salva ' dalla barra degli strumenti per creare il parametro.

    ![Immagine che mostra la creazione di un parametro di intervallo di tempo](./media/workbooks-time/time-settings.png)

Questo è il modo in cui la cartella di lavoro sarà simile in modalità lettura.

![Immagine che mostra un parametro di intervallo di tempo in modalità di lettura](./media/workbooks-time/parameters-time.png)

## <a name="referencing-a-time-parameter"></a>Riferimento a un parametro time
### <a name="via-bindings"></a>Tramite binding
1. Aggiungere un controllo query alla cartella di lavoro e selezionare una risorsa Application Insights.
2. La maggior parte dei controlli cartella di lavoro supporta una selezione ambito _intervallo di tempo_ . Aprire l'elenco a discesa _intervallo di tempo_ e selezionare il `{TimeRange}` nel gruppo di parametri time Rang in basso.
3. In questo modo il parametro dell'intervallo di tempo viene associato all'intervallo di tempo del grafico. L'ambito temporale della query di esempio è ora le ultime 24 ore.
4. Eseguire la query per visualizzare i risultati

    ![Immagine che mostra un parametro di intervallo di tempo a cui si fa riferimento tramite binding](./media/workbooks-time/time-binding.png)

### <a name="in-kql"></a>In KQL
1. Aggiungere un controllo query alla cartella di lavoro e selezionare una risorsa Application Insights.
2. In KQL immettere un filtro per l'ambito temporale utilizzando il parametro: `| where timestamp {TimeRange}`
3. In questo modo si espande il tempo di valutazione della query per `| where timestamp > ago(1d)`, che corrisponde al valore dell'intervallo di tempo del parametro.
4. Eseguire la query per visualizzare i risultati

    ![Immagine che mostra un intervallo di tempo a cui viene fatto riferimento in KQL](./media/workbooks-time/time-in-code.png)

### <a name="in-text"></a>Testo 
1. Aggiungere un controllo di testo alla cartella di lavoro.
2. In Markdown immettere `The chosen time range is {TimeRange:label}`
3. Scegli _modifica completato_
4. Il controllo di testo visualizzerà il testo: _l'intervallo di tempo scelto è le ultime 24 ore_

## <a name="time-parameter-options"></a>Opzioni relative ai parametri temporali
| Parametro | Spiegazione | Esempio |
| ------------- |:-------------|:-------------|
| `{TimeRange}` | Etichetta intervallo di tempo | Ultime 24 ore |
| `{TimeRange:label}` | Etichetta intervallo di tempo | Ultime 24 ore |
| `{TimeRange:value}` | Valore intervallo di tempo | > ago (1D) |
| `{TimeRange:query}` | Query di intervallo di tempo | > ago (1D) |
| `{TimeRange:start}` | Ora di inizio dell'intervallo di tempo | 3/20/2019 4:18 PM |
| `{TimeRange:end}` | Ora di fine intervallo di tempo | 3/21/2019 4:18 PM |
| `{TimeRange:grain}` | Granularità intervallo di tempo | 30 m |


### <a name="using-parameter-options-in-a-query"></a>Utilizzo delle opzioni di parametro in una query
```kusto
requests
| make-series Requests = count() default = 0 on timestamp from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain}
```

## <a name="next-steps"></a>Passaggi successivi

* Per [iniziare ad](workbooks-visualizations.md) apprendere altre informazioni sulle cartelle di lavoro, sono disponibili molte opzioni di visualizzazione avanzate.
* [Controllare](workbooks-access-control.md) e condividere l'accesso alle risorse della cartella di lavoro.
