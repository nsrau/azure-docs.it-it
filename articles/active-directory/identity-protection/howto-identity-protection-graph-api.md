---
title: API Microsoft Graph per Azure Active Directory Identity Protection
description: Informazioni su come eseguire query sui rilevamenti dei rischi Microsoft Graph e sulle informazioni associate da Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/29/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f5e5a4075705e43dc0ac37181bf33b078013177
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85555231"
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Introduzione ad Azure Active Directory Identity Protection e a Microsoft Graph

Microsoft Graph è l'endpoint API unificato di Microsoft e la posizione in cui risiedono le API di [Azure Active Directory Identity Protection](../active-directory-identityprotection.md) . Sono disponibili quattro API che espongono informazioni sugli utenti e gli accessi a rischio. La prima API, **riskDetection**, consente di eseguire query Microsoft Graph per un elenco di rilevamenti dei rischi collegati tra utenti e accessi e informazioni associate sul rilevamento. La seconda API, **riskyUsers**, consente di cercare in Microsoft Graph informazioni sugli utenti rilevati come a rischio da Identity Protection. La terza API, **signIn**, consente di cercare in Microsoft Graph informazioni sugli accessi ad Azure AD con specifiche proprietà relative a stato, dettaglio e livello del rischio. La quarta API, **identityRiskEvents**, consente di eseguire query Microsoft Graph per un elenco di [rilevamenti dei rischi](../reports-monitoring/concept-risk-events.md) e informazioni associate. L'API identityRiskEvents sarà deprecata il 10 gennaio 2020; si consiglia di usare invece l'API **riskDetections** . Questo articolo permette di iniziare a connettersi a Microsoft Graph e a eseguire query su queste API. Per una presentazione più approfondita, la documentazione completa e l'accesso a Graph Explorer, vedere il [sito di Microsoft Graph](https://graph.microsoft.io/) o la documentazione di riferimento specifica su queste API:

* [API riskDetection](/graph/api/resources/riskdetection?view=graph-rest-v1.0)
* [riskyUsers API](/graph/api/resources/riskyuser?view=graph-rest-v1.0) (API riskyUsers)
* [signIn API](/graph/api/resources/signin?view=graph-rest-v1.0) (API signIn)

## <a name="connect-to-microsoft-graph"></a>Connettersi a Microsoft Graph

La procedura per accedere ai dati di Identity Protection tramite Microsoft Graph si articola in quattro passaggi:

- [Recuperare il nome di dominio](#retrieve-your-domain-name)
- [Crea una nuova registrazione dell'app](#create-a-new-app-registration)
- [Configurare le autorizzazioni API](#configure-api-permissions)
- [Configurare credenziali valide](#configure-a-valid-credential)

### <a name="retrieve-your-domain-name"></a>Recuperare il nome di dominio 

1. Accedere al [portale di Azure](https://portal.azure.com).  
1. Passare a **Azure Active Directory**  >  **nomi di dominio personalizzati**. 
1. Prendere nota del `.onmicrosoft.com` dominio, che sarà necessario in un passaggio successivo.

### <a name="create-a-new-app-registration"></a>Creare una nuova registrazione di app

1. Nella portale di Azure individuare **Azure Active Directory**  >  **registrazioni app**.
1. Selezionare **Nuova registrazione**.
1. Nella pagina **Crea** seguire questa procedura:
   1. Nella casella di testo **Name (nome** ) digitare un nome per l'applicazione (ad esempio: Azure ad API di rilevamento del rischio).
   1. In **tipi di account supportati**selezionare il tipo di account che utilizzeranno le API.
   1. Selezionare **Registra**.
1. Copiare l' **ID applicazione**.

### <a name="configure-api-permissions"></a>Configurare le autorizzazioni API

1. Dall' **applicazione** creata selezionare **autorizzazioni API**.
1. Nella pagina **autorizzazioni configurate** , nella barra degli strumenti in alto, fare clic su **Aggiungi un'autorizzazione**.
1. Nella pagina **Aggiungi accesso all'API** fare clic su **Selezionare un'API**.
1. Nella pagina **Selezionare un'API** selezionare **Microsoft Graph** e quindi fare clic su **Seleziona**.
1. Nella pagina **autorizzazioni API richiesta** : 
   1. Selezionare **Autorizzazioni applicazione**.
   1. Selezionare le caselle di controllo accanto a `IdentityRiskEvent.Read.All` e `IdentityRiskyUser.Read.All` .
   1. Selezionare **Aggiungi autorizzazioni**.
1. Selezionare **concedi il consenso dell'amministratore per il dominio** 

### <a name="configure-a-valid-credential"></a>Configurare credenziali valide

1. Dall' **applicazione** creata selezionare **certificati & segreti**.
1. In **segreti client**selezionare **nuovo segreto client**.
   1. Fornire al client una **Descrizione** e impostare il periodo di tempo di scadenza in base ai criteri dell'organizzazione.
   1. Selezionare **Aggiungi**.

   > [!NOTE]
   > Se si perde la chiave, si dovrà tornare a questa sezione e crearne una nuova. Mantenere la chiave segreta, perché chiunque ne fosse a conoscenza potrà accedere ai dati.

## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-detections-api"></a>Eseguire l'autenticazione per Microsoft Graph ed eseguire query sull'API rilevamento dei rischi di identità

A questo punto saranno disponibili:

- Il nome di dominio del tenant
- ID applicazione (client) 
- Il segreto client o il certificato 

Per autenticarsi, inviare una richiesta POST a `https://login.microsoft.com` includendo nel corpo i parametri seguenti:

- grant_type: "**client_credentials**"
- risorsa: `https://graph.microsoft.com`
- client_id: \<your client ID\>
- client_secret: \<your key\>

Se ha esito positivo, questa richiesta restituisce un token di autenticazione.  
Per chiamare l'API, creare un'intestazione con il parametro seguente:

```
`Authorization`="<token_type> <access_token>"
```

Quando si esegue l'autenticazione, è possibile trovare il tipo di token e il token di accesso nel token restituito.

Inviare questa intestazione come richiesta all'URL dell'API seguente: `https://graph.microsoft.com/v1.0/identityProtection/riskDetections`

La risposta, in caso di esito positivo, è una raccolta di rilevamenti dei rischi di identità e di dati associati nel formato JSON OData, che può essere analizzato e gestito in base alle esigenze.

### <a name="sample"></a>Esempio

Di seguito è riportato il codice di esempio per autenticarsi e chiamare l'API con PowerShell.  
Aggiungere semplicemente l'ID client, la chiave privata e il dominio del tenant.

```PowerShell
    $ClientID       = "<your client ID here>"        # Should be a ~36 hex character string; insert your info here
    $ClientSecret   = "<your client secret here>"    # Should be a ~44 character string; insert your info here
    $tenantdomain   = "<your tenant domain here>"    # For example, contoso.onmicrosoft.com

    $loginURL       = "https://login.microsoft.com"
    $resource       = "https://graph.microsoft.com"

    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    Write-Output $oauth

    if ($oauth.access_token -ne $null) {
        $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

        $url = "https://graph.microsoft.com/v1.0/identityProtection/riskDetections"
        Write-Output $url

        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)

        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output $event
        }

    } else {
        Write-Host "ERROR: No Access Token"
    } 
```

## <a name="query-the-apis"></a>Eseguire query sulle API

Queste tre API offrono diverse opportunità di recuperare informazioni sugli utenti e sugli accessi a rischio nell'organizzazione. Ecco alcuni casi d'uso comuni per queste API e le richieste di esempio associate. È possibile eseguire queste query usando il codice di esempio precedente o [Graph explorer](https://developer.microsoft.com/graph/graph-explorer).

### <a name="get-all-of-the-offline-risk-detections-riskdetection-api"></a>Ottenere tutti i rilevamenti dei rischi offline (API riskDetection)

Con i criteri di rischio di accesso Identity Protection è possibile applicare condizioni quando il rischio viene rilevato in tempo reale. Ma cosa accade per i rilevamenti individuati offline? Per comprendere quali sono i rilevamenti non in linea e quindi non hanno attivato i criteri di rischio di accesso, è possibile eseguire una query sull'API riskDetection.

```
GET https://graph.microsoft.com/v1.0/identityProtection/riskDetections?$filter=detectionTimingType eq 'offline'
```

### <a name="get-all-of-the-users-who-successfully-passed-an-mfa-challenge-triggered-by-risky-sign-ins-policy-riskyusers-api"></a>Ottenere tutti gli utenti che hanno soddisfatto una richiesta MFA attivata dai criteri di accesso a rischio (API riskyUsers)

Per comprendere i criteri basati sul rischio di protezione delle identità nell'organizzazione, è possibile eseguire una query su tutti gli utenti che hanno superato correttamente una richiesta di autenticazione a più fattori attivata da un criterio di accesso rischioso. Queste informazioni possono essere utili per sapere quali utenti potrebbero essere stati erroneamente rilevati come a rischio da Identity Protection e quali utenti legittimi potrebbero eseguire azioni ritenute a rischio dall'intelligenza artificiale.

```
GET https://graph.microsoft.com/v1.0/identityProtection/riskyUsers?$filter=riskDetail eq 'userPassedMFADrivenByRiskBasedPolicy'
```

## <a name="next-steps"></a>Passaggi successivi

È appena stata creata la prima chiamata a Microsoft Graph.  
A questo punto è possibile eseguire una query sui rilevamenti dei rischi di identità e usare i dati comunque.

Per altre informazioni su Microsoft Graph e su come creare applicazioni usando l'API Graph, vedere la [documentazione](/graph/overview) e molto altro nel [sito di Microsoft Graph](https://developer.microsoft.com/graph). 

Per informazioni correlate, vedere:

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
- [Tipi di rilevamento dei rischi rilevati da Azure Active Directory Identity Protection](../reports-monitoring/concept-risk-events.md)
- [Microsoft Graph](https://developer.microsoft.com/graph/)
- [Overview of Microsoft Graph (Panoramica di Microsoft Graph)](https://developer.microsoft.com/graph/docs)
- [Azure AD Identity Protection Service Root (Radice del servizio Azure AD Identity Protection)](https://docs.microsoft.com/graph/api/resources/identityprotectionroot?view=graph-rest-1.0)
