---
title: Identità gestite
description: Informazioni su come funzionano le identità gestite in Servizio app di Azure e Funzioni di Azure, sulla loro configurazione e su come generare un token per una risorsa back-end.
author: mattchenderson
ms.topic: article
ms.date: 05/27/2020
ms.author: mahender
ms.reviewer: yevbronsh
ms.custom: devx-track-csharp, devx-track-python, devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 73b09c006b11e7f57dd3833191dd381b7f42a709
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2020
ms.locfileid: "93145838"
---
# <a name="how-to-use-managed-identities-for-app-service-and-azure-functions"></a>Come usare le identità gestite nel servizio app e in Funzioni di Azure

Questo argomento illustra come creare un'identità gestita per le applicazioni del servizio app e di Funzioni di Azure e come usarla per accedere ad altre risorse. 

> [!Important] 
> Se viene eseguita la migrazione dell'app tra sottoscrizioni/tenant, le identità gestite per il servizio app e per Funzioni di Azure non funzionano come previsto. L'app deve ottenere una nuova identità disabilitando e abilitando di nuovo la funzionalità. Vedere [Rimozione di un'identità](#remove) più avanti. Per usare la nuova identità, anche le risorse a valle devono disporre di criteri di accesso aggiornati.

[!INCLUDE [app-service-managed-identities](../../includes/app-service-managed-identities.md)]

## <a name="add-a-system-assigned-identity"></a>Aggiungere un'identità assegnata dal sistema

La creazione di un'app con un'identità assegnata dal sistema richiede l'impostazione di una proprietà aggiuntiva nell'applicazione.

### <a name="using-the-azure-portal"></a>Uso del portale di Azure

Per configurare un'identità gestita nel portale, è prima necessario creare un'applicazione come di consueto e quindi abilitare la funzionalità.

1. Creare un'app nel portale come di consueto. Accedervi nel portale.

2. Se si usa un'app per le funzioni, passare a **Funzionalità della piattaforma** . Per altri tipi di app, scorrere verso il basso fino al gruppo **Impostazioni** nel riquadro di spostamento a sinistra.

3. Selezionare **Identità** .

4. All'interno della scheda **Assegnata dal sistema** impostare **Stato** su **Attivato** . Fare clic su **Salva** .

    ![Screenshot che mostra dove impostare lo stato su on e quindi selezionare Save (Salva).](media/app-service-managed-service-identity/system-assigned-managed-identity-in-azure-portal.png)


> [!NOTE] 
> Per trovare l'identità gestita per l'app Web o l'app per slot nel portale di Azure, in **applicazioni aziendali** , cercare nella sezione **impostazioni utente** . In genere, il nome dello slot è simile a `<app name>/slots/<slot name>` .


### <a name="using-the-azure-cli"></a>Uso dell'interfaccia della riga di comando di Azure

Per configurare un'identità gestita usando l'interfaccia della riga di comando di Azure, è necessario usare il comando `az webapp identity assign` su un'applicazione esistente. Sono disponibili tre opzioni per l'esecuzione degli esempi di questa sezione:

- Usare [Azure Cloud Shell](../cloud-shell/overview.md) dal portale di Azure.
- Usare Azure Cloud Shell incorporato tramite il pulsante "Prova", situato nell'angolo in alto a destra di ogni blocco di codice indicato di seguito.
- [Installare la versione più recente dell'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) (2.0.31 o successiva) se si preferisce usare una console dell'interfaccia della riga di comando locale. 

La procedura seguente consente di creare di un'app Web e assegnarle un'identità usando l'interfaccia della riga di comando:

1. Se si usa l'interfaccia della riga di comando di Azure in una console locale, accedere prima di tutto ad Azure tramite [az login](/cli/azure/reference-index#az-login). Usare un account associato all'abbonamento di Azure in cui si desidera distribuire l'applicazione:

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

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

La procedura seguente illustra come creare un'app e assegnarle un'identità usando Azure PowerShell. Le istruzioni per la creazione di un'app Web e un'app per le funzioni sono diverse.

#### <a name="using-azure-powershell-for-a-web-app"></a>Uso di Azure PowerShell per un'app Web

1. Se necessario, installare Azure PowerShell usando l'istruzione presente nella [Guida di Azure PowerShell](/powershell/azure/) e quindi eseguire `Login-AzAccount` per creare una connessione con Azure.

2. Creare un'applicazione Web tramite Azure PowerShell. Per altri esempi su come usare Azure PowerShell con il Servizio app, vedere [Esempi di PowerShell del Servizio app](../app-service/samples-powershell.md):

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create an App Service plan in Free tier.
    New-AzAppServicePlan -Name $webappname -Location $location -ResourceGroupName $resourceGroupName -Tier Free

    # Create a web app.
    New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName $resourceGroupName
    ```

3. Eseguire il comando `Set-AzWebApp -AssignIdentity` per creare l'identità per l'applicazione:

    ```azurepowershell-interactive
    Set-AzWebApp -AssignIdentity $true -Name $webappname -ResourceGroupName $resourceGroupName 
    ```

#### <a name="using-azure-powershell-for-a-function-app"></a>Uso di Azure PowerShell per un'app per le funzioni

1. Se necessario, installare Azure PowerShell usando l'istruzione presente nella [Guida di Azure PowerShell](/powershell/azure/) e quindi eseguire `Login-AzAccount` per creare una connessione con Azure.

2. Creare un'app per le funzioni usando Azure PowerShell. Per altri esempi su come usare Azure PowerShell con funzioni di Azure, vedere il [riferimento AZ. Functions](/powershell/module/az.functions/?view=azps-4.1.0#functions):

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a storage account.
    New-AzStorageAccount -Name $storageAccountName -ResourceGroupName $resourceGroupName -SkuName $sku

    # Create a function app with a system-assigned identity.
    New-AzFunctionApp -Name $functionAppName -ResourceGroupName $resourceGroupName -Location $location -StorageAccountName $storageAccountName -Runtime $runtime -IdentityType SystemAssigned
    ```

È anche possibile aggiornare un'app per le funzioni esistente usando `Update-AzFunctionApp` invece.

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

La proprietà tenantId identifica a quale tenant di Azure AD appartiene l'identità.appartiene l'identità. La proprietà principalId è un identificatore univoco per la nuova identità dell'applicazione. In Azure AD, l'entità servizio ha lo stesso nome assegnato all'istanza di Servizio App o Funzioni di Azure.

Se è necessario fare riferimento a queste proprietà in una fase successiva del modello, è possibile farlo tramite la [ `reference()` funzione di modello](../azure-resource-manager/templates/template-functions-resource.md#reference) con il `'Full'` flag, come nell'esempio seguente:

```json
{
    "tenantId": "[reference(resourceId('Microsoft.Web/sites', variables('appName')), '2018-02-01', 'Full').identity.tenantId]",
    "objectId": "[reference(resourceId('Microsoft.Web/sites', variables('appName')), '2018-02-01', 'Full').identity.principalId]",
}
```

## <a name="add-a-user-assigned-identity"></a>Aggiungere un'identità assegnata dall'utente

La creazione di un'app con un'identità assegnata dall'utente richiede la creazione dell'identità e quindi l'aggiunta dell'identificatore di risorsa corrispondente alla configurazione dell'app.

### <a name="using-the-azure-portal"></a>Uso del portale di Azure

Sarà prima di tutto necessario creare una risorsa identità assegnata dall'utente.

1. Seguire [queste istruzioni](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) per creare una risorsa identità gestita assegnata dall'utente.

2. Creare un'app nel portale come di consueto. Accedervi nel portale.

3. Se si usa un'app per le funzioni, passare a **Funzionalità della piattaforma** . Per altri tipi di app, scorrere verso il basso fino al gruppo **Impostazioni** nel riquadro di spostamento a sinistra.

4. Selezionare **Identità** .

5. Nella scheda **Assegnate dall'utente** fare clic su **Aggiungi** .

6. Cercare l'identità creata in precedenza e selezionarla. Scegliere **Aggiungi** .

    ![Identità gestita nel servizio app](media/app-service-managed-service-identity/user-assigned-managed-identity-in-azure-portal.png)

### <a name="using-azure-powershell"></a>Uso di Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

La procedura seguente illustra come creare un'app e assegnarle un'identità usando Azure PowerShell.

> [!NOTE]
> La versione corrente del Azure PowerShell cmdlet per app Azure servizio non supporta le identità assegnate dall'utente. Di seguito sono riportate le istruzioni per funzioni di Azure.

1. Se necessario, installare Azure PowerShell usando l'istruzione presente nella [Guida di Azure PowerShell](/powershell/azure/) e quindi eseguire `Login-AzAccount` per creare una connessione con Azure.

2. Creare un'app per le funzioni usando Azure PowerShell. Per altri esempi su come usare Azure PowerShell con funzioni di Azure, vedere il [riferimento AZ. Functions](/powershell/module/az.functions/?view=azps-4.1.0#functions). Lo script seguente usa anche il `New-AzUserAssignedIdentity` quale deve essere installato separatamente in base alla [creazione, all'elenco o all'eliminazione di un'identità gestita assegnata dall'utente usando Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md).

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a storage account.
    New-AzStorageAccount -Name $storageAccountName -ResourceGroupName $resourceGroupName -SkuName $sku

    # Create a user-assigned identity. This requires installation of the "Az.ManagedServiceIdentity" module.
    $userAssignedIdentity = New-AzUserAssignedIdentity -Name $userAssignedIdentityName -ResourceGroupName $resourceGroupName

    # Create a function app with a user-assigned identity.
    New-AzFunctionApp -Name $functionAppName -ResourceGroupName $resourceGroupName -Location $location -StorageAccountName $storageAccountName -Runtime $runtime -IdentityType UserAssigned -IdentityId $userAssignedIdentity.Id
    ```

È anche possibile aggiornare un'app per le funzioni esistente usando `Update-AzFunctionApp` invece.

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

L'aggiunta del tipo assegnato dall'utente indica ad Azure di usare l'identità assegnata dall'utente specificata per l'applicazione.

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

La proprietà principalId è un identificatore univoco per l'identità che viene usato per l'amministrazione di Azure AD. clientId è un identificatore univoco per la nuova identità dell'applicazione, che viene usato per specificare l'identità da usare durante le chiamate di runtime.

## <a name="obtain-tokens-for-azure-resources"></a>Ottenere i token per le risorse di Azure

Un'app può usare la propria identità gestita per ottenere i token per l'accesso ad altre risorse protette da Azure AD, ad esempio Azure Key Vault. Questi token rappresentano le applicazioni che accedono alla risorsa e non un utente specifico dell'applicazione. 

Potrebbe essere necessario configurare la risorsa di destinazione per consentire l'accesso dall'applicazione. Ad esempio, se si richiede un token di accesso a Key Vault, è necessario verificare di aver aggiunto un criterio di accesso che include l'identità dell'applicazione. In caso contrario, le chiamate a Key Vault verranno rifiutate, anche se includono il token. Per altre informazioni sulle risorse che supportano i token di Azure Active Directory, vedere [Servizi di Azure che supportano l'autenticazione di Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

> [!IMPORTANT]
> I servizi back-end per le identità gestite conservano una cache per ogni URI di risorsa per circa 8 ore. Se si aggiornano i criteri di accesso di una determinata risorsa di destinazione e si recupera immediatamente un token per tale risorsa, è possibile ottenere un token memorizzato nella cache con autorizzazioni obsolete fino alla scadenza del token. Attualmente non è possibile forzare l'aggiornamento di un token.

È disponibile un semplice protocollo REST per ottenere un token del Servizio App e di Funzioni di Azure. Questo protocollo può essere usato per tutte le applicazioni e tutti i linguaggi. Per .NET e Java, Azure SDK fornisce un'astrazione basata su questo protocollo e facilita un'esperienza di sviluppo locale.

### <a name="using-the-rest-protocol"></a>Uso del protocollo API

> [!NOTE]
> Una versione precedente di questo protocollo, che usa la versione 2017-09-01 dell'API e l'intestazione `secret` anziché `X-IDENTITY-HEADER`, inoltre accetta solo la proprietà `clientid` per le identità assegnate dall'utente. Restituisce inoltre anche `expires_on` in formato timestamp. MSI_ENDPOINT può essere usato come alias per IDENTITY_ENDPOINT e MSI_SECRET può essere usato come alias per IDENTITY_HEADER. Questa versione del protocollo è attualmente necessaria per i piani di hosting a consumo Linux.

Un'app con un'identità gestita ha due variabili di ambiente definite:

- IDENTITY_ENDPOINT: URL del servizio token locale.
- IDENTITY_HEADER: intestazione usata per mitigare gli attacchi SSRF (Server Side Request Forgery). Il valore viene ruotato dalla piattaforma.

**IDENTITY_ENDPOINT** è un URL locale da cui l'app può richiedere i token. Per ottenere un token per una risorsa, eseguire una richiesta HTTP GET a questo endpoint, includendo i parametri seguenti:

> | Nome parametro    | In ingresso     | Descrizione                                                                                                                                                                                                                                                                                                                                |
> |-------------------|--------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | resource          | Query  | URI della risorsa Azure AD per cui è necessario ottenere un token. Può trattarsi di uno dei [servizi di Azure che supportano l'autenticazione di Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) o di qualsiasi altro URI di risorsa.    |
> | api-version       | Query  | Versione dell'API del token da usare. Usare "2019-08-01" o versione successiva (a meno che non si usi il consumo di Linux, che attualmente offre solo "2017-09-01", vedere la nota precedente).                                                                                                                                                                                                                                                                 |
> | X-IDENTITY-HEADER | Intestazione | Valore della variabile di ambiente IDENTITY_HEADER. Questa intestazione viene usata per mitigare gli attacchi SSRF (Server Side Request Forgery).                                                                                                                                                                                                    |
> | client_id         | Query  | (Facoltativo) ID client dell'identità assegnata dall'utente da usare. Non può essere usato in una richiesta che include `principal_id`, `mi_res_id` o `object_id`. Se vengono omessi tutti i parametri ID (`client_id`, `principal_id`, `object_id` e `mi_res_id`), viene usata l'identità assegnata dal sistema.                                             |
> | principal_id      | Query  | (Facoltativo) ID entità di sicurezza dell'identità assegnata dall'utente da usare. `object_id` è un alias che può essere usato in alternativa. Non può essere usato in una richiesta che include: client_id, mi_res_id oppure object_id. Se vengono omessi tutti i parametri ID (`client_id`, `principal_id`, `object_id` e `mi_res_id`), viene usata l'identità assegnata dal sistema. |
> | mi_res_id         | Query  | (Facoltativo) ID delle risorsa Azure dell'identità assegnata dall'utente da usare. Non può essere usato in una richiesta che include `principal_id`, `client_id` o `object_id`. Se vengono omessi tutti i parametri ID (`client_id`, `principal_id`, `object_id` e `mi_res_id`), viene usata l'identità assegnata dal sistema.                                      |

> [!IMPORTANT]
> Per ottenere i token per le identità assegnate dall'utente, è necessario includere una delle proprietà facoltative. In caso contrario, il servizio token tenterà di ottenere un token per un'identità assegnata dal sistema, che potrebbe o meno esistere.

Una risposta 200 OK con esito positivo include un corpo JSON con le proprietà seguenti:

> | Nome proprietà | Descrizione                                                                                                                                                                                                                                        |
> |---------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | access_token  | Token di accesso richiesto. Il servizio Web chiamante può usare questo token per l'autenticazione nel servizio Web ricevente.                                                                                                                               |
> | client_id     | ID client dell'identità usata.                                                                                                                                                                                                       |
> | expires_on    | Intervallo di tempo in cui il token di accesso scade. La data è rappresentata come numero di secondi da "1970-01-01T0:0:0Z UTC" (corrisponde all'attestazione `exp` del token).                                                                                |
> | not_before    | Intervallo di tempo in cui il token di accesso è valido e può essere accettato. La data è rappresentata come numero di secondi da "1970-01-01T0:0:0Z UTC" (corrisponde all'attestazione `nbf` del token).                                                      |
> | resource      | Risorsa per cui è stato richiesto il token di accesso, che corrisponde al parametro della stringa di query `resource` della richiesta.                                                                                                                               |
> | token_type    | Indica il valore del tipo di token. L'unico tipo supportato da Azure AD è FBearer. Per altre informazioni sui bearer token, vedere [OAuth 2.0 Authorization Framework: Bearer Token Usage (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt) (Framework di autorizzazione di OAuth 2.0: uso dei bearer token - RFC 6750). |

Questa risposta è uguale alla [risposta per la richiesta del token di accesso da servizio a servizio di Azure AD](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#service-to-service-access-token-response).

### <a name="rest-protocol-examples"></a>Esempi di protocollo REST

Un esempio di richiesta potrebbe apparire come segue:

```http
GET /MSI/token?resource=https://vault.azure.net&api-version=2019-08-01 HTTP/1.1
Host: localhost:4141
X-IDENTITY-HEADER: 853b9a84-5bfa-4b22-a3f3-0b9a43d9ad8a
```

E un esempio di risposta potrebbe apparire come segue:

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
    "access_token": "eyJ0eXAi…",
    "expires_on": "1586984735",
    "resource": "https://vault.azure.net",
    "token_type": "Bearer",
    "client_id": "5E29463D-71DA-4FE0-8E69-999B57DB23B0"
}
```

### <a name="code-examples"></a>Esempi di codice

# <a name="net"></a>[.NET](#tab/dotnet)

> [!TIP]
> Per i linguaggi .NET è anche possibile usare [Microsoft.Azure.Services.AppAuthentication](#asal) invece di creare la richiesta manualmente.

```csharp
private readonly HttpClient _client;
// ...
public async Task<HttpResponseMessage> GetToken(string resource)  {
    var request = new HttpRequestMessage(HttpMethod.Get, 
        String.Format("{0}/?resource={1}&api-version=2019-08-01", Environment.GetEnvironmentVariable("IDENTITY_ENDPOINT"), resource));
    request.Headers.Add("X-IDENTITY-HEADER", Environment.GetEnvironmentVariable("IDENTITY_HEADER"));
    return await _client.SendAsync(request);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const rp = require('request-promise');
const getToken = function(resource, cb) {
    let options = {
        uri: `${process.env["IDENTITY_ENDPOINT"]}/?resource=${resource}&api-version=2019-08-01`,
        headers: {
            'X-IDENTITY-HEADER': process.env["IDENTITY_HEADER"]
        }
    };
    rp(options)
        .then(cb);
}
```

# <a name="python"></a>[Python](#tab/python)

```python
import os
import requests

identity_endpoint = os.environ["IDENTITY_ENDPOINT"]
identity_header = os.environ["IDENTITY_HEADER"]

def get_bearer_token(resource_uri):
    token_auth_uri = f"{identity_endpoint}?resource={resource_uri}&api-version=2019-08-01"
    head_msi = {'X-IDENTITY-HEADER':identity_header}

    resp = requests.get(token_auth_uri, headers=head_msi)
    access_token = resp.json()['access_token']

    return access_token
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$resourceURI = "https://<AAD-resource-URI-for-resource-to-obtain-token>"
$tokenAuthURI = $env:IDENTITY_ENDPOINT + "?resource=$resourceURI&api-version=2019-08-01"
$tokenResponse = Invoke-RestMethod -Method Get -Headers @{"X-IDENTITY-HEADER"="$env:IDENTITY_HEADER"} -Uri $tokenAuthURI
$accessToken = $tokenResponse.access_token
```

---

### <a name="using-the-microsoftazureservicesappauthentication-library-for-net"></a><a name="asal"></a>Uso della libreria Microsoft.Azure.Services.AppAuthentication per .NET

Per le funzioni e le applicazioni .NET, il modo più semplice per usare un'identità gestita consiste nell'usare il pacchetto Microsoft.Azure.Services.AppAuthentication. Questa raccolta consente anche di testare il codice in locale nel computer di sviluppo usando l'account utente da Visual Studio, dall'[interfaccia della riga di comando di Azure](/cli/azure) o tramite l'autenticazione integrata di Active Directory. Quando è ospitato nel cloud, per impostazione predefinita viene usata un'identità assegnata dal sistema, ma è possibile personalizzare questo comportamento usando una variabile di ambiente della stringa di connessione che fa riferimento all'ID client di un'identità assegnata dall'utente. Per altre informazioni sulle opzioni di sviluppo con questa libreria, vedere il [riferimento a Microsoft. Azure. Services. AppAuthentication]. In questa sezione viene illustrato come muovere i primi passi con la libreria nel codice.

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

Se si desidera utilizzare un'identità gestita assegnata dall'utente, è possibile impostare l' `AzureServicesAuthConnectionString` impostazione dell'applicazione su `RunAs=App;AppId=<clientId-guid>` . Sostituire `<clientId-guid>` con l'ID client dell'identità che si vuole usare. È possibile definire più stringhe di connessione usando le impostazioni dell'applicazione personalizzate e passandone i valori nel costruttore AzureServiceTokenProvider.

```csharp
    var identityConnectionString1 = Environment.GetEnvironmentVariable("UA1_ConnectionString");
    var azureServiceTokenProvider1 = new AzureServiceTokenProvider(identityConnectionString1);
    
    var identityConnectionString2 = Environment.GetEnvironmentVariable("UA2_ConnectionString");
    var azureServiceTokenProvider2 = new AzureServiceTokenProvider(identityConnectionString2);
```

Per altre informazioni sulla configurazione di AzureServiceTokenProvider e sulle operazioni che espone, vedere le informazioni di [riferimento su Microsoft. Azure. Services. AppAuthentication] e il [servizio app e l'insieme di credenziali delle applicazioni con MSI .NET](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet).

### <a name="using-the-azure-sdk-for-java"></a>Uso di Azure SDK per Java

Per le applicazioni e le funzioni Java, il modo più semplice per usare un'identità gestita consiste nell'usare [Azure SDK per Java](https://github.com/Azure/azure-sdk-for-java). In questa sezione viene illustrato come muovere i primi passi con la libreria nel codice.

1. Aggiungere un riferimento alla [libreria Azure SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure). Per i progetti Maven, è possibile aggiungere questo frammento di codice alla sezione `dependencies` del file POM del progetto:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure</artifactId>
        <version>1.23.0</version>
    </dependency>
    ```

2. Usare l'oggetto `AppServiceMSICredentials` per l'autenticazione. L'esempio mostra come usare questo meccanismo con Azure Key Vault:

    ```java
    import com.microsoft.azure.AzureEnvironment;
    import com.microsoft.azure.management.Azure;
    import com.microsoft.azure.management.keyvault.Vault
    //...
    Azure azure = Azure.authenticate(new AppServiceMSICredentials(AzureEnvironment.AZURE))
            .withSubscription(subscriptionId);
    Vault myKeyVault = azure.vaults().getByResourceGroup(resourceGroup, keyvaultName);

    ```


## <a name="remove-an-identity"></a><a name="remove"></a>Rimuovere un'identità

Un'identità assegnata dal sistema può essere rimossa disabilitando la funzionalità tramite il portale, PowerShell o l'interfaccia della riga di comando nello stesso modo in cui è stata creata. Le identità assegnate dall'utente possono essere rimosse separatamente. Per rimuovere tutte le identità, impostare il tipo di identità su "None".

La rimozione di un'identità assegnata dal sistema in questo modo ne comporta l'eliminazione anche da Azure AD. Le identità assegnate dal sistema vengono rimosse automaticamente anche da Azure AD quando viene eliminata la risorsa app.

Per rimuovere tutte le identità in un [modello ARM](#using-an-azure-resource-manager-template):

```json
"identity": {
    "type": "None"
}
```

Per rimuovere tutte le identità in Azure PowerShell (solo funzioni di Azure):

```azurepowershell-interactive
# Update an existing function app to have IdentityType "None".
Update-AzFunctionApp -Name $functionAppName -ResourceGroupName $resourceGroupName -IdentityType None
```

> [!NOTE]
> È inoltre disponibile l'impostazione applicazione WEBSITE_DISABLE_MSI che, se attivata, disabilita il servizio token locale, L'identità non viene tuttavia disabilitata e negli strumenti continuerà a essere indicata come attivata o abilitata. Di conseguenza, l'uso di questa impostazione non è consigliato.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Accedere al database SQL in modo sicuro usando un'identità gestita](app-service-web-tutorial-connect-msi.md)

[Documentazione di riferimento della libreria Microsoft.Azure.Services.AppAuthentication]: ../key-vault/general/service-to-service-authentication.md
