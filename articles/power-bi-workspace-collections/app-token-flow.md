---
title: Autenticazione e autorizzazione con le raccolte di aree di lavoro di Power BI| Microsoft Docs
description: Autenticazione e autorizzazione con le raccolte di aree di lavoro di Power BI.
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ROBOTS: NOINDEX
ms.assetid: 1c1369ea-7dfd-4b6e-978b-8f78908fd6f6
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: asaxton
ms.openlocfilehash: ae9627c6bb5e7bb099598acaa2eb29375c35593e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="authenticating-and-authorizing-with-power-bi-workspace-collections"></a>Autenticazione e autorizzazione con le raccolte di aree di lavoro di Power BI

Le raccolta di aree di lavoro di Power BI usano le **chiavi** e i **token dell'app** per l'autenticazione e l'autorizzazione, invece dell'autenticazione esplicita dell'utente finale. In questo modello è l'applicazione a gestire l'autenticazione e l'autorizzazione degli utenti finali. Se necessario, l'app crea e invia i token dell'app al servizio Microsoft per richiedere il rendering del report richiesto. Questo modello non richiede che l'applicazione usi Azure Active Directory per l'autenticazione e l'autorizzazione dell'utente, sebbene sia possibile.

> [!IMPORTANT]
> Le raccolte di aree di lavoro di Power BI sono deprecate e sono disponibili fino a giugno 2018 o fino alla data specificata nel contratto. È consigliabile pianificare la migrazione a Power BI Embedded per evitare interruzioni nell'applicazione. Per informazioni su come eseguire la migrazione dei dati a Power BI Embedded, vedere [Come eseguire la migrazione del contenuto delle raccolte di aree di lavoro di Power BI a Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

## <a name="two-ways-to-authenticate"></a>Due modalità di autenticazione

**Chiave** : è possibile usare le chiavi per tutte le chiamate dell'API REST delle raccolte di aree di lavoro di Power BI. Per trovare le chiavi nel **portale di Microsoft Azure**, selezionare **Tutte le impostazioni** e quindi **Chiavi di accesso**. Gestire sempre la chiave come se fosse una password. Queste chiavi sono autorizzate a effettuare qualsiasi chiamata API REST su una raccolta specifica di aree di lavoro.

Per usare una chiave su una chiamata REST, aggiungere l'intestazione di autorizzazione seguente:

    Authorization: AppKey {your key}

**Token dell'app** : i token dell'app vengono usati per tutte le richieste di incorporamento. Sono stati progettati per essere eseguiti sul lato client, quindi sono limitati a un singolo report ed è consigliabile impostare una scadenza.

I token dell'app sono token JSON Web (JWT, JSON Web Token) firmati da una delle chiavi dell'utente.

Il token dell'app può contenere le attestazioni seguenti:

| Attestazione | DESCRIZIONE |
| --- | --- |
| **ver** |Versione del token dell'app. La versione corrente è 0.2.0. |
| **aud** |Destinatario previsto per il token. Per le raccolte di aree di lavoro di Power BI usare: "https://analysis.windows.net/powerbi/api". |
| **iss** |Stringa che indica l'applicazione che ha rilasciato il token. |
| **type** |Tipo di token dell'app da creare. L'unico tipo attualmente supportato è **embed**. |
| **wcn** |Nome della raccolta di aree di lavoro per cui viene emesso il token. |
| **wid** |ID dell'area di lavoro per cui viene emesso il token. |
| **rid** |ID del report per cui viene emesso il token. |
| **username** (facoltativa) |Usato con la sicurezza a livello di riga, username è una stringa che può semplificare l'identificazione dell'utente quando si applicano le regole di sicurezza a livello di riga. |
| **roles** (facoltativa) |Stringa contenente i ruoli da selezionare quando si applicano le regole di sicurezza a livello di riga. Se si passa più di un ruolo, è consigliabile passarli come matrice di stringhe. |
| **scp** (facoltativo) |Stringa contenente gli ambiti delle autorizzazioni. Se si passa più di un ruolo, è consigliabile passarli come matrice di stringhe. |
| **exp** (facoltativa) |Indica l'ora di scadenza del token. Il valore deve essere passato come timestamp Unix. |
| **nbf** (facoltativa) |Indica l'ora di inizio della validità del token. Il valore deve essere passato come timestamp Unix. |

Un token di app di esempio ha questo aspetto:

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ2ZXIiOiIwLjIuMCIsInR5cGUiOiJlbWJlZCIsIndjbiI6Ikd1eUluQUN1YmUiLCJ3aWQiOiJkNGZlMWViMS0yNzEwLTRhNDctODQ3Yy0xNzZhOTU0NWRhZDgiLCJyaWQiOiIyNWMwZDQwYi1kZTY1LTQxZDItOTMyYy0wZjE2ODc2ZTNiOWQiLCJzY3AiOiJSZXBvcnQuUmVhZCIsImlzcyI6IlBvd2VyQklTREsiLCJhdWQiOiJodHRwczovL2FuYWx5c2lzLndpbmRvd3MubmV0L3Bvd2VyYmkvYXBpIiwiZXhwIjoxNDg4NTAyNDM2LCJuYmYiOjE0ODg0OTg4MzZ9.v1znUaXMrD1AdMz6YjywhJQGY7MWjdCR3SmUSwWwIiI
```

Dopo la decodifica avrà un aspetto simile al seguente:

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

All'interno degli SDK sono disponibili metodi che semplificano la creazione di token di app. Ad esempio, per .NET è possibile esaminare la classe [Microsoft.PowerBI.Security.PowerBIToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken) e i metodi [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_).

Per l'SDK di .NET, è possibile fare riferimento ad [Ambiti](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.scopes).

## <a name="scopes"></a>Ambiti

Quando si usano token di incorporamento, può essere opportuno limitare l'uso delle risorse a cui si concede l'accesso. Per questo motivo, è possibile generare un token con autorizzazioni con ambito.

Di seguito sono riportati gli ambiti disponibili per le raccolte di aree di lavoro di Power BI.

|Scope|DESCRIZIONE|
|---|---|
|Dataset.Read|Concede l'autorizzazione per leggere il set di dati specificato.|
|Dataset.Write|Concede l'autorizzazione per scrivere il set di dati specificato.|
|Dataset.ReadWrite|Concede l'autorizzazione per leggere e scrivere nel set di dati specificato.|
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

Un token decodificato, con ambiti definiti, sarebbe simile al seguente:

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
|Creare (in memoria) un nuovo report basato su un set di dati e salvarlo.|Set di dati|* Dataset.Read<br>* Workspace.Report.Create|
|Visualizzare ed esplorare/modificare (in memoria) un report esistente. Report.Read implica Dataset.Read. Report.Read non consente il salvataggio delle modifiche.|Report|Report.Read|
|Modificare e salvare un report esistente.|Report|Report.ReadWrite|
|Salvare una copia di un report (Salva con nome).|Report|* Report.Read<br>* Workspace.Report.Copy|

## <a name="heres-how-the-flow-works"></a>Funzionamento del flusso
1. È necessario copiare le chiavi API nell'applicazione. È possibile ottenere le chiavi nel **portale di Azure**.
   
    ![Dove trovare le chiavi API nel portale di Azure](media/get-started-sample/azure-portal.png)
1. Il token esegue l'asserzione di un'attestazione e ha una scadenza.
   
    ![Flusso di token di app - Il token esegue l'asserzione di un'attestazione](media/get-started-sample/token-2.png)
1. Con le chiavi di accesso API si accede al token.
   
    ![Flusso di token di app - Il token viene firmato](media/get-started-sample/token-3.png)
1. L'utente richiede di visualizzare un report.
   
    ![Flusso di token di app - L'utente richiede di visualizzare un report](media/get-started-sample/token-4.png)
1. Il token viene convalidato con chiavi di accesso API.
   
   ![Flusso di token di app - Il token viene convalidato](media/get-started-sample/token-5.png)
1. Le raccolte di aree di lavoro di Power BI inviano un report all'utente.
   
   ![Flusso di token di app - Il servizio invia un report all'utente](media/get-started-sample/token-6.png)

Dopo l'invio di un report all'utente dalle **raccolte di aree di lavoro di Power BI**, l'utente può visualizzare il report nell'app personalizzata. Ad esempio, se è stato importato l'esempio [Analyzing Sales Data PBIX](http://download.microsoft.com/download/1/4/E/14EDED28-6C58-4055-A65C-23B4DA81C4DE/Analyzing_Sales_Data.pbix), l'app Web di esempio sarà simile alla seguente:

![Esempio di report incorporato in un'applicazione](media/get-started-sample/sample-web-app.png)

## <a name="see-also"></a>Vedere anche

[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_)  
[Esempio introduttivo delle raccolte di aree di lavoro di Power BI](get-started-sample.md)  
[Scenari comuni delle raccolte di aree di lavoro di Microsoft Power BI](scenarios.md)  
[Introduzione alle raccolte di aree di lavoro di Microsoft Power BI](get-started.md)  
[Repository Git PowerBI-CSharp](https://github.com/Microsoft/PowerBI-CSharp)

Altre domande? [Contattare la community di Power BI](http://community.powerbi.com/)