---
title: "Come usare un'identità del servizio gestito di una macchina virtuale di Azure per l'accesso e l'acquisizione di token"
description: "Istruzioni dettagliate per l'uso di un'entità servizio Identità del servizio gestito di una macchina virtuale di Azure per l'accesso e l'acquisizione di un token di accesso."
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/17/2017
ms.author: bryanla
ms.openlocfilehash: 905e7b0d8a0c45c98a86882a8c8f387be0950f9f
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2017
---
# <a name="how-to-use-an-azure-vm-managed-service-identity-msi-for-sign-in-and-token-acquisition"></a>Come usare un'identità del servizio gestito (MSI, Managed Service Identity) di una macchina virtuale di Azure per l'accesso e l'acquisizione di token 
[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)] Dopo aver abilitato l'identità del servizio gestito in una macchina virtuale di Azure, è possibile usarla per eseguire l'accesso e per richiedere un token di accesso. Questo articolo illustra diversi modi per usare un'[entità servizio](develop/active-directory-dev-glossary.md#service-principal-object) Identità del servizio gestito per eseguire l'accesso e acquisire un [token di accesso solo app](develop/active-directory-dev-glossary.md#access-token) per accedere ad altre risorse, tra cui:

- Accesso automatico da PowerShell o dall'interfaccia della riga di comando di Azure
- Acquisizione di token per vari client, tra cui .NET, PowerShell, Bash/CURL, REST
- Accesso con una versione di Azure SDK, tra cui .NET, Java, Node.js, Python, Ruby

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [msi-qs-configure-prereqs](../../includes/msi-qs-configure-prereqs.md)]

Se si prevede di usare gli esempi di PowerShell in questo articolo, assicurarsi di installare [Azure PowerShell versione 4.3.1](https://www.powershellgallery.com/packages/AzureRM) o versione successiva. Se si prevede di usare gli esempi dell'interfaccia della riga di comando di Azure in questo articolo, sono disponibili tre opzioni:
- Usare [Azure Cloud Shell](../cloud-shell/overview.md) dal portale di Azure.
- Usare Azure Cloud Shell incorporato tramite il pulsante per la prova, che si trova nell'angolo in alto a destra dei blocchi di codice dell'interfaccia della riga di comando di Azure.
- [Installare la versione più recente dell'interfaccia della riga di comando 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 o successiva) se si preferisce usare l'interfaccia della riga di comando in un prompt dei comandi locale. 


> [!IMPORTANT]
> - Tutti gli esempi di codice e script in questo articolo presuppongono che il client sia in esecuzione in una macchina virtuale abilitata per l'identità del servizio gestito. Per informazioni dettagliate sull'abilitazione dell'identità del servizio gestito in una macchina virtuale, vedere [Configurare un'Identità del servizio gestito della macchina virtuale tramite il portale di Azure](msi-qs-configure-portal-windows-vm.md) o una delle varianti dell'articolo (per PowerShell, interfaccia della riga di comando, un modello o una versione di Azure SDK). 
> - Per evitare errori di autorizzazione (403/AuthorizationFailed) negli esempi di codice e script, l'identità della macchina virtuale deve avere l'accesso in lettura nell'ambito della macchina virtuale, per consentire le operazioni di Azure Resource Manager nella macchina virtuale. Per dettagli, vedere [Assegnare a un'Identità del servizio gestito, ovvero MSI, l'accesso a una risorsa tramite il portale di Azure](msi-howto-assign-access-portal.md).
> - Prima di procedere con una delle sezioni seguenti, usare la funzionalità di connessione alla macchina virtuale nel portale di Azure per connettersi in remoto alla macchina virtuale abilitata per l'identità del servizio gestito.

## <a name="how-to-sign-in-from-powershell-or-cli-using-msi"></a>Come accedere da PowerShell o dall'interfaccia della riga di comando usando l'identità del servizio gestito

In precedenza, l'esecuzione di uno script nella propria identità significava:
- registrazione come applicazione client Web/riservata con Azure AD
- accesso con credenziali ID client/segreto dell'applicazione

Con l'identità del servizio gestito, il client di script non deve più eseguire la registrazione con Azure AD né fornire le credenziali. Negli esempi seguenti viene illustrato come usare un'entità servizio Identità del servizio gestito per l'accesso.

### <a name="azure-powershell"></a>Azure PowerShell

Lo script seguente illustra come:

- acquisire un token di accesso dell'identità del servizio gestito per una macchina virtuale di Azure
- usare il token di accesso per accedere ad Azure AD con l'entità servizio Identità del servizio gestito corrispondente
- Usare l'entità servizio Identità del servizio gestito per effettuare una chiamata ad Azure Resource Manager, per ottenere l'ID dell'entità servizio

```powershell
# Get an access token from MSI
$response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token `
                              -Method GET -Body @{resource="https://management.azure.com/"} -Headers @{Metadata="true"}
$content =$response.Content | ConvertFrom-Json
$access_token = $content.access_token

# Use the access token to sign in under the MSI service principal
Login-AzureRmAccount -AccessToken $access_token -AccountId “CLIENT”

# The MSI service principal is now signed in for this session.
# Next, a call to Azure Resource Manager is made to get the service principal ID for the VM's MSI. 
$spID = (Get-AzureRMVM -ResourceGroupName <RESOURCE-GROUP> -Name <VM-NAME>).identity.principalid
echo "The MSI service principal ID is $spID"
```
   
### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Lo script seguente illustra come:

- accedere ad Azure AD con l'entità servizio Identità del servizio gestito della macchina virtuale
- Usare l'entità servizio Identità del servizio gestito per effettuare una chiamata ad Azure Resource Manager, per ottenere l'ID dell'entità servizio

```azurecli-interactive
az login --msi
spID=$(az resource list -n <VM-NAME> --query [*].identity.principalId --out tsv)
echo The MSI service principal ID is $spID
```

## <a name="how-to-acquire-an-access-token-from-msi"></a>Come acquisire un token di accesso dall'identità del servizio gestito

Usando l'identità del servizio gestito, lo script o l'applicazione client non deve più eseguire la registrazione con Azure AD, né presentare i relativi ID client e segreto per ottenere un token di accesso. Il client può semplicemente chiedere all'endpoint dell'identità del servizio gestito locale un token di accesso, senza presentare le credenziali dell'applicazione. Questo token di accesso solo app viene generato per l'entità servizio Identità del servizio gestito ed è adatto per l'uso come token di connessione nelle [chiamate da servizio a servizio che richiedono le credenziali client](active-directory-protocols-oauth-service-to-service.md).

Gli esempi seguenti illustrano come usare un'identità del servizio gestito di una macchina virtuale per l'acquisizione di un token.

### <a name="net"></a>.NET

> [!IMPORTANT]
> Azure AD Authentication Library (ADAL) non si integra con l'identità del servizio gestito. Per ottenere un token di accesso usando un'identità del servizio gestito, effettuare una richiesta all'endpoint dell'identità del servizio gestito locale in una macchina virtuale. Per altre informazioni, vedere [Domande frequenti e problemi noti di Identità del servizio gestito (MSI)](msi-known-issues.md#frequently-asked-questions-faqs).

```csharp
// Build request to acquire MSI token
HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://localhost:50342/oauth2/token?resource=https://management.azure.com/");
request.Headers["Metadata"] = "true";
request.Method = "GET";

try
{
    // Call /token endpoint
    HttpWebResponse response = (HttpWebResponse)request.GetResponse();

    // Pipe response Stream to a StreamReader, and extract access token
    StreamReader streamResponse = new StreamReader(response.GetResponseStream()); 
    string stringResponse = streamResponse.ReadToEnd();
    JavaScriptSerializer j = new JavaScriptSerializer();
    Dictionary<string, string> list = (Dictionary<string, string>) j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
    string accessToken = list["access_token"];
}
catch (Exception e)
{
    string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
}

```

### <a name="azure-powershell-token"></a>Azure PowerShell

```powershell
# Get an access token from MSI
$response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token `
                              -Method GET -Body @{resource="https://management.azure.com/"} -Headers @{Metadata="true"}
$content =$response.Content | ConvertFrom-Json
$access_token = $content.access_token
echo "The MSI access token is $access_token"
```

### <a name="bashcurl"></a>Bash/CURL

```bash
response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The MSI access token is $access_token
```

### <a name="rest"></a>REST

Richiesta di esempio:

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Elemento | Descrizione |
| ------- | ----------- |
| `GET` | Verbo HTTP, che indica che si vuole recuperare i dati dall'endpoint. In questo caso, un token di accesso OAuth. | 
| `http://localhost:50342/oauth2/token` | Endpoint dell'identità del servizio gestito, dove 50342 è la porta predefinita ed è configurabile. |
| `resource` | Parametro della stringa di query, che indica l'URI ID app della risorsa di destinazione. Viene visualizzato anche nell'attestazione `aud` (audience, destinatari) del token emesso. In questo esempio viene richiesto un token per accedere ad Azure Resource Manager, con URI ID app https://management.azure.com/. |
| `Metadata` | Campo di intestazione della richiesta HTTP, richiesto dall'identità del servizio gestito come mitigazione contro attacchi SSRF (Server Side Request Forgery). Questo valore deve essere impostato su "true", usando tutte lettere minuscole.

Risposta di esempio:

```
HTTP/1.1 200 OK
Content-Type: application/json
{
  "access_token": "eyJ0eXAi...",
  "refresh_token": "",
  "expires_in": "3599",
  "expires_on": "1506484173",
  "not_before": "1506480273",
  "resource": "https://management.azure.com/",
  "token_type": "Bearer"
}
```

| Elemento | Descrizione |
| ------- | ----------- |
| `access_token` | Token di accesso richiesto. Quando si chiama un'API REST, il token è incorporato nel campo di intestazione della richiesta `Authorization` come token di connessione, per consentire all'API autenticare il chiamante. | 
| `refresh_token` | Non usato dall'identità del servizio gestito. |
| `expires_in` | Numero di secondi per cui il token di accesso continua a essere valido, prima della scadenza, dal momento del rilascio. L'ora del rilascio è indicata nell'attestazione `iat` del token. |
| `expires_on` | Intervallo di tempo in cui il token di accesso scade. La data è rappresentata come numero di secondi da "1970-01-01T0:0:0Z UTC" (corrisponde all'attestazione `exp` del token). |
| `not_before` | Intervallo di tempo in cui il token di accesso è valido e può essere accettato. La data è rappresentata come numero di secondi da "1970-01-01T0:0:0Z UTC" (corrisponde all'attestazione `nbf` del token). |
| `resource` | Risorsa per cui è stato richiesto il token di accesso, che corrisponde al parametro della stringa di query `resource` della richiesta. |
| `token_type` | Tipo di token, ovvero un token di accesso di connessione, che indica che la risorsa può concedere l'accesso al titolare del token. |

## <a name="how-to-use-msi-with-azure-sdk-libraries"></a>Come usare l'identità del servizio gestito con le librerie di Azure SDK

Azure supporta più piattaforme di programmazione tramite una serie di [Azure SDK](https://azure.microsoft.com/downloads). Alcuni di essi sono stati aggiornati per supportare l'accesso con l'identità del servizio gestito e forniscono esempi corrispondenti per illustrare l'uso. L'elenco viene aggiornato quando è disponibile supporto aggiuntivo:

| SDK | Esempio |
| --- | ------ | 
| .NET | [Deploy an ARM template from a Windows VM using Managed Service Identity](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet) (Distribuire un modello ARM da una macchina virtuale Windows tramite identità del servizio gestito) |
| .NET Core | [Call Azure services from a Linux VM using Managed Service Identity](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/) (Chiamare servizi Azure da una macchina virtuale Linux tramite identità del servizio condiviso) |
| Node.js| [Manage resources using Managed Service Identity](https://azure.microsoft.com/resources/samples/resources-node-manage-resources-with-msi/) (Gestire le risorse usando l'identità del servizio gestito) |
| Python | [Use MSI to authenticate simply from inside a VM](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) (Usare l'identità del servizio gestito per eseguire in modo semplice l'autenticazione in una macchina virtuale) |
| Ruby   | [Manage resources from an MSI-enabled VM](https://azure.microsoft.com/resources/samples/resources-ruby-manage-resources-with-msi/) (Gestire le risorse da una macchina virtuale abilitata per l'identità del servizio gestito) | 

## <a name="additional-information"></a>Informazioni aggiuntive

### <a name="token-expiration"></a>Scadenza del token

Il sottosistema dell'identità del servizio gestito locale memorizza i token nella cache. È quindi possibile eseguire tutte le chiamate desiderate e una chiamata in transito ad Azure AD restituisce un risultato solo se:
- si verifica un mancato riscontro nella cache a causa dell'assenza di token nella cache
- il token è scaduto

Se si memorizza nella cache il token nel codice, è consigliabile essere preparati a gestire gli scenari in cui la risorsa indica che il token è scaduto.

### <a name="resource-ids-for-azure-services"></a>ID di risorsa per i servizi di Azure

Per un elenco di servizi che supportano l'identità del servizio gestito con i relativi ID di risorsa, vedere [Servizi di Azure che supportano l'autenticazione di Azure AD](msi-overview.md#azure-services-that-support-azure-ad-authentication).

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="sign-in-or-token-acquisition-fails"></a>Errore di accesso o di acquisizione del token

Verificare che l'identità del servizio gestito sia stata abilitata correttamente. Tornare alla macchina virtuale di Azure nel [portale di Azure](https://portal.azure.com) e:

- Si esamina la pagina "Configurazione" e si verifica che l'Identità del servizio gestito sia attivata = "Sì".
- Si esamina la pagina "Estensioni" e si verifica che l'estensione dell'Identità del servizio gestito sia stata distribuita correttamente.

Se una delle due opzioni è errata, è necessario ridistribuire l'Identità del servizio gestito nella risorsa o risolvere il problema di distribuzione.

### <a name="http-request-error-responses"></a>Risposte di errore delle richieste HTTP

- *bad_request_102: Required metadata header not specified* (Intestazione dei metadati richiesta non specificata)

  Il campo di intestazione della richiesta `Metadata` non è presente nella richiesta oppure non è formattato correttamente. Il valore deve essere specificato come `true`, usando tutte lettere minuscole. Per un esempio, vedere Richiesta di esempio nella [sezione REST precedente](#rest).

- *unknown: Failed to retrieve token from the Active directory. For details see logs in \<file path\>* (Impossibile recuperare il token da Active Directory. Per informazioni dettagliate vedere i log in <percorso file>)

  Verificare che l'URI della richiesta HTTP GET sia formattato correttamente, in particolare l'URI della risorsa specificato nella stringa di query. Vedere Richiesta di esempio nella [sezione REST precedente](#rest) per un esempio oppure vedere [Servizi di Azure che supportano l'autenticazione di Azure AD](msi-overview.md#azure-services-that-support-azure-ad-authentication) per un elenco di servizi con i relativi ID di risorsa.

- *unknown_source: Origine sconosciuta \<URI\>*

  Verificare che l'URI della richiesta HTTP GET sia formattato correttamente. La parte `scheme:host/resource-path` deve essere specificata come `http://localhost:50342/oauth2/token`. Per un esempio, vedere Richiesta di esempio nella [sezione REST precedente](#rest).

## <a name="related-content"></a>Contenuti correlati

- Per una panoramica dell'Identità di servizio gestito, vedere [Panoramica dell'Identità di servizio gestito](msi-overview.md).
- Per abilitare l'Identità del servizio gestito in una macchina virtuale di Azure, vedere [Configurare un'Identità del servizio gestito della macchina virtuale di Azure mediante PowerShell](msi-qs-configure-powershell-windows-vm.md).

Usare la sezione dei commenti seguente per fornire commenti e suggerimenti utili per migliorare e organizzare i contenuti disponibili.

