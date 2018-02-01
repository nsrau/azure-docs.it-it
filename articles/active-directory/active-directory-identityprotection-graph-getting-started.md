---
title: Microsoft Graph per Azure Active Directory Identity Protection | Microsoft Docs
description: Informazioni su come eseguire query in Microsoft Graph per ottenere un elenco degli eventi di rischio e le informazioni associate da Azure Active Directory.
services: active-directory
keywords: "azure active directory identity protection, evento di rischio, vulnerabilità, criteri di sicurezza, Microsoft Graph"
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: fa109ba7-a914-437b-821d-2bd98e681386
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: markvi
ms.reviewer: nigu
ms.custom: seohack1
ms.openlocfilehash: df0d89fc93f1b9c19d669c29306398a8b25ee425
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/18/2018
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Introduzione ad Azure Active Directory Identity Protection e a Microsoft Graph
Microsoft Graph è l'endpoint API unificato di Microsoft e la posizione in cui risiedono le API di [Azure Active Directory Identity Protection](active-directory-identityprotection.md) . La prima API, **identityRiskEvents**, consente di eseguire una query in Microsoft Graph per ottenere un elenco di [eventi di rischio](active-directory-identityprotection-risk-events-types.md) e informazioni associate. Questo articolo fornisce un'introduzione all'esecuzione di query su questa API. Per una presentazione più approfondita, la documentazione completa e l'accesso a Graph Explorer, vedere il [sito di Microsoft Graph](https://graph.microsoft.io/).


La procedura per accedere ai dati di Identity Protection tramite Microsoft Graph si articola in quattro passaggi:

1. Recuperare il nome di dominio.
2. Creare una nuova registrazione di app. 
2. Usare questo segreto e qualche altra informazione per eseguire l'autenticazione in Microsoft Graph, dove si riceverà un token di autenticazione. 
3. Usare questo token per inviare richieste all'endpoint API e ottenere dati di Identity Protection.

Ecco gli elementi che occorre avere prima di iniziare:

* Privilegi di amministratore per creare l'applicazione in Azure AD
* Nome di dominio del tenant, ad esempio contoso.onmicrosoft.com


## <a name="retrieve-your-domain-name"></a>Recuperare il nome di dominio 

1. [Accedere](https://portal.azure.com) al portale di Azure come amministratore. 

2. Nel riquadro di spostamento a sinistra fare clic su **Active Directory**. 
   
    ![Creazione di un'applicazione](./media/active-directory-identityprotection-graph-getting-started/41.png)


3. Nella sezione **Gestisci** fare clic su **Proprietà**.

    ![Creazione di un'applicazione](./media/active-directory-identityprotection-graph-getting-started/42.png)

4. Copiare il nome di dominio.


## <a name="create-a-new-app-registration"></a>Creare una nuova registrazione di app

1. Nella sezione **Gestisci** della pagina **Active Directory** fare clic su **Registrazioni per l'app**.

    ![Creazione di un'applicazione](./media/active-directory-identityprotection-graph-getting-started/42.png)


2. Nel menu in alto fare clic su **Registrazione nuova applicazione**.
   
    ![Creazione di un'applicazione](./media/active-directory-identityprotection-graph-getting-started/43.png)

3. Nella pagina **Crea** seguire questa procedura:
   
    ![Creazione di un'applicazione](./media/active-directory-identityprotection-graph-getting-started/44.png)

    a. Nella casella di testo **Nome** digitare un nome per l'applicazione, ad esempio Applicazione API evento di rischio AADIP.
   
    b. Come **Tipo** selezionare **Applicazione Web e/o API Web**.
   
    c. Nella casella di testo **URL di accesso** digitare `http://localhost`.

    d. Fare clic su **Crea**.

4. Per aprire la pagina **Impostazioni**, fare clic sulla registrazione di app appena creata nell'elenco delle applicazioni. 

5. Copiare **ID applicazione**.


## <a name="grant-your-application-permission-to-use-the-api"></a>Concedere all'applicazione le autorizzazioni per l'uso dell'API

1. Nella pagina **Impostazioni** fare clic su **Autorizzazioni necessarie**.
   
    ![Creazione di un'applicazione](./media/active-directory-identityprotection-graph-getting-started/15.png)

2. Nella pagina **Autorizzazioni necessarie** fare clic su **Aggiungi** sulla barra degli strumenti in alto.
   
    ![Creazione di un'applicazione](./media/active-directory-identityprotection-graph-getting-started/16.png)
   
3. Nella pagina **Aggiungi accesso all'API** fare clic su **Selezionare un'API**.
   
    ![Creazione di un'applicazione](./media/active-directory-identityprotection-graph-getting-started/17.png)

4. Nella pagina **Selezionare un'API** selezionare **Microsoft Graph** e quindi fare clic su **Seleziona**.
   
    ![Creazione di un'applicazione](./media/active-directory-identityprotection-graph-getting-started/18.png)

5. Nella pagina **Aggiungi accesso all'API** fare clic su **Selezionare le autorizzazioni**.
   
    ![Creazione di un'applicazione](./media/active-directory-identityprotection-graph-getting-started/19.png)

6. Nella pagina **Abilita accesso** fare clic su **Legge tutte le informazioni sugli eventi di rischio dell'identità** e quindi su **Seleziona**.
   
    ![Creazione di un'applicazione](./media/active-directory-identityprotection-graph-getting-started/20.png)

7. Nella pagina **Aggiungi accesso all'API** fare clic su **Fatto**.
   
    ![Creazione di un'applicazione](./media/active-directory-identityprotection-graph-getting-started/21.png)

8. Nella pagina **Autorizzazioni necessarie** fare clic su **Concedi autorizzazioni** e quindi su **Sì**.
   
    ![Creazione di un'applicazione](./media/active-directory-identityprotection-graph-getting-started/22.png)



## <a name="get-an-access-key"></a>Ottenere una chiave di accesso

1. Nella pagina **Impostazioni** fare clic su **Chiavi**.
   
    ![Creazione di un'applicazione](./media/active-directory-identityprotection-graph-getting-started/23.png)

2. Nel pagina **Chiavi** seguire questa procedura:
   
    ![Creazione di un'applicazione](./media/active-directory-identityprotection-graph-getting-started/24.png)

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

-  resource: "**https://graph.microsoft.com**"

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


## <a name="next-steps"></a>Passaggi successivi

È appena stata creata la prima chiamata a Microsoft Graph.  
Ora è possibile eseguire query per ottenere un elenco di eventi di rischio per le identità e usare i dati come si preferisce.

Per altre informazioni su Microsoft Graph e su come creare applicazioni usando l'API Graph, vedere la [documentazione](https://graph.microsoft.io/docs) e molto altro nel [sito di Microsoft Graph](https://graph.microsoft.io/). Assicurarsi inoltre di creare un segnalibro alla pagina dell'API [Azure AD Identity Protection](https://graph.microsoft.io/docs/api-reference/beta/resources/identityprotection_root) che include l'elenco di tutte le API Identity Protection disponibili in Graph. In questa pagina sono visualizzate le nuove modalità aggiunte via via per l'utilizzo di Identity Protection tramite l'API.

Per informazioni correlate, vedere:

-  [Azure Active Directory Identity Protection](active-directory-identityprotection.md)

-  [Tipi di eventi di rischio rilevati da Azure Active Directory Identity Protection](active-directory-identityprotection-risk-events-types.md)

- [Microsoft Graph](https://graph.microsoft.io/)

- [Overview of Microsoft Graph (Panoramica di Microsoft Graph)](https://graph.microsoft.io/docs)

- [Azure AD Identity Protection Service Root (Radice del servizio Azure AD Identity Protection)](https://graph.microsoft.io/docs/api-reference/beta/resources/identityprotection_root)

