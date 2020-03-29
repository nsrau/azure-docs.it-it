---
title: API di Microsoft Graph per Azure Active Directory Identity Protection
description: Informazioni su come eseguire query sui rilevamenti dei rischi di Microsoft Graph e sulle informazioni associate da Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: reference
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: f6fd62ccab4d6e32b23835d280732797e133ada8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671628"
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Introduzione ad Azure Active Directory Identity Protection e a Microsoft Graph

Microsoft Graph è l'endpoint API unificato di Microsoft e la posizione in cui risiedono le API di [Azure Active Directory Identity Protection](../active-directory-identityprotection.md) . Sono disponibili quattro API che espongono informazioni sugli utenti e gli degli invii rischiosi. La prima API, **riskDetection**, consente di eseguire una query su Microsoft Graph per un elenco dei rilevamenti dei rischi collegati sia dell'utente che dell'accesso e delle informazioni associate sul rilevamento. La seconda API, **riskyUsers**, consente di cercare in Microsoft Graph informazioni sugli utenti rilevati come a rischio da Identity Protection. La terza API, **signIn**, consente di cercare in Microsoft Graph informazioni sugli accessi ad Azure AD con specifiche proprietà relative a stato, dettaglio e livello del rischio. La quarta API, **identityRiskEvents**, consente di eseguire una query su Microsoft Graph per un elenco dei [rilevamenti](../reports-monitoring/concept-risk-events.md) dei rischi e delle informazioni associate. L'API identityRiskEvents sarà deprecata il 10 gennaio 2020; ti consigliamo di usare invece l'API **riskDetections.** Questo articolo permette di iniziare a connettersi a Microsoft Graph e a eseguire query su queste API. Per una presentazione più approfondita, la documentazione completa e l'accesso a Graph Explorer, vedere il [sito di Microsoft Graph](https://graph.microsoft.io/) o la documentazione di riferimento specifica su queste API:

* [API riskDetection](/graph/api/resources/riskdetection?view=graph-rest-beta)
* [riskyUsers API](/graph/api/resources/riskyuser?view=graph-rest-beta) (API riskyUsers)
* [signIn API](/graph/api/resources/signin?view=graph-rest-beta) (API signIn)
* [API identityRiskEvents](/graph/api/resources/identityriskevent?view=graph-rest-beta) *Sarà deprecata il 10 gennaio 2020*

## <a name="connect-to-microsoft-graph"></a>Connettersi a Microsoft Graph

La procedura per accedere ai dati di Identity Protection tramite Microsoft Graph si articola in quattro passaggi:

1. Recuperare il nome di dominio.
2. Creare una nuova registrazione di app. 
3. Usare questo segreto e qualche altra informazione per eseguire l'autenticazione in Microsoft Graph, dove si riceverà un token di autenticazione. 
4. Usare questo token per inviare richieste all'endpoint API e ottenere dati di Identity Protection.

Ecco gli elementi che occorre avere prima di iniziare:

* Privilegi di amministratore per creare l'applicazione in Azure AD
* Nome di dominio del tenant, ad esempio contoso.onmicrosoft.com

## <a name="retrieve-your-domain-name"></a>Recuperare il nome di dominio 

1. [Accedere](https://portal.azure.com) al portale di Azure come amministratore. 
1. Nel riquadro di spostamento a sinistra fare clic su **Active Directory**. 

   ![Creazione di un'applicazione](./media/howto-identity-protection-graph-api/41.png)

1. Nella sezione **Gestisci** fare clic su **Proprietà**.

   ![Creazione di un'applicazione](./media/howto-identity-protection-graph-api/42.png)

1. Copiare il nome di dominio.

## <a name="create-a-new-app-registration"></a>Creare una nuova registrazione di app

1. Nella sezione **Gestisci** della pagina **Active Directory** fare clic su **Registrazioni per l'app**.

   ![Creazione di un'applicazione](./media/howto-identity-protection-graph-api/42.png)

1. Nel menu in alto fare clic su **Registrazione nuova applicazione**.

   ![Creazione di un'applicazione](./media/howto-identity-protection-graph-api/43.png)

1. Nella pagina **Crea** seguire questa procedura:

   ![Creazione di un'applicazione](./media/howto-identity-protection-graph-api/44.png)

   1. Nella casella di testo **Nome** digitare un nome per l'applicazione, ad esempio Applicazione API per il rilevamento dei rischi di Azure AD.

   1. Come **Tipo** selezionare **Applicazione Web e/o API Web**.

   1. Nella casella di testo URL `http://localhost`di **accesso** digitare .

   1. Fare clic su **Crea**.
1. Per aprire la pagina **Impostazioni**, fare clic sulla registrazione di app appena creata nell'elenco delle applicazioni. 
1. Copiare **ID applicazione**.

## <a name="grant-your-application-permission-to-use-the-api"></a>Concedere all'applicazione le autorizzazioni per l'uso dell'API

1. Nella pagina **Impostazioni** fare clic su **Autorizzazioni necessarie**.

   ![Creazione di un'applicazione](./media/howto-identity-protection-graph-api/15.png)

1. Nella pagina **Autorizzazioni necessarie** fare clic su **Aggiungi** sulla barra degli strumenti in alto.

   ![Creazione di un'applicazione](./media/howto-identity-protection-graph-api/16.png)

1. Nella pagina **Aggiungi accesso all'API** fare clic su **Selezionare un'API**.

   ![Creazione di un'applicazione](./media/howto-identity-protection-graph-api/17.png)

1. Nella pagina **Selezionare un'API** selezionare **Microsoft Graph** e quindi fare clic su **Seleziona**.

   ![Creazione di un'applicazione](./media/howto-identity-protection-graph-api/18.png)

1. Nella pagina **Aggiungi accesso all'API** fare clic su **Selezionare le autorizzazioni**.

   ![Creazione di un'applicazione](./media/howto-identity-protection-graph-api/19.png)

1. Nella pagina **Abilita accesso** fare clic su **Legge tutte le informazioni sugli eventi di rischio dell'identità** e quindi su **Seleziona**.

   ![Creazione di un'applicazione](./media/howto-identity-protection-graph-api/20.png)

1. Nella pagina **Aggiungi accesso all'API** fare clic su **Fatto**.

   ![Creazione di un'applicazione](./media/howto-identity-protection-graph-api/21.png)

1. Nella pagina **Autorizzazioni necessarie** fare clic su **Concedi autorizzazioni** e quindi su **Sì**.

   ![Creazione di un'applicazione](./media/howto-identity-protection-graph-api/22.png)

## <a name="get-an-access-key"></a>Ottenere una chiave di accesso

1. Nella pagina **Impostazioni** fare clic su **Chiavi**.

   ![Creazione di un'applicazione](./media/howto-identity-protection-graph-api/23.png)

1. Nel pagina **Chiavi** seguire questa procedura:

   ![Creazione di un'applicazione](./media/howto-identity-protection-graph-api/24.png)

   1. Nella casella di testo **Descrizione chiave** digitare una descrizione, ad esempio Rilevamento rischi di *Azure AD.*
   1. Come **Durata** selezionare **Tra 1 anno**.
   1. Fare clic su **Salva**.
   1. Copiare il valore della chiave e quindi incollarlo in una posizione sicura.   
   
   > [!NOTE]
   > Se si perde la chiave, si dovrà tornare a questa sezione e crearne una nuova. Mantenere la chiave segreta, perché chiunque ne fosse a conoscenza potrà accedere ai dati.
   > 

## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-detections-api"></a>Eseguire l'autenticazione a Microsoft Graph ed eseguire query sull'API Rilevamenti rischi di identitàAuthenticate to Microsoft Graph and query the Identity Risk Detections API

A questo punto saranno disponibili:

- Il nome di dominio del tenant
- L'ID client 
- La chiave 

Per autenticarsi, inviare una richiesta POST a `https://login.microsoft.com` includendo nel corpo i parametri seguenti:

- grant_type: "**client_credentials**"
- risorsa: `https://graph.microsoft.com`
- client_id: \<your client ID\>
- client_secret: \<your key\>

Se la richiesta riesce, verrà restituito un token di autenticazione.  
Per chiamare l'API, creare un'intestazione con il parametro seguente:

```
`Authorization`="<token_type> <access_token>"
```

Quando si esegue l'autenticazione, è possibile trovare il tipo di token e il token di accesso nel token restituito.

Inviare questa intestazione come richiesta all'URL dell'API seguente: `https://graph.microsoft.com/beta/identityRiskEvents`

La risposta, in caso di esito positivo, è una raccolta di rilevamenti dei rischi di identità e dati associati nel formato JSON OData, che possono essere analizzati e gestiti nel modo desiderato.

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

        $url = "https://graph.microsoft.com/beta/identityRiskEvents"
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

Con i criteri di accesso di Identity Protection, è possibile applicare le condizioni quando il rischio viene rilevato in tempo reale. Ma per quanto riguarda i rilevamenti rilevati offline? Per comprendere quali rilevamenti si sono verificati offline e pertanto non avrebbe attivato i criteri di rischio di accesso, è possibile eseguire una query sull'API riskDetection.

```
GET https://graph.microsoft.com/beta/riskDetections?$filter=detectionTimingType eq 'offline'
```

### <a name="get-all-of-the-users-who-successfully-passed-an-mfa-challenge-triggered-by-risky-sign-ins-policy-riskyusers-api"></a>Ottenere tutti gli utenti che hanno soddisfatto una richiesta MFA attivata dai criteri di accesso a rischio (API riskyUsers)

Per comprendere l'impatto dei criteri basati sul rischio di Identity Protection sull'organizzazione, è possibile eseguire una query su tutti gli utenti che hanno superato correttamente una richiesta di autenticazione a più fattori attivata da un criterio di accesso rischioso. Queste informazioni possono essere utili per sapere quali utenti potrebbero essere stati erroneamente rilevati come a rischio da Identity Protection e quali utenti legittimi potrebbero eseguire azioni ritenute a rischio dall'intelligenza artificiale.

```
GET https://graph.microsoft.com/beta/riskyUsers?$filter=riskDetail eq 'userPassedMFADrivenByRiskBasedPolicy'
```

### <a name="get-all-the-risky-sign-ins-for-a-specific-user-signin-api"></a>Ottenere tutti gli accessi a rischio per un utente specifico (API signIn)

Quando si ritiene che un utente possa essere stato compromesso, è possibile ottenere altre informazioni sullo stato del rischio recuperando tutti gli accessi a rischio. 

```
https://graph.microsoft.com/beta/identityRiskEvents?`$filter=userID eq '<userID>' and riskState eq 'atRisk'
```
## <a name="next-steps"></a>Passaggi successivi

È appena stata creata la prima chiamata a Microsoft Graph.  
A questo punto è possibile eseguire query sui rilevamenti dei rischi di identità e utilizzare i dati nel modo desiderato.

Per altre informazioni su Microsoft Graph e su come creare applicazioni usando l'API Graph, vedere la [documentazione](/graph/overview) e molto altro nel [sito di Microsoft Graph](https://developer.microsoft.com/graph). 

Per informazioni correlate, vedere:

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
- [Tipi di rilevamenti dei rischi rilevati da Azure Active Directory Identity Protection](../reports-monitoring/concept-risk-events.md)
- [Microsoft Graph](https://developer.microsoft.com/graph/)
- [Overview of Microsoft Graph (Panoramica di Microsoft Graph)](https://developer.microsoft.com/graph/docs)
- [Azure AD Identity Protection Service Root (Radice del servizio Azure AD Identity Protection)](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityprotection_root)
