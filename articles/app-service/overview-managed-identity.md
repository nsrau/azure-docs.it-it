---
title: Panoramica delle identità gestite - Servizio app di Azure | Microsoft Docs
description: Informazioni di riferimento teoriche e guida alla configurazione per le identità gestite in Servizio app di Azure e Funzioni di Azure
services: app-service
author: mattchenderson
manager: cfowler
editor: ''
ms.service: app-service
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/20/2018
ms.author: mahender
ms.openlocfilehash: 413473b856d76f9ebeff9669eb1facc54d89b509
ms.sourcegitcommit: ba9f95cf821c5af8e24425fd8ce6985b998c2982
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/17/2019
ms.locfileid: "54382530"
---
# <a name="how-to-use-managed-identities-for-app-service-and-azure-functions"></a>Come usare le identità gestite nel servizio app e in Funzioni di Azure

> [!NOTE] 
> Il supporto dell'identità gestita per Servizio app in Linux e in App Web per contenitori è attualmente in anteprima.

> [!Important] 
> Le identità gestite per il servizio app e per Funzioni di Azure non funzionano come previsto se viene eseguita la migrazione dell'app tra sottoscrizioni/tenant. L'app dovrà ottenere una nuova identità disabilitando e abilitando di nuovo la funzionalità. Vedere [Rimozione di un'identità](#remove) più avanti. Per usare la nuova identità, anche le risorse a valle dovranno disporre di criteri di accesso aggiornati.

Questo argomento illustra come creare un'identità gestita per le applicazioni del servizio app e di Funzioni di Azure e come usarla per accedere ad altre risorse. Un'identità gestita di Azure Active Directory consente all'app di accedere facilmente ad altre risorse protette da AAD, come Azure Key Vault. L'identità viene gestita dalla piattaforma Azure e non è necessario eseguire il provisioning o ruotare alcun segreto. Per altre informazioni sulle identità gestite in AAD, vedere [Identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md).

All'applicazione possono essere concessi due tipi di identità: 
- Un'**identità assegnata dal sistema** viene associata all'applicazione e viene eliminata in caso di eliminazione dell'app. A un'app può essere associata una sola identità assegnata dal sistema. Il supporto dell'identità assegnata dal sistema è disponibile a livello generale per le app di Windows. 
- Un'**identità assegnata dall'utente** è una risorsa di Azure autonoma che può essere assegnata all'app. Un'app può avere più identità assegnate dall'utente. Il supporto dell'identità assegnata dall'utente è in anteprima per tutti i tipi di app.

## <a name="adding-a-system-assigned-identity"></a>Aggiunta di un'identità assegnata dal sistema

La creazione di un'app con un'identità assegnata dal sistema richiede l'impostazione di una proprietà aggiuntiva nell'applicazione.

### <a name="using-the-azure-portal"></a>Uso del portale di Azure

Per configurare un'identità gestita nel portale, è prima necessario creare un'applicazione come di consueto e quindi abilitare la funzionalità.

1. Creare un'app nel portale come di consueto. Accedervi nel portale.

2. Se si usa un'app per le funzioni, passare a **Funzionalità della piattaforma**. Per altri tipi di app, scorrere verso il basso fino al gruppo **Impostazioni** nel riquadro di spostamento a sinistra.

3. Selezionare **Identità gestita**.

4. All'interno della scheda **Assegnata dal sistema** impostare **Stato** su **Attivato**. Fare clic su **Save**.

![Identità gestita nel servizio app](media/app-service-managed-service-identity/msi-blade-system.png)

### <a name="using-the-azure-cli"></a>Uso dell'interfaccia della riga di comando di Azure

Per configurare un'identità gestita usando l'interfaccia della riga di comando di Azure, è necessario usare il comando `az webapp identity assign` su un'applicazione esistente. Sono disponibili tre opzioni per l'esecuzione degli esempi di questa sezione:

- Usare [Azure Cloud Shell](../cloud-shell/overview.md) dal portale di Azure.
- Usare Azure Cloud Shell incorporato tramite il pulsante "Prova", che si trova nell'angolo in alto a destra di ogni blocco di codice indicato di seguito.
- [Installare la versione più recente dell'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.31 o successiva) se si preferisce usare una console dell'interfaccia della riga di comando locale. 

La procedura seguente consente di creare di un'app Web e assegnarle un'identità usando l'interfaccia della riga di comando:

1. Se si usa l'interfaccia della riga di comando di Azure in una console locale, accedere prima di tutto ad Azure tramite [az login](/cli/azure/reference-index#az-login). Usare un account associato alla sottoscrizione di Azure in cui si desidera distribuire l'applicazione:

    ```azurecli-interactive
    az login
    ```
2. Creare un'applicazione Web con l'interfaccia della riga di comando. Per altri esempi su come usare l'interfaccia della riga di comando con il Servizio app, vedere [Esempi dell'interfaccia della riga di comando del Servizio app](../app-service/samples-cli.md):

    ```azurecli-interactive
    az group create --name myResourceGroup --location westus
    az appservice plan create --name myPlan --resource-group myResourceGroup --sku S1
    az webapp create --name myApp --resource-group myResourceGroup --plan myPlan
    ```

3. Eseguire il comando `identity assign` per creare l'identità per l'applicazione:

    ```azurecli-interactive
    az webapp identity assign --name myApp --resource-group myResourceGroup
    ```

### <a name="using-azure-powershell"></a>Uso di Azure PowerShell

La procedura seguente consente di creare un'app Web e assegnarle un'identità tramite Azure PowerShell:

1. Se necessario, installare Azure PowerShell usando l'istruzione presente nella [Guida di Azure PowerShell](/powershell/azure/overview) e quindi eseguire `Login-AzureRmAccount` per creare una connessione con Azure.

2. Creare un'applicazione Web tramite Azure PowerShell. Per altri esempi su come usare Azure PowerShell con il Servizio app, vedere [Esempi di PowerShell del Servizio app](../app-service/samples-powershell.md):

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzureRmResourceGroup -Name myResourceGroup -Location $location
    
    # Create an App Service plan in Free tier.
    New-AzureRmAppServicePlan -Name $webappname -Location $location -ResourceGroupName myResourceGroup -Tier Free
    
    # Create a web app.
    New-AzureRmWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName myResourceGroup
    ```

3. Eseguire il comando `Set-AzureRmWebApp -AssignIdentity` per creare l'identità per l'applicazione:

    ```azurepowershell-interactive
    Set-AzureRmWebApp -AssignIdentity $true -Name $webappname -ResourceGroupName myResourceGroup 
    ```

### <a name="using-an-azure-resource-manager-template"></a>Uso di un modello di Azure Resource Manager

Per automatizzare la distribuzione delle risorse di Azure, è possibile usare un modello di Azure Resource Manager. Per altre informazioni sulla distribuzione nel Servizio App e in Funzioni, vedere [Automating resource deployment in App Service](../app-service/deploy-complex-application-predictably.md) (Automatizzare la distribuzione delle risorse nel Servizio App) e [Automatizzare la distribuzione di risorse per l'app per le funzioni in Funzioni di Azure](../azure-functions/functions-infrastructure-as-code.md).

Qualsiasi risorsa di tipo `Microsoft.Web/sites` può essere creata con un'identità, includendo la seguente proprietà nella definizione della risorsa:
```json
"identity": {
    "type": "SystemAssigned"
}    
```

> [!NOTE] 
> Un'applicazione può avere contemporaneamente sia identità assegnate dal sistema che assegnate dall'utente. In questo caso, la proprietà `type` sarebbe `SystemAssigned,UserAssigned`

L'aggiunta del tipo assegnato dal sistema indica ad Azure di creare e gestire l'identità per l'applicazione.

Ad esempio, un app Web può essere simile alla seguente:
```json
{
    "apiVersion": "2016-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('appName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "SystemAssigned"
    },
    "properties": {
        "name": "[variables('appName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "hostingEnvironment": "",
        "clientAffinityEnabled": false,
        "alwaysOn": true
    },
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
    ]
}
```

Quando viene creato, il sito ha le proprietà aggiuntive seguenti:
```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

Dove `<TENANTID>` e `<PRINCIPALID>` vengono sostituiti con GUID. La proprietà tenantId identifica a quale tenant AAD appartiene l'identità. La proprietà principalId è un identificatore univoco per la nuova identità dell'applicazione. In AAD, l'entità servizio ha lo stesso nome assegnato all'istanza di Servizio App o Funzioni di Azure.


## <a name="adding-a-user-assigned-identity-preview"></a>Aggiunta di un'identità assegnata dall'utente (anteprima)

> [!NOTE] 
> Le identità assegnate dall'utente sono attualmente in anteprima. I cloud sovrani non sono ancora supportati.

La creazione di un'app con un'identità assegnata dall'utente richiede la creazione dell'identità e quindi l'aggiunta dell'identificatore di risorsa corrispondente alla configurazione dell'app.

### <a name="using-the-azure-portal"></a>Uso del portale di Azure

> [!NOTE] 
> Questa esperienza del portale è in corso di distribuzione e potrebbe non essere ancora disponibile in tutte le aree.

Sarà prima di tutto necessario creare una risorsa identità assegnata dall'utente.

1. Seguire [queste istruzioni](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) per creare una risorsa identità gestita assegnata dall'utente.

2. Creare un'app nel portale come di consueto. Accedervi nel portale.

3. Se si usa un'app per le funzioni, passare a **Funzionalità della piattaforma**. Per altri tipi di app, scorrere verso il basso fino al gruppo **Impostazioni** nel riquadro di spostamento a sinistra.

4. Selezionare **Identità gestita**.

5. Nella scheda **Assegnate dall'utente (anteprima)** fare clic su **Aggiungi**.

6. Cercare l'identità creata in precedenza e selezionarla. Fare clic su **Aggiungi**.

![Identità gestita nel servizio app](media/app-service-managed-service-identity/msi-blade-user.png)

### <a name="using-an-azure-resource-manager-template"></a>Uso di un modello di Azure Resource Manager

Per automatizzare la distribuzione delle risorse di Azure, è possibile usare un modello di Azure Resource Manager. Per altre informazioni sulla distribuzione nel Servizio App e in Funzioni, vedere [Automating resource deployment in App Service](../app-service/deploy-complex-application-predictably.md) (Automatizzare la distribuzione delle risorse nel Servizio App) e [Automatizzare la distribuzione di risorse per l'app per le funzioni in Funzioni di Azure](../azure-functions/functions-infrastructure-as-code.md).

Qualsiasi risorsa di tipo `Microsoft.Web/sites` può essere creata con un'identità includendo il blocco seguente nella definizione della risorsa, sostituendo `<RESOURCEID>` con l'ID risorsa dell'identità desiderata:
```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}    
```

> [!NOTE] 
> Un'applicazione può avere contemporaneamente sia identità assegnate dal sistema che assegnate dall'utente. In questo caso, la proprietà `type` sarebbe `SystemAssigned,UserAssigned`

L'aggiunta del tipo assegnato dall'utente indica ad Azure di creare e gestire l'identità per l'applicazione.

Ad esempio, un app Web può essere simile alla seguente:
```json
{
    "apiVersion": "2016-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('appName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]": {}
        }
    },
    "properties": {
        "name": "[variables('appName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "hostingEnvironment": "",
        "clientAffinityEnabled": false,
        "alwaysOn": true
    },
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
    ]
}
```

Quando viene creato, il sito ha le proprietà aggiuntive seguenti:
```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {
            "principalId": "<PRINCIPALID>",
            "clientId": "<CLIENTID>"
        }
    }
}
```

Dove `<PRINCIPALID>` e `<CLIENTID>` vengono sostituiti con GUID. La proprietà principalId è un identificatore univoco per l'identità che viene usato per l'amministrazione di AAD. clientId è un identificatore univoco per la nuova identità dell'applicazione, che viene usato per specificare l'identità da usare durante le chiamate di runtime.


## <a name="obtaining-tokens-for-azure-resources"></a>Ottenimento di token per le risorse di Azure

Un'app può usare la propria identità per ottenere i token da altre risorse protette da AAD, ad esempio Azure Key Vault. Questi token rappresentano le applicazioni che accedono alla risorsa e non un utente specifico dell'applicazione. 

> [!IMPORTANT]
> Potrebbe essere necessario configurare la risorsa di destinazione per consentire l'accesso dall'applicazione. Ad esempio, se si richiede un token a Key Vault, è necessario verificare di aver aggiunto un criterio di accesso che include l'identità dell'applicazione. In caso contrario, le chiamate a Key Vault verranno rifiutate, anche se includono il token. Per altre informazioni sulle risorse che supportano i token di Azure Active Directory, vedere [Servizi di Azure che supportano l'autenticazione di Azure AD](../active-directory/managed-identities-azure-resources/services-support-msi.md#azure-services-that-support-azure-ad-authentication).

È disponibile un semplice protocollo REST per ottenere un token del Servizio App e di Funzioni di Azure. Per le applicazioni .NET, la libreria Microsoft.Azure.Services.AppAuthentication fornisce un'astrazione di questo protocollo e supporta un'esperienza di sviluppo locale.

### <a name="asal"></a>Uso della libreria Microsoft.Azure.Services.AppAuthentication per .NET

Per le funzioni e le applicazioni .NET, il modo più semplice per usare un'identità gestita consiste nell'usare il pacchetto Microsoft.Azure.Services.AppAuthentication. Questa raccolta consente anche di testare il codice in locale nel computer di sviluppo usando l'account utente da Visual Studio, dall'[interfaccia della riga di comando di Azure](/cli/azure) o tramite l'autenticazione integrata di Active Directory. Per altre informazioni sulle opzioni di sviluppo locale con questa libreria, vedere la [documentazione di riferimento della libreria Microsoft.Azure.Services.AppAuthentication]. In questa sezione viene illustrato come muovere i primi passi con la libreria nel codice.

1. Aggiungere un riferimento a [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) e a qualsiasi altro pacchetto NuGet necessario all'applicazione. L'esempio seguente usa anche [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault).

2. Aggiungere il codice seguente all'applicazione, modificandolo in base alla risorsa corretta. Questo esempio illustra due modalità d'uso di Azure Key Vault:

```csharp
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.KeyVault;
// ...
var azureServiceTokenProvider = new AzureServiceTokenProvider();
string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
// OR
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
```

Per altre informazioni su Microsoft.Azure.Services.AppAuthentication e sulle relative operazioni esposte, vedere la [documentazione di riferimento della libreria Microsoft.Azure.Services.AppAuthentication] e l'[esempio di servizio app e insieme di credenziali delle chiavi con MSI .NET](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet).

### <a name="using-the-rest-protocol"></a>Uso del protocollo API

Un'app con un'identità gestita ha due variabili di ambiente definite:

- MSI_ENDPOINT
- MSI_SECRET

**MSI_ENDPOINT** è un URL locale da cui l'app può richiedere token. Per ottenere un token per una risorsa, eseguire una richiesta HTTP GET a questo endpoint, includendo i parametri seguenti:

> |Nome parametro|In ingresso|DESCRIZIONE|
> |-----|-----|-----|
> |resource|Query|URI della risorsa AAD per cui è necessario ottenere un token. Può trattarsi di uno dei [servizi di Azure che supportano l'autenticazione di Azure AD](../active-directory/managed-identities-azure-resources/services-support-msi.md#azure-services-that-support-azure-ad-authentication) o di qualsiasi altro URI di risorsa.|
> |api-version|Query|Versione dell'API del token da usare. Al momento, l'unica versione supportata è: "2017-09-01".|
> |secret|Intestazione|Valore della variabile di ambiente MSI_SECRET.|
> |clientid|Query|(Facoltativo) L'ID dell'identità assegnata dall'utente da usare. Se omesso, viene usata l'identità assegnata dal sistema.|

Una risposta 200 OK con esito positivo include un corpo JSON con le proprietà seguenti:

> |Nome proprietà|DESCRIZIONE|
> |-------------|----------|
> |access_token|Token di accesso richiesto. Il servizio Web chiamante può usare questo token per l'autenticazione nel servizio Web ricevente.|
> |expires_on|Scadenza del token di accesso. La data è rappresentata come numero di secondi da 1970-01-01T0:0:0Z UTC fino alla scadenza. Questo valore viene usato per determinare la durata dei token memorizzati nella cache.|
> |resource|URI ID app del servizio Web ricevente.|
> |token_type|Indica il valore del tipo di token. L'unico tipo supportato da Azure AD è Bearer. Per altre informazioni sui bearer token, vedere [OAuth 2.0 Authorization Framework: Bearer Token Usage (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt) (Framework di autorizzazione di OAuth 2.0: uso dei bearer token - RFC 6750).|

Questa risposta è la stessa [risposta per la richiesta del token di accesso da servizio a servizio AAD](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md#service-to-service-access-token-response).

> [!NOTE]
> Le variabili di ambiente vengono configurate al primo avvio del processo, quindi dopo avere abilitato un'identità gestita per l'applicazione potrebbe essere necessario riavviare l'applicazione o ridistribuire il relativo codice per rendere disponibili `MSI_ENDPOINT` e `MSI_SECRET` per il codice.

### <a name="rest-protocol-examples"></a>Esempi di protocollo REST

Un esempio di richiesta potrebbe apparire come segue:

```
GET /MSI/token?resource=https://vault.azure.net&api-version=2017-09-01 HTTP/1.1
Host: localhost:4141
Secret: 853b9a84-5bfa-4b22-a3f3-0b9a43d9ad8a
```

E un esempio di risposta potrebbe apparire come segue:

```
HTTP/1.1 200 OK
Content-Type: application/json

{
    "access_token": "eyJ0eXAi…",
    "expires_on": "09/14/2017 00:00:00 PM +00:00",
    "resource": "https://vault.azure.net",
    "token_type": "Bearer"
}
```

### <a name="code-examples"></a>Esempi di codice

<a name="token-csharp"></a>Per eseguire questa richiesta in C#:

```csharp
public static async Task<HttpResponseMessage> GetToken(string resource, string apiversion)  {
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Add("Secret", Environment.GetEnvironmentVariable("MSI_SECRET"));
    return await client.GetAsync(String.Format("{0}/?resource={1}&api-version={2}", Environment.GetEnvironmentVariable("MSI_ENDPOINT"), resource, apiversion));
}
```

> [!TIP]
> Per i linguaggi .NET è anche possibile usare [Microsoft.Azure.Services.AppAuthentication](#asal) invece di creare la richiesta manualmente.

<a name="token-js"></a>In Node.JS:

```javascript
const rp = require('request-promise');
const getToken = function(resource, apiver, cb) {
    var options = {
        uri: `${process.env["MSI_ENDPOINT"]}/?resource=${resource}&api-version=${apiver}`,
        headers: {
            'Secret': process.env["MSI_SECRET"]
        }
    };
    rp(options)
        .then(cb);
}
```

<a name="token-powershell"></a>In PowerShell:

```powershell
$apiVersion = "2017-09-01"
$resourceURI = "https://<AAD-resource-URI-for-resource-to-obtain-token>"
$tokenAuthURI = $env:MSI_ENDPOINT + "?resource=$resourceURI&api-version=$apiVersion"
$tokenResponse = Invoke-RestMethod -Method Get -Headers @{"Secret"="$env:MSI_SECRET"} -Uri $tokenAuthURI
$accessToken = $tokenResponse.access_token
```

## <a name="remove"></a>Rimozione di un'identità

Un'identità assegnata dal sistema può essere rimossa disabilitando la funzionalità tramite il portale, PowerShell o l'interfaccia della riga di comando nello stesso modo in cui è stata creata. Le identità assegnate dall'utente possono essere rimosse separatamente. Per rimuovere tutte le identità, nel protocollo di modello REST/ARM, questa operazione viene eseguita impostando il tipo su "None":

```json
"identity": {
    "type": "None"
}
```

La rimozione di un'identità assegnata dal sistema in questo modo ne comporterà l'eliminazione anche da AAD. Le identità assegnate dal sistema vengono rimosse automaticamente anche da AAD quando viene eliminata la risorsa app.

> [!NOTE]
> È inoltre disponibile l'impostazione applicazione WEBSITE_DISABLE_MSI che, se attivata, disabilita il servizio token locale, L'identità non viene tuttavia disabilitata e negli strumenti continuerà a essere indicata come attivata o abilitata. Di conseguenza, l'uso di questa impostazione non è consigliato.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Accedere al database SQL in modo sicuro usando un'identità gestita](app-service-web-tutorial-connect-msi.md)

[Documentazione di riferimento della libreria Microsoft.Azure.Services.AppAuthentication]: https://go.microsoft.com/fwlink/p/?linkid=862452
