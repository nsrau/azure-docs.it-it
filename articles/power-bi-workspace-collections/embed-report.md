---
title: Incorporare un report nelle raccolte di aree di lavoro di Power BI | Microsoft Docs
description: Informazioni su come incorporare nell'applicazione un report che si trova nelle raccolte di aree di lavoro di Power BI.
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
ms.openlocfilehash: 56e7ca90132527c0ef9d4bd478e99b75ca055272
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="embed-a-report-in-power-bi-workspace-collections"></a>Incorporare un report nelle raccolte di aree di lavoro di Power BI

Informazioni su come incorporare nell'applicazione un report che si trova nelle raccolte di aree di lavoro di Power BI.

> [!IMPORTANT]
> Le raccolte di aree di lavoro di Power BI sono deprecate e sono disponibili fino a giugno 2018 o fino alla data specificata nel contratto. È consigliabile pianificare la migrazione a Power BI Embedded per evitare interruzioni nell'applicazione. Per informazioni su come eseguire la migrazione dei dati a Power BI Embedded, vedere [Come eseguire la migrazione del contenuto delle raccolte di aree di lavoro di Power BI a Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Verrà esaminato come incorporare effettivamente un report nell'applicazione. Ciò presuppone che esista già un report in un'area di lavoro della raccolta di aree di lavoro. Se questo passaggio non è ancora stato completato, vedere [Introduzione alle raccolte di aree di lavoro di Power BI](get-started.md).

Per compilare facilmente l'applicazione con le raccolte di aree di lavoro di Power BI, è possibile usare l'SDK per .NET (C#) o Node.js, insieme a JavaScript.

## <a name="using-the-access-keys-to-use-rest-apis"></a>Uso delle chiavi di accesso per API REST

Per chiamare l'API REST, è possibile passare la chiave di accesso che può essere ottenuta dal portale di Azure per una determinata raccolta di aree di lavoro. Per altre informazioni, vedere [Introduzione alle raccolte di aree di lavoro di Power BI](get-started.md).

## <a name="get-a-report-id"></a>Ottenere un ID report

Ogni token di accesso è basato su un report. È necessario ottenere lo specifico ID del report che si vuole incorporare. A questo scopo è possibile effettuare chiamate all'API REST [Get Reports](https://msdn.microsoft.com/library/azure/mt711510.aspx), che restituirà l'ID report e l'URL di incorporamento. È possibile usare Power BI .NET SDK oppure chiamare direttamente l'API REST.

### <a name="using-the-power-bi-net-sdk"></a>Uso di Power BI .NET SDK

Quando si usa .NET SDK, è necessario creare una credenziale token basata sulla chiave di accesso ottenuta dal portale di Azure. Ciò richiede l'installazione del [pacchetto NuGet Power BI API](https://www.nuget.org/profiles/powerbi).

**Installazione del pacchetto NuGet**

```
Install-Package Microsoft.PowerBI.Api
```

**Codice C#**

```
using Microsoft.PowerBI.Api.V1;
using Microsoft.Rest;

var credentials = new TokenCredentials("{access key}", "AppKey");
var client = new PowerBIClient(credentials);
client.BaseUri = new Uri(https://api.powerbi.com);

var reports = (IList<Report>)client.Reports.GetReports(workspaceCollectionName, workspaceId).Value;

// Select the report that you want to work with from the collection of reports.
```

### <a name="calling-the-rest-api-directly"></a>Chiamata diretta dell'API REST

```
System.Net.WebRequest request = System.Net.WebRequest.Create("https://api.powerbi.com/v1.0/collections/{collectionName}/workspaces/{workspaceId}/Reports") as System.Net.HttpWebRequest;

request.Method = "GET";
request.ContentLength = 0;
request.Headers.Add("Authorization", String.Format("AppKey {0}", accessToken.Value));

using (var response = request.GetResponse() as System.Net.HttpWebResponse)
{
    using (var reader = new System.IO.StreamReader(response.GetResponseStream()))
    {
        // Work with JSON response to get the report you want to work with.
    }

}
```

## <a name="create-an-access-token"></a>Creare un token di accesso

Le raccolte di aree di lavoro di Power BI usano token incorporati, ovvero token Web JSON con firma HMAC. I token sono firmati con la chiave di accesso della raccolta di aree di lavoro di Power BI. Per impostazione predefinita, i token di incorporamento vengono usati per concedere l'accesso di sola lettura a un report da incorporare in un'applicazione. I token di incorporamento vengono rilasciati per un report specifico e dovranno essere associati a un URL di incorporamento.

I token di accesso devono essere creati nel server perché le chiavi di accesso vengono usate per firmare/crittografare i token. Per informazioni sulla creazione di un token di accesso, vedere [Autenticazione e autorizzazione con le raccolte di aree di lavoro di Power BI](app-token-flow.md). È anche possibile esaminare il metodo [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_). Di seguito è riportato un esempio corrispondente all'uso di .NET SDK per Power BI.

Usare l'ID report recuperato in precedenza. Dopo avere creato il token di incorporamento, si userà la chiave di accesso per generare il token da usare per JavaScript. La *classe PowerBIToken* richiede l'installazione del [pacchetto NuGet Power BI Core](https://www.nuget.org/packages/Microsoft.PowerBI.Core/).

**Installazione del pacchetto NuGet**

```
Install-Package Microsoft.PowerBI.Core
```

**Codice C#**

```
using Microsoft.PowerBI.Security;

// rlsUsername, roles and scopes are optional.
embedToken = PowerBIToken.CreateReportEmbedToken(workspaceCollectionName, workspaceId, reportId, rlsUsername, roles, scopes);

var token = embedToken.Generate("{access key}");
```

### <a name="adding-permission-scopes-to-embed-tokens"></a>Aggiunta degli ambiti di autorizzazione ai token di incorporamento

Quando si usano token di incorporamento, può essere opportuno limitare l'utilizzo delle risorse a cui si concede l'accesso. Per questo motivo, è possibile generare un token con autorizzazioni con ambito. Per altre informazioni, vedere [Scopes](app-token-flow.md#scopes) (Ambiti).

## <a name="embed-using-javascript"></a>Incorporare con JavaScript

Dopo aver ottenuto il token di accesso e l'ID report, è possibile incorporare il report con JavaScript. Ciò richiede l'installazione del [pacchetto Power BI JavaScript](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/) disponibile in NuGet. L'URL di incorporamento sarà https://embedded.powerbi.com/appTokenReportEmbed.

> [!NOTE]
> Per testare il funzionamento, è possibile usare l'[esempio dell'incorporamento di report con JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/), che offre anche esempi di codice per le diverse operazioni disponibili.

**Installazione del pacchetto NuGet**

```
Install-Package Microsoft.PowerBI.JavaScript
```

**Codice JavaScript**

```
<script src="/scripts/powerbi.js"></script>
<div id="reportContainer"></div>

var embedConfiguration = {
    type: 'report',
    accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
    id: '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
    embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed'
};

var $reportContainer = $('#reportContainer');
var report = powerbi.embed($reportContainer.get(0), embedConfiguration);
```

### <a name="set-the-size-of-embedded-elements"></a>Impostare le dimensioni degli elementi incorporati

Il report verrà automaticamente incorporato in base alle dimensioni del contenitore. Per eseguire l'override delle dimensioni predefinite dell'elemento incorporato, è sufficiente aggiungere stili in linea o un attributo di classe CSS per larghezza e altezza.

## <a name="see-also"></a>Vedere anche 

[Esempio introduttivo](get-started-sample.md)  
[Autenticazione e autorizzazione con le raccolte di aree di lavoro di Power BI](app-token-flow.md)  
[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_)  
[Esempio di incorporamento con JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[Pacchetto Power BI JavaScript](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/)  
[Pacchetto NuGet Power BI API](https://www.nuget.org/profiles/powerbi)
[Pacchetto NuGet Power BI Core](https://www.nuget.org/packages/Microsoft.PowerBI.Core/)  
[Repository Git PowerBI-CSharp](https://github.com/Microsoft/PowerBI-CSharp)  
[Repository Git PowerBI-Node](https://github.com/Microsoft/PowerBI-Node)  

Altre domande? [Contattare la community di Power BI](http://community.powerbi.com/)
