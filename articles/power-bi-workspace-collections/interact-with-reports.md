---
title: Interagire con i report usando l'API JavaScript | Microsoft Docs
description: L'API JavaScript per Power BI consente di incorporare facilmente i report di Power BI nelle applicazioni.
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ROBOTS: NOINDEX
ms.assetid: bdd885d3-1b00-4dcf-bdff-531eb1f97bfb
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: asaxton
ms.openlocfilehash: 62a95807c35fcba15a8e5ffdf340a307dd22a642
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="interact-with-power-bi-reports-using-the-javascript-api"></a>Interagire con i report di Power BI usando l'API JavaScript

L'API JavaScript per Power BI consente di incorporare facilmente i report di Power BI nelle applicazioni. Con l'API, le applicazioni possono interagire a livello di codice con elementi diversi dei report, ad esempio pagine e filtri. Grazie a questa interattività i report di Power BI si integrano ancora meglio nell'applicazione.

> [!IMPORTANT]
> Le raccolte di aree di lavoro di Power BI sono deprecate e sono disponibili fino a giugno 2018 o fino alla data specificata nel contratto. È consigliabile pianificare la migrazione a Power BI Embedded per evitare interruzioni nell'applicazione. Per informazioni su come eseguire la migrazione dei dati a Power BI Embedded, vedere [Come eseguire la migrazione del contenuto della raccolta di aree di lavoro di Power BI a Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Per incorporare un report di Power BI nell'applicazione, usare un iframe ospitato nell'ambito dell'applicazione. L'iframe viene usato come limite tra l'applicazione e il report, come si può osservare nell'immagine seguente:

![iframe della raccolta di aree di lavoro di Power BI senza API JavaScript](media/interact-with-reports/iframe-without-javacript.png)

L'iframe facilita considerevolmente il processo di incorporamento, ma senza l'API JavaScript il report e l'applicazione non possono interagire. Questa mancanza di interazione può far credere che il report non faccia effettivamente parte dell'applicazione. Il report e l'applicazione devono effettivamente comunicare in entrambe le direzioni, come nell'immagine seguente:

![iframe della raccolta di aree di lavoro di Power BI con API JavaScript](media/interact-with-reports/iframe-with-javascript.png)

L'API JavaScript per Power BI consente di scrivere codice che può passare in modo sicuro attraverso il limite dell'iframe. Ciò consente all'applicazione di eseguire a livello di codice un'azione in un report e di essere in ascolto degli eventi generati dalle azioni eseguite dagli utenti nel report.

## <a name="what-can-you-do-with-the-power-bi-javascript-api"></a>Che cosa si può fare con l'API JavaScript per Power BI?

Con l'API JavaScript è possibile gestire i report, accedere alle pagine di un report, filtrare un report e gestire l'incorporamento degli eventi. Il diagramma seguente mostra la struttura dell'API.

![Diagramma dell'API JavaScript per Power BI](media/interact-with-reports/javascript-api-diagram.png)

### <a name="manage-reports"></a>Gestire i report
L'API Javascript consente di gestire il comportamento a livello di pagina e di report:

* Incorporare un report di Power BI specifico in modo sicuro nell'applicazione: provare l' [applicazione demo di incorporamento](http://azure-samples.github.io/powerbi-angular-client/#/scenario1)
  * Impostare il token di accesso
* Configurare il report
  * Abilitare e disabilitare il riquadro del filtro e il riquadro di spostamento della pagina: provare l' [applicazione demo di aggiornamento delle impostazioni](http://azure-samples.github.io/powerbi-angular-client/#/scenario6)
  * Impostare i valori predefiniti per le pagine e i filtri: provare la [demo di impostazione dei valori predefiniti](http://azure-samples.github.io/powerbi-angular-client/#/scenario5)
* Entrare e uscire dalla modalità schermo intero

[Altre informazioni sull'incorporamento di un report](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embedding-Basics)

### <a name="navigate-to-pages-in-a-report"></a>Passare alle pagine in un report
L'API JavaScript consente di trovare tutte le pagine di un report e di impostare la pagina corrente. Provare l' [applicazione demo di spostamento](http://azure-samples.github.io/powerbi-angular-client/#/scenario3).

[Altre informazioni sullo spostamento tra le pagine](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Page-Navigation)

### <a name="filter-a-report"></a>Filtrare un report
L'API JavaScript fornisce funzionalità di filtro di base e avanzate per le pagine di report e i report incorporati. Provare l' [applicazione demo di filtro](http://azure-samples.github.io/powerbi-angular-client/#/scenario4)ed esaminare un codice introduttivo qui.

#### <a name="basic-filters"></a>Filtri di base
Un filtro di base viene applicato a livello di colonna o di gerarchia e contiene un elenco di valori da includere o escludere.

```
const basicFilter: pbi.models.IBasicFilter = {
  $schema: "http://powerbi.com/product/schema#basic",
  target: {
    table: "Store",
    column: "Count"
  },
  operator: "In",
  values: [1,2,3,4]
}
```

#### <a name="advanced-filters"></a>Filtri avanzati
I filtri avanzati usano l'operatore logico AND o OR e accettano una o due condizioni, ognuna con il proprio operatore e valore. Le condizioni supportate sono:

* None
* LessThan
* LessThanOrEqual
* GreaterThan
* GreaterThanOrEqual
* Contiene
* DoesNotContain
* StartsWith
* DoesNotStartWith
* Is
* IsNot
* IsBlank
* IsNotBlank

```
const advancedFilter: pbi.models.IAdvancedFilter = {
  $schema: "http://powerbi.com/product/schema#advanced",
  target: {
    table: "Store",
    column: "Name"
  },
  logicalOperator: "Or",
  conditions: [
    {
      operator: "Contains",
      value: "Wash"
    },
    {
      operator: "Contains",
      value: "Park"
    }
  ]
}
```

[Altre informazioni sui filtri](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Filters)

### <a name="handling-events"></a>Gestione degli eventi

Oltre a inviare informazioni all'iframe, l'applicazione può anche ricevere informazioni sugli eventi seguenti provenienti dall'iframe:

* Embed
  * loaded
  * error
* Report
  * pageChanged
  * dataSelected (presto disponibile)

[Altre informazioni sulla gestione degli eventi](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Handling-Events)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'API JavaScript per Power BI, vedere i collegamenti seguenti:

* [JavaScript API Wiki (Wiki sull'API JavaScript)](https://github.com/Microsoft/PowerBI-JavaScript/wiki)
* [Object model reference (Informazioni di riferimento sul modello a oggetti)](https://microsoft.github.io/powerbi-models/modules/_models_.html)
* [Live demo (Demo live)](https://microsoft.github.io/PowerBI-JavaScript/demo/)