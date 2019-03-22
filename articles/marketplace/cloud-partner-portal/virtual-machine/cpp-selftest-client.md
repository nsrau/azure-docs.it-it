---
title: Client di verifica automatica per la convalida preliminare di una macchina virtuale - Azure Marketplace | Microsoft Docs
description: Come creare un client di verifica automatica per la convalida preliminare dell'immagine di una macchina virtuale per Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal, Virtual Machine
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: pbutlerm
ms.openlocfilehash: 8dc0a003a12eb0aca28c6a3238e2119dc449d661
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58309419"
---
# <a name="create-a-self-test-client-to-pre-validate-an-azure-virtual-machine-image"></a>Creare un client di verifica automatica per la convalida preliminare dell'immagine di una macchina virtuale di Azure

Usare questo articolo come guida per la creazione di un servizio client che utilizza l'API di verifica automatica. È possibile usare l'API di verifica automatica per preconvalidare una macchina virtuale e assicurarsi che soddisfi i requisiti di pubblicazione di Azure Marketplace più recenti. Il servizio client consente di testare una macchina virtuale prima di inviare l'offerta per la certificazione Microsoft.


## <a name="development-and-testing-overview"></a>Panoramica di sviluppo e test

Come parte del processo di verifica automatica, si creerà un client locale che si connette ad Azure Marketplace per convalidare una macchina virtuale in esecuzione nella sottoscrizione di Azure. La macchina virtuale può eseguire il sistema operativo Windows o Linux.

Il client locale esegue uno script che esegue l'autenticazione con l'API di verifica automatica, invia le informazioni di connessione e riceve i risultati del test.

I passaggi principali per la creazione di un client di verifica automatica sono:

1. Scegliere il tenant di Azure Active Directory (AD) per l'applicazione.
2. Registrare l'app client.
3. Creare un token per l'app Azure AD client.
4. Passare il token all'API di verifica automatica.

Dopo aver creato il client, è possibile eseguire il test nella macchina virtuale.


### <a name="self-test-client-authorization"></a>Autorizzazione del client di verifica automatica

Il diagramma seguente mostra il funzionamento dell'autorizzazione per le chiamate da servizio a servizio usando le credenziali del client (segreto condiviso o certificato).

![Processo di autorizzazione client](./media/stclient-dev-process.png)


## <a name="the-self-test-client-api"></a>API client di verifica automatica

L'API di verifica automatica include un unico endpoint in grado di supportare solo il metodo POST.  Presenta la struttura seguente.

```
Uri:             https:\//isvapp.azurewebsites.net/selftest-vm
Method:          Post
Request Header:  Content-Type: “application/json”
Authorization:   “Bearer xxxx-xxxx-xxxx-xxxxx”
Request body:    The Request body parameters should use the following JSON format:
                 {
                   "DNSName":"XXXX.westus.cloudapp.azure.com",
                   "User":"XXX",
                   "Password":"XXX@1234567",
                   "OS":"XXX",
                   "PortNo":"22",
                   "CompanyName":"ABCD",
                 }

```

La tabella seguente descrive i campi dell'API.


|      Campo         |    DESCRIZIONE    |
|  ---------------   |  ---------------  |
|  Authorization     |  La stringa "Bearer xxxx-xxxx-xxxx-xxxxx" contiene il token client di Azure Active Directory (AD), che può essere creato usando PowerShell.          |
|  DNSName           |  Nome DNS della macchina virtuale da testare    |
|  Utente              |  Nome utente per l'accesso alla macchina virtuale         |
|  Password          |  Password per l'accesso alla macchina virtuale          |
|  OS                |  Sistema operativo della macchina virtuale, ovvero `Linux` o `Windows`          |
|  PortNo            |  Numero della porta aperta per la connessione alla macchina virtuale. Il numero della porta in genere è `22` per Linux e `5986` per Windows.          |
|  |  |


## <a name="consuming-the-api"></a>Utilizzo dell'API

È possibile utilizzare l'API di verifica automatica con PowerShell o cURL.


### <a name="use-powershell-to-consume-the-api-on-the-linux-os"></a>Utilizzare l'API nel sistema operativo Linux con PowerShell

Per chiamare l'API in PowerShell, seguire questa procedura:

1. Usare il comando `Invoke-WebRequest` per chiamare l'API.
2. Il metodo è POST e il tipo di contenuto è JSON, come illustrato nella schermata e nell'esempio di codice seguenti.
3. Specificare i parametri del corpo in formato JSON.

L'esempio di codice seguente mostra una chiamata di PowerShell all'API.

```powershell
$accesstoken = “Get token for your Client AAD App”
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$headers.Add("Authorization", "Bearer $accesstoken")
$Body = @{
    "DNSName" = "XXXX.westus.cloudapp.azure.com"
    "User" = "XXX"
    "Password" = "XXX@123456"
    "OS" = "Linux"
    "PortNo" = "22"
    "CompanyName" = "ABCD"

} | ConvertTo-Json
$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" –Headers $headers; 
$Content = $res | ConvertFrom-Json
```
La schermata seguente mostra un esempio per chiamare l'API in PowerShell.

![Chiamare l'API con PowerShell per il sistema operativo Linux](./media/stclient-call-api-ps-linuxvm.png)

Usando l'esempio precedente, è possibile recuperare il file JSON e analizzarlo in modo da ottenere i dettagli seguenti:

```powershell
$testresult = ConvertFrom-Json –InputObject (ConvertFrom-Json –InputObject $res)

  Write-Host "OSName: $($testresult.OSName)"
  Write-Host "OSVersion: $($testresult.OSVersion)"
  Write-Host "Overall Test Result: $($testresult.TestResult)"

For ($i=0; $i -lt $testresult.Tests.Length; $i++) 
{
    Write-Host "TestID: $($testresult.Tests[$i].TestID)"
    Write-Host "TestCaseName: $($testresult.Tests[$i].TestCaseName)"
    Write-Host "Description: $($testresult.Tests[$i].Description)"
    Write-Host "Result: $($testresult.Tests[$i].Result)"
    Write-Host "ActualValue: $($testresult.Tests[$i].ActualValue)"
}
```

La schermata seguente, che mostra `$res.Content`, fornisce i dettagli dei risultati del test in formato JSON.

![Risultati JSON dalla chiamata di PowerShell a Linux](./media/stclient-pslinux-rescontent-json.png)

La schermata seguente mostra un esempio di risultati del test JSON in un visualizzatore JSON online, ad esempio [Code Beautify](https://codebeautify.org/jsonviewer) o [JSON Viewer](https://jsonformatter.org/json-viewer).

![Risultati JSON dalla chiamata di PowerShell alla macchina virtuale Linux](./media/stclient-consume-api-pslinux-json.png)

### <a name="use-powershell-to-consume-the-api-on-the-windows-os"></a>Utilizzare l'API nel sistema operativo Windows con PowerShell

Per chiamare l'API in PowerShell, seguire questa procedura:

1. Usare il comando `Invoke-WebRequest` per chiamare l'API.
2. Il metodo è POST e il tipo di contenuto è JSON, come illustrato nella schermata e nell'esempio di codice seguenti.
3. Creare i parametri del corpo in formato JSON.

L'esempio di codice seguente mostra una chiamata di PowerShell all'API.

```powershell
$accesstoken = “Get token for your Client AAD App”
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$headers.Add("Authorization", "Bearer $accesstoken")
$Body = @{
    "DNSName" = "XXXX.westus.cloudapp.azure.com"
    "User" = "XXX"
    "Password" = "XXX@123456"
    "OS" = "Windows"
    "PortNo" = "5986"
    "CompanyName" = "ABCD"

} | ConvertTo-Json
$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" –Headers $headers;
$Content = $res | ConvertFrom-Json
```

La schermata seguente mostra un esempio per chiamare l'API in PowerShell.

![Chiamare l'API con PowerShell per una macchina virtuale Windows](./media/stclient-call-api-ps-windowsvm.png)

Usando l'esempio precedente, è possibile recuperare il file JSON e analizzarlo in modo da ottenere i dettagli seguenti:

```powershell
$testresult = ConvertFrom-Json –InputObject (ConvertFrom-Json –InputObject $res)

  Write-Host "OSName: $($testresult.OSName)"
  Write-Host "OSVersion: $($testresult.OSVersion)"
  Write-Host "Overall Test Result: $($testresult.TestResult)"

For ($i=0; $i -lt $testresult.Tests.Length; $i++) 
{
    Write-Host "TestID: $($testresult.Tests[$i].TestID)"
    Write-Host "TestCaseName: $($testresult.Tests[$i].TestCaseName)"
    Write-Host "Description: $($testresult.Tests[$i].Description)"
    Write-Host "Result: $($testresult.Tests[$i].Result)"
    Write-Host "ActualValue: $($testresult.Tests[$i].ActualValue)"
}
```

La schermata seguente, che mostra `$res.Content`, fornisce i dettagli dei risultati del test in formato JSON.

![Risultati JSON dalla chiamata di PowerShell a Windows](./media/stclient-pswindows-rescontent-json.png)

La schermata seguente mostra i risultati del test in un visualizzatore JSON online,
ad esempio, [Code Beautify](https://codebeautify.org/jsonviewer) o [JSON Viewer](https://jsonformatter.org/json-viewer).

![Risultati JSON dalla chiamata di PowerShell alla macchina virtuale Windows](./media/stclient-consume-api-pswindows-json.png)

### <a name="use-curl-to-consume-the-api-on-the-linux-os"></a>Utilizzare l'API nel sistema operativo Linux con cURL

Per chiamare l'API con cURL, seguire questa procedura:

1. Usare il comando curl per chiamare l'API.
2. Il metodo è POST e il tipo di contenuto è JSON, come illustrato nel frammento di codice seguente.

```
CURL POST -H "Content-Type:application/json" 
-H "Authorization: Bearer XXXXXX-Token-XXXXXXXX”
https://isvapp.azurewebsites.net/selftest-vm 
-d '{ "DNSName":"XXXX.westus.cloudapp.azure.com", "User":"XXX", "Password":"XXXX@123456", "OS":"Linux", "PortNo":"22", "CompanyName":"ABCD"}'

```
La schermata seguente mostra un esempio dell'uso di curl per chiamare l'API.

![Chiamare l'API usando il comando curl](./media/stclient-consume-api-curl.png)

La schermata seguente mostra i risultati JSON dalla chiamata di curl.

![JSON risultante dalla chiamata di curl](./media/stclient-consume-api-curl-json.png)


## <a name="choose-the-azure-ad-tenant-for-the-app"></a>Scegliere il tenant di Azure AD per l'app

Seguire questa procedura per scegliere il tenant di Azure AD in cui si vuole creare l'applicazione.

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Nella barra dei menu superiore selezionare l'account e nell'elenco Directory scegliere il tenant di Active Directory in cui registrare l'applicazione. In alternativa, selezionare l'icona **Directory e sottoscrizione** per visualizzare il filtro sottoscrizioni globali. La schermata seguente illustra un esempio di questo filtro.

   ![Selezionare il filtro delle sottoscrizioni](./media/stclient-subscription-filter.png)

3. Nella barra di spostamento a sinistra selezionare **Tutti i servizi** e quindi selezionare **Azure Active Directory**.

   Nei passaggi seguenti può essere necessario il nome del tenant (o della directory) oppure l'ID del tenant (o della directory).

   **Per ottenere le informazioni sul tenant:**
  
   Nella **Panoramica di Azure Active Directory** cercare "Proprietà" e quindi selezionare **Proprietà**. Usare la schermata seguente come esempio:

   - **Nome** -Nome del tenant o della directory
   - **ID directory** -ID del tenant o della directory, in alternativa usare la barra di scorrimento per trovare le proprietà.

   ![Pagina delle proprietà di Azure Active Directory](./media/stclient-aad-properties.png)

## <a name="register-the-client-app"></a>Registrare l'app client

Seguire questa procedura per registrare l'app client.

1. Nella barra di spostamento a sinistra selezionare **Tutti i servizi** e quindi selezionare **Registrazioni app**.
2. In **Registrazioni app** selezionare **+ Registrazione nuova applicazione**.
3. In **Crea** inserire le informazioni necessarie per i campi seguenti:

   - **Nome**: immettere un nome descrittivo per l'app. Ad esempio, "SelfTestClient".
   - **Tipo di applicazione**: selezionare **App Web/API**
   - **URL Sign-on** – tipo di "https:\//isvapp.azurewebsites.net/selftest-vm"

4. Selezionare **Create**.
5. In **Registrazioni app** oppure **App registrata** copiare l'**ID applicazione**.

   ![Ottenere l'ID dell'applicazione](./media/stclient-app-id.png)

6. Nella barra dell'app registrata selezionare **Impostazioni**.
7. Selezionare **Autorizzazioni necessarie** per configurare le autorizzazioni per l'applicazione.
8. In **Autorizzazioni necessarie** selezionare **+ Aggiungi**.
9. In **Aggiungi accesso all'API** scegliere **Selezionare un'API**.
10. In **Selezionare un'API** digitare "Modello di distribuzione classica di Microsoft Azure" per cercare l'API.
11. Nei risultati della ricerca selezionare il **modello di distribuzione classica di Azure** e quindi fare clic su **Seleziona**.

    ![Configurare il supporto multi-tenant per l'app](./media/stclient-select-api.png)

12. In **Aggiungi accesso all'API** fare clic su **Selezionare le autorizzazioni**.
13. Selezionare **Accesso "API Gestione dei servizi Microsoft Azure"**.

    ![Abilitare l'accesso all'API per l'app](./media/stclient-enable-api-access.png)

14. Fare clic su **Seleziona**.
15. Selezionare **Operazione completata**.
16. In **Impostazioni** selezionare **Proprietà**.
17. In **Proprietà** scorrere verso il basso fino a **Multi-tenant**. Selezionare **Sì**.  

    ![Configurare il supporto multi-tenant per l'app](./media/stclient-yes-multitenant.png)

18. Selezionare **Salva**.
19. In **Impostazioni** selezionare **Chiavi**.
20. Creare una chiave privata selezionando la casella di testo **DESCRIZIONE** della chiave. Configurare i campi seguenti:

    - Digitare un nome di chiave. Ad esempio, "selftestclient"
    - Nell'elenco a discesa **SCADENZA** selezionare "Tra 1 anno".
    - Selezionare **Salva** per generare la chiave.
    - In**VALORE** copiare la chiave.

      >[!Important]
      >Non sarà possibile vedere il valore della chiave dopo aver chiuso il modulo **Chiavi**.

    ![Modulo valore della chiave](./media/stclient-create-key.png)

## <a name="create-the-token-for-the-client-app"></a>Creare il token per l'app client

Per creare e ottenere un token tramite l'API REST di OAuth, è possibile usare uno qualsiasi dei programmi seguenti:

- postman
- cURL in Linux
- C&#35;
- PowerShell

### <a name="to-create-and-get-a-token-using-postman"></a>Per creare e ottenere un token tramite Postman

 Per richiedere token a Auth0 per qualsiasi applicazione autorizzata, eseguire un'operazione POST sull'endpoint [https://login.microsoftonline.com/common/oauth2/token](https://login.microsoftonline.com/common/oauth2/token) con un payload nel formato seguente:

```
Method Type : POST
Base Url: https://login.microsoftonline.com/common/oauth2/token
```
Passare i parametri seguenti nel corpo della richiesta:

```
Body Content-Type: x-www-form-urlencoded
client_id: XXX (Paste your Application ID of Web App/API Type client AD App)
grant_type: client_credentials
client_secret: XXX (Paste your Secret Key of Web App/API Type client AD App)
resource: https://management.core.windows.net
```

Passare i parametri seguenti nell'intestazione della richiesta:

```
Content-Type: application/x-www-form-urlencoded
```

La schermata seguente illustra un esempio dell'uso di Postman per ottenere un token.

![Ottenere un token con Postman](./media/stclient-postman-get-token.png)

### <a name="to-create-and-get-a-token-using-curl-in-linux"></a>Per creare e ottenere un token tramite cURL in Linux

Per richiedere token a Auth0 per qualsiasi applicazione autorizzata, eseguire un'operazione POST sull'endpoint [https://login.microsoftonline.com/common/oauth2/token](https://login.microsoftonline.com/common/oauth2/token) con un payload nel formato seguente:

```
Request:

curl --request POST \
                --url 'https://login.microsoftonline.com/common/oauth2/token' \
                --header "Content-Type:application/x-www-form-urlencoded" \
                --data "grant_type=client_credentials&client_id=XXXXX-XXXX-XXXX-XXXXXXX&client_secret=XXXXXXXXX&resource=https://management.core.windows.net"

Response:

{"token":"UClCUUKxUlkdbhE1cHLz3kyjbIZYVh9eB34A5Q21Y3FPqKGSJs","expires":"2014-02-17 18:46:08"}
```

La schermata seguente illustra un esempio dell'uso del comando curl per ottenere un token.

![Ottenere un token con il comando curl](./media/stclient-curl-get-token.png)

### <a name="to-create-and-get-a-token-using-c35"></a>Per creare e ottenere un token tramite C&#35;

Per richiedere token a Auth0 per qualsiasi applicazione autorizzata, eseguire un'operazione POST sull'endpoint [https://soamtenant.auth0.com/oauth/token](https://soamtenant.auth0.com/oauth/token) con un payload nel formato seguente:

```
string clientId = "Your Application Id";
string clientSecret = "Your Application Secret";
string audience = "https://management.core.windows.net";
string authority = String.Format(System.Globalization.CultureInfo.InvariantCulture, "https://login.microsoftonline.com/common/oauth2/token");
string grantType = "client_credentials";
var client = new RestClient(authority);
var request = new RestRequest(Method.POST);
       request.AddHeader("content-type", "application/x-www-form- urlencoded");

string requestBody = "grant_type=" + grantType + "&" + "client_id=" +   clientId + "&" + "client_secret=" + clientSecret + "&" + "resource=" +  audience;

request.AddParameter("application/x-www-form-urlencoded", requestBody,  ParameterType.RequestBody);

IRestResponse response = client.Execute(request);
var content = response.Content;
var token = JObject.Parse(content)["access_token"];
```

### <a name="to-create-and-get-a-token-using-powershell"></a>Per creare e ottenere un token tramite PowerShell

Per richiedere token a Auth0 per qualsiasi applicazione autorizzata, eseguire un'operazione POST sull'endpoint [https://soamtenant.auth0.com/oauth/token](https://soamtenant.auth0.com/oauth/token) con un payload nel formato seguente:

```
$clientId = "Application Id of AD Client APP";
$clientSecret = "Secret Key of AD Client APP “
$audience = "https://management.core.windows.net";
$authority = "https://login.microsoftonline.com/common/oauth2/token"
$grantType = "client_credentials";

$requestBody = "grant_type=" + $grantType + "&" + "client_id=" +  $clientId   + "&" + "client_secret=" + $clientSecret + "&" + "resource=" + $audience;

$headers = New-Object  "System.Collections.Generic.Dictionary[[String],[String]]"
             $headers.Add("ContentType", "application/x-www-form-urlencoded")
resp = Invoke-WebRequest -Method Post -Uri $authority -Headers $headers -ContentType 'application/x-www-form-urlencoded' -Body $requestBody

$token = $resp.Content | ConvertFrom-Json
$token.AccessToken

```

## <a name="pass-the-client-app-token-to-the-api"></a>Passare il token dell'app client all'API

Passare il token all'API di verifica automatica usando il codice seguente nell'intestazione dell'autorizzazione:

```
$redirectUri = ‘https://isvapp.azurewebsites.net/selftest-vm’
$accesstoken = ‘place your token here’

$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$headers.Add("Authorization", "Bearer $accesstoken")
$Body =
@{
      'DNSName'="XXXX.cloudapp.azure.com";
      'User'="XXX";
      'Password'="XXXX@12345";
      'OS'="Linux";
      'PortNo'="22"
  } | ConvertTo-Json

$result=Invoke-WebRequest -Method Post -Uri $redirectUri -Headers $headers -ContentType 'application/json' -Body $Body
$result
echo 'Test Results:'
$result.Content

```

## <a name="test-your-self-test-client"></a>Testare il client di verifica automatica

Per testare il client, seguire questa procedura:

1. Distribuire la macchina virtuale che si vuole testare.
2. Chiamare l'API di verifica automatica usando il token dell'app client per l'autorizzazione.
3. Ottenere i risultati del test in formato JSON.

### <a name="test-result-examples"></a>Esempi di risultati del test

I frammenti di codice seguenti mostrano mostra i risultati del test in formato JSON.

**Risultati del test per una macchina virtuale Windows:**

```
{
  "SchemaVersion": 1,
  "AppCertificationCategory": "Microsoft Single VM Certification",
  "ProviderID": "050DE427-2A99-46D4-817C-5354D3BF2AE8",
  "OSName": "Microsoft Windows Server 2016 Datacenter",
  "OSVersion": "10.0.14393",
  "CreatedDate": "06/01/2018 07:48:04",
  "TestResult": "Pass",
  "APIVersion": "1.1",
  "Tests": [
    {
      "Tes tID": "1.1.4",
      "TestCaseName": "OS Architecture",
      "Description": "Azure supports 64bit Operating System Only.",
      "Result": "Passed",
      "ActualValue": "64 Bit",
      "Re quiredValue": "OS Should be 64 bit"
    },
    {
      "TestID": "1.1.5",
      "TestCaseName": "User account dependency",
      "Description": "Application execution should not have de pendency on administrator account.",
      "Result": "Passed",
      "ActualValue": "isv",
      "RequiredValue": "Logged in user should not be an administrator"
    },
    {
      "TestID": " 1.1.6",
      "TestCaseName": "Failover Cluster",
      "Description": "Windows Server Failover Clustering feature is not yet supported, Application should not have dependency on this feature.",
      "Result": "Passed",
      "ActualValue": "Disabled",
      "RequiredValue": "Failover Clustering feature is disabled"
    },
```

**Risultati del test per una macchina virtuale Linux:**

```
{
  "SchemaVersion": 1,
  "AppCertificationCategory": "Microsoft Single VM Certification",
  "ProviderID": "050DE427-2A99-46D4-817C-5354D3BF2AE8",
  "OSName": "Ubuntu  16.04",
  "OSVersion": "16.04",
  "CreatedDate": "06/01/2018 07:04:24",
  "TestResult": "Pass",
  "APIVersion": "1.1",
  "Tests": [
    {
      "TestID": "48",
      "TestCaseName": "Bash Hi story",
      "Description": "Bash history files should be cleared before creating the VM image.",
      "Result": "Passed",
      "ActualValue": "No file Exist",
      "RequiredVal ue": "1024"
    },
    {
      "TestID": "39",
      "TestCaseName": "Linux Agent Version",
      "Description": "Azure Linux Agent 2.2.10 and above should be installed. ",
      "Result": "Pas sed",
      "ActualValue": "2.2.20",
      "RequiredValue": "2.2.10"
    },
    {
      "TestID": "40",
      "TestCaseName": "Required Kernel Parameters",
      "Description": "Verifies the following  kernel parameters are set console=ttyS0, earlyprintk=ttyS0, rootdelay=300",
      "Result": "Passed",
      "ActualValue": "Matched Parameter: console=ttyS0,earlypri ntk=ttyS0,rootdelay=300",
      "RequiredValue": "console=ttyS0#earlyprintk=ttyS0#rootdelay=300"
    },
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver testato correttamente la macchina virtuale di Azure, è possibile [pubblicare l'offerta](./cpp-publish-offer.md).
