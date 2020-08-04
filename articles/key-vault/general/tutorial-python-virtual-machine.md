---
title: 'Esercitazione: Usare Azure Key Vault con una macchina virtuale in Python | Microsoft Docs'
description: In questa esercitazione si configura un'applicazione ASP.NET Core per la lettura di un segreto dall'insieme di credenziali delle chiavi.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 07/20/2020
ms.author: mbaldwin
ms.custom: mvc, tracking-python
ms.openlocfilehash: 453307b304c4cb1899b1de31117c944ac66fcddb
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87093938"
---
# <a name="tutorial-use-azure-key-vault-with-a-virtual-machine-in-python"></a>Esercitazione: Usare Azure Key Vault con una macchina virtuale in Python

Azure Key Vault facilita la protezione di segreti come le chiavi API, le stringhe di connessione di database necessarie per accedere alle applicazioni, i servizi e le risorse IT.

In questa esercitazione viene descritto come configurare un'applicazione console per leggere un segreto da Azure Key Vault. Per farlo, usare identità gestite per le risorse di Azure. 

L'esercitazione illustra come:

> [!div class="checklist"]
> * Creare un insieme di credenziali delle chiavi.
> * Aggiungere un segreto all'insieme di credenziali delle chiavi.
> * Recuperare un segreto dall'insieme di credenziali delle chiavi.
> * Creare una macchina virtuale di Azure.
> * Abilitare un'identità gestita.
> * Assegnare autorizzazioni per l'identità della macchina virtuale.

Prima di iniziare, leggere i [concetti di base di Key Vault](basic-concepts.md). 

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Prerequisiti

Per Windows, Mac e Linux:
  * [Git](https://git-scm.com/downloads)
  * Questa esercitazione richiede l'esecuzione dell'interfaccia della riga di comando di Azure nell'ambiente locale. È necessario che sia installata l'interfaccia della riga di comando di Azure versione 2.0.4 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario installare o aggiornare l'interfaccia della riga di comando, vedere [Installare l'interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli).

## <a name="log-in-to-azure"></a>Accedere ad Azure

Per accedere ad Azure tramite l'interfaccia della riga di comando di Azure, immettere:

```azurecli
az login
```

### <a name="create-a-resource-group-and-key-vault"></a>Creare un gruppo di risorse e un insieme di credenziali delle chiavi

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

## <a name="add-a-secret-to-the-key-vault"></a>Aggiungere un segreto all'insieme di credenziali delle chiavi

Verrà aggiunto un segreto per illustrare il funzionamento di questa operazione. Può trattarsi di una stringa di connessione SQL o di qualsiasi altra informazione che è necessario conservare in modo sicuro e rendere allo stesso tempo disponibile per l'applicazione.

Per creare un segreto denominato **AppSecret** nell'insieme di credenziali delle chiavi, immettere il comando seguente:

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Questo segreto archivia il valore **MySecret**.

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale
È possibile creare una macchina virtuale scegliendo tra i seguenti metodi:

* [L’interfaccia della riga di comando di Azure](../../virtual-machines/windows/quick-create-cli.md)
* [PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
* [Il portale di Azure](../../virtual-machines/windows/quick-create-portal.md)

## <a name="assign-an-identity-to-the-vm"></a>Assegnare un'identità alla macchina virtuale
In questo passaggio si crea un'identità assegnata dal sistema per la macchina virtuale eseguendo questo comando nell'interfaccia della riga di comando di Azure:

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
A questo punto è possibile assegnare le autorizzazioni dell’identità creata in precedenza all'insieme di credenziali delle chiavi eseguendo il comando seguente:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-on-to-the-virtual-machine"></a>Accesso alla macchina virtuale

Per accedere alla macchina virtuale, seguire le istruzioni riportate in [Come connettersi e accedere a una macchina virtuale di Azure che esegue Windows](../../virtual-machines/windows/connect-logon.md).

## <a name="create-and-run-a-sample-python-app"></a>Creare ed eseguire un'app Python di esempio

Nella prossima sezione è riportato un file di esempio denominato *Sample.py*. Usa la libreria [requests](https://2.python-requests.org/en/master/) per effettuare chiamate HTTP GET.

## <a name="edit-samplepy"></a>Modificare Sample.py

Dopo aver creato il file *Sample.py*, aprirlo e copiare il codice riportato in questa sezione. 

Il codice è un processo in due passaggi che:
1. Recupera un token dall'endpoint dell'identità del servizio gestita locale nella macchina virtuale.  
  Anche questa operazione recupera un token da Azure AD.
1. Passa il token all'insieme di credenziali delle chiavi e recupera il segreto. 

```python
    # importing the requests library 
    import requests 

    # Step 1: Fetch an access token from a Managed Identity enabled azure resource.
    # Resources with an MSI configured recieve an AAD access token by using the Azure Instance Metadata Service (IMDS)
    # IMDS provides an endpoint accessible to all IaaS VMs using a non-routable well-known IP Address
    # To learn more about IMDS and MSI Authentication see the following link: https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service
    # Note that the resource here is https://vault.azure.net for public cloud and api-version is 2018-02-01
    MSI_ENDPOINT = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net"
    r = requests.get(MSI_ENDPOINT, headers = {"Metadata" : "true"}) 
      
    # extracting data in json format 
    # This request gets an access_token from Azure AD by using the local MSI endpoint.
    data = r.json() 
    
    # Step 2: Pass the access_token received from previous HTTP GET call to your key vault.
    KeyVaultURL = "https://{YOUR KEY VAULT NAME}.vault.azure.net/secrets/{YOUR SECRET NAME}?api-version=2016-10-01"
    kvSecret = requests.get(url = KeyVaultURL, headers = {"Authorization": "Bearer " + data["access_token"]})
    
    print(kvSecret.json()["value"])
```

Eseguendo il codice seguente verrà visualizzato il valore del segreto: 

```console
python Sample.py
```

Il codice precedente mostra come eseguire operazioni con Azure Key Vault in una macchina virtuale Windows. 

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile eliminare la macchina virtuale e l'insieme di credenziali delle chiavi.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [API REST di Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
