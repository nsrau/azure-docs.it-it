---
title: Esercitazione - Usare Azure Key Vault con una macchina virtuale Windows in .NET | Microsoft Docs
description: In questa esercitazione si configura un'applicazione ASP.NET Core per la lettura di un segreto dall'insieme di credenziali delle chiavi.
services: key-vault
documentationcenter: ''
author: prashanthyv
manager: rajvijan
ms.assetid: 0e57f5c7-6f5a-46b7-a18a-043da8ca0d83
ms.service: key-vault
ms.workload: key-vault
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: pryerram
ms.custom: mvc
ms.openlocfilehash: fb17afa4bfe8c00c91cc8fb33ab3326452065a9e
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58885418"
---
# <a name="tutorial-use-azure-key-vault-with-a-windows-virtual-machine-in-net"></a>Esercitazione: Usare Azure Key Vault con una macchina virtuale Windows in .NET

Azure Key Vault facilita la protezione di segreti come le chiavi API, le stringhe di connessione di database necessarie per accedere alle applicazioni, i servizi e le risorse IT.

In questa esercitazione viene descritto come configurare un'applicazione console per leggere un segreto da Azure Key Vault. Per farlo, usare identità gestite per le risorse di Azure. 

L'esercitazione illustra come:

> [!div class="checklist"]
> * Creare un gruppo di risorse.
> * Creare un insieme di credenziali delle chiavi.
> * Aggiungere un segreto all'insieme di credenziali delle chiavi.
> * Recuperare un segreto dall'insieme di credenziali delle chiavi.
> * Creare una macchina virtuale di Azure.
> * Abilitare un'[identità gestita](../active-directory/managed-identities-azure-resources/overview.md) per la macchina virtuale.
> * Assegnare autorizzazioni per l'identità della macchina virtuale.

Prima di iniziare, leggere i [concetti di base di Key Vault](key-vault-whatis.md#basic-concepts). 

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Prerequisiti

Per Windows, Mac e Linux:
  * [Git](https://git-scm.com/downloads)
  * Questa esercitazione richiede l'esecuzione dell'interfaccia della riga di comando di Azure nell'ambiente locale. È necessario che sia installata l'interfaccia della riga di comando di Azure versione 2.0.4 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario installare o aggiornare l'interfaccia della riga di comando, vedere [Installare l'interfaccia della riga di comando di Azure 2.0](https://review.docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="about-managed-service-identity"></a>Informazioni sull'identità del servizio gestito

Con Azure Key Vault è possibile archiviare le credenziali in modo sicuro in modo da non visualizzarle nel codice. Tuttavia, è necessario eseguire l'autenticazione con Azure Key Vault per recuperare le chiavi. E per eseguire l'autenticazione con Key Vault servono le credenziali. È un classico circolo vizioso. Identità del servizio gestita risolve questo problema fornendo un'_identità bootstrap_ che semplifica il processo.

Quando si abilita l'identità del servizio gestita per un servizio di Azure, come Macchine virtuali di Microsoft Azure, Servizio app di Azure o Funzioni di Azure, Azure crea un'[entità servizio](key-vault-whatis.md#basic-concepts) per l'istanza del servizio in Azure Active Directory (Azure AD) e inserisce le credenziali dell'entità servizio in tale istanza. 

![Identità del servizio gestita](media/MSI.png)

Il codice chiama quindi un servizio di metadati locale disponibile nella risorsa di Azure per ottenere un token di accesso. Il codice usa il token di accesso ottenuto dall'endpoint dell'identità del servizio gestita locale per eseguire l'autenticazione con un servizio Azure Key Vault. 

## <a name="log-in-to-azure"></a>Accedere ad Azure

Per accedere ad Azure tramite l'interfaccia della riga di comando di Azure, immettere:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group#az-group-create). 

Selezionare quindi il nome di un gruppo di risorse e compilare il segnaposto. L'esempio seguente crea un gruppo di risorse nell'area Stati Uniti occidentali:

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Questo gruppo di risorse verrà usato durante tutta l'esercitazione.

## <a name="create-a-key-vault"></a>Creare un insieme di credenziali delle chiavi

Per creare un insieme di credenziali delle chiavi nel gruppo di risorse creato nel passaggio precedente, specificare le informazioni seguenti:

* Nome dell'insieme di credenziali delle chiavi: una stringa contenente da 3 a 24 caratteri, limitati a numeri (0-9), lettere (a-z, A-Z) e trattini (-)
* Nome del gruppo di risorse
* Percorso: **Stati Uniti occidentali**

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
A questo punto, l'account Azure è l'unico autorizzato a eseguire operazioni su questo nuovo insieme di credenziali.

## <a name="add-a-secret-to-the-key-vault"></a>Aggiungere un segreto all'insieme di credenziali delle chiavi

Verrà aggiunto un segreto per illustrare il funzionamento di questa operazione. Può trattarsi di una stringa di connessione SQL o di qualsiasi altra informazione che è necessario conservare in modo sicuro e rendere allo stesso tempo disponibile per l'applicazione.

Per creare un segreto nell'insieme di credenziali delle chiavi denominato **AppSecret**, immettere il comando seguente:

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Questo segreto archivia il valore **MySecret**.

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale
È possibile creare una macchina virtuale scegliendo tra i seguenti metodi:

* [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-cli)
* [PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell)
* [Portale di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal)

## <a name="assign-an-identity-to-the-vm"></a>Assegnare un'identità alla macchina virtuale
In questo passaggio si crea un'identità assegnata dal sistema per la macchina virtuale eseguendo questo comando nell'interfaccia della riga di comando di Azure:

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Annotare l'identità assegnata dal sistema che viene visualizzata nel codice seguente. L'output del comando precedente sarà: 

```azurecli
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="assign-permissions-to-the-vm-identity"></a>Assegnare autorizzazioni per l'identità della macchina virtuale
A questo punto è possibile assegnare le autorizzazioni dell’identità creata in precedenza all'insieme di credenziali delle chiavi eseguendo il comando seguente:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-on-to-the-virtual-machine"></a>Accesso alla macchina virtuale

Per accedere alla macchina virtuale, seguire le istruzioni riportate in [Come connettersi e accedere a una macchina virtuale di Azure che esegue Windows](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon).

## <a name="install-net-core"></a>Installare .NET Core

Per installare .NET Core, visitare la pagina dei [download di .NET](https://www.microsoft.com/net/download).

## <a name="create-and-run-a-sample-net-app"></a>Creare ed eseguire un'app .NET di esempio

Aprire un prompt dei comandi.

È possibile stampare sulla console "Hello World" eseguendo i comandi seguenti:

```batch
dotnet new console -o helloworldapp
cd helloworldapp
dotnet run
```

## <a name="edit-the-console-app"></a>Modificare l'app console

Aprire il file *Program.cs* e aggiungere questi pacchetti:

```csharp
using System;
using System.IO;
using System.Net;
using System.Text;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
```

Modificare il file di classe per contenere il codice nel seguente processo in due passaggi:

1. Recupera un token dall'endpoint dell'identità del servizio gestita locale nella macchina virtuale. Anche questa operazione recupera un token da Azure AD.
1. Passa il token all'insieme di credenziali delle chiavi e recupera il segreto. 

```csharp
 class Program
    {
        static void Main(string[] args)
        {
            // Step 1: Get a token from the local (URI) Managed Service Identity endpoint, which in turn fetches it from Azure AD
            var token = GetToken();

            // Step 2: Fetch the secret value from your key vault
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
            WebRequest kvRequest = WebRequest.Create("https://<YourVaultName>.vault.azure.net/secrets/<YourSecretName>?api-version=2016-10-01");
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

Il codice precedente mostra come eseguire operazioni con Azure Key Vault in una macchina virtuale Windows.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile eliminare la macchina virtuale e l'insieme di credenziali delle chiavi.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [API REST di Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
