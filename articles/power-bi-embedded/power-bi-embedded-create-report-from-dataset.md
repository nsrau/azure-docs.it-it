---
title: Creare un nuovo report da un set di dati in Azure Power BI Embedded | Microsoft Docs
description: "È ora possibile creare report di Power BI Embedded da un set di dati nell'applicazione."
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
ms.translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 457f53aa76059dbb2faed6b264102f1f59b9918a
ms.contentlocale: it-it
ms.lasthandoff: 03/14/2017

---
# <a name="create-a-new-report-from-a-dataset-in-power-bi-embedded"></a>Creare un nuovo report da un set di dati in Power BI Embedded

È ora possibile creare report di Power BI Embedded da un set di dati nell'applicazione. 

Il metodo di autenticazione è simile a quello usato per l'incorporamento di report ed è basato su token di accesso specifici di un set di dati. I token usati per PowerBI.com vengono rilasciati da Azure Active Directory (AAD) e quelli per Power BI Embedded vengono rilasciati dal servizio dell'utente.

Quando si crea un report incorporato, i token rilasciati riguardano un set di dati specifico. I token dovranno essere associati all'URL di incorporamento per lo stesso elemento affinché ognuno abbia un token univoco. Per creare un report incorporato, nel token di accesso devono essere specificati gli ambiti *Dataset.Read e Workspace.Report.Create*.

## <a name="create-access-token-needed-to-create-new-report"></a>Creare il token di accesso necessario per creare un nuovo report

Power BI Embedded usa token di incorporamento, che sono token JSON Web con firma HMAC. I token sono firmati con la chiave di accesso della raccolta di aree di lavoro di Azure Power BI Embedded. Per impostazione predefinita, i token di incorporamento vengono usati per concedere l'accesso di sola lettura a un report da incorporare in un'applicazione. I token di incorporamento vengono rilasciati per un report specifico e dovranno essere associati a un URL di incorporamento.

I token di accesso devono essere creati nel server perché le chiavi di accesso vengono usate per firmare/crittografare i token. Per informazioni sulla creazione di un token di accesso, vedere [Autenticazione e autorizzazione con Power BI Embedded](power-bi-embedded-app-token-flow.md). È anche possibile esaminare il metodo [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_). Di seguito è riportato un esempio corrispondente all'uso di .NET SDK per Power BI.

In questo esempio si ha l'ID set di dati su cui si vuole creare il nuovo report. È anche necessario aggiungere gli ambiti per *Dataset.Read e Workspace.Report.Create*.

La *classe PowerBIToken* richiede l'installazione del [pacchetto NuGet Power BI Core](https://www.nuget.org/packages/Microsoft.PowerBI.Core/).

**Installazione del pacchetto NuGet**

```
Install-Package Microsoft.PowerBI.Core
```

**Codice C#**

```
using Microsoft.PowerBI.Security;

// rlsUsername and roles are optional
string scopes = "Dataset.Read Workspace.Report.Create";
PowerBIToken embedToken = PowerBIToken.CreateReportEmbedTokenForCreation(workspaceCollectionName, workspaceId, datasetId, null, null, scopes);

var token = embedToken.Generate("{access key}");
```

## <a name="create-a-new-blank-report"></a>Creare un nuovo report vuoto

Per creare un nuovo report, deve essere specificata la configurazione di creazione. Questa dovrà includere il token di accesso, l'URL di incorporamento e l'ID set di dati per cui si vuole creare il report. Ciò richiede l'installazione del [pacchetto Power BI JavaScript](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/). L'URL di incorporamento sarà https://embedded.powerbi.com/appTokenReportEmbed.

> [!NOTE]
> Per testare il funzionamento, è possibile usare l'[esempio dell'incorporamento di report con JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/), che offre anche esempi di codice per le diverse operazioni disponibili.

**Installazione del pacchetto NuGet**

```
Install-Package Microsoft.PowerBI.JavaScript
```

**Codice JavaScript**

```
<div id="reportContainer"></div>
  
var embedCreateConfiguration = {
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        datasetId: '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
    };
    
    // Grab the reference to the div HTML element that will host the report
    var reportContainer = $('#reportContainer')[0];

    // Create report
    var report = powerbi.createReport(reportContainer, embedCreateConfiguration);
```

Chiamando *powerbi.createReport()* verrà visualizzata un'area di disegno vuota in modalità di modifica nell'elemento *div*.

![](media/power-bi-embedded-create-report-from-dataset/pbi-embedded-create-new-report.png)

## <a name="save-new-reports"></a>Salvare i nuovi report

Il report non verrà effettivamente creato finché non si chiama l'operazione **Salva con nome**. A tale scopo è possibile usare il menu File o JavaScript.

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
> Un nuovo report viene creato solo dopo che è stata chiamata l'operazione **Salva con nome**. Dopo il salvataggio, l'area di disegno continuerà a visualizzare il set di dati in modalità di modifica e non il report. Sarà necessario ricaricare il nuovo report nello stesso modo in cui si ricarica qualsiasi altro report.

![](media/power-bi-embedded-create-report-from-dataset/pbi-embedded-save-new-report.png)

## <a name="load-the-new-report"></a>Caricare il nuovo report

Per interagire con il nuovo report è necessario incorporarlo così come l'applicazione incorpora un report regolare, ovvero è necessario rilasciare un nuovo token specifico per il nuovo report e quindi chiamare il metodo di incorporamento.

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

## <a name="automate-save-and-load-of-a-new-report-using-the-saved-event"></a>Automatizzare il salvataggio e il caricamento di un nuovo report con l'evento "saved"

Per automatizzare il processo di salvataggio con nome e successivo caricamento del nuovo report, è possibile usare l'evento "saved". Questo evento viene generato al termine dell'operazione di salvataggio e restituisce un oggetto JSON che contiene il nuovo ID report, il nome del report e l'ID report precedente (se esistente) e specifica se si è trattato di un'operazione di salvataggio o di salvataggio con nome.

```
{
  "reportObjectId": "5dac7a4a-4452-46b3-99f6-a25915e0fe54",
  "reportName": "newReport",
  "saveAs": true,
  "originalReportObjectId": null
}
```

Per automatizzare il processo è possibile restare in ascolto dell'evento "saved", usare il nuovo ID report per creare il nuovo token e incorporare il nuovo report con tale token.

```
<div id="reportContainer"></div>
  
var embedCreateConfiguration = {
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        datasetId: '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
    };
    
    // Grab the reference to the div HTML element that will host the report
    var reportContainer = $('#reportContainer')[0];

    // Create report
    var report = powerbi.createReport(reportContainer, embedCreateConfiguration);


   var saveAsParameters = {
        name: "newReport"
    };

    // SaveAs report
    report.saveAs(saveAsParameters);

    // report.on will add an event handler which prints to Log window.
    report.on("saved", function(event) {
        
         // get new Token
         var newReportId =  event.detail.reportObjectId;

        // create new Token. This is a function that the application should provide
        var newToken = createAccessToken(newReportId,scopes /*provide the wanted scopes*/);
        
        
    var embedConfiguration = {
        accessToken: newToken ,
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        reportId: newReportId,
    };

    // Embed report
    var report = powerbi.embed(reportContainer, embedConfiguration);
       
   // report.off removes a given event handler if it exists.
   report.off("saved");
    });
```

## <a name="see-also"></a>Vedere anche

[Esempio introduttivo](power-bi-embedded-get-started-sample.md)  
[Salvare i report](power-bi-embedded-save-reports.md)  
[Incorporare un report](power-bi-embedded-embed-report.md)  
[Autenticazione e autorizzazione con Power BI Embedded](power-bi-embedded-app-token-flow.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[Esempio di incorporamento con JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[Pacchetto NuGet Power BI Core](https://www.nuget.org/packages/Microsoft.PowerBI.Core/)  
[Pacchetto Power BI JavaScript](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/)  
Altre domande? [Contattare la community di Power BI](http://community.powerbi.com/)
