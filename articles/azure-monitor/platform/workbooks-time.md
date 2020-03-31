---
title: Parametri temporali delle cartelle di lavoro di Azure MonitorAzure Monitor workbooks time parameters
description: Semplificare la creazione di report complessi con cartelle di lavoro con parametri predefinite e personalizzate
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 380b8a7ce286ab06b6935bf63bf3a0e82f371c2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658014"
---
# <a name="workbook-time-parameters"></a>Parametri temporali della cartella di lavoro

I parametri temporali consentono agli utenti di impostare il contesto temporale dell'analisi e vengono utilizzati da quasi tutti i report. È relativamente semplice da configurare e utilizzare - consentendo agli autori di specificare gli intervalli di tempo da visualizzare nel menu a discesa, inclusa l'opzione per gli intervalli di tempo personalizzati. 

## <a name="creating-a-time-parameter"></a>Creazione di un parametro di tempoCreating a time parameter
1. Iniziare con una cartella di lavoro vuota in modalità di modifica.
2. Scegliere _Aggiungi parametri_ dai collegamenti all'interno della cartella di lavoro.
3. Fare clic sul pulsante blu _Aggiungi parametro._
4. Nel nuovo riquadro dei parametri visualizzato immettere:
    1. Nome parametro:`TimeRange`
    2. Tipo di parametro:`Time range picker`
    3. Obbligatorio:`checked`
    4. Intervalli di tempo disponibili: Ultima ora, Ultime 12 ore, Ultime 24 ore, Ultime 48 ore, Ultimi 3 giorni, Ultimi 7 giorni e Consenti selezione intervallo di tempo personalizzato
5. Scegliere 'Salva' dalla barra degli strumenti per creare il parametro.

    ![Immagine che mostra la creazione di un parametro di intervallo di tempo](./media/workbooks-time/time-settings.png)

Questo è l'aspetto della cartella di lavoro in modalità di lettura.

![Immagine che mostra un parametro dell'intervallo di tempo in modalità di lettura](./media/workbooks-time/parameters-time.png)

## <a name="referencing-a-time-parameter"></a>Riferimento a un parametro di tempo
### <a name="via-bindings"></a>Tramite binding
1. Aggiungere un controllo query alla cartella di lavoro e selezionare una risorsa di Application Insights.Add a query control to the workbook and select an Application Insights resource.
2. La maggior parte dei controlli della cartella di lavoro supporta una selezione ambito _Intervallo di tempo._ Aprire l'elenco a discesa `{TimeRange}` _Intervallo_ di tempo e selezionare il gruppo di parametri nel lasso temporale nella parte inferiore.
3. In questo modo il parametro dell'intervallo di tempo viene associato all'intervallo di tempo del grafico. L'ambito temporale della query di esempio è ora delle ultime 24 ore.
4. Eseguire una query per visualizzare i risultati

    ![Immagine che mostra un parametro dell'intervallo di tempo a cui viene fatto riferimento tramite associazioni](./media/workbooks-time/time-binding.png)

### <a name="in-kql"></a>In KQL
1. Aggiungere un controllo query alla cartella di lavoro e selezionare una risorsa di Application Insights.Add a query control to the workbook and select an Application Insights resource.
2. Nel KQL, immettere un filtro ambito temporale utilizzando il parametro:`| where timestamp {TimeRange}`
3. Questo si espande sul `| where timestamp > ago(1d)`tempo di valutazione delle query a , che è il valore dell'intervallo di tempo del parametro.
4. Eseguire una query per visualizzare i risultati

    ![Immagine che mostra un intervallo di tempo a cui si fa riferimento in KQL](./media/workbooks-time/time-in-code.png)

### <a name="in-text"></a>Nel testo 
1. Aggiungere un controllo di testo alla cartella di lavoro.
2. Nel markdown, immettere`The chosen time range is {TimeRange:label}`
3. Scegliere _Fine modifica_
4. Il controllo di testo mostrerà il testo: _l'intervallo di tempo scelto è Ultime 24 ore_

## <a name="time-parameter-options"></a>Opzioni dei parametri temporali
| Parametro | Spiegazione | Esempio |
| ------------- |:-------------|:-------------|
| `{TimeRange}` | Etichetta intervallo di tempo | Ultime 24 ore |
| `{TimeRange:label}` | Etichetta intervallo di tempo | Ultime 24 ore |
| `{TimeRange:value}` | Valore intervallo di tempo | > fa(1d) |
| `{TimeRange:query}` | Query intervallo di tempo | > fa(1d) |
| `{TimeRange:start}` | Ora di inizio intervallo di tempo | 20/03/2019 16:18 |
| `{TimeRange:end}` | Intervallo di tempo Ora di fine | 21/03/2019 16:18 |
| `{TimeRange:grain}` | Grana dell'intervallo di tempo | 30 m |


### <a name="using-parameter-options-in-a-query"></a>Utilizzo delle opzioni dei parametri in una query
```kusto
requests
| make-series Requests = count() default = 0 on timestamp from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain}
```

## <a name="next-steps"></a>Passaggi successivi

* [Iniziare a](workbooks-visualizations.md) conoscere le cartelle di lavoro su molte opzioni di visualizzazione avanzate.
* [Controllare](workbooks-access-control.md) e condividere l'accesso alle risorse della cartella di lavoro.
