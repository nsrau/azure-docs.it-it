---
title: Esempi e definizioni di API per la creazione di report sull&quot;uso in Azure AD B2C | Microsoft Docs
description: Guida ed esempi su come ottenere report su utenti, autenticazioni e MFA per i tenant B2C.
services: active-directory-b2c
documentationcenter: dev-center-name
author: rojasja
manager: mbaldwin
ms.service: active-directory-b2c
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/08/2017
ms.author: joroja
ms.translationtype: Human Translation
ms.sourcegitcommit: 4b2cbf5f62ee63bada42b2a06506b793d4349fdb
ms.openlocfilehash: 7db9a45a4c80ea8d01937837dfa7a15c171fb66b
ms.contentlocale: it-it
ms.lasthandoff: 02/10/2017


---
# <a name="accessing-usage-reports-in-azure-ad-b2c-via-the-reporting-api"></a>Accesso ai report sull'uso in Azure AD B2C tramite l'API per la creazione di report

Azure Active Directory B2C offre accesso e autenticazione MFA per tutti gli utenti finali della propria famiglia di applicazioni nei vari provider di identità.  Conoscere il numero di utenti registrati nel tenant, i provider usati per la registrazione e il numero di autenticazioni per tipo consente di ottenere le informazioni seguenti:
* Numero di utenti per ogni tipo di provider di identità (ad esempio, account Microsoft o LinkedIn) registrati negli ultimi 10 giorni.
* Numero di autenticazioni a più fattori eseguite correttamente nell'ultimo mese.
* Numero di autenticazioni basate su accesso eseguite correttamente nel mese corrente, per ogni giorno e per ogni applicazione.
* Previsione del costo mensile dell'attività del proprio tenant B2C.

Questo articolo si concentra sui report collegati più strettamente all'attività di fatturazione, che si basa sul numero di utenti, sul numero di autenticazioni fatturabili basate su accesso e sul numero di autenticazioni a più fattori.


## <a name="prerequisites-to-access-the-azure-ad-reporting-api"></a>Prerequisiti di accesso all'API di creazione report di Azure AD
Prima di iniziare è necessario soddisfare i [prerequisiti per l'accesso alle API per la creazione di report di Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/).  Creare un'applicazione, ottenere il relativo segreto e concedere i diritti di accesso ai report del tenant di Azure AD B2C. Di seguito vengono riportati anche esempi di *script Bash* e *script Python*.

## <a name="powershell-script"></a>Script di PowerShell
Questo script illustra i quattro report sull'uso che impiegano il parametro **TimeStamp** e il filtro **- ApplicationId**.

```
# This script will require the Web Application and permissions setup in Azure Active Directory

# Constants
$ClientID      = "your-client-application-id-here"  
$ClientSecret  = "your-client-application-secret-here"
$loginURL      = "https://login.windows.net"
$tenantdomain  = "your-b2c-tenant-domain.onmicrosoft.com"  
# Get an Oauth 2 access token based on client id, secret and tenant domain
$body          = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
$oauth         = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body
if ($oauth.access_token -ne $null) {
    $headerParams  = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

    Write-host Data from the tenantUserCount report
    Write-host ====================================================
     # Returns a JSON document for the report
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/tenantUserCount?api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the tenantUserCount report with datetime filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/tenantUserCount?%24filter=TimeStamp+gt+2016-10-15&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cAuthenticationCountSummary report
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cAuthenticationCountSummary?api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cAuthenticationCount report with datetime filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cAuthenticationCount?%24filter=TimeStamp+gt+2016-09-20+and+TimeStamp+lt+2016-10-03&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cAuthenticationCount report with ApplicationId filter
    Write-host ====================================================
    # Returns a JSON document for the " " report
        $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cAuthenticationCount?%24filter=ApplicationId+eq+ada78934-a6da-4e69-b816-10de0d79db1d&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cMfaRequestCountSummary
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cMfaRequestCountSummary?api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cMfaRequestCount report with datetime filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cMfaRequestCount?%24filter=TimeStamp+gt+2016-09-10+and+TimeStamp+lt+2016-10-04&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cMfaRequestCount report with ApplicationId filter
    Write-host ====================================================
       $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cMfaRequestCountSummary?%24filter=ApplicationId+eq+ada78934-a6da-4e69-b816-10de0d79db1d&api-version=beta")
     Write-host $myReport.Content

} else {
    Write-Host "ERROR: No Access Token"
    }
```


## <a name="usage-report-definitions"></a>Definizioni dei report sull'uso
**tenantUserCount** – Il numero di utenti del tenant al giorno per tipo di provider di identità negli ultimi 30 giorni (facoltativo: un filtro TimeStamp specifica il numero di utenti da una data specificata alla data attuale). Questo report offre le informazioni seguenti:
 * TotalUserCount = il numero totale degli oggetti utente
 * OtherUserCount = il numero di utenti della directory AAD (utenti non B2C)
 * LocalUserCount = il numero di account utente B2C creati con le credenziali locali di accesso al tenant B2C

**AlternateIdUserCount** = il numero di utenti B2C registrati con provider di identità esterni (ad esempio, Facebook, account Microsoft o altri tenant AAD, detti anche OrgId)

**b2cAuthenticationCountSummary** – Il numero totale giornaliero di autenticazioni fatturabili eseguite negli ultimi 30 giorni, per data e per tipo di flusso di autenticazione

**b2cAuthenticationCount** - Il numero di autenticazioni eseguite entro un determinato periodo di tempo. Il valore predefinito corrisponde agli ultimi 30 giorni  (facoltativo: i valori TimeStamp di inizio e fine possono definire l'intervallo specifico di conteggi desiderati). L'output include StartTimeStamp (la prima data di attività del tenant) e EndTimeStamp (l'ultimo aggiornamento)

**b2cMfaRequestCountSummary** - Il numero totale giornaliero di autenticazioni a più fattori (voce o SMS), per data e per tipo


## <a name="limitations"></a>Limitazioni
* I dati relativi al numero di utenti vengono aggiornati con una frequenza compresa tra 24 e 48 ore.  Le autenticazioni vengono aggiornate diverse volte al giorno.
* Quando si usa il filtro ApplicationId, se si ottiene un report vuoto la causa può essere una delle seguenti:
 * L'ID dell'applicazione non esiste nel tenant. Verificare che sia corretto.
 * L'ID dell'applicazione esiste, ma non sono stati trovati dati nel periodo di reporting selezionato. Controllare i valori dei parametri Data/Ora.


## <a name="next-steps"></a>Passaggi successivi
### <a name="estimating-your-azure-ad-monthly-bill"></a>Stima del costo mensile di Azure AD.
È possibile fare riferimento ai [prezzi più aggiornati di Azure AD B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/) per stimare il proprio consumo di Azure giornaliero, settimanale e mensile.  Questa stima è particolarmente utile quando si pianificano modifiche nel comportamento del tenant che possono influire sul costo complessivo.  Per conoscere i costi effettivi, consultare la [sottoscrizione Azure collegata](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-how-to-enable-billing).

### <a name="options-for-other-output-formats"></a>Opzioni per altri formati di output
```
# to output to JSON use following line in the PowerShell sample
$myReport.Content | Out-File -FilePath b2cUserJourneySummaryEvents.json -Force

# to output the content to a name value list
($myReport.Content | ConvertFrom-Json).value | Out-File -FilePath name-your-file.txt -Force

# to output the content in XML use the following line
(($myReport.Content | ConvertFrom-Json).value | ConvertTo-Xml).InnerXml | Out-File -FilePath name-your-file.xml -Force
```


<!--Reference style links - using these makes the source content way more readable than using inline links-->
[gog]: http://google.com/        
[yah]: http://search.yahoo.com/  
[msn]: http://search.msn.com/    

