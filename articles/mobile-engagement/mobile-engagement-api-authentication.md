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
ms.openlocfilehash: 66bcd738b86f846eae3499b289a6629323009a44
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/05/2018
---
# <a name="authenticate-with-mobile-engagement-rest-apis"></a>Eseguire l'autenticazione con le API REST di Mobile Engagement

## <a name="overview"></a>Panoramica

Questo documento descrive come ottenere token per l'autenticazione con le API REST di Engagement Mobile un Oauth di Active Directory di Azure valido.

Si presuppone che si dispone di una sottoscrizione Azure valida e creata un'app di Mobile Engagement usando uno del [esercitazioni per sviluppatori](mobile-engagement-windows-store-dotnet-get-started.md).

## <a name="authentication"></a>Authentication

È necessario usare per l'autenticazione un token OAuth basato su Microsoft Azure Active Directory. 

Alla richiesta di autenticazione, un'API, è inoltre necessario aggiungere un'intestazione di autorizzazione per ogni richiesta, ovvero il formato seguente:

    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGmJlNmV2ZWJPamg2TTNXR1E...

> [!NOTE]
> I token di Azure Active Directory scadono entro un'ora.
> 
> 

È possibile ottenere un token in diversi modi: Poiché le API vengono chiamate da un servizio cloud, si desidera utilizzare una chiave API. Nella terminologia di Azure una chiave API è una password dell'entità servizio. La procedura seguente descrive come impostarla in modo manuale.

### <a name="one-time-setup-using-script"></a>Installazione singola (mediante script)

Seguire le istruzioni riportate di seguito per eseguire il programma di installazione tramite uno script di PowerShell, che accetta il tempo minimo per l'installazione, ma usa le impostazioni predefinite di massimo consentite. Facoltativamente, è anche possibile seguire le istruzioni per [l'installazione manuale](mobile-engagement-api-authentication-manual.md) per eseguire questa operazione direttamente dal portale di Azure con una configurazione più precisa.

1. Usare la versione più recente di Azure PowerShell che può essere scaricata [qui](http://aka.ms/webpi-azps). Per ulteriori informazioni sulle istruzioni di download, è possibile visualizzare questo [collegamento](/powershell/azure/overview).
2. Dopo aver installato Azure PowerShell, usare i comandi seguenti per assicurarsi che il **modulo Azure** sia installato:

    a. Assicurarsi che il modulo Azure PowerShell sia presente nell'elenco dei moduli disponibili.

        Get-Module –ListAvailable

    ![Moduli di Azure disponibili][1]

    b. Se non si trova il modulo PowerShell di Azure nell'elenco precedente, è necessario eseguire:

        Import-Module Azure
3. Effettuare l'accesso a Gestione risorse di Azure tramite PowerShell eseguendo il comando seguente e fornire il nome utente e la password per l'account di Azure: 

        Login-AzureRmAccount
4. Se si dispone di più sottoscrizioni, è consigliabile eseguire:

    a. Ottenere un elenco di tutte le sottoscrizioni e copiare il SubscriptionId della sottoscrizione che si desidera utilizzare. Verificare che la sottoscrizione corrisponde a quella che ha l'App Mobile Engagement, che si prevede di interagire con le API. 

        Get-AzureRmSubscription

    b. Eseguire il comando seguente specificando il SubscriptionId per configurare la sottoscrizione da usare.

        Select-AzureRmSubscription –SubscriptionId <subscriptionId>
5. Copiare il testo per lo script [New-AzureRmServicePrincipalOwner.ps1](https://raw.githubusercontent.com/matt-gibbs/azbits/master/src/New-AzureRmServicePrincipalOwner.ps1) nel computer locale, salvarlo come cmdlet PowerShell (ad esempio `APIAuth.ps1`) ed eseguirlo `.\APIAuth.ps1`.
6. Lo script richiederà di immettere un **principalName**. Fornire un nome adeguato da utilizzare per creare l'applicazione Active Directory (ad esempio APIAuth). 
7. Dopo il completamento dello script, accertarsi di copiare i quattro valori seguenti che verranno visualizzati, necessari per un'autenticazione a livello di codice con AD  

    **TenantId**, **SubscriptionId**, **ApplicationId** e **Secret**.

    TenantId sarà usato come `{TENANT_ID}`, ApplicationId come `{CLIENT_ID}` e Secret come `{CLIENT_SECRET}`.

   > [!NOTE]
   > I criteri di sicurezza predefiniti possono bloccare l'esecuzione degli script PowerShell. In questo caso, configurare temporaneamente i criteri di esecuzione in modo da consentire l'esecuzione degli script usando il comando seguente:
   > 
   > Set-ExecutionPolicy RemoteSigned
8. L'insieme di cmdlet PS sarà simile al seguente.
    ![][3]
9. Nel portale di Azure, accedere ad Active Directory, fare clic su **registrazioni di App** e cercare l'app per assicurarsi che esista![][4]

### <a name="steps-to-get-a-valid-token"></a>Procedura per ottenere un token valido

1. Chiamare l'API con i parametri seguenti e assicurarsi di sostituire TENANT\_ID, CLIENT\_ID e CLIENT\_SECRET:
   
   * **URL della richiesta** come`https://login.microsoftonline.com/{TENANT_ID}/oauth2/token`
   * **Intestazione Content-Type HTTP** come`application/x-www-form-urlencoded`
   * **Corpo della richiesta HTTP** come`grant_type=client\_credentials&client_id={CLIENT_ID}&client_secret={CLIENT_SECRET}&resource=https%3A%2F%2Fmanagement.core.windows.net%2F`
     
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
     Questo esempio include la codifica URL dei parametri POST. Il valore `resource` effettivo è `https://management.core.windows.net/`. Prestare attenzione anche alla codifica URL di `{CLIENT_SECRET}` perché può contenere caratteri speciali.

     > [!NOTE]
     > Per i test, è possibile usare uno strumento client HTTP come [Fiddler](http://www.telerik.com/fiddler) o [l'estensione Chrome Postman](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop) 
     > 
     > 
2. Ora in ogni chiamata all'API includere l'intestazione della richiesta di autorizzazione:
   
        Authorization: Bearer {ACCESS_TOKEN}
   
    Se viene restituito il codice di stato 401, rivedere il corpo della risposta e controllare che il token non sia scaduto. In caso affermativo, ottenere un nuovo token.

## <a name="using-the-apis"></a>Uso delle API
Ora che si dispone di un token valido, è possibile eseguire chiamate API.

1. È necessario passare in ciascuna richiesta API un token valido e non scaduto, ottenuto nella sezione precedente.
2. È necessario inserire alcuni parametri nella richiesta URI che identifica l'applicazione. L'URI di richiesta è simile al seguente:
   
        https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/
        providers/Microsoft.MobileEngagement/appcollections/{app-collection}/apps/{app-resource-name}/
   
    Per ottenere i parametri, fare clic sul nome dell'applicazione e quindi su Dashboard. Verrà visualizzata una pagina simile a quella riportata di seguito, con tutti e tre i parametri.
   
   * **1**`{subscription-id}`
   * **2**`{app-collection}`
   * **3**`{app-resource-name}`
   * **4**Il nome del gruppo di risorse sarà **MobileEngagement** a meno che non ne sia stato creato uno nuovo. 

> [!NOTE]
> <br/>
> 
> 1. Ignorare l'indirizzo radice dell'API perché riferito alle API precedenti.<br/>
> 2. Se è stato creato l'app usando il portale di Azure è necessario utilizzare il nome di risorsa dell'applicazione che è diverso dal nome dell'applicazione stessa. Se è stato creato l'app nel portale di Azure è necessario utilizzare il nome dell'applicazione stessa (non vi è alcuna differenza tra il nome di risorsa dell'applicazione e il nome dell'App per le app create nel nuovo portale).  
> 
> 

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication/azure-module.png
[2]: ./media/mobile-engagement-api-authentication/mobile-engagement-api-uri-params.png
[3]: ./media/mobile-engagement-api-authentication/ps-cmdlets.png
[4]: ./media/mobile-engagement-api-authentication/search-app.png



