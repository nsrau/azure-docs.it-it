---
title: Introduzione ad Azure Active Directory Identity Protection e a Microsoft Graph | Documentazione Microsoft
description: Fornisce un'introduzione all'esecuzione di query su Microsoft Graph per ottenere un elenco di eventi di rischio e le relative informazioni da Azure Active Directory.
services: active-directory
keywords: "azure active directory identity protection, evento di rischio, vulnerabilità, criteri di sicurezza, Microsoft Graph"
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: fa109ba7-a914-437b-821d-2bd98e681386
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 9b01ff86da6a1fd4a439a6ba59ea15ed6480cdad
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2017
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Introduzione ad Azure Active Directory Identity Protection e a Microsoft Graph
Microsoft Graph è l'endpoint API unificato di Microsoft e la posizione in cui risiedono le API di [Azure Active Directory Identity Protection](active-directory-identityprotection.md) . La prima API, **identityRiskEvents**, consente di eseguire una query in Microsoft Graph per ottenere un elenco di [eventi di rischio](active-directory-identityprotection-risk-events-types.md) e informazioni associate. Questo articolo fornisce un'introduzione all'esecuzione di query su questa API. Per una presentazione più approfondita, la documentazione completa e l'accesso a Graph Explorer, vedere il [sito di Microsoft Graph](https://graph.microsoft.io/).

> [!IMPORTANT]
> Microsoft consiglia di gestire Azure AD usando l'[interfaccia di amministrazione di Azure AD](https://aad.portal.azure.com) nel portale di Azure invece di usare il portale di Azure classico citato nel presente articolo.

Per l'accesso ai dati di Identity Protection tramite Microsoft Graph sono disponibili tre passaggi:

1. Aggiungere un'applicazione con un segreto client. 
2. Usare questo segreto e qualche altra informazione per eseguire l'autenticazione in Microsoft Graph, dove si riceverà un token di autenticazione. 
3. Usare questo token per inviare richieste all'endpoint API e ottenere dati di Identity Protection.

Ecco gli elementi che occorre avere prima di iniziare:

* Privilegi di amministratore per creare l'applicazione in Azure AD
* Nome di dominio del tenant, ad esempio contoso.onmicrosoft.com

## <a name="add-an-application-with-a-client-secret"></a>Aggiungere un'applicazione con un segreto client
1. [Accedere](https://manage.windowsazure.com) al portale di Azure classico come amministratore. 
2. Nel riquadro di spostamento sinistro fare clic su **Active Directory**. 
   
    ![Creazione di un'applicazione](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_01.png)
3. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
4. Scegliere **Applicazioni**dal menu in alto.
   
    ![Creazione di un'applicazione](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_02.png)
5. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Creazione di un'applicazione](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_03.png)
6. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungi un'applicazione che l'organizzazione sta sviluppando**.
   
    ![Creazione di un'applicazione](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_04.png)
7. Nella finestra di dialogo **Informazioni sull'applicazione** seguire questa procedura:
   
    ![Creazione di un'applicazione](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_05.png)
   
    a. Nella casella di testo **Nome** digitare un nome per l'applicazione, ad esempio Applicazione API evento di rischio AADIP.
   
    b. Come **Tipo** selezionare **Applicazione Web e/o API Web**.
   
    c. Fare clic su **Avanti**.
8. Nella finestra di dialogo **Proprietà dell'app** seguire questa procedura:
   
    ![Creazione di un'applicazione](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_06.png)
   
    a. Nella casella di testo **URL di accesso** digitare `http://localhost`.
   
    b. Nella casella di testo **URI ID app** digitare `http://localhost`.
   
    c. Fare clic su **Completa**.

A questo punto è possibile configurare l'applicazione.

![Creazione di un'applicazione](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_07.png)

## <a name="grant-your-application-permission-to-use-the-api"></a>Concedere all'applicazione le autorizzazioni per l'uso dell'API
1. Nella pagina dell'applicazione scegliere **Configura**dal menu in alto. 
   
    ![Creazione di un'applicazione](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_08.png)
2. Nella sezione **Autorizzazioni per altre applicazioni** fare clic su **Aggiungi applicazione**.
   
    ![Creazione di un'applicazione](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_09.png)
3. Nella finestra di dialogo **Autorizzazioni per altre applicazioni** seguire questa procedura:
   
    ![Creazione di un'applicazione](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_10.png)
   
    a. Selezionare **Microsoft Graph**.
   
    b. Fare clic su **Complete**.
4. Fare clic su **Autorizzazioni applicazione: 0** e quindi selezionare **Legge tutte le informazioni sugli eventi di rischio dell'identità**.
   
    ![Creazione di un'applicazione](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_11.png)
5. Fare clic su **Salva** nella parte inferiore della pagina.
   
    ![Creazione di un'applicazione](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_12.png)

## <a name="get-an-access-key"></a>Ottenere una chiave di accesso
1. Nella sezione **Chiavi** della pagina dell'applicazione selezionare 1 anno come durata.
   
    ![Creazione di un'applicazione](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_13.png)
2. Fare clic su **Salva** nella parte inferiore della pagina.
   
    ![Creazione di un'applicazione](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_12.png)
3. Nella sezione Chiavi copiare il valore della chiave appena creata e quindi incollarla in una posizione sicura.
   
    ![Creazione di un'applicazione](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_14.png)
   
   > [!NOTE]
   > Se si perde la chiave, si dovrà tornare a questa sezione e crearne una nuova. Mantenere la chiave segreta, perché chiunque ne fosse a conoscenza potrà accedere ai dati.
   > 
   > 
4. Nella sezione **Proprietà** copiare l'**ID client** e conservarlo in un luogo sicuro. 

## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-events-api"></a>Autenticarsi in Microsoft Graph ed eseguire query sull'API eventi di rischio per le identità
A questo punto saranno disponibili:

* L'ID client copiato in precedenza
* La chiave copiata in precedenza
* Il nome di dominio del tenant

Per autenticarsi, inviare una richiesta POST a `https://login.microsoft.com` includendo nel corpo i parametri seguenti:

* grant_type: "**client_credentials**"
* resource: "**https://graph.microsoft.com**"
* client_id: <your client ID>
* client_secret: <your key>

> [!NOTE]
> È necessario fornire i valori per i parametri **client_id** e **client_secret**.
> 
> 

Se la richiesta riesce, verrà restituito un token di autenticazione.  
Per chiamare l'API, creare un'intestazione con il parametro seguente:

    `Authorization`=”<token_type> <access_token>"


Quando si esegue l'autenticazione, è possibile trovare il tipo di token e il token di accesso nel token restituito.

Inviare questa intestazione come richiesta all'URL dell'API seguente: `https://graph.microsoft.com/beta/identityRiskEvents`

La risposta, se la richiesta riesce, è una raccolta di eventi di rischio per le identità e di dati associati nel formato JSON OData, che può essere analizzato e gestito secondo le esigenze.

Ecco il codice di esempio per autenticarsi e chiamare l'API con Powershell.  
Aggiungere semplicemente l'ID client, la chiave e il dominio del tenant.

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

## <a name="additional-resources"></a>Risorse aggiuntive
* [Azure Active Directory Identity Protection](active-directory-identityprotection.md)
* [Tipi di eventi di rischio rilevati da Azure Active Directory Identity Protection](active-directory-identityprotection-risk-events-types.md)
* [Microsoft Graph](https://graph.microsoft.io/)
* [Overview of Microsoft Graph (Panoramica di Microsoft Graph)](https://graph.microsoft.io/docs)
* [Azure AD Identity Protection Service Root (Radice del servizio Azure AD Identity Protection)](https://graph.microsoft.io/docs/api-reference/beta/resources/identityprotection_root)

