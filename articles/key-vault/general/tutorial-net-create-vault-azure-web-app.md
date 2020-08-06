---
title: Esercitazione - Usare Azure Key Vault con un'app Web di Azure in .NET | Microsoft Docs
description: In questa esercitazione si configura un'applicazione ASP.NET Core per la lettura di un segreto dall'insieme di credenziali delle chiavi.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: mbaldwin
ms.openlocfilehash: a198456412c3146db2bc3e2a2483377e387f452d
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2020
ms.locfileid: "87513333"
---
# <a name="tutorial-use-a-managed-identity-to-connect-key-vault-to-an-azure-web-app-with-net"></a>Esercitazione: Usare un'identità gestita per connettere Key Vault a un'app Web di Azure con .NET

Azure Key Vault consente di archiviare in modo sicuro le credenziali e altri segreti, ma il codice deve effettuare l'autenticazione in Key Vault per recuperarle. Nella [panoramica delle identità gestite per le risorse di Azure](../../active-directory/managed-identities-azure-resources/overview.md) viene spiegato come risolvere il problema, assegnando ai servizi di Azure un'identità gestita automaticamente in Azure AD. È possibile usare questa identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD, incluso Key Vault, senza visualizzare le credenziali nel codice.

Questa esercitazione usa un'identità gestita per l'autenticazione di un'app Web di Azure con Azure Key Vault. Nonostante in questi passaggi vengano usate la [libreria client di Azure Key Vault v4 per .NET](/dotnet/api/overview/azure/key-vault?view=azure-dotnet) e l'[interfaccia della riga di comando di Azure](/cli/azure/get-started-with-azure-cli), gli stessi principi di base si applicano quando si usano il linguaggio di sviluppo preferito, Azure PowerShell e/o il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida introduttiva:

* Una sottoscrizione di Azure: [creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [.NET Core 3.1 SDK o versione successiva](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) o [Azure PowerShell](/powershell/azure/)

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. Creare un gruppo di risorse per ospitare sia l'insieme di credenziali delle chiavi che l'app Web con il comando [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create):

```azurecli-interactive
az group create --name "myResourceGroup" -l "EastUS"
```

## <a name="set-up-your-key-vault"></a>Configurare l'insieme di credenziali delle chiavi

A questo punto si creerà un insieme di credenziali delle chiavi in cui si inserirà un segreto, per usarli più avanti in questa esercitazione.

Per creare un insieme di credenziali delle chiavi, usare il comando [az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create):

> [!Important]
> Ogni insieme di credenziali delle chiavi deve avere un nome univoco. Negli esempi seguenti sostituire <your-keyvault-name> con il nome dell'insieme di credenziali delle chiavi in uso.

```azurecli-interactive
az keyvault create --name "<your-keyvault-name>" -g "myResourceGroup"
```

Prendere nota del valore di `vaultUri` restituito, che avrà il formato "https://&lt;your-keyvault-name&gt;.vault.azure.net/". Verrà usato nel passaggio [Aggiornare il codice](#update-the-code).

[!INCLUDE [Create a secret](../../../includes/key-vault-create-secret.md)]

## <a name="create-a-net-web-app"></a>Creare un'app Web .NET

### <a name="create-a-local-app"></a>Creare un'app locale

In una finestra del terminale nel computer creare una directory denominata `akvwebapp` e passare dalla directory corrente a questa.

```bash
mkdir akvwebapp
cd akvwebapp
```

Creare quindi una nuova app .NET Core con il comando [dotnet new web](/dotnet/core/tools/dotnet-new):

```bash
dotnet new web
```

Eseguire l'applicazione in locale, in modo da verificare l'aspetto che assumerà dopo la distribuzione in Azure. 

```bash
dotnet run
```

Aprire un Web browser e passare all'app all'indirizzo `http://localhost:5000`.

Nella pagina verrà visualizzato il messaggio **Hello World** dell'app di esempio.

### <a name="initialize-the-git-repository"></a>Inizializzare il repository Git

Nella finestra del terminale premere **CTRL+C** per uscire dal server Web.  Inizializzare un repository Git per il progetto .NET Core.

```bash
git init
git add .
git commit -m "first commit"
```

### <a name="configure-a-deployment-user"></a>Configurare un utente della distribuzione

FTP e l'istanza Git locale possono essere usati per la distribuzione in un'app Web tramite un *utente della distribuzione*. Dopo aver configurato l'utente della distribuzione, è possibile usarlo per tutte le distribuzioni di Azure. Il nome utente e la password della distribuzione a livello di account sono diversi dalle credenziali della sottoscrizione di Azure. 

Per configurare l'utente della distribuzione, eseguire il comando [az webapp deployment user set](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set). Scegliere un nome utente e una password che rispettino le linee guida seguenti: 

- Il nome utente deve essere univoco in Azure e per i push Git locali non deve contenere il simbolo "\@". 
- La password deve essere composta da almeno otto caratteri, con due dei tre elementi seguenti: lettere, numeri e simboli. 

```azurecli-interactive
az webapp deployment user set --user-name "<username>" --password "<password>"
```

L'output JSON mostra la password come `null`. Se viene visualizzato un errore `'Conflict'. Details: 409`, modificare il nome utente. Se viene visualizzato un errore `'Bad Request'. Details: 400`, usare una password più complessa. 

Registrare il nome utente e la password da usare per distribuire le app Web.

### <a name="create-an-app-service-plan"></a>Creare un piano di servizio app

Creare un piano di servizio app con il comando [az appservice plan create](/cli/azure/appservice/plan?view=azure-cli-latest) dell'interfaccia della riga di comando di Azure. L'esempio seguente crea un piano di servizio app denominato `myAppServicePlan` nel piano tariffario **Gratuito**:

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

Al termine della creazione del piano di servizio app, l'interfaccia della riga di comando di Azure visualizza informazioni simili all'esempio seguente:

<pre>
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "West Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "West Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  &lt; JSON data removed for brevity. &gt;
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
} 
</pre>


### <a name="create-a-remote-web-app"></a>Creare un'app Web remota

Creare un'[app Web di Azure](../../app-service/containers/app-service-linux-intro.md) nel piano di servizio app `myAppServicePlan`. 

> [!Important]
> Così come un insieme di credenziali delle chiavi, un'app Web di Azure deve avere un nome univoco. Negli esempi seguenti sostituire \<your-webapp-name\> con il nome dell'app Web.


```azurecli-interactive
az webapp create --resource-group "myResourceGroup" --plan "myAppServicePlan" --name "<your-webapp-name>" --deployment-local-git
```

Dopo la creazione dell'app Web, l'interfaccia della riga di comando di Azure mostra un output simile all'esempio seguente:

<pre>
Local git is configured with url of 'https://&lt;username&gt;@&lt;your-webapp-name&gt;.scm.azurewebsites.net/&lt;ayour-webapp-name&gt;.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "clientCertExclusionPaths": null,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;your-webapp-name&gt;.azurewebsites.net",
  "deploymentLocalGitUrl": "https://&lt;username&gt;@&lt;your-webapp-name&gt;.scm.azurewebsites.net/&lt;your-webapp-name&gt;.git",
  "enabled": true,
  &lt; JSON data removed for brevity. &gt;
}
</pre>


L'URL dell'elemento Git remoto è riportato nella proprietà `deploymentLocalGitUrl`, con il formato `https://<username>@<your-webapp-name>.scm.azurewebsites.net/<your-webapp-name>.git`. Salvare questo URL, perché sarà necessario in un secondo momento.

Passare all'app appena creata. Sostituire _&lt;your-webapp-name>_ con il nome dell'app.

```bash
https://<your-webapp-name>.azurewebsites.net
```

Verrà visualizzata la pagina Web predefinita dell'app Web di Azure appena creata.

### <a name="deploy-your-local-app"></a>Distribuire l'app locale

Nella finestra del terminale locale aggiungere un'istanza remota di Azure al repository Git locale, sostituendo *\<deploymentLocalGitUrl-from-create-step>* con l'URL dell'elemento Git remoto salvato nel passaggio [Creare un'app Web remota](#create-a-remote-web-app).

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Effettuare il push all'istanza remota di Azure per distribuire l'app con il comando seguente. Quando Git Credential Manager richiede le credenziali, usare le credenziali create nel passaggio [Configurare un utente della distribuzione](#configure-a-deployment-user).

```bash
git push azure master
```

L'esecuzione del comando può richiedere alcuni minuti. Durante l'esecuzione, il comando visualizza informazioni simili all'esempio seguente:
<pre>
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 285 bytes | 95.00 KiB/s, done.
Total 3 (delta 2), reused 0 (delta 0), pack-reused 0
remote: Deploy Async
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'd6b54472f7'.
remote: Repository path is /home/site/repository
remote: Running oryx build...
remote: Build orchestrated by Microsoft Oryx, https://github.com/Microsoft/Oryx
remote: You can report issues at https://github.com/Microsoft/Oryx/issues
remote:
remote: Oryx Version      : 0.2.20200114.13, Commit: 204922f30f8e8d41f5241b8c218425ef89106d1d, ReleaseTagName: 20200114.13
remote: Build Operation ID: |imoMY2y77/s=.40ca2a87_
remote: Repository Commit : d6b54472f7e8e9fd885ffafaa64522e74cf370e1
.
.
.
remote: Deployment successful.
remote: Deployment Logs : 'https://&lt;your-webapp-name&gt;.scm.azurewebsites.net/newui/jsonviewer?view_url=/api/deployments/d6b54472f7e8e9fd885ffafaa64522e74cf370e1/log'
To https://&lt;your-webapp-name&gt;.scm.azurewebsites.net:443/&lt;your-webapp-name&gt;.git
   d87e6ca..d6b5447  master -> master
</pre>

Passare all'applicazione distribuita o aggiornarla con il Web browser.

```bash
http://<your-webapp-name>.azurewebsites.net
```

Verrà visualizzato il messaggio "Hello World!" visualizzato in precedenza visitando `http://localhost:5000`.

## <a name="create-and-assign-a-managed-identity"></a>Creare e assegnare un'identità gestita

Nell'interfaccia della riga di comando di Azure eseguire il comando [az webapp-identity assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) per creare l'identità per l'applicazione:

```azurecli-interactive
az webapp identity assign --name "<your-webapp-name>" --resource-group "myResourceGroup"
```

L'operazione restituirà il frammento JSON seguente:

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```

Per concedere all'app Web l'autorizzazione per eseguire operazioni **get** e **list** sull'insieme di credenziali delle chiavi, passare principalID al comando [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) dell'interfaccia della riga di comando di Azure:

```azurecli-interactive
az keyvault set-policy --name "<your-keyvault-name>" --object-id "<principalId>" --secret-permissions get list
```


## <a name="modify-the-app-to-access-your-key-vault"></a>Modificare l'app per l'accesso all'insieme di credenziali delle chiavi

### <a name="install-the-packages"></a>Installare i pacchetti

Nella finestra del terminale installare i pacchetti della libreria client di Azure Key Vault per .NET:

```console
dotnet add package Azure.Identity
dotnet add package Azure.Security.KeyVault.Secrets
```

### <a name="update-the-code"></a>Aggiornare il codice

Trovare e aprire il file Startup.cs nel progetto akvwebapp. 

Aggiungere queste due righe all'intestazione:

```csharp
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;
using Azure.Core;
```

Aggiungere le righe prima della chiamata di `app.UseEndpoints`, aggiornando l'URI in modo da riflettere il valore di `vaultUri` dell'insieme di credenziali delle chiavi. Il codice seguente usa ['DefaultAzureCredential()'](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet) per l'autenticazione all'insieme di credenziali delle chiavi, che usa il token dell'identità gestita dell'applicazione per l'autenticazione. Viene anche usato il backoff esponenziale per la ripetizione dei tentativi in caso di limitazione dell'insieme di credenziali delle chiavi.

```csharp
SecretClientOptions options = new SecretClientOptions()
    {
        Retry =
        {
            Delay= TimeSpan.FromSeconds(2),
            MaxDelay = TimeSpan.FromSeconds(16),
            MaxRetries = 5,
            Mode = RetryMode.Exponential
         }
    };
var client = new SecretClient(new Uri("https://<your-unique-key-vault-name>.vault.azure.net/"), new DefaultAzureCredential(),options);

KeyVaultSecret secret = client.GetSecret("mySecret");

string secretValue = secret.Value;
```

Aggiornare la riga `await context.Response.WriteAsync("Hello World!");` come segue:

```csharp
await context.Response.WriteAsync(secretValue);
```

Assicurarsi di salvare le modifiche prima di procedere al passaggio successivo.

### <a name="redeploy-your-web-app"></a>Ridistribuire l'app Web

Dopo aver aggiornato il codice, è possibile ridistribuirlo in Azure con i comandi Git seguenti:

```bash
git add .
git commit -m "Updated web app to access my key vault"
git push azure master
```

## <a name="visit-your-completed-web-app"></a>Visitare l'app Web completata

```bash
http://<your-webapp-name>.azurewebsites.net
```

Dove prima veniva visualizzato **Hello World**, verrà ora visualizzato il valore del segreto: **Operazione riuscita**

## <a name="next-steps"></a>Passaggi successivi

- Vedere altre informazioni sulle [identità gestite per le risorse di Azure](../../active-directory/managed-identities-azure-resources/overview.md)
- Vedere altre informazioni sulle [identità gestite per il servizio app](../../app-service/overview-managed-identity.md?tabs=dotnet)
- Vedere la [documentazione di riferimento API sulla libreria client di Azure Key Vault per .NET](/dotnet/api/overview/azure/key-vault?view=azure-dotnet)
- Vedere il [codice sorgente della libreria client di Azure Key Vault per .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault)
- Vedere il [pacchetto NuGet della libreria client di Azure Key Vault v4 per .NET](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)


