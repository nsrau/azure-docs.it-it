---
title: Esempi dell'API del report sull'attività di accesso di Azure Active Directory | Documentazione Microsoft
description: Come iniziare a usare l'API di creazione report di Azure Active Directory
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: c41c1489-726b-4d3f-81d6-83beb932df9c
ms.service: active-directory
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 05/31/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: b02c0ac482c13868606674a097a84976780b7ef6
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2018
ms.locfileid: "41921004"
---
# <a name="azure-active-directory-sign-in-activity-report-api-samples"></a>Esempi dell'API del report sull'attività di accesso di Azure Active Directory
Questo articolo fa parte di una raccolta di articoli sull'API di creazione report di Azure Active Directory.  
La creazione di report di Azure Active Directory fornisce un'API che consente di accedere ai dati sull'attività di accesso tramite codice o strumenti correlati.  
L'obiettivo di questo articolo è fornire il codice di esempio per l'**API sull'attività di accesso**.

Vedere:

* [Log di controllo](overview-reports.md#activity-reports) per informazioni più concettuali
* [Introduzione all'API di creazione report di Azure Active Directory](concept-reporting-api.md) .


## <a name="prerequisites"></a>Prerequisiti
Prima di poter usare gli esempi contenuti in questo articolo, è necessario completare i [prerequisiti di accesso all'API di creazione report di Azure AD](howto-configure-prerequisites-for-reporting-api.md).  

## <a name="powershell-script"></a>Script di PowerShell

```powershell

# This script will require the Web Application and permissions setup in Azure Active Directory
$clientID       = "<appid>"             # ApplicationId
$clientSecret   = "<key>"         # Should be a ~44 character string insert your info here
$loginURL       = "https://login.windows.net/"
$tenantdomain   = "<domain>"            # For example, contoso.onmicrosoft.com
$msgraphEndpoint = "https://graph.microsoft.com"
$countOfSignInDocsToBeSavedInAFile = 2000
    
# Get an Oauth 2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$msgraphEndpoint;client_id=$clientID;client_secret=$clientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body
    
if ($oauth.access_token -ne $null) {
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
    
    $url = "$msgraphEndpoint/beta/auditLogs/signIns"
    Write-Output "Fetching data using Uri: $url"
    $i=0
    $docCount=0
    Do{
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        $jsonReport = ($myReport.Content | ConvertFrom-Json).value
        $fetchedRecordCount = $jsonReport.Count
        $docCount = $docCount + $fetchedRecordCount
        $totalFetchedRecordCount = $totalFetchedRecordCount + $fetchedRecordCount
        Write-Output "Fetched $fetchedRecordCount records and saved into SignIns$i.json"
        if($docCount -le $countOfSignInDocsToBeSavedInAFile)
        {
            $myReport.Content | Out-File -FilePath SignIns$i.json -append  -Force       
        }
        else
        {           
            $docCount=0
            $i = $i+1
        }
            
        #Get url from next link
        $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'         
    }while($url -ne $null)
    Write-Output "Total Fetched record count is : $totalFetchedRecordCount"
                
} else {
    Write-Host "ERROR: No Access Token"
}

```




## <a name="executing-the-script"></a>Esecuzione dello script
Una volta modificato lo script, eseguirlo e verificare che vengano restituiti i dati corretti dal report dei log di accesso.

Lo script restituisce l'output del report sugli accessi in formato JSON. Crea anche un file `SignIns.json` con lo stesso output. È possibile provare a modificare lo script per restituire i dati di altri report e rimuovere i commenti per i formati di output non necessari.

## <a name="next-steps"></a>Passaggi successivi
* Personalizzare gli esempi contenuti in questo articolo? Consultare la pagina [Riferimento API del report sull'attività di accesso di Azure Active Directory](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin). 
* Se si desidera visualizzare una panoramica completa sull'uso dell'API di creazione report di Azure Active Directory, vedere [Introduzione all'API di creazione report di Azure Active Directory](concept-reporting-api.md).
* Per altre informazioni sulla creazione di report di Azure Active Directory, vedere [Guida alla creazione di report in Azure Active Directory](overview-reports.md).  

