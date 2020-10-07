---
title: 'Esercitazione: Usare Azure Key Vault con una macchina virtuale in .NET | Microsoft Docs'
description: In questa esercitazione si configura un'applicazione ASP.NET Core in una macchina virtuale per la lettura di un segreto dall'insieme di credenziali delle chiavi.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 07/20/2020
ms.author: mbaldwin
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: e1abc26296b1e3bd16bebf95d3489a4e95595e1a
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91336718"
---
# <a name="tutorial-use-azure-key-vault-with-a-virtual-machine-in-net"></a>Esercitazione: Usare Azure Key Vault con una macchina virtuale in .NET

Azure Key Vault facilita la protezione di segreti come le chiavi API, le stringhe di connessione di database necessarie per accedere alle applicazioni, i servizi e le risorse IT.

In questa esercitazione viene descritto come configurare un'applicazione console per leggere un segreto da Azure Key Vault. L'applicazione userà l'identità gestita della macchina virtuale per l'autenticazione con Key Vault. 

L'esercitazione illustra come:

> [!div class="checklist"]
> * Creare un gruppo di risorse.
> * Creare un insieme di credenziali delle chiavi.
> * Aggiungere un segreto all'insieme di credenziali delle chiavi.
> * Recuperare un segreto dall'insieme di credenziali delle chiavi.
> * Creare una macchina virtuale di Azure.
> * Abilitare un'[identità gestita](../../active-directory/managed-identities-azure-resources/overview.md) per la macchina virtuale.
> * Assegnare autorizzazioni per l'identità della macchina virtuale.

Prima di iniziare, leggere i [concetti di base di Key Vault](basic-concepts.md). 

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Prerequisiti

Per Windows, Mac e Linux:
  * [Git](https://git-scm.com/downloads)
  * [.NET Core 3.1 SDK o versione successiva](https://dotnet.microsoft.com/download/dotnet-core/3.1).
  * [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-resources-and-assign-permissions"></a>Creare le risorse e assegnare le autorizzazioni

Prima di iniziare a scrivere il codice, è necessario creare alcune risorse, inserire un segreto nell'insieme di credenziali delle chiavi e assegnare le autorizzazioni.

### <a name="sign-in-to-azure"></a>Accedere ad Azure

Per accedere ad Azure tramite l'interfaccia della riga di comando di Azure, immettere:

```azurecli
az login
```

## <a name="create-a-resource-group-and-key-vault"></a>Creare un gruppo di risorse e un insieme di credenziali delle chiavi

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

## <a name="populate-your-key-vault-with-a-secret"></a>Popolare l'insieme di credenziali delle chiavi con un segreto

[!INCLUDE [Create a secret](../../../includes/key-vault-create-secret.md)]

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale
Creare una macchina virtuale Windows o Linux con uno dei metodi seguenti:

| Windows | Linux |
|--|--|
| [Interfaccia della riga di comando di Azure](../../virtual-machines/windows/quick-create-cli.md) | [Interfaccia della riga di comando di Azure](../../virtual-machines/linux/quick-create-cli.md) |  
| [PowerShell](../../virtual-machines/windows/quick-create-powershell.md) | [PowerShell](../../virtual-machines/linux/quick-create-powershell.md) |
| [Azure portal](../../virtual-machines/windows/quick-create-portal.md) | [Azure portal](../../virtual-machines/linux/quick-create-portal.md) |

## <a name="assign-an-identity-to-the-vm"></a>Assegnare un'identità alla macchina virtuale
Creare un'identità assegnata dal sistema per la macchina virtuale con il comando [az vm identity assign](/cli/azure/vm/identity?view=azure-cli-latest#az-vm-identity-assign):

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Annotare l'identità assegnata dal sistema che viene visualizzata nel codice seguente. L'output del comando precedente sarà: 

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="assign-permissions-to-the-vm-identity"></a>Assegnare autorizzazioni per l'identità della macchina virtuale
Assegnare le autorizzazioni dell'identità creata in precedenza all'insieme di credenziali delle chiavi con il comando [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy):

```azurecli
az keyvault set-policy --name '<your-unique-key-vault-name>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="sign-in-to-the-virtual-machine"></a>Accedere alla macchina virtuale

Per accedere alla macchina virtuale, seguire le istruzioni riportate in [Connettersi e accedere a una macchina virtuale Windows di Azure](../../virtual-machines/windows/connect-logon.md) oppure [Connettersi e accedere a una macchina virtuale Linux di Azure](../../virtual-machines/linux/login-using-aad.md).

## <a name="set-up-the-console-app"></a>Impostare l'app console

Creare un'app console e installare i pacchetti necessari usando il comando `dotnet`.

### <a name="install-net-core"></a>Installare .NET Core

Per installare .NET Core, visitare la pagina dei [download di .NET](https://www.microsoft.com/net/download).

### <a name="create-and-run-a-sample-net-app"></a>Creare ed eseguire un'app .NET di esempio

Aprire un prompt dei comandi.

È possibile stampare sulla console "Hello World" eseguendo i comandi seguenti:

```console
dotnet new console -n keyvault-console-app
cd keyvault-console-app
dotnet run
```

### <a name="install-the-package"></a>Installare il pacchetto

Nella finestra della console installare la libreria client dei segreti di Azure Key Vault per .NET:

```console
dotnet add package Azure.Security.KeyVault.Secrets
```

Per questo argomento di avvio rapido, è necessario installare il pacchetto di identità seguente per eseguire l'autenticazione con Azure Key Vault:

```console
dotnet add package Azure.Identity
```

## <a name="edit-the-console-app"></a>Modificare l'app console

Aprire il file *Program.cs* e aggiungere questi pacchetti:

```csharp
using System;
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;
```

Aggiungere queste righe per aggiornare l'URI in modo che rispecchi il valore `vaultUri` dell'insieme di credenziali delle chiavi. Il codice seguente usa ['DefaultAzureCredential()'](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet) per l'autenticazione all'insieme di credenziali delle chiavi, che usa il token dell'identità gestita dell'applicazione per l'autenticazione. Viene anche usato il backoff esponenziale per la ripetizione dei tentativi in caso di limitazione dell'insieme di credenziali delle chiavi.

```csharp
  class Program
    {
        static void Main(string[] args)
        {
            string secretName = "mySecret";

            var kvUri = "https://<your-key-vault-name>.vault.azure.net";
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

            var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential(),options);

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
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile eliminare la macchina virtuale e l'insieme di credenziali delle chiavi.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [API REST di Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
