---
title: 'Avvio rapido : Libreria client di Azure Key Vault per .NET'
description: Fornisce criteri di formato e contenuto per la scrittura di argomenti di avvio rapido per le librerie client di Azure SDK.
author: msmbaldwin
ms.author: mbaldwin
ms.date: 05/20/2019
ms.service: key-vault
ms.topic: quickstart
ms.openlocfilehash: 4f039fde275e5dca49b3c78550722d827bead062
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155109"
---
# <a name="quickstart-azure-key-vault-client-library-for-net"></a>Guida introduttiva: Libreria client di Azure Key Vault per .NET

Introduzione alla libreria client di Azure Key Vault per .NET. Seguire questi passaggi per installare il pacchetto e provare il codice di esempio per le attività di base.

L'insieme di credenziali delle chiavi di Azure consente di proteggere le chiavi e i segreti di crittografia usati da servizi e applicazioni cloud. Usare la libreria client di Azure Key Vault per .NET per:

- Aumentare la sicurezza e il controllo di chiavi e password.
- Creare e importare chiavi di crittografia in pochi minuti.
- Ridurre la latenza con ridondanza globale e su scala cloud.
- Semplificare e automatizzare le attività per i certificati SSL/TLS.
- Usare moduli di protezione hardware convalidati in base agli standard FIPS 140-2 livello 2.

[Documentazione di riferimento dell'API](/dotnet/api/overview/azure/key-vault?view=azure-dotnet) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/KeyVault) | [Pacchetto (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.KeyVault/)

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure: [creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [.NET Core 2.1 SDK o versione successiva](https://dotnet.microsoft.com/download/dotnet-core/2.1).
* [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) o [Azure PowerShell](/powershell/azure/overview)

Questo argomento di avvio rapido presuppone che si eseguano i comandi di `dotnet`, dell'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) e di Windows in un terminale di Windows, ad esempio [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-6), [Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6) o [Azure Cloud Shell](https://shell.azure.com/).

## <a name="setting-up"></a>Configurazione

### <a name="create-new-net-console-app"></a>Creare una nuova app console .NET

Creare una nuova applicazione .NET Core nell'ambiente di sviluppo integrato o nell'editor preferito.

In una finestra di una console usare il comando `dotnet new` per creare una nuova app console denominata `akv-dotnet`.


```console
dotnet new console -n akvdotnet
```

Spostarsi nella cartella dell'app appena creata. È possibile compilare l'applicazione con il comando seguente:

```console
dotnet build
```

L'output di compilazione non deve contenere alcun avviso o errore.

```console
Build succeeded.
 0 Warning(s)
 0 Error(s)
```

### <a name="install-the-package"></a>Installare il pacchetto

Nella finestra della console installare la libreria client di Azure Key Vault per .NET:

```console
dotnet add package Microsoft.Azure.KeyVault
```

Per questo argomento di avvio rapido, è necessario installare anche i pacchetti seguenti:

```console
dotnet add package System.Threading.Tasks
dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory
dotnet add package Microsoft.Azure.Management.ResourceManager.Fluent
```

### <a name="create-a-resource-group-and-key-vault"></a>Creare un gruppo di risorse e un insieme di credenziali delle chiavi

In questo argomento di avvio rapido si usa un'istanza creata in precedenza di Azure Key Vault. È possibile creare un insieme di credenziali delle chiavi seguendo la procedura descritta negli argomenti di avvio rapido per l'[interfaccia della riga di comando di Azure](quick-create-cli.md), [Azure PowerShell](quick-create-powershell.md) o il [portale di Azure](quick-create-portal.md). In alternativa, è possibile eseguire semplicemente i seguenti comandi dell'interfaccia della riga di comando di Azure.

> [!Important]
> Ogni insieme di credenziali delle chiavi deve avere un nome univoco. Negli esempi seguenti sostituire <your-unique-keyvault-name> con il nome dell'insieme di credenziali delle chiavi in uso.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

### <a name="create-a-service-principal"></a>Creare un'entità servizio

Il modo più semplice per autenticare un'applicazione .NET basata sul cloud consiste nell'usare un'identità gestita. Per informazioni dettagliate, vedere [Autenticazione da servizio a servizio ad Azure Key Vault usando .NET](service-to-service-authentication.md). Per semplicità, tuttavia, in questo argomento di avvio rapido viene creata un'applicazione console .NET. Per l'autenticazione di un'applicazione desktop con Azure è necessario usare un'entità servizio.
Creare un'entità servizio usando il comando [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) dell'interfaccia della riga di comando di Azure:

```azurecli
az ad sp create-for-rbac -n "http://mySP" --sdk-auth
```

Questa operazione restituisce una serie di coppie chiave-valore. 

```console
{
  "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
  "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
  "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
  "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "activeDirectoryGraphResourceId": "https://graph.windows.net/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Prendere nota dei valori di clientId e clientSecret, perché si useranno nel successivo passaggio [Eseguire l'autenticazione con l'insieme di credenziali delle chiavi](#authenticate-to-your-key-vault).

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Concedere all'entità servizio l'accesso all'insieme di credenziali delle chiavi

Creare un criterio di accesso per l'insieme di credenziali delle chiavi che conceda l'autorizzazione all'entità servizio, passando il valore di clientId al comando [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy). Concedere all'entità servizio le autorizzazioni get, list e set sia per le chiavi che per i segreti.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

## <a name="object-model"></a>Modello a oggetti

La libreria client di Azure Key Vault per .NET consente di gestire le chiavi e gli asset correlati, come i certificati e i segreti. Gli esempi di codice seguenti mostrano come impostare e recuperare un segreto.

L'intera app console è disponibile all'indirizzo https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/akvdotnet.

## <a name="code-examples"></a>Esempi di codice

### <a name="add-directives"></a>Aggiungere le direttive

Aggiungere le direttive seguenti all'inizio del codice:

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=directives)]

### <a name="authenticate-to-your-key-vault"></a>Eseguire l'autenticazione con l'insieme di credenziali delle chiavi

Questo argomento di avvio rapido per .NET si basa sulle variabili di ambiente per archiviare le credenziali che non dovranno essere inserite nel codice. 

Prima di compilare ed eseguire l'app, usare il comando `setx` per impostare le variabili di ambiente `akvClientId`, `akvClientSecret`, `akvTenantId` e `akvSubscriptionId` sui valori annotati in precedenza.

```console
setx akvClientId <your-clientID>

setx akvClientSecret <your-clientSecret>
````

Ogni volta che si chiama `setx`, si dovrebbe ottenere la risposta "OPERAZIONE RIUSCITA: valore specificato salvato".

Assegnare queste variabili di ambiente alle stringhe del codice, quindi eseguire l'autenticazione dell'applicazione passandole alla [classe KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient):

[!code-csharp[Authentication](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=authentication)]

### <a name="save-a-secret"></a>Salvare un segreto

Ora che l'applicazione è stata autenticata, è possibile inserire un segreto nell'insieme di credenziali delle chiavi usando il [metodo SetSecretAsync](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync). Questa operazione richiede l'URL dell'insieme di credenziali delle chiavi, che è nel formato `https://<your-unique-keyvault-name>.vault.azure.net/secrets/`. Richiede anche un nome per il segreto, in questo esempio "mySecret".  È consigliabile assegnare queste stringhe a variabili per il riutilizzo.

[!code-csharp[Set secret](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=setsecret)]

È possibile verificare che il segreto sia stato impostato con il comando [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show):

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Recuperare un segreto

È ora possibile recuperare il valore impostato in precedenza con il [metodo GetSecretAsync](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync)

[!code-csharp[Get secret](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=getsecret)]

Il segreto è ora salvato come `keyvaultSecret.Value;`.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, è possibile usare l'interfaccia della riga di comando di Azure o Azure PowerShell per rimuovere l'insieme di credenziali delle chiavi e il gruppo di risorse corrispondente.

```azurecli
az group delete -g "myResourceGroup" -l "EastUS" 
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stato creato un insieme di credenziali delle chiavi, quindi è stato archiviato e recuperato un segreto. Vedere l'[intera app console in GitHub](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/akvdotnet).

Per altre informazioni sul servizio Key Vault e su come integrarlo nelle applicazioni, continuare con gli articoli seguenti.

- Implementare [Autenticazione da servizio a servizio ad Azure Key Vault usando .NET](service-to-service-authentication.md)
- Leggere una [panoramica di Azure Key Vault](key-vault-overview.md)
- Vedere la [Guida per gli sviluppatori per Azure Key Vault](key-vault-developers-guide.md)
- Vedere le informazioni su [chiavi, segreti e certificati](about-keys-secrets-and-certificates.md)
- Esaminare le [procedure consigliate per Azure Key Vault](key-vault-best-practices.md)
