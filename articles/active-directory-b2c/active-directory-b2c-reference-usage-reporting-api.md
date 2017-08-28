---
title: Esempi e definizioni di API per la creazione di report sull'uso in Azure Active Directory B2C | Microsoft Docs
description: "Guida ed esempi su come ottenere i report su utenti, autenticazioni e autenticazioni a più fattori del tenant di Azure AD B2C"
services: active-directory-b2c
documentationcenter: dev-center-name
author: rojasja
manager: mbaldwin
ms.service: active-directory-b2c
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/04/2017
ms.author: joroja
ms.translationtype: Human Translation
ms.sourcegitcommit: 8be2bcb9179e9af0957fcee69680ac803fd3d918
ms.openlocfilehash: 0171ce0bbeab783ac9b63c1fa02c7a9184cc5145
ms.contentlocale: it-it
ms.lasthandoff: 06/26/2017

---
# <a name="accessing-usage-reports-in-azure-ad-b2c-via-the-reporting-api"></a>Accesso ai report sull'uso in Azure AD B2C tramite l'API per la creazione di report

Azure Active Directory B2C (Azure AD B2C) fornisce l'autenticazione basata sull'accesso utente e sull'autenticazione a più fattori di Azure. L'autenticazione è disponibile per gli utenti finali della famiglia di applicazioni tra i provider di identità. Quando si conosce il numero di utenti registrati nel tenant, i provider usati per la registrazione e il numero di autenticazioni per tipo è possibile ottenere le informazioni seguenti:
* Numero di utenti per ogni tipo di provider di identità (ad esempio, account Microsoft o LinkedIn) registrati negli ultimi 10 giorni.
* Numero di autenticazioni che usano l'autenticazione a più fattori eseguite correttamente nell'ultimo mese.
* Numero di autenticazioni basate su accesso eseguite correttamente nel mese corrente, per ogni giorno e per ogni applicazione.
* Previsione del costo mensile dell'attività del proprio tenant Azure AD B2C.

Questo articolo si concentra sui report collegati all'attività di fatturazione, che si basa sul numero di utenti, di autenticazioni fatturabili basate su accesso e di autenticazioni a più fattori.


## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare è necessario completare i passaggi nei [prerequisiti per l'accesso alle API per la creazione di report di Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/). Creare un'applicazione, ottenere il relativo segreto e concedere i diritti di accesso ai report del tenant di Azure AD B2C. Di seguito vengono riportati anche esempi di *script Bash* e *script Python*. 

## <a name="powershell-script"></a>Script di PowerShell
Questo script illustra la creazione di quattro report sull'uso impiegando il parametro `TimeStamp` e il filtro `ApplicationId`.

```powershell
# This script will require the Web Application and permissions setup in Azure Active Directory

# Constants
$ClientID      = "your-client-application-id-here"  
$ClientSecret  = "your-client-application-secret-here"
$loginURL      = "https://login.microsoftonline.com"
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
* **tenantUserCount**: il numero di utenti del tenant al giorno per tipo di provider di identità negli ultimi 30 giorni. (Facoltativo: un filtro `TimeStamp` specifica il numero di utenti da una data specificata alla data attuale). Questo report offre le informazioni seguenti:
  * **TotalUserCount**: il numero totale degli oggetti utente.
  * **OtherUserCount**: il numero di utenti di Azure Active Directory (non gli utenti di Azure AD B2C).
  * **LocalUserCount**: il numero di account utente Azure AD B2C creati con le credenziali locali di accesso al tenant Azure AD B2C.

* **AlternateIdUserCount**: il numero di utenti di Azure AD B2C registrati con provider di identità esterno (ad esempio, Facebook, un account Microsoft o un altro tenant di Azure Active Directory, detto anche `OrgId`).

* **b2cAuthenticationCountSummary**: il riepilogo del numero giornaliero di autenticazioni fatturabili eseguite negli ultimi 30 giorni, per data e per tipo di flusso di autenticazione.

* **b2cAuthenticationCount**: il numero di autenticazioni eseguite entro un determinato periodo di tempo. Il valore predefinito corrisponde agli ultimi 30 giorni.  (Facoltativo: i parametri `TimeStamp` di inizio e fine definiscono un periodo di tempo specifico). L'output include `StartTimeStamp` (prima data dell'attività per questo tenant) e `EndTimeStamp` (ultimo aggiornamento).

* **b2cMfaRequestCountSummary**: il riepilogo del numero giornaliero di autenticazioni a più fattori (voce o SMS), per data e per tipo.


## <a name="limitations"></a>Limitazioni
I dati relativi al numero di utenti vengono aggiornati con una frequenza compresa tra 24 e 48 ore. Le autenticazioni vengono aggiornate diverse volte al giorno. Quando si usa il filtro `ApplicationId`, se si ottiene un report vuoto la causa può essere una delle seguenti:
  * L'ID dell'applicazione non esiste nel tenant. Verificare che sia corretto.
  * L'ID dell'applicazione esiste, ma non sono stati trovati dati nel periodo di reporting selezionato. Controllare i valori dei parametri Data/Ora.


## <a name="next-steps"></a>Passaggi successivi
### <a name="monthly-bill-estimates-for-azure-ad"></a>Stime della fattura mensile per Azure AD
È possibile fare riferimento ai [prezzi più aggiornati di Azure AD B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/) per stimare il proprio consumo di Azure giornaliero, settimanale e mensile.  Questa stima è particolarmente utile quando si pianificano modifiche nel comportamento del tenant che potrebbero influire sul costo complessivo. È possibile rivedere i costi attuali nella [sottoscrizione Azure collegata](active-directory-b2c-how-to-enable-billing.md).

### <a name="options-for-other-output-formats"></a>Opzioni per altri formati di output
Il codice seguente illustra esempi di invio di output a JSON, un elenco di valori nome, e XML:
```powershell
# to output to JSON use following line in the PowerShell sample
$myReport.Content | Out-File -FilePath b2cUserJourneySummaryEvents.json -Force

# to output the content to a name value list
($myReport.Content | ConvertFrom-Json).value | Out-File -FilePath name-your-file.txt -Force

# to output the content in XML use the following line
(($myReport.Content | ConvertFrom-Json).value | ConvertTo-Xml).InnerXml | Out-File -FilePath name-your-file.xml -Force
```

