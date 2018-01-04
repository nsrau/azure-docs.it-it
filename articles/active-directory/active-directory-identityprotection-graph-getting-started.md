---
title: Introduzione ad Azure Active Directory Identity Protection e a Microsoft Graph | Documentazione Microsoft
description: Informazioni su come eseguire una query di Microsoft Graph per un elenco di eventi di rischio e le informazioni associate da Azure Active Directory.
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
ms.openlocfilehash: fafad74f46baaf56a8220dab05028781b2f2258e
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Introduzione ad Azure Active Directory Identity Protection e a Microsoft Graph
Microsoft Graph è l'endpoint API unificato di Microsoft e la posizione in cui risiedono le API di [Azure Active Directory Identity Protection](active-directory-identityprotection.md) . La prima API, **identityRiskEvents**, consente di eseguire una query in Microsoft Graph per ottenere un elenco di [eventi di rischio](active-directory-identityprotection-risk-events-types.md) e informazioni associate. Questo articolo fornisce un'introduzione all'esecuzione di query su questa API. Per una presentazione più approfondita, la documentazione completa e l'accesso a Graph Explorer, vedere il [sito di Microsoft Graph](https://graph.microsoft.io/).


Esistono quattro passaggi per l'accesso ai dati di protezione dell'identità tramite Microsoft Graph:

1. Recuperare il nome di dominio.
2. Creare una nuova registrazione di app. 
2. Usare questo segreto e qualche altra informazione per eseguire l'autenticazione in Microsoft Graph, dove si riceverà un token di autenticazione. 
3. Usare questo token per inviare richieste all'endpoint API e ottenere dati di Identity Protection.

Ecco gli elementi che occorre avere prima di iniziare:

* Privilegi di amministratore per creare l'applicazione in Azure AD
* Nome di dominio del tenant, ad esempio contoso.onmicrosoft.com


## <a name="retrieve-your-domain-name"></a>Recuperare il nome di dominio 

1. [Accedi](https://portal.azure.com) al portale di Azure come amministratore. 

2. Nel riquadro di spostamento a sinistra, fare clic su **Active Directory**. 
   
    ![Creazione di un'applicazione](./media/active-directory-identityprotection-graph-getting-started/41.png)


3. Nel **Gestisci** fare clic su **proprietà**.

    ![Creazione di un'applicazione](./media/active-directory-identityprotection-graph-getting-started/42.png)

4. Copiare il nome di dominio.


## <a name="create-a-new-app-registration"></a>Creare una nuova registrazione di app

1. Nel **Active Directory** nella pagina di **Gestisci** fare clic su **registrazioni di App**.

    ![Creazione di un'applicazione](./media/active-directory-identityprotection-graph-getting-started/42.png)


2. Nel menu in alto, fare clic su **nuova registrazione applicazione**.
   
    ![Creazione di un'applicazione](./media/active-directory-identityprotection-graph-getting-started/43.png)

3. Nel **crea** pagina, effettuare le seguenti operazioni:
   
    ![Creazione di un'applicazione](./media/active-directory-identityprotection-graph-getting-started/44.png)

    a. Nella casella di testo **Nome** digitare un nome per l'applicazione, ad esempio Applicazione API evento di rischio AADIP.
   
    b. Come **Tipo** selezionare **Applicazione Web e/o API Web**.
   
    c. Nella casella di testo **URL di accesso** digitare `http://localhost`.

    d. Fare clic su **Crea**.

4. Per aprire la **impostazioni** pagina, nell'elenco delle applicazioni, fare clic sulla registrazione di app appena creato. 

5. Copia il **ID applicazione**.


## <a name="grant-your-application-permission-to-use-the-api"></a>Concedere all'applicazione le autorizzazioni per l'uso dell'API

1. Nel **impostazioni** pagina, fare clic su **delle autorizzazioni necessarie**.
   
    ![Creazione di un'applicazione](./media/active-directory-identityprotection-graph-getting-started/15.png)

2. Nel **delle autorizzazioni necessarie** nella barra degli strumenti in alto fare clic su **Aggiungi**.
   
    ![Creazione di un'applicazione](./media/active-directory-identityprotection-graph-getting-started/16.png)
   
3. Nel **accesso API aggiungere** pagina, fare clic su **selezionare un'API**.
   
    ![Creazione di un'applicazione](./media/active-directory-identityprotection-graph-getting-started/17.png)

4. Nel **selezionare un'API** selezionare **Microsoft Graph**, quindi fare clic su **selezionare**.
   
    ![Creazione di un'applicazione](./media/active-directory-identityprotection-graph-getting-started/18.png)

5. Nel **accesso API aggiungere** pagina, fare clic su **selezionare autorizzazioni**.
   
    ![Creazione di un'applicazione](./media/active-directory-identityprotection-graph-getting-started/19.png)

6. Nel **Abilita accesso** pagina, fare clic su **leggere tutte le informazioni di identità rischio**e quindi fare clic su **selezionare**.
   
    ![Creazione di un'applicazione](./media/active-directory-identityprotection-graph-getting-started/20.png)

7. Nel **accesso API aggiungere** pagina, fare clic su **eseguita**.
   
    ![Creazione di un'applicazione](./media/active-directory-identityprotection-graph-getting-started/21.png)

8. Nel **autorizzazioni obbligatorie** pagina, fare clic su **concedere autorizzazioni**e quindi fare clic su **Sì**.
   
    ![Creazione di un'applicazione](./media/active-directory-identityprotection-graph-getting-started/22.png)



## <a name="get-an-access-key"></a>Ottenere una chiave di accesso

1. Nel **impostazioni** pagina, fare clic su **chiavi**.
   
    ![Creazione di un'applicazione](./media/active-directory-identityprotection-graph-getting-started/23.png)

2. Nel **chiavi** pagina, effettuare le seguenti operazioni:
   
    ![Creazione di un'applicazione](./media/active-directory-identityprotection-graph-getting-started/24.png)

    a. Nel **chiave descrizione** casella di testo, digitare una descrizione (ad esempio, *AADIP rischio evento*).
    
    b. Come **durata**selezionare **In 1 anno**.

    c. Fare clic su **Save**.
   
    d. Copiare il valore della chiave e quindi incollarlo in un luogo sicuro.   
   
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

- client_id: \<l'ID client\>

- client_secret: \<la chiave\>


Se la richiesta riesce, verrà restituito un token di autenticazione.  
Per chiamare l'API, creare un'intestazione con il parametro seguente:

    `Authorization`=”<token_type> <access_token>"


Quando si esegue l'autenticazione, è possibile trovare il tipo di token e il token di accesso nel token restituito.

Inviare questa intestazione come richiesta all'URL dell'API seguente: `https://graph.microsoft.com/beta/identityRiskEvents`

La risposta, se ha esito positivo, è una raccolta di eventi di rischio di identità e i dati associati in formato JSON OData, che può essere analizzato e gestito nel modo desiderato.

Di seguito è riportato il codice di esempio per l'autenticazione e la chiamata dell'API tramite PowerShell.  
È sufficiente aggiungere l'ID client, la chiave privata e il dominio del tenant.

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

