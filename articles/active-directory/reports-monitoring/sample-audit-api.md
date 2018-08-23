---
title: Esempi dell'API di controllo di creazione report di Azure Active Directory | Documentazione Microsoft
description: Come iniziare a usare l'API di creazione report di Azure Active Directory
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: de8b8ec3-49b3-4aa8-93fb-e38f52c99743
ms.service: active-directory
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 05/30/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 345daaa8ac991cad9f098b317a9b59d52de73ead
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2018
ms.locfileid: "41920840"
---
# <a name="azure-active-directory-reporting-audit-api-samples"></a>Esempi dell'API di creazione report di Azure Active Directory
Questo articolo fa parte di una raccolta di articoli sull'API di creazione report di Azure Active Directory.  
La creazione di report di Azure Active Directory fornisce un'API che consente di accedere ai dati di controllo tramite codice o strumenti correlati.
L'obiettivo di questo articolo è fornire codice di esempio per l'**API di controllo**.

Vedere:

* [Log di controllo](overview-reports.md#activity-reports) .
* [Introduzione all'API di creazione report di Azure Active Directory](concept-reporting-api.md) .

Per domande, problemi o suggerimenti, contattare la [Guida per la creazione di report AAD](mailto:aadreportinghelp@microsoft.com).


## <a name="prerequisites"></a>Prerequisiti
Prima di poter usare gli esempi contenuti in questo articolo, è necessario completare i [prerequisiti di accesso all'API di creazione report di Azure AD](howto-configure-prerequisites-for-reporting-api.md).  

## <a name="known-issue"></a>Problema noto
L'autenticazione dell'applicazione non funziona se il tenant si trova nell'area dell'Unione Europea. Per l'accesso all'API di controllo come soluzione alternativa fino a quando non si risolve il problema, usare l'autenticazione degli utenti. 

## <a name="powershell-script"></a>Script di PowerShell


```powershell

# This script will require the Web Application and permissions setup in Azure Active Directory
$clientID       = "<appid>"             # ApplicationId
$clientSecret   = "<key>"         # Should be a ~44 character string insert your info here
$loginURL       = "https://login.windows.net/"
$tenantdomain   = "<domain>"            # For example, contoso.onmicrosoft.com
$msgraphEndpoint = "https://graph.microsoft.com"
$countOfAuditDocsToBeSavedInAFile = 2000
    
# Get an Oauth 2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$msgraphEndpoint;client_id=$clientID;client_secret=$clientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body
    
if ($oauth.access_token -ne $null) {
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
    
    $url = "$msgraphEndpoint/beta/auditLogs/directoryAudits"
    Write-Output "Fetching data using Uri: $url"
    $i=0
    $docCount=0
    Do{
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        $jsonReport = ($myReport.Content | ConvertFrom-Json).value
        $fetchedRecordCount = $jsonReport.Count
        $docCount = $docCount + $fetchedRecordCount
        $totalFetchedRecordCount = $totalFetchedRecordCount + $fetchedRecordCount
        Write-Output "Fetched $fetchedRecordCount records and saved into Audits$i.json"
        if($docCount -le $countOfAuditDocsToBeSavedInAFile)
        {
            $myReport.Content | Out-File -FilePath Audits$i.json -append  -Force        
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

### <a name="executing-the-powershell-script"></a>Esecuzione dello script di PowerShell
Una volta modificato lo script, eseguirlo e verificare che vengano restituiti i dati corretti dal report Log di controllo.

Lo script restituisce l'output del report di controllo in formato JSON. Crea anche un file `Audits.json` con lo stesso output. È possibile provare a modificare lo script per restituire i dati di altri report e rimuovere i commenti per i formati di output non necessari.





## <a name="next-steps"></a>Passaggi successivi
* Personalizzare gli esempi contenuti in questo articolo? Vedere le [informazioni di riferimento sull'API di controllo Azure Active Directory](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit). 
* Se si desidera visualizzare una panoramica completa sull'uso dell'API di creazione report di Azure Active Directory, vedere [Introduzione all'API di creazione report di Azure Active Directory](concept-reporting-api.md).
* Per altre informazioni sulla creazione di report di Azure Active Directory, vedere [Guida alla creazione di report in Azure Active Directory](overview-reports.md).  

