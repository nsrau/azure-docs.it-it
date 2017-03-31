---
title: Autenticazione e autorizzazione con Power BI Embedded
description: Autenticazione e autorizzazione con Power BI Embedded
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.assetid: 1c1369ea-7dfd-4b6e-978b-8f78908fd6f6
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 03/11/2017
ms.author: asaxton
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 93027f0f5467e0b21c47bbcbc84c67cdd50b26b8
ms.lasthandoff: 03/14/2017


---
# <a name="authenticating-and-authorizing-with-power-bi-embedded"></a>Autenticazione e autorizzazione con Power BI Embedded

Il servizio Power BI Embedded usa le **chiavi** e i **token dell'app** per l'autenticazione e l'autorizzazione anziché usare l'autenticazione esplicita dell'utente finale. In questo modello è l'applicazione a gestire l'autenticazione e l'autorizzazione degli utenti finali. Se necessario, l'app crea e invia i token dell'app al servizio Microsoft per richiedere il rendering del report richiesto. Questo modello non richiede che l'applicazione usi Azure Active Directory per l'autenticazione e l'autorizzazione dell'utente, sebbene sia possibile.

## <a name="two-ways-to-authenticate"></a>Due modalità di autenticazione

**Chiave** : è possibile usare le chiavi per tutte le chiamate API REST di Power BI Embedded. Per trovare le chiavi nel **portale di Azure**, fare clic su **Tutte le impostazioni** e quindi su **Chiavi di accesso**. Occorre gestire sempre la chiave come se fosse una password. Queste chiavi sono autorizzate a effettuare qualsiasi chiamata API REST su una raccolta specifica di aree di lavoro.

Per usare una chiave su una chiamata REST, aggiungere l'intestazione di autorizzazione seguente:            

    Authorization: AppKey {your key}

**Token dell'app** : i token dell'app vengono usati per tutte le richieste di incorporamento. Sono stati progettati per essere eseguiti sul lato client, quindi sono limitati a un singolo report ed è consigliabile impostare una scadenza.

I token dell'app sono token JSON Web (JWT, JSON Web Token) firmati da una delle chiavi dell'utente.

Il token dell'app può contenere le attestazioni seguenti:

| Attestazione | Descrizione |
| --- | --- |
| **ver** |Versione del token dell'app. La versione corrente è&0;.2.0. |
| **aud** |Destinatario previsto per il token. Per Power BI Embedded usare: "https://analysis.windows.net/powerbi/api". |
| **iss** |Stringa che indica l'applicazione che ha emesso il token. |
| **type** |Tipo di token dell'app da creare. L'unico tipo attualmente supportato è **embed**. |
| **wcn** |Nome della raccolta di aree di lavoro per cui viene emesso il token. |
| **wid** |ID dell'area di lavoro per cui viene emesso il token. |
| **rid** |ID del report per cui viene emesso il token. |
| **username** (facoltativa) |Usata con la sicurezza a livello di riga, questa stringa può semplificare l'identificazione dell'utente quando si applicano le regole di sicurezza a livello di riga. |
| **roles** (facoltativa) |Stringa contenente i ruoli da selezionare quando si applicano le regole di sicurezza a livello di riga. Se si passa più di un ruolo, è consigliabile passarli come matrice di stringhe. |
| **scp** (facoltativo) |Stringa contenente gli ambiti delle autorizzazioni. Se si passa più di un ruolo, è consigliabile passarli come matrice di stringhe. |
| **exp** (facoltativa) |Indica l'ora di scadenza del token. Questi valori devono essere passati come timestamp Unix. |
| **nbf** (facoltativa) |Indica l'ora di inizio della validità del token. Questi valori devono essere passati come timestamp Unix. |

Un token dell'app di esempio avrà un aspetto analogo al seguente:

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ2ZXIiOiIwLjIuMCIsInR5cGUiOiJlbWJlZCIsIndjbiI6Ikd1eUluQUN1YmUiLCJ3aWQiOiJkNGZlMWViMS0yNzEwLTRhNDctODQ3Yy0xNzZhOTU0NWRhZDgiLCJyaWQiOiIyNWMwZDQwYi1kZTY1LTQxZDItOTMyYy0wZjE2ODc2ZTNiOWQiLCJzY3AiOiJSZXBvcnQuUmVhZCIsImlzcyI6IlBvd2VyQklTREsiLCJhdWQiOiJodHRwczovL2FuYWx5c2lzLndpbmRvd3MubmV0L3Bvd2VyYmkvYXBpIiwiZXhwIjoxNDg4NTAyNDM2LCJuYmYiOjE0ODg0OTg4MzZ9.v1znUaXMrD1AdMz6YjywhJQGY7MWjdCR3SmUSwWwIiI
```

Quando viene decodificato, avrà un aspetto analogo al seguente:

```
Header

{
    typ: "JWT",
    alg: "HS256:
}

Body

{
  "ver": "0.2.0",
  "wcn": "SupportDemo",
  "wid": "ca675b19-6c3c-4003-8808-1c7ddc6bd809",
  "rid": "96241f0f-abae-4ea9-a065-93b428eddb17",
  "iss": "PowerBISDK",
  "aud": "https://analysis.windows.net/powerbi/api",
  "exp": 1360047056,
  "nbf": 1360043456
}

```

All'interno di SDK sono disponibili metodi che semplificano la creazione di apptokens. Ad esempio, per .NET è possibile esaminare la classe [Microsoft.PowerBI.Security.PowerBIToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken) e i metodi [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_).

Per l'SDK di .NET, è possibile fare riferimento ad [Ambiti](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.scopes).

## <a name="scopes"></a>Ambiti

Quando si usano token di incorporamento, può essere opportuno limitare l'uso delle risorse a cui si concede l'accesso. Per questo motivo, è possibile generare un token con autorizzazioni con ambito.

Di seguito sono riportati gli ambiti disponibili per Power BI Embedded.

|Scope|Descrizione|
|---|---|
|Dataset.Read|Concede l'autorizzazione per leggere il set di dati specificato.|
|Dataset.Write|Concede l'autorizzazione per scrivere il set di dati specificato.|
|Dataset.ReadWrite|Concede l'autorizzazione per leggere e scrivere il set di dati specificato.|
|Report.Read|Concede l'autorizzazione per visualizzare il report specificato.|
|Report.ReadWrite|Concede l'autorizzazione per visualizzare e modificare il report specificato.|
|Workspace.Report.Create|Concede l'autorizzazione per creare un nuovo report nell'area di lavoro specificata.|
|Workspace.Report.Copy|Concede l'autorizzazione per clonare un report esistente nell'area di lavoro specificata.|

È possibile specificare più ambiti mediante uno spazio tra gli ambiti come nel caso seguente.

```
string scopes = "Dataset.Read Workspace.Report.Create";
```

**Attestazioni e ambiti necessari**

scp: {scopesClaim} scopesClaim può essere una stringa o una matrice di stringhe, che annota le autorizzazioni concesse nelle risorse dell'area di lavoro (Report, Dataset, e così via)

Un token decodificato, con ambiti definiti, sarebbe simile al seguente.

```
Header

{
    typ: "JWT",
    alg: "HS256:
}

Body

{
  "ver": "0.2.0",
  "wcn": "SupportDemo",
  "wid": "ca675b19-6c3c-4003-8808-1c7ddc6bd809",
  "rid": "96241f0f-abae-4ea9-a065-93b428eddb17",
  "scp": "Report.Read",
  "iss": "PowerBISDK",
  "aud": "https://analysis.windows.net/powerbi/api",
  "exp": 1360047056,
  "nbf": 1360043456
}

```

### <a name="operations-and-scopes"></a>Operazioni e ambiti

|Operazione|Risorsa di destinazione|Autorizzazioni del token|
|---|---|---|
|Creare (in memoria) un nuovo report basato su un set di dati.|Set di dati|Dataset.Read|
|Creare (in memoria) un nuovo report basato su un set di dati e salvarlo.|Set di dati|*Dataset.Read<br>* Workspace.Report.Create|
|Visualizzare ed esplorare/modificare (in memoria) un report esistente. Report.Read implica Dataset.Read. In questo modo le autorizzazioni non potranno salvare le modifiche.|Report|Report.Read|
|Modificare e salvare un report esistente.|Report|Report.ReadWrite|
|Salvare una copia di un report (Salva con nome).|Report|*Report.Read<br>* Workspace.Report.Copy|

## <a name="heres-how-the-flow-works"></a>Funzionamento del flusso
1. È necessario copiare le chiavi API nell'applicazione. È possibile ottenere le chiavi nel **portale di Azure**.
   
    ![](media/powerbi-embedded-get-started-sample/azure-portal.png)
2. Il token esegue l'asserzione di un'attestazione e ha una scadenza.
   
    ![](media/powerbi-embedded-get-started-sample/power-bi-embedded-token-2.png)
3. Con le chiavi di accesso API si accede al token.
   
    ![](media/powerbi-embedded-get-started-sample/power-bi-embedded-token-3.png)
4. L'utente richiede di visualizzare un report.
   
    ![](media/powerbi-embedded-get-started-sample/power-bi-embedded-token-4.png)
5. Il token viene convalidato con chiavi di accesso API.
   
   ![](media/powerbi-embedded-get-started-sample/power-bi-embedded-token-5.png)
6. Power BI Embedded invia un report all'utente.
   
   ![](media/powerbi-embedded-get-started-sample/power-bi-embedded-token-6.png)

Dopo che **Power BI Embedded** invia un report all'utente, l'utente può visualizzare il report nella propria app. Ad esempio, se è stato importato l'esempio [Analyzing Sales Data PBIX](http://download.microsoft.com/download/1/4/E/14EDED28-6C58-4055-A65C-23B4DA81C4DE/Analyzing_Sales_Data.pbix), l'app Web di esempio sarà simile alla seguente:

![](media/powerbi-embedded-get-started-sample/sample-web-app.png)

## <a name="see-also"></a>Vedere anche

[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_)  
[Introduzione all'esempio di Microsoft Power BI Embedded](power-bi-embedded-get-started-sample.md)  
[Scenari comuni di Microsoft Power BI Embedded](power-bi-embedded-scenarios.md)  
[Introduzione a Microsoft Power BI Embedded](power-bi-embedded-get-started.md)  
[Repository Git PowerBI-CSharp](https://github.com/Microsoft/PowerBI-CSharp)  
Altre domande? [Contattare la community di Power BI](http://community.powerbi.com/)


