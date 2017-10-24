---
title: "Passare dalla modalità di visualizzazione alla modalità di modifica dei report e viceversa nelle raccolte di aree di lavoro di Power BI | Microsoft Docs"
description: "Informazioni su come passare dalla modalità di visualizzazione alla modalità di modifica dei report e viceversa nelle raccolte di aree di lavoro di Power BI."
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ROBOTS: NOINDEX
ms.assetid: 
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: asaxton
ms.openlocfilehash: e66778697f3f4f2f065d2757b3b60ac2699c0334
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="toggle-between-view-and-edit-mode-for-reports-in-power-bi-workspace-collections"></a>Passare dalla modalità di visualizzazione alla modalità di modifica dei report e viceversa nelle raccolte di aree di lavoro di Power BI

Informazioni su come passare dalla modalità di visualizzazione alla modalità di modifica dei report e viceversa nelle raccolte di aree di lavoro di Power BI.

> [!IMPORTANT]
> Le raccolte di aree di lavoro di Power BI sono deprecate e sono disponibili fino a giugno 2018 o fino alla data specificata nel contratto. È consigliabile pianificare la migrazione a Power BI Embedded per evitare interruzioni nell'applicazione. Per informazioni su come eseguire la migrazione dei dati a Power BI Embedded, vedere [Come eseguire la migrazione del contenuto delle raccolte di aree di lavoro di Power BI a Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

## <a name="creating-an-access-token"></a>Creazione di un token di accesso

È necessario creare un token di accesso che consenta sia di visualizzare che di modificare un report. Per modificare e salvare un report, è necessaria l'autorizzazione del token **Report.ReadWrite**. Per altre informazioni, vedere [Autenticazione e autorizzazione nelle raccolte di aree di lavoro di Power BI](app-token-flow.md).

> [!NOTE]
> In questo modo sarà possibile modificare un report esistente e salvare le modifiche. Se si vuole supportare anche la funzione **Salva con nome**, è necessario specificare autorizzazioni aggiuntive. Per altre informazioni, vedere [Scopes](app-token-flow.md#scopes) (Ambiti).

```
using Microsoft.PowerBI.Security;

// rlsUsername and roles are optional
string scopes = "Report.ReadWrite";
PowerBIToken embedToken = PowerBIToken.CreateReportEmbedTokenForCreation(workspaceCollectionName, workspaceId, datasetId, null, null, scopes);

var token = embedToken.Generate("{access key}");
```

## <a name="embed-configuration"></a>Configurazione di incorporamento

Per visualizzare il pulsante Salva in modalità di modifica, sarà necessario specificare le autorizzazioni e una modalità di visualizzazione. Per altre informazioni, vedere [Embed configuration details](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details) (Dettagli della configurazione di incorporamento).

In JavaScript, ad esempio:

```
   <div id="reportContainer"></div>

    // Get models. Models, it contains enums that can be used.
    var models = window['powerbi-client'].models;

    // Embed configuration used to describe the what and how to embed.
    // This object is used when calling powerbi.embed.
    // This also includes settings and options such as filters.
    // You can find more information at https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details.
    var config= {
        type: 'report',
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        id:  '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
        permissions: models.Permissions.ReadWrite /*both save & save as buttons will be visible*/,
        viewMode: models.ViewMode.View,
        settings: {
            filterPaneEnabled: true,
            navContentPaneEnabled: true
        }
    };

    // Get a reference to the embedded report HTML element
    var reportContainer = $('#reportContainer')[0];

    // Embed the report and display it within the div container.
    var report = powerbi.embed(reportContainer, config);
```

In questo modo viene indicato di incorporare il report in modalità di visualizzazione in base all'impostazione di **viewMode** su **models.ViewMode.View**.

## <a name="view-mode"></a>Modalità di visualizzazione

Se si è in modalità di modifica, è possibile usare il codice JavaScript seguente per passare alla modalità di visualizzazione.

```
// Get a reference to the embedded report HTML element
var reportContainer = $('#reportContainer')[0];

// Get a reference to the embedded report.
report = powerbi.get(reportContainer);

// Switch to view mode.
report.switchMode("view");

```

## <a name="edit-mode"></a>Modalità di modifica

Se si è in modalità di visualizzazione, è possibile usare il codice JavaScript seguente per passare alla modalità di modifica.

```
// Get a reference to the embedded report HTML element
var reportContainer = $('#reportContainer')[0];

// Get a reference to the embedded report.
report = powerbi.get(reportContainer);

// Switch to edit mode.
report.switchMode("edit");

```

## <a name="see-also"></a>Vedere anche

[Esempio introduttivo](get-started-sample.md)  
[Incorporare un report](embed-report.md)  
[Autenticazione e autorizzazione con le raccolte di aree di lavoro di Power BI](app-token-flow.md)  
[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_)  
[Esempio di incorporamento con JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[Repository Git PowerBI-CSharp](https://github.com/Microsoft/PowerBI-CSharp)  
[Repository Git PowerBI-Node](https://github.com/Microsoft/PowerBI-Node)  

Altre domande? [Contattare la community di Power BI](http://community.powerbi.com/)
