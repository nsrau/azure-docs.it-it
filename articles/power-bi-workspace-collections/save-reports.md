---
title: Salvare report nelle raccolte di aree di lavoro di Power BI | Microsoft Docs
description: Informazioni su come salvare i report nelle raccolte di aree di lavoro di Power BI. Per il corretto funzionamento sono necessarie le autorizzazioni appropriate.
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
ms.openlocfilehash: 94a72ba4478aa317b4a1930b2894c1346d0590c1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="save-reports-in-power-bi-workspace-collections"></a>Salvare report nelle raccolte di aree di lavoro di Power BI

Informazioni su come salvare i report nelle raccolte di aree di lavoro di Power BI. Per il corretto salvataggio di report sono necessarie le autorizzazioni appropriate.

> [!IMPORTANT]
> Le raccolte di aree di lavoro di Power BI sono deprecate e sono disponibili fino a giugno 2018 o fino alla data specificata nel contratto. È consigliabile pianificare la migrazione a Power BI Embedded per evitare interruzioni nell'applicazione. Per informazioni su come eseguire la migrazione dei dati a Power BI Embedded, vedere [Come eseguire la migrazione del contenuto delle raccolte di aree di lavoro di Power BI a Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Con le raccolte di aree di lavoro di Power BI è possibile modificare report esistenti e salvarli, nonché creare un nuovo report ed eseguirne il salvataggio con nome.

Per salvare un report, è prima di tutto necessario creare un token per il report specifico con gli ambiti corretti:

* Per abilitare il salvataggio, è necessario l'ambito Report.ReadWrite
* Per abilitare il salvataggio con nome, sono necessari gli ambiti Report.Read e Workspace.Report.Copy
* Per abilitare entrambi, sono necessari Report.ReadWrite e Workspace.Report.Copy

Per abilitare i pulsanti Salva/Salva con nome corretti nel menu File, è necessario specificare l'autorizzazione appropriata nella configurazione di incorporamento quando si incorpora il report, rispettivamente:

* models.Permissions.ReadWrite
* models.Permissions.Copy
* models.Permissions.All

> [!NOTE]
> Anche il token di accesso necessita degli ambiti appropriati. Per altre informazioni, vedere [Scopes](app-token-flow.md#scopes) (Ambiti).

## <a name="embed-report-in-edit-mode"></a>Incorporare un report in modalità di modifica

Se si vuole incorporare un report in modalità di modifica all'interno dell'app, è sufficiente passare le proprietà corrette nella configurazione di incorporamento e chiamare powerbi.embed(). Per visualizzare i pulsanti Salva e Salva con nome in modalità di modifica, specificare le autorizzazioni e una modalità di visualizzazione. Per altre informazioni, vedere [Embed configuration details](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details) (Dettagli della configurazione di incorporamento).

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
        permissions: models.Permissions.All /*both save & save as buttons will be visible*/,
        viewMode: models.ViewMode.Edit,
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

Un report viene così incorporato nell'app in modalità di modifica.

## <a name="save-report"></a>Salvare il report

Dopo aver incorporato il report in modalità di modifica con il token e le autorizzazioni appropriati, è possibile salvare il report dal menu File o da JavaScript:

```
 // Get a reference to the embedded report.
    report = powerbi.get(reportContainer);

 // Save report
    report.save();
```

## <a name="save-as"></a>Salva con nome

```
// Get a reference to the embedded report.
    report = powerbi.get(reportContainer);
    
    var saveAsParameters = {
        name: "newReport"
    };

    // SaveAs report
    report.saveAs(saveAsParameters);
```

> [!IMPORTANT]
> Un nuovo report viene creato solo dopo l'operazione *Salva con nome*. Dopo il salvataggio, l'area di disegno continuerà a visualizzare il report precedente in modalità di modifica e non il nuovo report. Incorporare il nuovo report creato. L'incorporamento di un nuovo report richiede un nuovo token di accesso, perché vengono creati per singolo report.

Dopo il *salvataggio con nome* sarà quindi necessario caricare il nuovo report. Il caricamento del nuovo report è simile all'incorporamento di qualsiasi report.

```
<div id="reportContainer"></div>
  
var embedConfiguration = {
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MJ',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        reportId: '5dac7a4a-4452-46b3-99f6-a25915e0fe54',
    };
    
    // Grab the reference to the div HTML element that will host the report
    var reportContainer = $('#reportContainer')[0];

    // Embed report
    var report = powerbi.embed(reportContainer, embedConfiguration);
```

## <a name="see-also"></a>Vedere anche 

[Esempio introduttivo](get-started-sample.md)  
[Incorporare un report](embed-report.md)  
[Creare un nuovo report da un set di dati](create-report-from-dataset.md)  
[Autenticazione e autorizzazione con le raccolte di aree di lavoro di Power BI](app-token-flow.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[Esempio di incorporamento con JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

Altre domande? [Contattare la community di Power BI](http://community.powerbi.com/)

