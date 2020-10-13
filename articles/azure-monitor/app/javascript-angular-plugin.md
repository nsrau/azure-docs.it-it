---
title: Plug-in angolare per Application Insights JavaScript SDK
description: Come installare e usare il plug-in angolare per Application Insights JavaScript SDK.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: lagayhar
ms.openlocfilehash: 152ba4b1c8a4e09db0bce759f5b67f577ec5d584
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91843893"
---
# <a name="angular-plugin-for-application-insights-javascript-sdk"></a>Plug-in angolare per Application Insights JavaScript SDK

Il plug-in angolare per il Application Insights JavaScript SDK, Abilita:

- Rilevamento delle modifiche del router
- Statistiche sull'utilizzo dei componenti angolari

> [!WARNING]
> Il plug-in angolare non è compatibile con ECMAScript 3 (ES3).

## <a name="getting-started"></a>Guida introduttiva

Installare il pacchetto NPM:

```bash
npm install @microsoft/applicationinsights-angularplugin-js
```

## <a name="basic-usage"></a>Uso di base

Configurare un'istanza di Application Insights nel componente entry nell'app:

```js
import { Component, OnInit } from '@angular/core';
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { AngularPlugin, AngularPluginService } from '@microsoft/applicationinsights-angularplugin-js';
import { Router } from '@angular/router';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent implements OnInit {
    private appInsights;
    constructor(
        private router: Router,
        private angularPluginService: AngularPluginService 
    ){
        var angularPlugin = new AngularPlugin();
        this.angularPluginService.init(angularPlugin, this.router);
        this.appInsights = new ApplicationInsights({ config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [angularPlugin],
        extensionConfig: {
            [angularPlugin.identifier]: { router: this.router }
        }
        } });
    }

    ngOnInit() {
        this.appInsights.loadAppInsights();
    }
}

```

Per usare il `trackMetric` metodo per tenere traccia dell'utilizzo di componenti angolari, aggiungere `AngularPluginService` come provider nell'elenco dei provider in `app.module.ts` file.

```js
import { AngularPluginService } from '@microsoft/applicationinsights-angularplugin-js';

@NgModule({
    ...
  providers: [ AngularPluginService ],
})
export class AppModule { }
```

Per tenere traccia della durata di un componente, chiamare `trackMetric` nel `ngOnDestroy` metodo di tale componente. Quando il componente viene eliminato definitivamente, viene attivato un `trackMetric` evento che invia l'ora in cui l'utente è rimasto nella pagina e il nome del componente.

```js
import { Component, OnDestroy, HostListener } from '@angular/core';
import { AngularPluginService } from '@microsoft/applicationinsights-angularplugin-js';

@Component({
  selector: 'app-test',
  templateUrl: './test.component.html',
  styleUrls: ['./test.component.css']
})
export class TestComponent implements OnDestroy {

  constructor(private angularPluginService: AngularPluginService) {}

  @HostListener('window:beforeunload')
  ngOnDestroy() {
    this.angularPluginService.trackMetric();
  }
}
```

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni su JavaScript SDK, vedere la [documentazione di Application Insights JavaScript SDK](javascript.md) .
- [Plug-in angolare su GitHub](https://github.com/microsoft/ApplicationInsights-JS/tree/master/extensions/applicationinsights-angularplugin-js)