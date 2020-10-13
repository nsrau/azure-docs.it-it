---
title: Avvio rapido - Libreria client di Azure Key Vault per .NET (v4)
description: Informazioni su come creare, recuperare ed eliminare segreti da Azure Key Vault usando la libreria client .NET (v4)
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/23/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: 97578233c6b636b5ffe35fa8ff0b138903425f79
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91631699"
---
# <a name="quickstart-azure-key-vault-client-library-for-net-sdk-v4"></a>Avvio rapido: Libreria client di Azure Key Vault per .NET (SDK v4)

Introduzione alla libreria dei segreti client di Azure Key Vault per .NET. Seguire questi passaggi per installare il pacchetto e provare il codice di esempio per le attività di base.

[Documentazione di riferimento dell'API](/dotnet/api/azure.security.keyvault.secrets?view=azure-dotnet&preserve-view=true) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault) | [Pacchetto (NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuitamente](https://azure.microsoft.com/free/dotnet)
* [.NET Core 3.1 SDK o versioni successive](https://dotnet.microsoft.com/download/dotnet-core)
* [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?view=azure-cli-latest)

In questo argomento di avvio rapido si usano `dotnet` e l'interfaccia della riga di comando di Azure

## <a name="setup"></a>Configurazione

### <a name="create-a-new-console-app"></a>Creare una nuova applicazione console

Questo argomento di avvio rapido usa la libreria di identità di Azure con l'interfaccia della riga di comando di Azure per autenticare l'utente nei servizi di Azure. Gli sviluppatori possono anche usare Visual Studio o Visual Studio Code per autenticare le chiamate. Per altre informazioni, vedere [Autenticare il client con la libreria client Azure Identity](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme?view=azure-dotnet#authenticate-the-client&preserve-view=true).

### <a name="sign-in-to-azure"></a>Accedere ad Azure

1. Eseguire il comando `login`.

    ```azurecli-interactive
    az login
    ```

    Se l'interfaccia della riga di comando può aprire il browser predefinito, eseguirà questa operazione e caricherà una pagina di accesso di Azure.

    In caso contrario, aprire una pagina del browser all'indirizzo [https://aka.ms/devicelogin](https://aka.ms/devicelogin) e immettere il codice di autorizzazione visualizzato nel terminale.

2. Accedere con le credenziali dell'account nel browser.


### <a name="create-new-net-console-app"></a>Creare una nuova app console .NET

1. In una shell dei comandi eseguire questo comando per creare un progetto denominato `key-vault-console-app`:

    ```dotnetcli
    dotnet new console --name key-vault-console-app
    ```

1. Passare alla directory *key-vault-console-app* appena creata ed eseguire questo comando per compilare il progetto:

    ```dotnetcli
    dotnet build
    ```

    L'output di compilazione non deve contenere alcun avviso o errore.
    
    ```console
    Build succeeded.
     0 Warning(s)
     0 Error(s)
    ```

### <a name="install-the-packages"></a>Installare i pacchetti

Nella shell dei comandi installare la libreria client di Azure Key Vault per .NET:

```dotnetcli
dotnet add package Azure.Security.KeyVault.Secrets
```

Per questo argomento di avvio rapido è anche necessario installare la libreria client di Azure SDK per Azure Identity:

```dotnetcli
dotnet add package Azure.Identity
```

### <a name="create-a-resource-group-and-key-vault"></a>Creare un gruppo di risorse e un insieme di credenziali delle chiavi

[!INCLUDE[Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

#### <a name="grant-access-to-your-key-vault"></a>Concedere l'accesso all'insieme di credenziali delle chiavi

Creare un criterio di accesso per l'insieme di credenziali delle chiavi che concede l'autorizzazione per il segreto all'account utente

```console
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --secret-permissions delete get list set
```

#### <a name="set-environment-variables"></a>Impostare le variabili di ambiente

Questa applicazione usa il nome dell'insieme di credenziali delle chiavi come variabile di ambiente denominata `KEY_VAULT_NAME`.

Windows
```cmd
set KEY_VAULT_NAME=<your-key-vault-name>
````
Windows PowerShell
```powershell
$Env:KEY_VAULT_NAME=<your-key-vault-name>
```

macOS o Linux
```cmd
export KEY_VAULT_NAME=<your-key-vault-name>
````

## <a name="object-model"></a>Modello a oggetti

La libreria dei segreti client di Azure Key Vault per .NET consente di gestire i segreti. La sezione [Esempi di codice](#code-examples) illustra come creare un client e come impostare, recuperare ed eliminare un segreto.

L'intera app console è disponibile all'indirizzo https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app.

## <a name="code-examples"></a>Esempi di codice

### <a name="add-directives"></a>Aggiungere le direttive

Aggiungere le direttive seguenti all'inizio di *Program.cs*:

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=directives)]

### <a name="authenticate-and-create-a-client"></a>Autenticare e creare un client

In questo argomento di avvio rapido viene usato l'utente connesso per eseguire l'autenticazione in Key Vault, che è il metodo preferito per lo sviluppo locale. Per le applicazioni distribuite in Azure, l'identità gestita deve essere assegnata al servizio app o alla macchina virtuale. Per altre informazioni, vedere [Informazioni sulle identità gestite per le risorse di Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

Nell'esempio seguente, il nome dell'insieme di credenziali delle chiavi viene esteso al relativo URI, nel formato "https://\<your-key-vault-name\>.vault.azure.net". Questo esempio usa la classe ['DefaultAzureCredential()'](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet&preserve-view=true), che consente di usare lo stesso codice in ambienti diversi con opzioni diverse per specificare l'identità. Per altre informazioni, vedere [Autenticazione DefaultAzureCredential](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme?view=azure-dotnet#defaultazurecredential). 

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=authenticate)]

### <a name="save-a-secret"></a>Salvare un segreto

Una volta autenticata l'applicazione console, aggiungere un segreto all'insieme di credenziali delle chiavi. Per questa attività, usare il metodo [client.SetSecret](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync). Il primo parametro del metodo accetta un nome per il segreto, ovvero "My Secret" in questo esempio.

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=setsecret)]

È possibile verificare che il segreto sia stato impostato con il comando [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show&preserve-view=true):

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

```azurepowershell
(Get-AzKeyVaultSecret -VaultName <your-unique-keyvault-name> -Name mySecret).SecretValueText
```

### <a name="retrieve-a-secret"></a>Recuperare un segreto

A questo punto è possibile recuperare il valore impostato in precedenza con il metodo [client.GetSecret](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync).

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=getsecret)]

Il segreto è ora salvato come `secret.Value`.

### <a name="delete-a-secret"></a>consente di eliminare un segreto

Eliminare infine il segreto dall'insieme di credenziali delle chiavi con il metodo [client.DeleteSecret](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync).

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=deletesecret)]

È possibile verificare che il segreto non sia più dotato del comando [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show&preserve-view=true):

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

```azurepowershell
(Get-AzKeyVaultSecret -VaultName <your-unique-keyvault-name> -Name mySecret).SecretValueText
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile usare l'interfaccia della riga di comando di Azure o Azure PowerShell per rimuovere l'insieme di credenziali delle chiavi e il gruppo di risorse corrispondente.

### <a name="delete-a-key-vault"></a>Eliminare un insieme di credenziali delle chiavi

```azurecli
az keyvault delete --name <your-unique-keyvault-name>
```

```azurepowershell
Remove-AzKeyVault -VaultName <your-unique-keyvault-name>
```

### <a name="purge-a-key-vault"></a>Rimuovere un insieme di credenziali delle chiavi

```azurecli
az keyvault purge --location eastus --name <your-unique-keyvault-name>
```

```azurepowershell
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

Modificare l'applicazione console .NET Core per interagire con l'insieme di credenziali delle chiavi completando la procedura seguente:

1. Sostituire il codice in *Program.cs* con il codice seguente:

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
                const string secretName = "mySecret";
                var keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
                var kvUri = $"https://{keyVaultName}.vault.azure.net";
    
                var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
    
                Console.Write("Input the value of your secret > ");
                var secretValue = Console.ReadLine();
    
                Console.Write($"Creating a secret in {keyVaultName} called '{secretName}' with the value '{secretValue}' ...");
                client.SetSecret(secretName, secretValue);
                Console.WriteLine(" done.");
    
                Console.WriteLine("Forgetting your secret.");
                secretValue = string.Empty;
                Console.WriteLine($"Your secret is '{secretValue}'.");
    
                Console.WriteLine($"Retrieving your secret from {keyVaultName}.");
                KeyVaultSecret secret = client.GetSecret(secretName);
                Console.WriteLine($"Your secret is '{secret.Value}'.");
    
                Console.Write($"Deleting your secret from {keyVaultName} ...");
                DeleteSecretOperation operation = client.StartDeleteSecret(secretName);
                // You only need to wait for completion if you want to purge or recover the secret.
                while (!operation.HasCompleted)
                {
                    Thread.Sleep(2000);
                
                    operation.UpdateStatus();
                }
                client.PurgeDeletedSecret(secretName);
                Console.WriteLine(" done.");
            }
        }
    }
    ```

1. Eseguire il comando seguente per eseguire l'app.

    ```dotnetcli
    dotnet run
    ```

1. Quando richiesto, immettere un valore di un segreto. Ad esempio, mySecretPassword.

    Compare una variante dell'output seguente:

    ```console
    Input the value of your secret > mySecretPassword
    Creating a secret in <your-unique-keyvault-name> called 'mySecret' with the value 'mySecretPassword' ... done.
    Forgetting your secret.
    Your secret is ''.
    Retrieving your secret from <your-unique-keyvault-name>.
    Your secret is 'mySecretPassword'.
    Deleting your secret from <your-unique-keyvault-name> ... done.    
    ```

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stato creato un insieme di credenziali delle chiavi e quindi è stato archiviato e recuperato un segreto. Vedere l'[intera app console in GitHub](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app).

Per altre informazioni su Key Vault e su come integrarlo nelle app, vedere gli articoli seguenti:

- Leggere una [panoramica di Azure Key Vault](../general/overview.md)
- Seguire un'[esercitazione sull'accesso a Key Vault da un'applicazione del servizio app](../general/tutorial-net-create-vault-azure-web-app.md)
- Seguire un'[esercitazione sull'accesso a Key Vault da una macchina virtuale](../general/tutorial-net-virtual-machine.md)
- Vedere la [Guida per gli sviluppatori per Azure Key Vault](../general/developers-guide.md)
- Esaminare le [procedure consigliate per Azure Key Vault](../general/best-practices.md)
