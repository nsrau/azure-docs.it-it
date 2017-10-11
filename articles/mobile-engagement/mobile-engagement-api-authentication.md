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
ms.openlocfilehash: b05181d9252c0a804648e01b4058019278ae5abe
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="authenticate-with-mobile-engagement-rest-apis"></a>Eseguire l'autenticazione con le API REST di Mobile Engagement
## <a name="overview"></a>Panoramica
Il presente documento descrive come ottenere un token Oauth AAD per eseguire l'autenticazione con le API REST di Mobile Engagement. 

Si presuppone che l'utente abbia una sottoscrizione di Azure valida e che abbia creato un'app Mobile Engagement usando una delle [Esercitazioni per sviluppatori](mobile-engagement-windows-store-dotnet-get-started.md).

## <a name="authentication"></a>Autenticazione
È necessario usare per l'autenticazione un token OAuth basato su Microsoft Azure Active Directory. 

Per autenticare richieste API, è necessario aggiungere un'intestazione di autorizzazione a ciascuna richiesta della seguente forma:

    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGmJlNmV2ZWJPamg2TTNXR1E...

> [!NOTE]
> I token di Azure Active Directory scadono in un'ora.
> 
> 

È possibile ottenere un token in diversi modi: Dal momento che le API vengono in genere chiamate da un servizio cloud, si vorrà usare una chiave API. Nella terminologia di Azure una chiave API è una password dell'entità servizio. La procedura seguente descrive come impostarla in modo manuale.

### <a name="one-time-setup-using-script"></a>Installazione singola (mediante script)
È necessario osservare le istruzioni seguenti per eseguire l'installazione tramite uno script di PowerShell che richiede il minor tempo ma che utilizza le impostazioni predefinite più ammissibili. Facoltativamente, è anche possibile seguire le istruzioni per [l'installazione manuale](mobile-engagement-api-authentication-manual.md) per eseguire questa operazione direttamente dal portale di Azure con una configurazione più precisa. 

1. Usare la versione più recente di Azure PowerShell che può essere scaricata [qui](http://aka.ms/webpi-azps). Per ulteriori informazioni sulle istruzioni di download, è possibile visualizzare questo [collegamento](/powershell/azure/overview).  
2. Dopo aver installato Azure PowerShell, usare i comandi seguenti per assicurarsi che il **modulo Azure** sia installato:
   
    a. Assicurarsi che il modulo Azure PowerShell sia presente nell'elenco dei moduli disponibili. 
   
        Get-Module –ListAvailable 
   
    ![Moduli di Azure disponibili][1]
   
    b. Se il modulo Azure PowerShell non è presente nell'elenco precedente, è necessario procedere come segue:
   
        Import-Module Azure 
3. Accedere ad Azure Resource Manager da PowerShell eseguendo il comando seguente e fornendo nome utente e password dell'account Azure: 
   
        Login-AzureRmAccount
4. In caso di più sottoscrizioni, procedere come segue:
   
    a. Ottenere un elenco di tutte le sottoscrizioni e copiare il SubscriptionId della sottoscrizione che si desidera utilizzare. Assicurarsi che la sottoscrizione corrisponda a quella dell'app Mobile Engagement con cui si andrà a interagire tramite le API. 
   
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
   > 
   > 
8. L'insieme di cmdlet PS sarà simile al seguente. 
   
    ![][3]
9. Controllare nel portale di gestione di Azure che sia stata creata una nuova applicazione AD con il nome specificato per lo script denominato **principalName** in **Mostra Applicazioni di proprietà dell'azienda**.
   
    ![][4]

#### <a name="steps-to-get-a-valid-token"></a>Procedura per ottenere un token valido
1. Chiamare l'API con i parametri seguenti e assicurarsi di sostituire TENANT\_ID, CLIENT\_ID e CLIENT\_SECRET:
   
   * **URL richiesta** come *https://login.microsoftonline.com/{TENANT\_ID}/oauth2/token*
   * **Intestazione Content-Type HTTP** come *application/x-www-form-urlencoded*
   * **Corpo richiesta HTTP** come *grant\_type=client\_credentials&client_id={CLIENT\_ID}&client_secret={CLIENT\_SECRET}&resource=https%3A%2F%2Fmanagement.core.windows.net%2F*
     
     Di seguito è riportata una richiesta di esempio:
     
       POST / {TENANT_ID} / oauth2/token Host HTTP/1.1: login.microsoftonline.com Content-Type: grant_type application/x-www-form-urlencoded = client_credentials & client_id = {CLIENT_ID} & client_secret = {CLIENT_SECRET} & reso urce=https%3A%2F%2Fmanagement.core.windows.net%2F
     
     Di seguito è riportata una risposta di esempio:
     
       HTTP/1.1 200 OK Content-Type: application/json; charset = utf-8. Content-Length: 1234
     
       {"token_type":"Bearer","expires_in":"3599","expires_on":"1445395811","not_before":"144   5391911","resource":"https://management.core.windows.net/","access_token":{ACCESS_TOKEN}}
     
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
   
   * **1** `{subscription-id}`
   * **2** `{app-collection}`
   * **3** `{app-resource-name}`
   * **4**Il nome del gruppo di risorse sarà **MobileEngagement** a meno che non ne sia stato creato uno nuovo. 
     
     ![Parametri URI API di Mobile Engagement][2]

> [!NOTE]
> <br/>
> 
> 1. Ignorare l'indirizzo radice dell'API perché riferito alle API precedenti.<br/>
> 2. Se l'app è stata creata tramite il portale di Azure classico, è necessario usare il nome della Risorsa applicazione, che è diverso dal nome dell'applicazione stessa. Se l'app è stata creata nel portale di Azure, è necessario usare il nome dell'applicazione stessa. In questo caso, il nome della Risorsa applicazione non differisce dal nome dell'app per le applicazioni create nel nuovo portale.  
> 
> 

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication/azure-module.png
[2]: ./media/mobile-engagement-api-authentication/mobile-engagement-api-uri-params.png
[3]: ./media/mobile-engagement-api-authentication/ps-cmdlets.png
[4]: ./media/mobile-engagement-api-authentication/ad-app-creation.png



