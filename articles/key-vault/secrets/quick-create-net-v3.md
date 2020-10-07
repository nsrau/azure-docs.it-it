---
title: Avvio rapido - Libreria client di Azure Key Vault per .NET (SDK v3)
description: Informazioni su come creare, recuperare ed eliminare segreti da Azure Key Vault usando la libreria client .NET (v3)
author: msmbaldwin
ms.author: mbaldwin
ms.date: 11/05/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: 2e5f3357baa774c9690e079a82b11d2c651ebe9b
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "87078868"
---
# <a name="quickstart-azure-key-vault-client-library-for-net-sdk-v3"></a>Avvio rapido: Libreria client di Azure Key Vault per .NET (SDK v3)

Introduzione alla libreria client di Azure Key Vault per .NET. Seguire questi passaggi per installare il pacchetto e provare il codice di esempio per le attività di base.

> [!NOTE]
> Questa Guida di avvio rapido usa la versione 3.0.4 della libreria client Microsoft.Azure.KeyVault. Per usare la versione più aggiornata della libreria client di Azure Key Vault, vedere [Libreria client di Azure Key Vault per .NET (SDK v4)](quick-create-net.md). 

L'insieme di credenziali delle chiavi di Azure consente di proteggere le chiavi e i segreti di crittografia usati da servizi e applicazioni cloud. Usare la libreria client di Azure Key Vault per .NET per:

- Aumentare la sicurezza e il controllo di chiavi e password.
- Creare e importare chiavi di crittografia in pochi minuti.
- Ridurre la latenza con ridondanza globale e su scala cloud.
- Semplificare e automatizzare le attività per i certificati TLS/SSL.
- Usare moduli di protezione hardware convalidati in base agli standard FIPS 140-2 livello 2.

[Documentazione di riferimento dell'API](/dotnet/api/overview/azure/key-vault?view=azure-dotnet) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault) | [Pacchetto (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.KeyVault/)

> [!NOTE]
> Ogni insieme di credenziali delle chiavi deve avere un nome univoco. Negli esempi seguenti sostituire <your-unique-keyvault-name> con il nome dell'insieme di credenziali delle chiavi in uso.


## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure: [creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [.NET Core 3.1 SDK o versione successiva](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) o [Azure PowerShell](/powershell/azure/)

Questo argomento di avvio rapido presuppone che si eseguano i comandi di `dotnet`, dell'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) e di Windows in un terminale di Windows, ad esempio [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-6), [Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6) o [Azure Cloud Shell](https://shell.azure.com/).

## <a name="setting-up"></a>Configurazione

### <a name="create-new-net-console-app"></a>Creare una nuova app console .NET

In una finestra della console usare il comando `dotnet new` per creare una nuova app console .NET denominata `akv-dotnet`.


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

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

### <a name="create-a-service-principal"></a>Creare un'entità servizio

[!INCLUDE [Create a service principal](../../../includes/key-vault-sp-creation.md)]

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Concedere all'entità servizio l'accesso all'insieme di credenziali delle chiavi

[!INCLUDE [Give the service principal access to your key vault](../../../includes/key-vault-sp-kv-access.md)]

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

**Windows**

```console
setx akvClientId "<your-clientID>"
setx akvClientSecret "<your-clientSecret>"
```

**Linux**

```bash
export akvClientId = "<your-clientID>"
export akvClientSecret = "<your-clientSecret>"
```

**MacOS**

```bash
export akvClientId = "<your-clientID>"
export akvClientSecret = "<your-clientSecret>"
```

Assegnare queste variabili di ambiente alle stringhe del codice, quindi eseguire l'autenticazione dell'applicazione passandole alla [classe KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient):

[!code-csharp[Authentication](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=authentication)]

### <a name="save-a-secret"></a>Salvare un segreto

Ora che l'applicazione è stata autenticata, è possibile inserire un segreto nell'insieme di credenziali delle chiavi usando il [metodo SetSecretAsync](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync). Questa operazione richiede l'URL dell'insieme di credenziali delle chiavi, che è nel formato `https://<your-unique-keyvault-name>.vault.azure.net/secrets/`. Richiede anche un nome per il segreto, in questo esempio "mySecret". 

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
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stato creato un insieme di credenziali delle chiavi, quindi è stato archiviato e recuperato un segreto. Vedere l'[intera app console in GitHub](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/akvdotnet).

Per altre informazioni sul servizio Key Vault e su come integrarlo nelle applicazioni, continuare con gli articoli seguenti.

- Implementare [Autenticazione da servizio a servizio ad Azure Key Vault usando .NET](../general/service-to-service-authentication.md)
- Leggere una [panoramica di Azure Key Vault](../general/overview.md)
- Vedere la [Guida per gli sviluppatori per Azure Key Vault](../general/developers-guide.md)
- Esaminare le [procedure consigliate per Azure Key Vault](../general/best-practices.md)
