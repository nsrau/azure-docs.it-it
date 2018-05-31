---
title: 'Azure Active Directory B2C: definizioni ed esempi dei log di controllo'
description: Guida ed esempi per l'accesso ai log di controllo di Azure AD B2C
services: active-directory-b2c
author: sromeroz
manager: sasubram
ms.author: sezambra
ms.service: active-directory-b2c
ms.topic: article
ms.workload: identity
ms.date: 08/04/2017
ms.openlocfilehash: 8cc48853b0677230c4e19df4f0ecd93ce88b119d
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2018
ms.locfileid: "33206454"
---
# <a name="accessing-azure-ad-b2c-audit-logs"></a>Accesso ai log di controllo di Azure AD B2C

Azure Active Directory B2C (Azure AD B2C) genera log di controllo contenenti informazioni sulle attività relative ad accesso amministrativo, token rilasciati e risorse B2C. Questo articolo offre una breve panoramica delle informazioni disponibili tramite i log di controllo e le istruzioni per accedere a questi dati per il tenant di Azure AD B2C.

> [!IMPORTANT]
> I log di controllo vengono conservati solo per sette giorni. Se è necessario conservarli per un periodo più lungo, pianificare il download e l'archiviazione dei log con uno dei metodi illustrati sotto. 

##<a name="overview-of-activities-available-in-the-b2c-category-of-audit-logs"></a>Panoramica delle attività disponibili nella categoria B2C dei log di controllo
La categoria **B2C** nei log di controllo contiene i tipi di attività seguenti:
|Tipo di attività |DESCRIZIONE  |
|---------|---------|
|Authorization |Attività relative all'autorizzazione di un utente ad accedere alle risorse B2C (ad esempio, un amministratore che accede a un elenco di criteri B2C)         |
|Directory |Attività relative agli attributi della directory recuperati quando un amministratore esegue l'accesso usando il portale di Azure |
|Applicazione | Operazioni CRUD sulle applicazioni B2C |
|Chiave |Operazioni CRUD sulle chiavi archiviate nel contenitore di chiavi B2C |
|Risorsa |Operazioni CRUD sulle risorse B2C (ad esempio, criteri e provider di identità)
|Authentication |Convalida delle credenziali utente e rilascio dei token|

> [!NOTE]
> Per le attività CRUD sugli oggetti utente, vedere la categoria **Directory principale**.

##<a name="example-activity"></a>Attività di esempio
L'esempio seguente mostra i dati acquisiti quando un utente esegue l'accesso con un provider di identità esterno: ![Log di controllo - Esempio](./media/active-directory-b2c-reference-audit-logs/audit-logs-example.png)

##<a name="accessing-audit-logs-through-the-azure-portal"></a>Accesso ai log di controllo dal portale di Azure
1. Accedere al [portale di Azure](https://portal.azure.com). Verificare di essere nella directory B2C.
2. Fare clic su **Azure Active Directory** nella barra dei preferiti a sinistra 
    
    ![Log di controllo - Pulsante AAD](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-aad.png)

1. In **Attività** fare clic su **Log di controllo**

    ![Log di controllo - Sezione Log](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-section.png)

2. Nella casella di riepilogo a discesa **Categoria** selezionare **B2C**
3. Fare clic su **Applica**

    ![Log di controllo - Categoria](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-category.png)

Verrà visualizzato un elenco delle attività registrate negli ultimi sette giorni. 
- Usare la casella di riepilogo a discesa **Tipo di risorsa attività** per filtrare in base ai tipi di attività illustrati sopra
- Usare la casella di riepilogo a discesa **Intervallo di date** per filtrare l'intervallo di date delle attività visualizzate
- Se si fa clic su una riga specifica nell'elenco, una casella contestuale a destra visualizzerà gli attributi aggiuntivi associati all'attività
- Fare clic su **Scarica** per scaricare le attività come file CSV

##<a name="accessing-audit-logs-through-the-azure-ad-reporting-api"></a>Accesso ai log di controllo tramite l'API di creazione di report di Azure AD
I log di controllo vengono pubblicati nella stessa pipeline delle altre attività di Azure Active Directory e quindi sono accessibili tramite l'[API di creazione di report di Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-api-audit-reference). 

###<a name="prerequisites"></a>prerequisiti
Per l'autenticazione con l'API di creazione di report di Azure AD, è prima necessario registrare un'applicazione. Assicurarsi di seguire i passaggi nei [prerequisiti per l'accesso alle API per la creazione di report di Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/).

###<a name="accesing-the-api"></a>Accesso all'API
Per scaricare i log di controllo di Azure AD B2C tramite l'API, sarà necessario filtrare i log nella categoria **B2C**. Per filtrare in base alla categoria, usare il parametro della stringa di query quando si chiama l'endpoint API di creazione di report di Azure AD, come illustrato di seguito:

`https://graph.windows.net/your-b2c-tentant.onmicrosoft.com/activities/audit?api-version=beta&$filter=category eq 'B2C'`

###<a name="powershell-script"></a>Script di PowerShell
Lo script seguente fornisce un esempio dell'uso di PowerShell per eseguire una query sull'API di creazione di report di Azure AD e archiviare i risultati come file JSON:

```powershell
# This script will require registration of a Web Application in Azure Active Directory (see https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/)

# Constants
$ClientID       = "your-client-application-id-here"       # Insert your application's Client ID, a Globally Unique ID (registered by Global Admin)
$ClientSecret   = "your-client-application-secret-here"   # Insert your application's Client Key/Secret string
$loginURL       = "https://login.microsoftonline.com"     
$tenantdomain   = "your-b2c-tenant.onmicrosoft.com"       # AAD B2C Tenant; for example, contoso.onmicrosoft.com
$resource       = "https://graph.windows.net"             # Azure AD Graph API resource URI
$7daysago       = "{0:s}" -f (get-date).AddDays(-7) + "Z" # Use 'AddMinutes(-5)' to decrement minutes, for example
Write-Output "Searching for events starting $7daysago"

# Create HTTP header, get an OAuth2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

# Parse audit report items, save output to file(s): auditX.json, where X = 0 thru n for number of nextLink pages
if ($oauth.access_token -ne $null) {   
    $i=0
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
    $url = 'https://graph.windows.net/' + $tenantdomain + '/activities/audit?api-version=beta&$filter=category eq ''B2C''and activityDate gt ' + $7daysago 

    # loop through each query page (1 through n)
    Do{
        # display each event on the console window
        Write-Output "Fetching data using Uri: $url"
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output ($event | ConvertTo-Json)
        }

        # save the query page to an output file
        Write-Output "Save the output to a file audit$i.json"
        $myReport.Content | Out-File -FilePath audit$i.json -Force
        $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'
        $i = $i+1
    } while($url -ne $null)
} else {
    Write-Host "ERROR: No Access Token"
}
```

