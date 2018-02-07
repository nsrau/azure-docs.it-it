---
title: Eseguire l'autenticazione con le API REST di Mobile Engagement
description: Descrive come eseguire l'autenticazione con le API REST di Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: da82cb36-957a-4e19-a805-b44733cf6597
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 10/05/2016
ms.author: wesmc;ricksal
ms.openlocfilehash: 574e699a1cfca2caef0cf20872570bbb8650117b
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2018
---
# <a name="authenticate-with-mobile-engagement-rest-apis"></a>Eseguire l'autenticazione con le API REST di Mobile Engagement

## <a name="overview"></a>Panoramica

Questo documento descrive come ottenere un token OAuth di Azure Active Directory (Azure AD) per eseguire l'autenticazione con le API REST di Mobile Engagement.

La procedura presuppone che l'utente abbia una sottoscrizione di Azure valida e che abbia creato un'app Mobile Engagement usando una delle [esercitazioni per sviluppatori](mobile-engagement-windows-store-dotnet-get-started.md).

## <a name="authentication"></a>Authentication

È necessario usare per l'autenticazione un token OAuth basato su Microsoft Azure Active Directory. 

Per autenticare richieste API, è necessario aggiungere un'intestazione di autorizzazione a ciascuna richiesta. L'intestazione di autorizzazione è nel formato seguente:

    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGmJlNmV2ZWJPamg2TTNXR1E...

> [!NOTE]
> I token di Azure Active Directory scadono in un'ora.
> 
> 

È possibile ottenere un token in diversi modi: Dal momento che le API vengono chiamate da un servizio cloud, si vorrà usare una chiave API. Nella terminologia di Azure una chiave API è una password dell'entità servizio. La procedura seguente descrive come impostarla in modo manuale.

### <a name="one-time-setup-using-a-script"></a>Configurazione una tantum (mediante script)

Per eseguire la configurazione usando uno script di PowerShell, eseguire i passaggi nelle istruzioni seguenti. Uno script di PowerShell richiede il minor tempo, ma usa le impostazioni predefinite più ammissibili. 

Facoltativamente, è anche possibile seguire le istruzioni per la [configurazione manuale](mobile-engagement-api-authentication-manual.md) per eseguire questa operazione direttamente dal portale di Azure. In questo caso, si può eseguire una configurazione più precisa.

1. Usare la versione più recente di Azure PowerShell che può essere scaricata [qui](http://aka.ms/webpi-azps). Per altre informazioni sulle istruzioni di download, vedere questa [panoramica](/powershell/azure/overview).

2. Dopo aver installato PowerShell, usare i comandi seguenti per assicurarsi che il **modulo di Azure** sia installato:

    a. Assicurarsi che il modulo Azure PowerShell sia presente nell'elenco dei moduli disponibili.

        Get-Module –ListAvailable

    ![Moduli di Azure disponibili][1]

    b. Se il modulo Azure PowerShell non è presente nell'elenco precedente, eseguire il comando seguente:

        Import-Module Azure
3. Accedere ad Azure Resource Manager da PowerShell eseguendo il comando seguente. Immettere nome utente e password per l'account Azure: 

        Login-AzureRmAccount
4. Se si hanno più sottoscrizioni, procedere come segue:

    a. Ottenere un elenco di tutte le proprie sottoscrizioni. Quindi copiare il valore **SubscriptionId** della sottoscrizione che si vuole usare. Verificare che la sottoscrizione abbia l'app Mobile Engagement. Si userà questa app per interagire con le API. 

        Get-AzureRmSubscription

    b. Eseguire il comando indicato di seguito. Fornire il **SubscriptionId** per configurare la sottoscrizione da usare:

        Select-AzureRmSubscription –SubscriptionId <subscriptionId>
5. Copiare il testo per lo script [New-AzureRmServicePrincipalOwner.ps1](https://raw.githubusercontent.com/matt-gibbs/azbits/master/src/New-AzureRmServicePrincipalOwner.ps1) nel computer locale. Salvarlo come cmdlet di PowerShell (ad esempio `APIAuth.ps1`) ed eseguirlo.

         `.\APIAuth.ps1`.

6. Lo script richiederà di immettere un **principalName**. Fornire un nome adeguato da usare per creare l'applicazione Active Directory (ad esempio APIAuth). 

7. Al termine dell'esecuzione dello script, vengono visualizzati quattro valori. Assicurarsi di copiarli, perché sono necessari per eseguire l'autenticazione a livello di programmazione con Active Directory: 

   - **TenantId**
   - **SubscriptionId**
   - **ApplicationId**
   - **Segreto**

   TenantId sarà usato come `{TENANT_ID}`, ApplicationId come `{CLIENT_ID}` e Secret come `{CLIENT_SECRET}`.

   > [!NOTE]
   > I criteri di sicurezza predefiniti possono bloccare l'esecuzione degli script di PowerShell. In questo caso, usare il comando seguente per configurare temporaneamente i criteri di esecuzione in modo da consentire l'esecuzione degli script:
   > 
   > Set-ExecutionPolicy RemoteSigned
8. Ecco l'aspetto del set di cmdlet di PowerShell.
    ![Cmdlet di PowerShell][3]
9. Nel portale di Azure passare ad Active Directory, selezionare **Registrazioni per l'app** e quindi cercare l'app per verificare che esista.
    ![Cercare l'app][4]

### <a name="steps-to-get-a-valid-token"></a>Procedura per ottenere un token valido

1. Chiamare l'API con i parametri seguenti. Assicurarsi di sostituire **TENANT\_ID**, **CLIENT\_ID** e **CLIENT\_SECRET**:
   
   * **URL della richiesta** come `https://login.microsoftonline.com/{TENANT_ID}/oauth2/token`

   * **Intestazione Content-Type HTTP** come `application/x-www-form-urlencoded`
   
   * **Corpo della richiesta HTTP** come `grant_type=client\_credentials&client_id={CLIENT_ID}&client_secret={CLIENT_SECRET}&resource=https%3A%2F%2Fmanagement.core.windows.net%2F`
     
    Di seguito è riportata una richiesta di esempio:
    ```
    POST /{TENANT_ID}/oauth2/token HTTP/1.1
    Host: login.microsoftonline.com
    Content-Type: application/x-www-form-urlencoded
    grant_type=client_credentials&client_id={CLIENT_ID}&client_secret={CLIENT_SECRET}&reso
    urce=https%3A%2F%2Fmanagement.core.windows.net%2F
    ```
    Di seguito è riportata una risposta di esempio:
    ```
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Content-Length: 1234

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1445395811","not_before":"144
    5391911","resource":"https://management.core.windows.net/","access_token":{ACCESS_TOKEN}}
    ```
     Questo esempio include la codifica URL dei parametri POST, in cui il valore `resource` effettivo è `https://management.core.windows.net/`. Prestare attenzione anche alla codifica URL di `{CLIENT_SECRET}` perché può contenere caratteri speciali.

     > [!NOTE]
     > Per i test, è possibile usare uno strumento client HTTP come [Fiddler](http://www.telerik.com/fiddler) o [l'estensione Chrome Postman](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop). 
     > 
     > 
2. Ora in ogni chiamata all'API includere l'intestazione della richiesta di autorizzazione:
   
        Authorization: Bearer {ACCESS_TOKEN}
   
    Se la richiesta restituisce un codice di stato 401, controllare il corpo della risposta. Potrebbe indicare che il token è scaduto. In caso affermativo, ottenere un nuovo token.

## <a name="use-the-apis"></a>Usare le API
Ora che si dispone di un token valido, è possibile eseguire chiamate API.

1. In ogni richiesta API, è necessario passare un token valido e non scaduto. Si è ottenuto il token non scaduto nella sezione precedente.

2. Inserire alcuni parametri nell'URI della richiesta che identifica l'applicazione. L'URI della richiesta è simile al codice seguente:
   
        https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/
        providers/Microsoft.MobileEngagement/appcollections/{app-collection}/apps/{app-resource-name}/
   
    Per ottenere i parametri, selezionare il nome dell'applicazione. Quindi, selezionare **Dashboard**. Viene visualizzata una pagina con tutti i tre parametri, come illustrato di seguito:
   
   * **1**`{subscription-id}`
   * **2**`{app-collection}`
   * **3**`{app-resource-name}`
   * **4**Il nome del gruppo di risorse sarà **MobileEngagement** a meno che non ne sia stato creato uno nuovo. 

> [!NOTE]
> Ignorare l'indirizzo radice dell'API, in quanto si riferisce alle API precedenti.
> 
> Se l'app è stata creata usando il portale di Azure, è necessario usare il nome risorsa dell'applicazione, che è diverso dal nome dell'app stessa. Se l'app è stata creata tramite il portale di Azure, usare il nome dell'app. Il nome risorsa dell'applicazione non differisce dal nome dell'app per le applicazioni create nel nuovo portale.
> 
> 

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication/azure-module.png
[2]: ./media/mobile-engagement-api-authentication/mobile-engagement-api-uri-params.png
[3]: ./media/mobile-engagement-api-authentication/ps-cmdlets.png
[4]: ./media/mobile-engagement-api-authentication/search-app.png



