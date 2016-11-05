---
title: Esempi dell'API del report sull'attività di accesso di Azure Active Directory | Microsoft Docs
description: Come iniziare a usare l'API di creazione report di Azure Active Directory
services: active-directory
documentationcenter: ''
author: dhanyahk
manager: femila
editor: ''

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/25/2016
ms.author: dhanyahk;markvi

---
# <a name="azure-active-directory-sign-in-activity-report-api-samples"></a>Esempi dell'API del report sull'attività di accesso di Azure Active Directory
Questo argomento fa parte di una raccolta di argomenti sull'API di creazione report di Azure Active Directory.  
La creazione di report di Azure Active Directory fornisce un'API che consente di accedere ai dati sull'attività di accesso tramite codice o strumenti correlati.  
L'obiettivo di questo argomento è fornire il codice di esempio per l' **API sull'attività di accesso**.

Vedere:

* [Log di controllo](active-directory-reporting-azure-portal.md#audit-logs) per informazioni più concettuali
* [Introduzione all'API di creazione report di Azure Active Directory](active-directory-reporting-api-getting-started.md) .

Per domande, problemi o suggerimenti, contattare la [Guida per la creazione di report AAD](mailto:aadreportinghelp@microsoft.com).

## <a name="prerequisites"></a>Prerequisiti
Prima di poter usare gli esempi contenuti in questo argomento, è necessario completare i [prerequisiti di accesso all'API di creazione report di Azure AD](active-directory-reporting-api-prerequisites.md).  

## <a name="powershell-script"></a>Script di PowerShell
    # This script will require the Web Application and permissions setup in Azure Active Directory
    $ClientID       = "<clientId>"             # Should be a ~35 character string insert your info here
    $ClientSecret   = "<clientSecret>"         # Should be a ~44 character string insert your info here
    $loginURL       = "https://login.windows.net/"
    $tenantdomain   = "<tenantDomain>"
    $ daterange            # For example, contoso.onmicrosoft.com

    $7daysago = "{0:s}" -f (get-date).AddDays(-7) + "Z"
    # or, AddMinutes(-5)

    Write-Output $7daysago

    # Get an Oauth 2 access token based on client id, secret and tenant domain
    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}

    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    if ($oauth.access_token -ne $null) {
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

    $url = "https://graph.windows.net/$tenantdomain/activities/signinEvents?api-version=beta&`$filter=signinDateTime ge $7daysago"

    $i=0

    Do{
        Write-Output "Fetching data using Uri: $url"
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        Write-Output "Save the output to a file SigninActivities$i.json"
        Write-Output "---------------------------------------------"
        $myReport.Content | Out-File -FilePath SigninActivities$i.json -Force
        $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'
        $i = $i+1
    } while($url -ne $null)

    } else {

        Write-Host "ERROR: No Access Token"
    }




## <a name="executing-the-script"></a>Esecuzione dello script
Una volta modificato lo script, eseguirlo e verificare che vengano restituiti i dati corretti dal report Log di controllo.

Lo script restituisce l'output del report sugli accessi in formato JSON. Crea anche un file `SigninActivities.json` con lo stesso output. È possibile provare a modificare lo script per restituire i dati di altri report e rimuovere i commenti per i formati di output non necessari.

## <a name="next-steps"></a>Passaggi successivi
* Si desidera personalizzare gli esempi contenuti in questo argomento? Consultare la pagina [Riferimento API del report sull'attività di accesso di Azure Active Directory](active-directory-reporting-api-sign-in-activity-reference.md). 
* Se si desidera visualizzare una panoramica completa sull'uso dell'API di creazione report di Azure Active Directory, vedere [Introduzione all'API di creazione report di Azure Active Directory](active-directory-reporting-api-getting-started.md).
* Per altre informazioni sulla creazione di report di Azure Active Directory, vedere [Guida alla creazione di report in Azure Active Directory](active-directory-reporting-guide.md).  

<!--HONumber=Oct16_HO2-->


