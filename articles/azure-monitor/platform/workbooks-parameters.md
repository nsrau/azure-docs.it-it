---
title: Cartelle di lavoro di Azure Monitor che creano parametriAzure Monitor workbooks creating parameters
description: Semplificare la creazione di report complessi con cartelle di lavoro con parametri predefinite e personalizzate
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 3e7dda85f1f890d5ae0eb4722c3e028b373fdcab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658218"
---
# <a name="workbook-parameters"></a>Parametri della cartella di lavoro

I parametri consentono agli autori di cartelle di lavoro di raccogliere input dai consumer e farvi riferimento in altre parti della cartella di lavoro, in genere per definire l'ambito del set di risultati o l'impostazione dell'oggetto visivo corretto. Si tratta di una funzionalità chiave che consente agli autori di creare report ed esperienze interattive. 

Le cartelle di lavoro consentono di controllare la modalità di presentazione dei controlli dei parametri ai consumer, ovvero casella di testo e elenco a discesa, valori a selezione singola e multipla, da testo, JSON, KQL o Azure Resource Graph e così via.  

I tipi di parametro supportati includono:
* [Tempo:](workbooks-time.md) consente all'utente di selezionare intervalli di tempo prepopolati o selezionare un intervallo personalizzato
* [Elenco a discesa](workbooks-dropdowns.md) : consente all'utente di selezionare da un valore o un set di valori
* [Testo](workbooks-text.md) - consente all'utente di immettere testo arbitrario
* [Risorsa:](workbooks-resources.md) consente a un utente di selezionare una o più risorse di AzureResource - allows a user to select one or more Azure resources
* [Sottoscrizione:](workbooks-resources.md) consente a un utente di selezionare una o più risorse di sottoscrizione di AzureSubscription - allows a user to select one or more Azure subscription resources
* Tipo di risorsa: consente a un utente di selezionare uno o più valori del tipo di risorsa di AzureResource Type - allows a user to select one or more Azure resource type values
* Posizione: consente a un utente di selezionare uno o più valori di posizione di AzureLocation - allows a user to select one or more Azure location values

È possibile fare riferimento a questi valori di parametro in altre parti delle cartelle di lavoro tramite associazioni o espansioni di valori.

## <a name="creating-a-parameter"></a>Creazione di un parametro
1. Iniziare con una cartella di lavoro vuota in modalità di modifica.
2. Scegliere _Aggiungi parametri_ dai collegamenti all'interno della cartella di lavoro.
3. Fare clic sul pulsante blu _Aggiungi parametro._
4. Nel nuovo riquadro dei parametri visualizzato immettere:
    1. Nome parametro: `TimeRange` *(si noti che i nomi dei __parametri__ non **possono** includere spazi o caratteri speciali)*
    2. Nome visualizzato: `Time Range` *(tuttavia, i nomi __visualizzati__ possono includere spazi, caratteri speciali, emoji, ecc.)*  
    2. Tipo di parametro:`Time range picker`
    3. Obbligatorio:`checked`
    4. Intervalli di tempo disponibili: Ultima ora, Ultime 12 ore, Ultime 24 ore, Ultime 48 ore, Ultimi 3 giorni, Ultimi 7 giorni e Consenti selezione intervallo di tempo personalizzato
5. Scegliere 'Salva' dalla barra degli strumenti per creare il parametro.

   ![Immagine che mostra la creazione di un parametro di intervallo di tempo](./media/workbooks-parameters/time-settings.png)

Questo è l'aspetto della cartella di lavoro in modalità di lettura, nello stile "Pills".

   ![Immagine che mostra un parametro dell'intervallo di tempo in modalità di lettura](./media/workbooks-parameters/parameters-time.png)

## <a name="referencing-a-parameter"></a>Riferimento a un parametro
### <a name="via-bindings"></a>Tramite binding
1. Aggiungere un controllo query alla cartella di lavoro e selezionare una risorsa di Application Insights.Add a query control to the workbook and select an Application Insights resource.
2. Aprire l'elenco a discesa `Time Range` _Intervallo_ di tempo e selezionare l'opzione dalla sezione Parametri nella parte inferiore.
3. In questo modo il parametro dell'intervallo di tempo viene associato all'intervallo di tempo del grafico. L'ambito temporale della query di esempio è ora delle ultime 24 ore.
4. Eseguire una query per visualizzare i risultati

    ![Immagine che mostra un parametro dell'intervallo di tempo a cui viene fatto riferimento tramite associazioni](./media/workbooks-parameters/time-binding.png)

### <a name="in-kql"></a>In KQL
1. Aggiungere un controllo query alla cartella di lavoro e selezionare una risorsa di Application Insights.Add a query control to the workbook and select an Application Insights resource.
2. Nel KQL, immettere un filtro ambito temporale utilizzando il parametro:`| where timestamp {TimeRange}`
3. Questo si espande sul `| where timestamp > ago(1d)`tempo di valutazione delle query a , che è il valore dell'intervallo di tempo del parametro.
4. Eseguire una query per visualizzare i risultati

    ![Immagine che mostra un intervallo di tempo a cui si fa riferimento in KQL](./media/workbooks-parameters/time-in-code.png)

### <a name="in-text"></a>Nel testo 
1. Aggiungere un controllo di testo alla cartella di lavoro.
2. Nel markdown, immettere`The chosen time range is {TimeRange:label}`
3. Scegliere _Fine modifica_
4. Il controllo di testo mostrerà il testo: _l'intervallo di tempo scelto è Ultime 24 ore_

## <a name="parameter-options"></a>Opzioni dei parametri:
La sezione _In_ `label` testo utilizza il parametro anziché il relativo valore. I parametri espongono varie opzioni di questo tipo a seconda del tipo: ad esempio, le selezioni dell'intervallo di tempo consentono valori, etichette, query, inizio, fine e granulosità.

Utilizzare `Previews` la sezione del riquadro _Modifica parametro_ per visualizzare le opzioni di espansione per il parametro:

![Immagine che mostra le opzioni dei parametri di un intervallo di tempo](./media/workbooks-parameters/time-previews.png)

## <a name="next-steps"></a>Passaggi successivi

* [Iniziare a](workbooks-visualizations.md) conoscere le cartelle di lavoro su molte opzioni di visualizzazione avanzate.
* [Controllare](workbooks-access-control.md) e condividere l'accesso alle risorse della cartella di lavoro.
