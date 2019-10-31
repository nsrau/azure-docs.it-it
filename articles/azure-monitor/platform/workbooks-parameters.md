---
title: Creare report interattivi con le cartelle di lavoro di monitoraggio di Azure | Creazione di parametri | Microsoft docs
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
ms.openlocfilehash: c221abf423a21d424bd5198696a61d7ec83521e9
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73165758"
---
# <a name="workbook-parameters"></a>Parametri cartella di lavoro

I parametri consentono agli autori di cartelle di lavoro di raccogliere input dai consumer e farvi riferimento in altre parti della cartella di lavoro, in genere per definire l'ambito del set di risultati o impostare l'oggetto visivo appropriato. Si tratta di una funzionalità chiave che consente agli autori di creare report ed esperienze interattive. 

Le cartelle di lavoro consentono di controllare il modo in cui i controlli dei parametri vengono presentati ai consumer, ovvero la casella di testo e l'elenco a discesa, la selezione singola e la selezione multifunzione, i valori di testo, JSON, KQL o Graph di risorse di Azure e così via.  

I tipi di parametro supportati sono:
* [Tempo](workbooks-time.md) : consente a un utente di selezionare dagli intervalli di tempo prepopolati o di selezionare un intervallo personalizzato
* [Elenco a discesa](workbooks-dropdowns.md) : consente a un utente di selezionare un valore o un set di valori
* [Testo](workbooks-text.md) : consente a un utente di immettere testo arbitrario
* [Risorsa](workbooks-resources.md) : consente agli utenti di selezionare una o più risorse di Azure
* [Sottoscrizione](workbooks-resources.md) : consente agli utenti di selezionare una o più risorse della sottoscrizione di Azure
* Tipo di risorsa: consente a un utente di selezionare uno o più valori del tipo di risorsa di Azure
* Località: consente a un utente di selezionare uno o più valori di località di Azure

È possibile fare riferimento a questi valori di parametro in altre parti di cartelle di lavoro tramite binding o espansioni di valori.

## <a name="creating-a-parameter"></a>Creazione di un parametro
1. Iniziare con una cartella di lavoro vuota in modalità di modifica.
2. Scegliere _Aggiungi parametri_ dai collegamenti all'interno della cartella di lavoro.
3. Fare clic sul pulsante _Aggiungi parametro_ blu.
4. Nel riquadro nuovo parametro visualizzato immettere:
    1. Nome parametro: `TimeRange` *(si noti che __i nomi dei__ parametri **non possono** includere spazi o caratteri speciali)*
    2. Nome visualizzato: `Time Range` *(Tuttavia, __i nomi visualizzati__ possono includere spazi, caratteri speciali, emoji e così via)*
    2. Tipo di parametro: `Time range picker`
    3. Obbligatorio: `checked`
    4. Intervalli di tempo disponibili: ultima ora, ultime 12 ore, ultime 24 ore, ultime 48 ore, ultimi 3 giorni, ultimi 7 giorni e Consenti selezione dell'intervallo di tempo personalizzato
5. Scegliere ' Salva ' dalla barra degli strumenti per creare il parametro.

   ![Immagine che mostra la creazione di un parametro di intervallo di tempo](./media/workbooks-parameters/time-settings.png)

Questo è il modo in cui la cartella di lavoro sarà simile in modalità lettura, nello stile "pillole".

   ![Immagine che mostra un parametro di intervallo di tempo in modalità di lettura](./media/workbooks-parameters/parameters-time.png)

## <a name="referencing-a-parameter"></a>Riferimento a un parametro
### <a name="via-bindings"></a>Tramite binding
1. Aggiungere un controllo query alla cartella di lavoro e selezionare una risorsa Application Insights.
2. Aprire l'elenco a discesa _intervallo di tempo_ e selezionare l'opzione `Time Range` nella parte inferiore della sezione parametri.
3. In questo modo il parametro dell'intervallo di tempo viene associato all'intervallo di tempo del grafico. L'ambito temporale della query di esempio è ora le ultime 24 ore.
4. Eseguire la query per visualizzare i risultati

    ![Immagine che mostra un parametro di intervallo di tempo a cui si fa riferimento tramite binding](./media/workbooks-parameters/time-binding.png)

### <a name="in-kql"></a>In KQL
1. Aggiungere un controllo query alla cartella di lavoro e selezionare una risorsa Application Insights.
2. In KQL immettere un filtro per l'ambito temporale utilizzando il parametro: `| where timestamp {TimeRange}`
3. In questo modo si espande il tempo di valutazione della query per `| where timestamp > ago(1d)`, che corrisponde al valore dell'intervallo di tempo del parametro.
4. Eseguire la query per visualizzare i risultati

    ![Immagine che mostra un intervallo di tempo a cui viene fatto riferimento in KQL](./media/workbooks-parameters/time-in-code.png)

### <a name="in-text"></a>Testo 
1. Aggiungere un controllo di testo alla cartella di lavoro.
2. In Markdown immettere `The chosen time range is {TimeRange:label}`
3. Scegli _modifica completato_
4. Il controllo di testo visualizzerà il testo: _l'intervallo di tempo scelto è le ultime 24 ore_

## <a name="parameter-options"></a>Opzioni dei parametri:
La sezione _nel testo_ usava la `label` del parametro anziché il relativo valore. I parametri espongono varie opzioni, a seconda del tipo, ad esempio i prelievo di intervallo di tempo consentono valore, etichetta, query, inizio, fine e granularità.

Usare la sezione `Previews` del riquadro _modifica parametro_ per visualizzare le opzioni di espansione per il parametro:

![Immagine che mostra le opzioni del parametro intervallo di tempo](./media/workbooks-parameters/time-previews.png)

## <a name="next-steps"></a>Passaggi successivi

* Per [iniziare ad](workbooks-visualizations.md) apprendere altre informazioni sulle cartelle di lavoro, sono disponibili molte opzioni di visualizzazione avanzate.
* [Controllare](workbooks-access-control.md) e condividere l'accesso alle risorse della cartella di lavoro.
