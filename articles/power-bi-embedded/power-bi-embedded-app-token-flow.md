---
title: Autenticazione e autorizzazione con Power BI Embedded
description: Autenticazione e autorizzazione con Power BI Embedded
services: power-bi-embedded
documentationcenter: ''
author: guyinacube
manager: erikre
editor: ''
tags: ''

ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 10/04/2016
ms.author: asaxton

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
| **ver** |Versione del token dell'app. La versione corrente è 0.2.0. |
| **aud** |Destinatario previsto per il token. Per Power BI Embedded usare: "https://analysis.windows.net/powerbi/api". |
| **iss** |Stringa che indica l'applicazione che ha emesso il token. |
| **type** |Tipo di token dell'app da creare. L'unico tipo attualmente supportato è **embed**. |
| **wcn** |Nome della raccolta di aree di lavoro per cui viene emesso il token. |
| **wid** |ID dell'area di lavoro per cui viene emesso il token. |
| **rid** |ID del report per cui viene emesso il token. |
| **username** (facoltativa) |Usata con la sicurezza a livello di riga, questa stringa può semplificare l'identificazione dell'utente quando si applicano le regole di sicurezza a livello di riga. |
| **roles** (facoltativa) |Stringa contenente i ruoli da selezionare quando si applicano le regole di sicurezza a livello di riga. Se si passa più di un ruolo, è consigliabile passarli come matrice di stringhe. |
| **exp** (facoltativa) |Indica l'ora di scadenza del token. Questi valori devono essere passati come timestamp Unix. |
| **nbf** (facoltativa) |Indica l'ora di inizio della validità del token. Questi valori devono essere passati come timestamp Unix. |

Un token dell'app di esempio avrà un aspetto analogo al seguente:

![](media\\power-bi-embedded-app-token-flow\\power-bi-embedded-app-token-flow-sample-coded.png)

Quando viene decodificato, avrà un aspetto analogo al seguente:

![](media\\power-bi-embedded-app-token-flow\\power-bi-embedded-app-token-flow-sample-decoded.png)

## <a name="here's-how-the-flow-works"></a>Funzionamento del flusso
1. È necessario copiare le chiavi API nell'applicazione. È possibile ottenere le chiavi nel **portale di Azure**.
   
    ![](media\\powerbi-embedded-get-started-sample\\azure-portal.png)
2. Il token esegue l'asserzione di un'attestazione e ha una scadenza.
   
    ![](media\\powerbi-embedded-get-started-sample\\power-bi-embedded-token-2.png)
3. Con le chiavi di accesso API si accede al token.
   
    ![](media\\powerbi-embedded-get-started-sample\\power-bi-embedded-token-3.png)
4. L'utente richiede di visualizzare un report.
   
    ![](media\\powerbi-embedded-get-started-sample\\power-bi-embedded-token-4.png)
5. Il token viene convalidato con chiavi di accesso API.
   
   ![](media\\powerbi-embedded-get-started-sample\\power-bi-embedded-token-5.png)
6. Power BI Embedded invia un report all'utente.
   
   ![](media\\powerbi-embedded-get-started-sample\\power-bi-embedded-token-6.png)

Dopo che **Power BI Embedded** invia un report all'utente, l'utente può visualizzare il report nella propria app. Ad esempio, se è stato importato l'esempio [Analyzing Sales Data PBIX](http://download.microsoft.com/download/1/4/E/14EDED28-6C58-4055-A65C-23B4DA81C4DE/Analyzing_Sales_Data.pbix), l'app Web di esempio sarà simile alla seguente:

![](media\\powerbi-embedded-get-started-sample\\sample-web-app.png)

## <a name="see-also"></a>Vedere anche
* [Introduzione all'esempio di Microsoft Power BI Embedded](power-bi-embedded-get-started-sample.md)
* [Scenari comuni di Microsoft Power BI Embedded](power-bi-embedded-scenarios.md)
* [Introduzione a Microsoft Power BI Embedded](power-bi-embedded-get-started.md)

<!--HONumber=Oct16_HO2-->


