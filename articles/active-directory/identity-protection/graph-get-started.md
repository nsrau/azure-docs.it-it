---
title: Microsoft Graph per Azure Active Directory Identity Protection | Microsoft Docs
description: Informazioni su come eseguire query in Microsoft Graph per ottenere un elenco degli eventi di rischio e le informazioni associate da Azure Active Directory.
services: active-directory
keywords: azure active directory identity protection, evento di rischio, vulnerabilità, criteri di sicurezza, Microsoft Graph
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: fa109ba7-a914-437b-821d-2bd98e681386
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: markvi
ms.reviewer: nigu
ms.custom: seohack1
ms.openlocfilehash: 4ee2f0387ee3c56dda7cd1a89f5c808f55699856
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/02/2019
ms.locfileid: "55658624"
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Introduzione ad Azure Active Directory Identity Protection e a Microsoft Graph

Microsoft Graph è l'endpoint API unificato di Microsoft e la posizione in cui risiedono le API di [Azure Active Directory Identity Protection](../active-directory-identityprotection.md) . Esistono tre API che espongono informazioni sugli utenti e sugli accessi a rischio. La prima API, **identityRiskEvents**, consente di eseguire una query in Microsoft Graph per ottenere un elenco di [eventi di rischio](../reports-monitoring/concept-risk-events.md) e informazioni associate. La seconda API, **riskyUsers**, consente di cercare in Microsoft Graph informazioni sugli utenti rilevati come a rischio da Identity Protection. La terza API, **signIn**, consente di cercare in Microsoft Graph informazioni sugli accessi ad Azure AD con specifiche proprietà relative a stato, dettaglio e livello del rischio. Questo articolo permette di iniziare a [connettersi a Microsoft Graph](#Connect-to-Microsoft-Graph) e a [eseguire query su queste API](#Query-the-APIs). Per una presentazione più approfondita, la documentazione completa e l'accesso a Graph Explorer, vedere il [sito di Microsoft Graph](https://graph.microsoft.io/) o la documentazione di riferimento specifica su queste API:

* [identityRiskEvents API](https://developer.microsoft.com/en-us/graph/docs/api-reference/beta/resources/identityriskevent) (API identityRiskEvents)
* [riskyUsers API](https://developer.microsoft.com/en-us/graph/docs/api-reference/beta/resources/riskyuser) (API riskyUsers)
* [signIn API](https://developer.microsoft.com/en-us/graph/docs/api-reference/beta/resources/signin) (API signIn)


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

2. Nel riquadro di spostamento a sinistra fare clic su **Active Directory**. 
   
    ![Creazione di un'applicazione](./media/graph-get-started/41.png)


3. Nella sezione **Gestisci** fare clic su **Proprietà**.

    ![Creazione di un'applicazione](./media/graph-get-started/42.png)

4. Copiare il nome di dominio.


## <a name="create-a-new-app-registration"></a>Creare una nuova registrazione di app

1. Nella sezione **Gestisci** della pagina **Active Directory** fare clic su **Registrazioni per l'app**.

    ![Creazione di un'applicazione](./media/graph-get-started/42.png)


2. Nel menu in alto fare clic su **Registrazione nuova applicazione**.
   
    ![Creazione di un'applicazione](./media/graph-get-started/43.png)

3. Nella pagina **Crea** seguire questa procedura:
   
    ![Creazione di un'applicazione](./media/graph-get-started/44.png)

    a. Nella casella di testo **Nome** digitare un nome per l'applicazione, ad esempio Applicazione API evento di rischio AADIP.
   
    b. Come **Tipo** selezionare **Applicazione Web e/o API Web**.
   
    c. Nella casella di testo **URL di accesso** digitare `http://localhost`.

    d. Fare clic su **Create**(Crea).

4. Per aprire la pagina **Impostazioni**, fare clic sulla registrazione di app appena creata nell'elenco delle applicazioni. 

5. Copiare **ID applicazione**.


## <a name="grant-your-application-permission-to-use-the-api"></a>Concedere all'applicazione le autorizzazioni per l'uso dell'API

1. Nella pagina **Impostazioni** fare clic su **Autorizzazioni necessarie**.
   
    ![Creazione di un'applicazione](./media/graph-get-started/15.png)

2. Nella pagina **Autorizzazioni necessarie** fare clic su **Aggiungi** sulla barra degli strumenti in alto.
   
    ![Creazione di un'applicazione](./media/graph-get-started/16.png)
   
3. Nella pagina **Aggiungi accesso all'API** fare clic su **Selezionare un'API**.
   
    ![Creazione di un'applicazione](./media/graph-get-started/17.png)

4. Nella pagina **Selezionare un'API** selezionare **Microsoft Graph** e quindi fare clic su **Seleziona**.
   
    ![Creazione di un'applicazione](./media/graph-get-started/18.png)

5. Nella pagina **Aggiungi accesso all'API** fare clic su **Selezionare le autorizzazioni**.
   
    ![Creazione di un'applicazione](./media/graph-get-started/19.png)

6. Nella pagina **Abilita accesso** fare clic su **Legge tutte le informazioni sugli eventi di rischio dell'identità** e quindi su **Seleziona**.
   
    ![Creazione di un'applicazione](./media/graph-get-started/20.png)

7. Nella pagina **Aggiungi accesso all'API** fare clic su **Fatto**.
   
    ![Creazione di un'applicazione](./media/graph-get-started/21.png)

8. Nella pagina **Autorizzazioni necessarie** fare clic su **Concedi autorizzazioni** e quindi su **Sì**.
   
    ![Creazione di un'applicazione](./media/graph-get-started/22.png)



## <a name="get-an-access-key"></a>Ottenere una chiave di accesso

1. Nella pagina **Impostazioni** fare clic su **Chiavi**.
   
    ![Creazione di un'applicazione](./media/graph-get-started/23.png)

2. Nel pagina **Chiavi** seguire questa procedura:
   
    ![Creazione di un'applicazione](./media/graph-get-started/24.png)

    a. Nella casella di testo **Descrizione chiave** digitare una descrizione, ad esempio *AADIP Risk Event*.
    
    b. Come **Durata** selezionare **Tra 1 anno**.

    c. Fare clic su **Save**.
   
    d. Copiare il valore della chiave e quindi incollarlo in una posizione sicura.   
   
   > [!NOTE]
   > Se si perde la chiave, si dovrà tornare a questa sezione e crearne una nuova. Mantenere la chiave segreta, perché chiunque ne fosse a conoscenza potrà accedere ai dati.
   > 
   > 

## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-events-api"></a>Autenticarsi in Microsoft Graph ed eseguire query sull'API eventi di rischio per le identità
A questo punto saranno disponibili:

- Il nome di dominio del tenant

- L'ID client 

- La chiave 


Per autenticarsi, inviare una richiesta POST a `https://login.microsoft.com` includendo nel corpo i parametri seguenti:

- grant_type: "**client_credentials**"

-  resource: “**https://graph.microsoft.com**”

- client_id: \<your client ID\>

- client_secret: \<your key\>


Se la richiesta riesce, verrà restituito un token di autenticazione.  
Per chiamare l'API, creare un'intestazione con il parametro seguente:

    `Authorization`=”<token_type> <access_token>"


Quando si esegue l'autenticazione, è possibile trovare il tipo di token e il token di accesso nel token restituito.

Inviare questa intestazione come richiesta all'URL dell'API seguente: `https://graph.microsoft.com/beta/identityRiskEvents`

La risposta, se la richiesta riesce, è una raccolta di eventi di rischio per le identità e di dati associati nel formato JSON OData, che può essere analizzato e gestito secondo le esigenze.

Di seguito è riportato il codice di esempio per autenticarsi e chiamare l'API con PowerShell.  
Aggiungere semplicemente l'ID client, la chiave privata e il dominio del tenant.

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

## <a name="query-the-apis"></a>Eseguire query sulle API

Queste tre API offrono diverse opportunità di recuperare informazioni sugli utenti e sugli accessi a rischio nell'organizzazione. Ecco alcuni casi d'uso comuni per queste API e le richieste di esempio associate. È possibile eseguire queste query usando il codice di esempio precedente o [Graph explorer](https://developer.microsoft.com/en-us/graph/graph-explorer).

### <a name="get-the-high-risk-and-medium-risk-events-identityriskevents-api"></a>Ottenere gli eventi ad alto rischio e a rischio medio (API identityRiskEvents)

Gli eventi a rischio medio e alto sono quelli che potrebbero attivare i criteri di rischio di accesso o utente di Identity Protection. Poiché esiste una probabilità media o alta che l'utente che sta provando ad accedere non sia il proprietario legittimo dell'identità, rimediare a questi eventi deve essere una priorità. 

```
GET https://graph.microsoft.com/beta/identityRiskEvents?`$filter=riskLevel eq 'high' or riskLevel eq 'medium'" 
```

### <a name="get-all-of-the-users-who-successfully-passed-an-mfa-challenge-triggered-by-risky-sign-ins-policy-riskyusers-api"></a>Ottenere tutti gli utenti che hanno soddisfatto una richiesta MFA attivata dai criteri di accesso a rischio (API riskyUsers)

Per comprendere l'impatto dei criteri basati sul rischio di Identity Protection sull'organizzazione, è possibile eseguire una query di tutti gli utenti che hanno soddisfatto una richiesta MFA attivata da un criterio di accesso a rischio. Queste informazioni possono essere utili per sapere quali utenti potrebbero essere stati erroneamente rilevati come a rischio da Identity Protection e quali utenti legittimi potrebbero eseguire azioni ritenute a rischio dall'intelligenza artificiale.

```
GET https://graph.microsoft.com/beta/riskyUsers?$filter=riskDetail eq 'userPassedMFADrivenByRiskBasedPolicy'
```

### <a name="get-all-the-risky-sign-ins-for-a-specific-user-signin-api"></a>Ottenere tutti gli accessi a rischio per un utente specifico (API signIn)

Quando si ritiene che un utente possa essere stato compromesso, è possibile ottenere altre informazioni sullo stato del rischio recuperando tutti gli accessi a rischio. 
```
https://graph.microsoft.com/beta/identityRiskEvents?`$filter=userID eq '<userID>' and riskState eq 'atRisk'
```




# <a name="next-steps"></a>Passaggi successivi

È appena stata creata la prima chiamata a Microsoft Graph.  
Ora è possibile eseguire query per ottenere un elenco di eventi di rischio per le identità e usare i dati come si preferisce.


Per altre informazioni su Microsoft Graph e su come creare applicazioni usando l'API Graph, vedere la [documentazione](https://docs.microsoft.com/graph/overview) e molto altro nel [sito di Microsoft Graph](https://developer.microsoft.com/en-us/graph). 


Per informazioni correlate, vedere:

-  [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)

-  [Tipi di eventi di rischio rilevati da Azure Active Directory Identity Protection](../reports-monitoring/concept-risk-events.md)

- [Microsoft Graph](https://developer.microsoft.com/graph/)

- [Overview of Microsoft Graph (Panoramica di Microsoft Graph)](https://developer.microsoft.com/graph/docs)

- [Azure AD Identity Protection Service Root (Radice del servizio Azure AD Identity Protection)](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityprotection_root)
