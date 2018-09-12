---
title: Identità del servizio gestito in Servizio App e Funzioni di Azure | Microsoft Docs
description: Guida di riferimento teorico e programma di installazione per il supporto di Identità del servizio gestito nel servizio App di Azure e Funzioni di Azure
services: app-service
author: mattchenderson
manager: cfowler
editor: ''
ms.service: app-service
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 06/25/2018
ms.author: mahender
ms.openlocfilehash: c7a819f987de41ba7705d21bb6de95475cd3f9c8
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2018
ms.locfileid: "44027187"
---
# <a name="how-to-use-azure-managed-service-identity-in-app-service-and-azure-functions"></a>Come usare Identità del servizio gestita di Azure nel Servizio App e in Funzioni di Azure

> [!NOTE] 
> Il Servizio App su Linux e le app Web per contenitori attualmente non supportano Identità del servizio gestita.

> [!Important] 
> Identità del servizio gestita per Servizio app di Azure e Funzioni di Azure non funzioneranno come previsto se viene eseguita la migrazione dell'app tra sottoscrizioni/tenant. L'app dovrà ottenere una nuova identità disabilitando e abilitando di nuovo la funzionalità. Vedere [Rimozione di un'identità](#remove) più avanti. Per usare la nuova identità, anche le risorse a valle dovranno disporre di criteri di accesso aggiornati.

Questo argomento illustra come creare un'identità di applicazione gestita per le applicazioni di Servizio App e Funzioni di Azure e come usarla per accedere ad altre risorse. Un'identità del servizio gestito da Azure Active Directory consente all'app di accedere facilmente ad altre risorse protette da Azure come ad esempio Azure Key Vault. L'identità viene gestita dalla piattaforma Azure e non è necessario eseguire il provisioning o ruotare alcun segreto. Per altre informazioni sull'identità del servizio gestito, vedere il [Identità del servizio gestito per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="creating-an-app-with-an-identity"></a>Creazione di un'app con un'identità

La creazione di un'app con un'identità richiede l'impostazione di una proprietà aggiuntiva nell'applicazione.

### <a name="using-the-azure-portal"></a>Uso del portale di Azure

Per impostare un'identità del servizio gestito nel portale, è prima necessario creare un'applicazione come di consueto e quindi abilitare la funzionalità.

1. Creare un'app nel portale come di consueto. Accedervi nel portale.

2. Se si usa un'app per le funzioni, passare a **Funzionalità della piattaforma**. Per altri tipi di app, scorrere verso il basso fino al gruppo **Impostazioni** nel riquadro di spostamento a sinistra.

3. Selezionare **Managed service identity** (Identità servizio gestito).

4. Impostare **Register with Azure Active Directory** (Registra con Azure Active Directory) su **On**. Fare clic su **Save**.

![Identità del servizio gestito in Servizio app](media/app-service-managed-service-identity/msi-blade.png)

### <a name="using-the-azure-cli"></a>Uso dell'interfaccia della riga di comando di Azure

Per configurare un'identità del servizio gestito usando l'interfaccia della riga di comando di Azure, è necessario usare il comando `az webapp identity assign` sull'applicazione esistente. Sono disponibili tre opzioni per l'esecuzione degli esempi di questa sezione:

- Usare [Azure Cloud Shell](../cloud-shell/overview.md) dal portale di Azure.
- Usare Azure Cloud Shell incorporato tramite il pulsante "Prova", che si trova nell'angolo in alto a destra di ogni blocco di codice indicato di seguito.
- [Installare la versione più recente dell'interfaccia della riga di comando 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.31 o successiva) se si preferisce usare una console dell'interfaccia della riga di comando locale. 

La procedura seguente consente di creare di un'app Web e assegnarle un'identità usando l'interfaccia della riga di comando:

1. Se si usa l'interfaccia della riga di comando di Azure in una console locale, accedere prima di tutto ad Azure tramite [az login](/cli/azure/reference-index#az-login). Usare un account associato alla sottoscrizione di Azure in cui si desidera distribuire l'applicazione:

    ```azurecli-interactive
    az login
    ```
2. Creare un'applicazione Web con l'interfaccia della riga di comando. Per altri esempi su come usare l'interfaccia della riga di comando con il Servizio app, vedere [Esempi dell'interfaccia della riga di comando del Servizio app](../app-service/app-service-cli-samples.md):

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

2. Creare un'applicazione Web tramite Azure PowerShell. Per altri esempi su come usare Azure PowerShell con il Servizio app, vedere [Esempi di PowerShell del Servizio app](../app-service/app-service-powershell-samples.md):

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzureRmResourceGroup -Name myResourceGroup -Location $location
    
    # Create an App Service plan in Free tier.
    New-AzureRmAppServicePlan -Name $webappname -Location $location -ResourceGroupName myResourceGroup -Tier Free
    
    # Create a web app.
    New-AzureRmWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName myResourceGroup
    ```

3. Eseguire il comando `identity assign` per creare l'identità per l'applicazione:

    ```azurepowershell-interactive
    Set-AzureRmWebApp -AssignIdentity $true -Name $webappname -ResourceGroupName myResourceGroup 
    ```

### <a name="using-an-azure-resource-manager-template"></a>Uso di un modello di Azure Resource Manager

Per automatizzare la distribuzione delle risorse di Azure, è possibile usare un modello di Azure Resource Manager. Per altre informazioni sulla distribuzione nel Servizio App e in Funzioni, vedere [Automating resource deployment in App Service](../app-service/app-service-deploy-complex-application-predictably.md) (Automatizzare la distribuzione delle risorse nel Servizio App) e [Automatizzare la distribuzione di risorse per l'app per le funzioni in Funzioni di Azure](../azure-functions/functions-infrastructure-as-code.md).

Qualsiasi risorsa di tipo `Microsoft.Web/sites` può essere creata con un'identità, includendo la seguente proprietà nella definizione della risorsa:
```json
"identity": {
    "type": "SystemAssigned"
}    
```

Questa proprietà indica ad Azure di creare e gestire l'identità dell'applicazione.

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
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

Dove `<TENANTID>` e `<PRINCIPALID>` vengono sostituiti con GUID. La proprietà tenantId identifica a quale tenant AAD appartiene l'identità. La proprietà principalId è un identificatore univoco per la nuova identità dell'applicazione. In AAD, l'entità servizio ha lo stesso nome assegnato all'istanza di Servizio App o Funzioni di Azure.

## <a name="obtaining-tokens-for-azure-resources"></a>Ottenimento di token per le risorse di Azure

Un'app può usare la propria identità per ottenere i token da altre risorse protette da AAD, ad esempio Azure Key Vault. Questi token rappresentano le applicazioni che accedono alla risorsa e non un utente specifico dell'applicazione. 

> [!IMPORTANT]
> Potrebbe essere necessario configurare la risorsa di destinazione per consentire l'accesso dall'applicazione. Ad esempio, se si richiede un token a Key Vault, è necessario verificare di aver aggiunto un criterio di accesso che include l'identità dell'applicazione. In caso contrario, le chiamate a Key Vault verranno rifiutate, anche se includono il token. Per altre informazioni sulle risorse che supportano i token di Identità del servizio gestito, vedere [Servizi di Azure che supportano l'autenticazione di Azure AD](../active-directory/managed-identities-azure-resources/services-support-msi.md#azure-services-that-support-azure-ad-authentication).

È disponibile un semplice protocollo REST per ottenere un token del Servizio App e di Funzioni di Azure. Per le applicazioni .NET, la libreria Microsoft.Azure.Services.AppAuthentication fornisce un'astrazione di questo protocollo e supporta un'esperienza di sviluppo locale.

### <a name="asal"></a>Uso della libreria Microsoft.Azure.Services.AppAuthentication per .NET

Per le funzioni e le applicazioni .NET, il modo più semplice per funzionare con un'identità del servizio gestito è tramite il pacchetto Microsoft.Azure.Services.AppAuthentication. Questa raccolta consente anche di testare il codice in locale nel computer di sviluppo usando l'account utente da Visual Studio, dall'[interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) o tramite l'autenticazione integrata di Active Directory. Per altre informazioni sulle opzioni di sviluppo locale con questa libreria, vedere la [documentazione di riferimento della libreria Microsoft.Azure.Services.AppAuthentication]. In questa sezione viene illustrato come muovere i primi passi con la libreria nel codice.

1. Aggiungere un riferimento ai pacchetti NuGet [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) e [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) all'applicazione.

2.  Aggiungere il codice seguente all'applicazione:

```csharp
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.KeyVault;
// ...
var azureServiceTokenProvider = new AzureServiceTokenProvider();
string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");
// OR
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
```

Per altre informazioni su Microsoft.Azure.Services.AppAuthentication e sulle relative operazioni esposte, vedere la [documentazione di riferimento della libreria Microsoft.Azure.Services.AppAuthentication] e l'[esempio di servizio app e insieme di credenziali delle chiavi con MSI .NET](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet).

### <a name="using-the-rest-protocol"></a>Uso del protocollo API

Un'applicazione con un'identità del servizio gestito ha due variabili di ambiente definite:
- MSI_ENDPOINT
- MSI_SECRET

**MSI_ENDPOINT** è un URL locale da cui l'app può richiedere token. Per ottenere un token per una risorsa, eseguire una richiesta HTTP GET a questo endpoint, includendo i parametri seguenti:

> [!div class="mx-tdBreakAll"]
> |Nome parametro|In ingresso|DESCRIZIONE|
> |-----|-----|-----|
> |resource|Query|URI della risorsa AAD per cui è necessario ottenere un token.|
> |api-version|Query|Versione dell'API del token da usare. Al momento, l'unica versione supportata è: "2017-09-01".|
> |secret|Intestazione|Valore della variabile di ambiente MSI_SECRET.|


Una risposta 200 OK con esito positivo include un corpo JSON con le proprietà seguenti:

> [!div class="mx-tdBreakAll"]
> |Nome proprietà|DESCRIZIONE|
> |-------------|----------|
> |access_token|Token di accesso richiesto. Il servizio Web chiamante può usare questo token per l'autenticazione nel servizio Web ricevente.|
> |expires_on|Scadenza del token di accesso. La data è rappresentata come numero di secondi da 1970-01-01T0:0:0Z UTC fino alla scadenza. Questo valore viene usato per determinare la durata dei token memorizzati nella cache.|
> |resource|URI ID app del servizio Web ricevente.|
> |token_type|Indica il valore del tipo di token. L'unico tipo supportato da Azure AD è Bearer. Per altre informazioni sui token di connessione, vedere [OAuth 2.0 Authorization Framework: Bearer Token Usage (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt) (Framework di autorizzazione di OAuth 2.0: uso dei token di connessione - RFC 6750).|


Questa risposta è la stessa [risposta per la richiesta del token di accesso da servizio a servizio AAD](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md#service-to-service-access-token-response).

> [!NOTE] 
> Le variabili di ambiente vengono impostate all'avvio del processo, pertanto dopo aver abilitato l'Identità del servizio gestito per l'applicazione è necessario riavviare l'applicazione o ridistribuire il relativo codice, prima che `MSI_ENDPOINT` e `MSI_SECRET` siano disponibili per il codice.

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
const rp = require('request-promise');
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

Un'identità può essere rimossa disabilitando la funzionalità tramite il portale, PowerShell o l'interfaccia della riga di comando nello stesso modo in cui è stata creata. Nel protocollo di modello REST/ARM, questa operazione viene eseguita impostando il tipo su "None":

```json
"identity": {
    "type": "None"
}    
```

Rimuovendo l'identità in questo modo verrà eliminata anche l'entità di sicurezza da Azure Active Directory. Le identità assegnate dal sistema vengono rimosse automaticamente da Azure Active Directory quando viene eliminata la risorsa app.

> [!NOTE] 
> È inoltre disponibile l'impostazione applicazione WEBSITE_DISABLE_MSI che, se attivata, disabilita il servizio token locale, senza tuttavia disattivare l'identità, per cui gli strumenti mostreranno comunque MSI come "on" o "enabled". Di conseguenza, l'uso di questa impostazione non è consigliato.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Accedere al database SQL in modo sicuro con un'identità del servizio gestito](app-service-web-tutorial-connect-msi.md)

[Documentazione di riferimento della libreria Microsoft.Azure.Services.AppAuthentication]: https://go.microsoft.com/fwlink/p/?linkid=862452
