---
title: Esercitazione - Usare una macchina virtuale Linux e un'applicazione console ASP.NET per archiviare segreti in Azure Key Vault | Microsoft Docs
description: In questa esercitazione viene descritto come configurare un'applicazione console ASP.NET per leggere un segreto da Azure Key Vault.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: 8c142b4c1663a7ccd462836e9aac4ee80accaae4
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/15/2019
ms.locfileid: "67874825"
---
# <a name="tutorial-use-a-linux-vm-and-a-net-app-to-store-secrets-in-azure-key-vault"></a>Esercitazione: Usare una macchina virtuale Linux e un'app .NET per archiviare segreti in Azure Key Vault

Azure Key Vault facilita la protezione di segreti come le chiavi API, le stringhe di connessione di database necessarie per accedere alle applicazioni, i servizi e le risorse IT.

In questa esercitazione viene configurata un'applicazione console ASP.NET per leggere informazioni da Azure Key Vault tramite identità gestite per risorse di Azure. Si apprenderà come:

> [!div class="checklist"]
> * Creare un insieme di credenziali delle chiavi
> * Archiviare un segreto nell'insieme di credenziali delle chiavi
> * Creare una macchina virtuale Linux di Azure
> * Abilitare un'[identità gestita](../active-directory/managed-identities-azure-resources/overview.md) per la macchina virtuale
> * Concedere le autorizzazioni necessarie per l'applicazione console per la lettura di dati dall'insieme di credenziali delle chiavi
> * Recuperare un segreto dall'insieme di credenziali delle chiavi

Prima di iniziare, leggere i [concetti di base di Key Vault](key-vault-whatis.md#basic-concepts).

## <a name="prerequisites"></a>Prerequisiti

* [Git](https://git-scm.com/downloads).
* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
* [Interfaccia della riga di comando di Azure 2.0 o versioni successive](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) o Azure Cloud Shell.

[!INCLUDE [Azure Cloud Shell](../../includes/cloud-shell-try-it.md)]

## <a name="understand-managed-service-identity"></a>Informazioni sull'identità del servizio gestita

Azure Key Vault consente di archiviare le credenziali in una posizione sicura in modo che non siano incluse nel codice, tuttavia per recuperarle è necessario eseguire l'autenticazione con Azure Key Vault. Tuttavia, per eseguire l'autenticazione in Key Vault, sono necessarie le credenziali. Si tratta di un classico problema di bootstrap. Con Azure e Azure Active Directory (Azure AD), l'identità del servizio gestita può fornire un'identità di bootstrap che semplifica il processo iniziale.

Quando si abilita l'identità del servizio gestita per un servizio di Azure come Macchine virtuali, Servizio app o Funzioni, Azure crea un'entità servizio per l'istanza del servizio in Azure Active Directory. Azure inserisce quindi le credenziali per l'entità servizio nell'istanza del servizio.

![Identità del servizio gestita](media/MSI.png)

Il codice chiama quindi un servizio di metadati locale disponibile nella risorsa di Azure per ottenere un token di accesso.
Il codice usa il token di accesso ottenuto dal MSI_ENDPOINT locale per eseguire l'autenticazione con un servizio Azure Key Vault.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Per accedere ad Azure tramite l'interfaccia della riga di comando di Azure, immettere:

```azurecli-interactive
az login
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse usando il comando `az group create`. Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

Creare un gruppo di risorse nell'area Stati Uniti occidentali. Scegliere un nome per il gruppo di risorse e sostituire `YourResourceGroupName` nell'esempio seguente:

```azurecli-interactive
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Questo gruppo di risorse verrà usato durante tutta l'esercitazione.

## <a name="create-a-key-vault"></a>Creare un insieme di credenziali delle chiavi

Successivamente, creare un insieme di credenziali delle chiavi nel gruppo di risorse. Specificare le informazioni seguenti:

* Nome dell'insieme di credenziali delle chiavi: una stringa contenente da 3 a 24 caratteri, limitati a numeri, lettere e trattini ( 0-9, a-z, A-Z e \-).
* Nome del gruppo di risorse
* Percorso: **Stati Uniti occidentali**

```azurecli-interactive
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

A questo punto, solo l'account Azure è autorizzato a eseguire qualsiasi operazione su questo nuovo insieme di credenziali.

## <a name="add-a-secret-to-the-key-vault"></a>Aggiungere un segreto all'insieme di credenziali delle chiavi

Ora è possibile aggiungere un segreto. In uno scenario reale, si potrebbe archiviare una stringa di connessione SQL o qualsiasi altra informazione che è necessario conservare in modo sicuro, rendendola però disponibile per l'applicazione.

Per questa esercitazione, digitare i comandi seguenti per creare un segreto nell'insieme di credenziali delle chiavi. Il segreto è denominato **AppSecret** e il relativo valore è **MySecret**.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-linux-virtual-machine"></a>Creare una macchina virtuale Linux

Creare una macchina virtuale con il comando `az vm create`.

Nell'esempio seguente viene creata una macchina virtuale denominata **myVM** e aggiunto un account utente denominato **azureuser**. Il parametro `--generate-ssh-keys` viene usato per generare automaticamente una chiave SSH e inserito nella posizione predefinita della chiave ( **~/.ssh**). Per usare invece un set specifico di chiavi, usare l'opzione `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

La creazione della macchina virtuale e delle risorse di supporto richiede alcuni minuti. L'output di esempio seguente mostra che l'operazione di creazione della macchina virtuale ha avuto esito positivo.

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-00-00-00-00-00",
  "powerState": "VM running",
  "privateIpAddress": "XX.XX.XX.XX",
  "publicIpAddress": "XX.XX.XXX.XXX",
  "resourceGroup": "myResourceGroup"
}
```

Annotare `publicIpAddress` nell'output della macchina virtuale. Questo indirizzo verrà usato nei passaggi successivi per accedere alla macchina virtuale.

## <a name="assign-an-identity-to-the-vm"></a>Assegnare un'identità alla macchina virtuale

Creare un'identità assegnata dal sistema per la macchina virtuale eseguendo il comando seguente:

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

L'output del comando dovrebbe essere:

```azurecli
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

Prendere nota di `systemAssignedIdentity`. Sarà necessario nel passaggio successivo.

## <a name="give-the-vm-identity-permission-to-key-vault"></a>Assegnare all'identità della macchina virtuale l'autorizzazione per Key Vault

È ora possibile assegnare l'autorizzazione per Key Vault all'identità creata. Eseguire il comando seguente:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-in-to-the-vm"></a>Accedere alla macchina virtuale

Accedere alla macchina virtuale usando un terminale.

```terminal
ssh azureuser@<PublicIpAddress>
```

## <a name="install-net-core-on-linux"></a>Installare .NET Core in Linux

Nella macchina virtuale Linux:

Registrare il codice Product Key Microsoft come attendibile eseguendo i comandi seguenti:

   ```console
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
   ```

Configurare il feed di pacchetti host della versione desiderato in base al sistema operativo:

```console
   # Ubuntu 17.10
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-artful-prod artful main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
   
   # Ubuntu 17.04
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-zesty-prod zesty main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
   
   # Ubuntu 16.04 / Linux Mint 18
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-xenial-prod xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
   
   # Ubuntu 14.04 / Linux Mint 17
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-trusty-prod trusty main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
```

Installare .NET e controllare la versione:

   ```console
   sudo apt-get install dotnet-sdk-2.1.4
   dotnet --version
   ```

## <a name="create-and-run-a-sample-net-app"></a>Creare ed eseguire un'app .NET di esempio

Eseguire i comandi seguenti. Nella console dovrebbe essere visualizzato "Hello World".

```console
dotnet new console -o helloworldapp
cd helloworldapp
dotnet run
```

## <a name="edit-the-console-app-to-fetch-your-secret"></a>Modificare l'app console per recuperare il segreto

Aprire il file Program.cs e aggiungere questi pacchetti:

   ```csharp
   using System;
   using System.IO;
   using System.Net;
   using System.Text;
   using Newtonsoft.Json;
   using Newtonsoft.Json.Linq;
   ```

È un processo in due passaggi per modificare il file di classe per consentire all'app di accedere al segreto nell'insieme di credenziali delle chiavi.

1. Recuperare un token dall'endpoint locale dell'identità del servizio gestita nella macchina virtuale, che a sua volta recupera un token da Azure Active Directory.
1. Passare il token a Key Vault e recuperare il segreto.

   Modificare il file di classe per contenere il codice seguente:

   ```csharp
    class Program
       {
           static void Main(string[] args)
           {
               // Step 1: Get a token from local (URI) Managed Service Identity endpoint which in turn fetches it from Azure Active Directory
               var token = GetToken();

               // Step 2: Fetch the secret value from Key Vault
               System.Console.WriteLine(FetchSecretValueFromKeyVault(token));
           }

           static string GetToken()
           {
               WebRequest request = WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net");
               request.Headers.Add("Metadata", "true");
               WebResponse response = request.GetResponse();
               return ParseWebResponse(response, "access_token");
           }

           static string FetchSecretValueFromKeyVault(string token)
           {
               WebRequest kvRequest = WebRequest.Create("https://prashanthwinvmvault.vault.azure.net/secrets/RandomSecret?api-version=2016-10-01");
               kvRequest.Headers.Add("Authorization", "Bearer "+  token);
               WebResponse kvResponse = kvRequest.GetResponse();
               return ParseWebResponse(kvResponse, "value");
           }

           private static string ParseWebResponse(WebResponse response, string tokenName)
           {
               string token = String.Empty;
               using (Stream stream = response.GetResponseStream())
               {
                   StreamReader reader = new StreamReader(stream, Encoding.UTF8);
                   String responseString = reader.ReadToEnd();

                   JObject joResponse = JObject.Parse(responseString);
                   JValue ojObject = (JValue)joResponse[tokenName];
                   token = ojObject.Value.ToString();
               }
               return token;
           }
       }
   ```

A questo punto si è appreso come eseguire operazioni con Azure Key Vault in un'applicazione .NET in esecuzione in una macchina virtuale Linux di Azure.

## <a name="clean-up-resources"></a>Pulire le risorse

Eliminare il gruppo di risorse, la macchina virtuale e tutte le risorse correlate quando non servono più. A tale scopo, selezionare il gruppo di risorse per la VM e selezionare **Elimina**.

Eliminare l'insieme di credenziali delle chiavi usando il comando `az keyvault delete`:

```azurecli-interactive
az keyvault delete --name
                   [--resource group]
                   [--subscription]
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [API REST di Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
