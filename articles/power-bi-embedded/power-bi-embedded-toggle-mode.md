---
title: "Passare dalla modalità di visualizzazione alla modalità di modifica dei report e viceversa in Azure Power BI Embedded | Microsoft Docs"
description: "Informazioni su come passare dalla modalità di visualizzazione alla modalità di modifica dei report e viceversa in Power BI Embedded."
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 03/11/2017
ms.author: asaxton
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: f73bf05b41523a5833cc9366fb84cb7021b4b7a9
ms.lasthandoff: 03/14/2017

---
# <a name="toggle-between-view-and-edit-mode-for-reports-in-power-bi-embedded"></a>Passare dalla modalità di visualizzazione alla modalità di modifica dei report e viceversa in Power BI Embedded

Informazioni su come passare dalla modalità di visualizzazione alla modalità di modifica dei report e viceversa in Power BI Embedded.

## <a name="creating-an-access-token"></a>Creazione di un token di accesso

È necessario creare un token di accesso che consenta sia di visualizzare che di modificare un report. Per modificare e salvare un report, è necessaria l'autorizzazione del token **Report.ReadWrite**. Per altre informazioni, vedere [Autenticazione e autorizzazione con Power BI Embedded](power-bi-embedded-app-token-flow.md).

> [!NOTE]
> In questo modo sarà possibile modificare un report esistente e salvare le modifiche. Se si vuole supportare anche la funzione **Salva con nome**, è necessario specificare autorizzazioni aggiuntive. Per altre informazioni, vedere [Scopes](power-bi-embedded-app-token-flow.md#scopes) (Ambiti).

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

In questo modo verrà indicato di incorporare il report in modalità di visualizzazione in base all'impostazione di **viewMode** su **models.ViewMode.View**.

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

[Esempio introduttivo](power-bi-embedded-get-started-sample.md)  
[Incorporare un report](power-bi-embedded-embed-report.md)  
[Autenticazione e autorizzazione con Power BI Embedded](power-bi-embedded-app-token-flow.md)  
[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_)  
[Esempio di incorporamento con JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[Repository Git PowerBI-CSharp](https://github.com/Microsoft/PowerBI-CSharp)  
[Repository Git PowerBI-Node](https://github.com/Microsoft/PowerBI-Node)  
Altre domande? [Contattare la community di Power BI](http://community.powerbi.com/)

