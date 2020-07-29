---
title: Avvio rapido - Libreria client di Azure Key Vault per .NET (v4)
description: Informazioni su come creare, recuperare ed eliminare segreti da Azure Key Vault usando la libreria client .NET (v4)
author: msmbaldwin
ms.author: mbaldwin
ms.date: 03/12/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: a0e27927c91c8b8ed1cfca410e08a5eb90117f58
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87076723"
---
# <a name="quickstart-azure-key-vault-client-library-for-net-sdk-v4"></a>Avvio rapido: Libreria client di Azure Key Vault per .NET (SDK v4)

Introduzione alla libreria client di Azure Key Vault per .NET. Seguire questi passaggi per installare il pacchetto e provare il codice di esempio per le attività di base.

L'insieme di credenziali delle chiavi di Azure consente di proteggere le chiavi e i segreti di crittografia usati da servizi e applicazioni cloud. Usare la libreria client di Azure Key Vault per .NET per:

- Aumentare la sicurezza e il controllo di chiavi e password.
- Creare e importare chiavi di crittografia in pochi minuti.
- Ridurre la latenza con ridondanza globale e su scala cloud.
- Semplificare e automatizzare le attività per i certificati TLS/SSL.
- Usare moduli di protezione hardware convalidati in base agli standard FIPS 140-2 livello 2.

[Documentazione di riferimento dell'API](/dotnet/api/azure.security.keyvault.secrets?view=azure-dotnet) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault) | [Pacchetto (NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure: [creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [.NET Core 3.1 SDK o versione successiva](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) o [Azure PowerShell](/powershell/azure/)

Questo argomento di avvio rapido presuppone che si eseguano i comandi di `dotnet`, dell'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) e di Windows in un terminale di Windows, ad esempio [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-6), [Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6) o [Azure Cloud Shell](https://shell.azure.com/).

## <a name="setting-up"></a>Configurazione

### <a name="create-new-net-console-app"></a>Creare una nuova app console .NET

In una finestra della console usare il comando `dotnet new` per creare una nuova app console .NET denominata `key-vault-console-app`.

```console
dotnet new console -n key-vault-console-app
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
dotnet add package Azure.Security.KeyVault.Secrets
```

Per questo argomento di avvio rapido, è necessario installare anche i pacchetti seguenti:

```console
dotnet add package Azure.Identity
```

### <a name="create-a-resource-group-and-key-vault"></a>Creare un gruppo di risorse e un insieme di credenziali delle chiavi

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

### <a name="create-a-service-principal"></a>Creare un'entità servizio

[!INCLUDE [Create a service principal](../../../includes/key-vault-sp-creation.md)]

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Concedere all'entità servizio l'accesso all'insieme di credenziali delle chiavi

[!INCLUDE [Give the service principal access to your key vault](../../../includes/key-vault-sp-kv-access.md)]

#### <a name="set-environmental-variables"></a>Impostare le variabili di ambiente

[!INCLUDE [Set environmental variables](../../../includes/key-vault-set-environmental-variables.md)]

## <a name="object-model"></a>Modello a oggetti

La libreria client di Azure Key Vault per .NET consente di gestire le chiavi e gli asset correlati, come i certificati e i segreti. Gli esempi di codice riportati di seguito illustrano come creare un client e come impostare, recuperare ed eliminare un segreto.

L'intera app console è disponibile all'indirizzo https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app.

## <a name="code-examples"></a>Esempi di codice

### <a name="add-directives"></a>Aggiungere le direttive

Aggiungere le direttive seguenti all'inizio del codice:

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=directives)]

### <a name="authenticate-and-create-a-client"></a>Autenticare e creare un client

L'autenticazione rispetto all'insieme di credenziali delle chiavi e la creazione di un client dell'insieme di credenziali delle chiavi dipendono dalle variabili di ambiente indicate nel passaggio precedente [Impostare le variabili di ambiente](#set-environmental-variables). Il nome dell'insieme di credenziali delle chiavi viene espanso nel relativo URI, nel formato "https://\<your-key-vault-name\>.vault.azure.net". Il codice seguente utilizza  ['DefaultAzureCredential()'](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet) per l’autenticazione nell’insieme di credenziali delle chiavi, che legge le variabili di ambiente per recuperare il token di accesso. 

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=authenticate)]

### <a name="save-a-secret"></a>Salvare un segreto

Ora che l'applicazione è stata autenticata, è possibile inserire un segreto nell'insieme di credenziali delle chiavi usando il metodo [client.SetSecret](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync). L'operazione richiede un nome per il segreto. In questo esempio viene usato "mySecret".  

[!code-csharp[Set secret](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=setsecret)]

È possibile verificare che il segreto sia stato impostato con il comando [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show):

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

```azurepowershell
(Get-AzKeyVaultSecret -VaultName <your-unique-keyvault-name> -Name mySecret).SecretValueText
```

### <a name="retrieve-a-secret"></a>Recuperare un segreto

È ora possibile recuperare il valore impostato in precedenza con il [metodo client.GetSecret](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync).

[!code-csharp[Get secret](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=getsecret)]

Il segreto è ora salvato come `secret.Value`.

### <a name="delete-a-secret"></a>consente di eliminare un segreto

Eliminare infine il segreto dall'insieme di credenziali delle chiavi con il [metodo client.DeleteSecret](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync).

[!code-csharp[Delete secret](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=deletesecret)]

È possibile verificare che il segreto non sia più dotato del comando [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show):

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

```azurepowershell
(Get-AzKeyVaultSecret -VaultName <your-unique-keyvault-name> -Name mySecret).SecretValueText
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, è possibile usare l'interfaccia della riga di comando di Azure o Azure PowerShell per rimuovere l'insieme di credenziali delle chiavi e il gruppo di risorse corrispondente.

### <a name="delete-a-key-vault"></a>Eliminare un insieme di credenziali delle chiavi
```azurecli
az keyvault delete --name <your-unique-keyvault-name>
```

```powershell
Remove-AzKeyVault -VaultName <your-unique-keyvault-name>
```

### <a name="purge-a-key-vault"></a>Rimuovere un insieme di credenziali delle chiavi
```azurecli
az keyvault purge --location eastus --name <your-unique-keyvault-name>
```

```powershell
Remove-AzKeyVault -VaultName <your-unique-keyvault-name> -InRemovedState -Location eastus
```

### <a name="delete-a-resource-group"></a>Eliminare un gruppo di risorse

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>Codice di esempio

```csharp
using System;
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;

namespace key_vault_console_app
{
    class Program
    {
        static void Main(string[] args)
        {
            string secretName = "mySecret";

            string keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
            var kvUri = "https://" + keyVaultName + ".vault.azure.net";

            var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());

            Console.Write("Input the value of your secret > ");
            string secretValue = Console.ReadLine();

            Console.Write("Creating a secret in " + keyVaultName + " called '" + secretName + "' with the value '" + secretValue + "` ...");

            client.SetSecret(secretName, secretValue);

            Console.WriteLine(" done.");

            Console.WriteLine("Forgetting your secret.");
            secretValue = "";
            Console.WriteLine("Your secret is '" + secretValue + "'.");

            Console.WriteLine("Retrieving your secret from " + keyVaultName + ".");

            KeyVaultSecret secret = client.GetSecret(secretName);

            Console.WriteLine("Your secret is '" + secret.Value + "'.");

            Console.Write("Deleting your secret from " + keyVaultName + " ...");

            client.StartDeleteSecret(secretName);

            System.Threading.Thread.Sleep(5000);
            Console.WriteLine(" done.");

        }
    }
}
```


## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stato creato un insieme di credenziali delle chiavi, quindi è stato archiviato e recuperato un segreto. Vedere l'[intera app console in GitHub](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app).

Per altre informazioni sul servizio Key Vault e su come integrarlo nelle applicazioni, continuare con gli articoli seguenti.

- Implementare [Autenticazione da servizio a servizio ad Azure Key Vault usando .NET](../general/service-to-service-authentication.md)
- Leggere una [panoramica di Azure Key Vault](../general/overview.md)
- Vedere la [Guida per gli sviluppatori per Azure Key Vault](../general/developers-guide.md)
- Esaminare le [procedure consigliate per Azure Key Vault](../general/best-practices.md)
