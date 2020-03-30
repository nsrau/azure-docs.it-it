---
title: Accedere ai registri di controllo ed esaminarli
titleSuffix: Azure AD B2C
description: Come accedere ai log di controllo B2C di Azure AD a livello di codice e nel portale di Azure.How to access Azure AD B2C audit logs programmatically and in the Azure portal.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 02/20/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 83086fa2cb96eba423b9111134a0406d7256821f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264219"
---
# <a name="accessing-azure-ad-b2c-audit-logs"></a>Accesso ai log di controllo di Azure AD B2C

Azure Active Directory B2C (Azure AD B2C) genera log di controllo contenenti informazioni sulle attività relative a risorse B2C, token rilasciati e accesso come amministratore. Questo articolo offre una breve panoramica delle informazioni disponibili nei log di controllo e istruzioni su come accedere a questi dati per il tenant B2C di Azure AD.

Gli eventi del registro di controllo vengono conservati solo per **sette giorni.** Se è necessario conservarli per un periodo più lungo, pianificare il download e l'archiviazione dei log con uno dei metodi illustrati sotto.

> [!NOTE]
> Non è possibile visualizzare gli accessi utente per singole applicazioni B2C di Azure AD nella sezione Utenti delle pagine **di Azure Active Directory** o Azure AD B2C nel portale di Azure.You can't see user sign-ins for individual Azure AD B2C applications under the **Users** section of the Azure Active Directory or Azure AD **B2C** pages in the Azure portal. Gli eventi di accesso mostrano l'attività dell'utente, ma non possono essere correlati all'applicazione B2C a cui l'utente ha eseguito l'accesso. È necessario utilizzare i registri di controllo per questo, come spiegato più avanti in questo articolo.

## <a name="overview-of-activities-available-in-the-b2c-category-of-audit-logs"></a>Panoramica delle attività disponibili nella categoria B2C dei log di controllo

La categoria **B2C** nei log di controllo contiene i tipi di attività seguenti:

|Tipo di attività |Descrizione  |
|---------|---------|
|Autorizzazione |Attività relative all'autorizzazione di un utente ad accedere alle risorse B2C (ad esempio, un amministratore che accede a un elenco di criteri B2C).         |
|Directory |Attività correlate agli attributi di directory recuperati quando un amministratore accede tramite il portale di Azure.Activities related to directory attributes retrieved when an administrator signs in using the Azure portal. |
|Applicazione | Creare, leggere, aggiornare ed eliminare (CRUD) operazioni sulle applicazioni B2C. |
|Chiave |Operazioni CRUD sulle chiavi archiviate in un contenitore di chiavi B2C. |
|Risorsa |operazioni CRUD sulle risorse B2C. Ad esempio, criteri e provider di identità.
|Authentication |Convalida delle credenziali utente e rilascio di token.|

Per le attività CRUD sugli oggetti utente, vedere la categoria **Directory principale**.

## <a name="example-activity"></a>Attività di esempio

This example image from the Azure portal shows the data captured when a user signs in with an external identity provider, in this case, Facebook:

![Esempio di pagina Dettagli attività log di controllo nel portale di AzureExample of Audit Log Activity Details page in Azure portal](./media/view-audit-logs/audit-logs-example.png)

Il pannello dei dettagli dell'attività contiene le seguenti informazioni pertinenti:

|Sezione|Campo|Descrizione|
|-------|-----|-----------|
| Attività | Nome | L'attività ha avuto luogo. Ad esempio, *Emettere un id_token all'applicazione*, che conclude l'accesso effettivo dell'utente. |
| Azione avviata da (attore) | ObjectId | **ID oggetto** dell'applicazione B2C a cui l'utente sta effettuando l'accesso. Questo identificatore non è visibile nel portale di Azure, ma è accessibile tramite l'API Microsoft Graph.This identifier is not visible in the Azure portal, but is accessible via the Microsoft Graph API. |
| Azione avviata da (attore) | Spn | **ID applicazione** dell'applicazione B2C a cui l'utente sta effettuando l'accesso. |
| Destinazione/i | ObjectId | **ID oggetto** dell'utente che esegue l'accesso. |
| Altri dettagli | TenantId | **ID tenant** del tenant B2C di Azure AD. |
| Altri dettagli | PolicyId | **ID criterio** del flusso utente utilizzato per l'accesso. |
| Altri dettagli | ApplicationId | **ID applicazione** dell'applicazione B2C a cui l'utente sta effettuando l'accesso. |

## <a name="view-audit-logs-in-the-azure-portal"></a>Visualizzare i log di controllo nel portale di AzureView audit logs in the Azure portal

Il portale di Azure fornisce l'accesso agli eventi del log di controllo nel tenant B2C di Azure AD.

1. Accedere al portale di [AzureSign](https://portal.azure.com) in to the Azure portal
1. Passare alla directory che contiene il tenant B2C di Azure AD e quindi passare ad **Azure AD B2C.**
1. In **Attività** nel menu a sinistra selezionare **Registri di controllo**.

Viene visualizzato un elenco di eventi attività registrati negli ultimi sette giorni.

![Filtro di esempio con due eventi di attività nel portale di AzureExample filter with two activity events in Azure portal](./media/view-audit-logs/audit-logs-example-filter.png)

Sono disponibili diverse opzioni di filtro, tra cui:

* **Tipo di risorsa attività:** filtrare in base ai tipi di attività illustrati nella tabella nella sezione [Panoramica delle attività disponibili.](#overview-of-activities-available-in-the-b2c-category-of-audit-logs)
* **Data:** consente di filtrare l'intervallo di date delle attività visualizzate.

Se si seleziona una riga nell'elenco, vengono visualizzati i dettagli dell'attività per l'evento.

Per scaricare l'elenco degli eventi di attività in un file con valori delimitati da virgole (CSV), selezionare **Scarica**.

## <a name="get-audit-logs-with-the-azure-ad-reporting-api"></a>Ottenere i log di controllo con l'API di creazione di report di Azure ADGet audit logs with the Azure AD reporting API

I log di controllo vengono pubblicati nella stessa pipeline delle altre attività di Azure Active Directory e quindi sono accessibili tramite l'[API di creazione di report di Azure Active Directory](https://docs.microsoft.com/graph/api/directoryaudit-list). Per altre informazioni, vedere [Introduzione all'API](../active-directory/reports-monitoring/concept-reporting-api.md)per la creazione di report di Azure Active Directory.

### <a name="enable-reporting-api-access"></a>Abilitare l'accesso all'API di creazione di reportEnable reporting

Per consentire l'accesso basato su script o applicazione all'API di creazione di report di Azure AD, è necessaria un'applicazione registrata nel tenant B2C di Azure AD con le autorizzazioni API seguenti. È possibile abilitare queste autorizzazioni per la registrazione di un'applicazione esistente all'interno del tenant B2C o crearne una nuova in modo specifico per l'utilizzo con l'automazione del log di controllo.

* Autorizzazioni dell'applicazione di > di Microsoft Graph > AuditLog > AuditLog.Read.All

Seguire i passaggi nel seguente articolo per registrare un'applicazione con le autorizzazioni necessarie:

[Gestire Azure AD B2C con Microsoft GraphManage Azure AD B2C with Microsoft Graph](microsoft-graph-get-started.md)

Dopo aver registrato un'applicazione con le autorizzazioni appropriate, vedere la sezione Script di PowerShell più avanti in questo articolo per un esempio di come è possibile ottenere eventi di attività con uno script.

### <a name="access-the-api"></a>Accedere all'API

Per scaricare gli eventi del log di controllo B2C `B2C` di Azure AD tramite l'API, filtrare i log in base alla categoria. Per filtrare in `filter` base alla categoria, usare il parametro della stringa di query quando si chiama l'endpoint dell'API di report di Azure AD.

```HTTP
https://graph.microsoft.com/v1.0/auditLogs/directoryAudits?$filter=loggedByService eq 'B2C' and activityDateTime gt 2019-09-10T02:28:17Z
```

### <a name="powershell-script"></a>Script di PowerShell

Lo script di PowerShell seguente mostra un esempio di come eseguire query sull'API di creazione di report di Azure AD. Dopo aver eseguito una query sull'API, gli eventi registrati vengono stampati nell'output standard, quindi scrive l'output JSON in un file.

È possibile provare questo script in [Azure Cloud Shell.](overview.md) Assicurarsi di aggiornarlo con l'ID applicazione, il segreto client e il nome del tenant B2C di Azure AD.

```powershell
# This script requires an application registration that's granted Microsoft Graph API permission
# https://docs.microsoft.com/azure/active-directory-b2c/microsoft-graph-get-started

# Constants
$ClientID       = "your-client-application-id-here"       # Insert your application's client ID, a GUID
$ClientSecret   = "your-client-application-secret-here"   # Insert your application's client secret
$tenantdomain   = "your-b2c-tenant.onmicrosoft.com"       # Insert your Azure AD B2C tenant domain name

$loginURL       = "https://login.microsoftonline.com"
$resource       = "https://graph.microsoft.com"           # Microsoft Graph API resource URI
$7daysago       = "{0:s}" -f (get-date).AddDays(-7) + "Z" # Use 'AddMinutes(-5)' to decrement minutes, for example
Write-Output "Searching for events starting $7daysago"

# Create HTTP header, get an OAuth2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

# Parse audit report items, save output to file(s): auditX.json, where X = 0 through n for number of nextLink pages
if ($oauth.access_token -ne $null) {
    $i=0
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
    $url = "https://graph.microsoft.com/v1.0/auditLogs/directoryAudits?`$filter=loggedByService eq 'B2C' and activityDateTime gt  " + $7daysago

    # loop through each query page (1 through n)
    Do {
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

Ecco la rappresentazione JSON dell'evento di attività di esempio illustrato in precedenza nell'articolo:Here's the JSON representation of the example activity event shown earlier in the article:

```JSON
{
    "id": "B2C_DQO3J_4984536",
    "category": "Authentication",
    "correlationId": "00000000-0000-0000-0000-000000000000",
    "result": "success",
    "resultReason": "N/A",
    "activityDisplayName": "Issue an id_token to the application",
    "activityDateTime": "2019-09-14T18:13:17.0618117Z",
    "loggedByService": "B2C",
    "operationType": "",
    "initiatedBy": {
        "user": null,
        "app": {
            "appId": "00000000-0000-0000-0000-000000000000",
            "displayName": null,
            "servicePrincipalId": null,
            "servicePrincipalName": "00000000-0000-0000-0000-000000000000"
        }
    },
    "targetResources": [
        {
            "id": "00000000-0000-0000-0000-000000000000",
            "displayName": null,
            "type": "User",
            "userPrincipalName": null,
            "groupType": null,
            "modifiedProperties": []
        }
    ],
    "additionalDetails": [
        {
            "key": "TenantId",
            "value": "test.onmicrosoft.com"
        },
        {
            "key": "PolicyId",
            "value": "B2C_1A_signup_signin"
        },
        {
            "key": "ApplicationId",
            "value": "00000000-0000-0000-0000-000000000000"
        },
        {
            "key": "Client",
            "value": "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.132 Safari/537.36"
        },
        {
            "key": "IdentityProviderName",
            "value": "facebook"
        },
        {
            "key": "IdentityProviderApplicationId",
            "value": "0000000000000000"
        },
        {
            "key": "ClientIpAddress",
            "value": "127.0.0.1"
        }
    ]
}
```

## <a name="next-steps"></a>Passaggi successivi

È possibile automatizzare altre attività di amministrazione, ad esempio [gestire gli account utente di Azure AD B2C con Microsoft Graph.](manage-user-accounts-graph-api.md)
