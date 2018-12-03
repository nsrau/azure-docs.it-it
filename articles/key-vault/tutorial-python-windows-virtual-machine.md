---
title: 'Esercitazione: Come usare Azure Key Vault con una macchina virtuale Windows di Azure in Python | Microsoft Docs'
description: "Esercitazione: Configurare un'applicazione ASP.NET Core per la lettura di un segreto da un insieme di credenziali delle chiavi"
services: key-vault
documentationcenter: ''
author: prashanthyv
manager: rajvijan
ms.assetid: 0e57f5c7-6f5a-46b7-a18a-043da8ca0d83
ms.service: key-vault
ms.workload: key-vault
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: pryerram
ms.custom: mvc
ms.openlocfilehash: 26b5b16e3eb016edbe53c3526e51c3aa44f307b5
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/29/2018
ms.locfileid: "52583585"
---
# <a name="tutorial-how-to-use-azure-key-vault-with-azure-windows-virtual-machine-in-python"></a>Esercitazione: Come usare Azure Key Vault con una macchina virtuale Windows di Azure in Python

Azure Key Vault facilita la protezione di segreti come le chiavi API, le stringhe di connessione di database necessarie per accedere alle applicazioni, i servizi e le risorse IT.

In questa esercitazione si eseguiranno i passaggi necessari per ottenere un'applicazione Web di Azure per leggere le informazioni da Azure Key Vault usando le identità gestite per le risorse di Azure. L'esercitazione è basata su [App Web di Azure](../app-service/app-service-web-overview.md). Nel corso dell'esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un insieme di credenziali delle chiavi.
> * Archiviare un segreto nell'insieme di credenziali delle chiavi.
> * Recuperare un segreto dall'insieme di credenziali delle chiavi.
> * Creare una macchina virtuale di Azure.
> * Abilitare un'[identità gestita](../active-directory/managed-identities-azure-resources/overview.md) per la macchina virtuale.
> * Concedere le autorizzazioni necessarie per l'applicazione console per la lettura dei dati dall'insieme di credenziali delle chiavi.
> * Recuperare segreti da Key Vault.

Prima di continuare, leggere i [concetti di base](key-vault-whatis.md#basic-concepts).

## <a name="prerequisites"></a>Prerequisiti
* Tutte le piattaforme:
  * Git ([download](https://git-scm.com/downloads)).
  * Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
  * [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) versione 2.0.4 o versioni successive. È disponibile per Windows, Mac e Linux.

Questa esercitazione usa un'identità del servizio gestita.

## <a name="what-is-managed-service-identity-and-how-does-it-work"></a>Che cos'è un'identità del servizio gestita e come funziona?
Prima di continuare, è importante conoscere i concetti alla base dell'identità del servizio gestita. Azure Key Vault consente di archiviare le credenziali in una posizione sicura in modo che non siano incluse nel codice, tuttavia per recuperarle è necessario eseguire l'autenticazione con Azure Key Vault. E per eseguire l'autenticazione con Key Vault servono le credenziali. Un classico problema di bootstrap. Tramite Azure e Azure AD, l'identità del servizio gestita offre una "identità di bootstrap" che semplifica notevolmente il processo iniziale.

Ecco come funziona. Quando si abilita l'identità del servizio gestita per un servizio di Azure come Macchine virtuali, Servizio app o Funzioni, Azure crea un'[entità servizio](key-vault-whatis.md#basic-concepts) per l'istanza del servizio in Azure Active Directory e inserisce le credenziali per l'entità servizio nell'istanza del servizio. 

![Identità del servizio gestita](media/MSI.png)

Il codice chiama quindi un servizio di metadati locale disponibile nella risorsa di Azure per ottenere un token di accesso.
Il codice usa il token di accesso ottenuto dal MSI_ENDPOINT locale per eseguire l'autenticazione con un servizio Azure Key Vault. 

## <a name="log-in-to-azure"></a>Accedere ad Azure

Per accedere ad Azure tramite l'interfaccia della riga di comando di Azure, immettere:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group#az-group-create). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

Selezionare il nome di un gruppo di risorse e compilare il segnaposto.
L'esempio seguente crea un gruppo di risorse nell'area Stati Uniti occidentali:

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Il gruppo di risorse appena creato viene usato in tutto l'articolo.

## <a name="create-a-key-vault"></a>Creare un insieme di credenziali delle chiavi

Viene ora creato un insieme di credenziali delle chiavi nel gruppo di risorse creato nel passaggio precedente. Specificare le informazioni seguenti:

* Nome dell'insieme di credenziali delle chiavi: il nome deve essere una stringa di 3-24 caratteri e deve contenere solo (0-9, a-z, A-Z e -).
* Nome del gruppo di risorse.
* Località: **Stati Uniti occidentali**.

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
A questo punto, l'account Azure è l'unico autorizzato a eseguire qualsiasi operazione su questo nuovo insieme di credenziali.

## <a name="add-a-secret-to-the-key-vault"></a>Aggiungere un segreto all'insieme di credenziali delle chiavi

Verrà aggiunto un segreto per illustrare il funzionamento di questa operazione. Si potrebbe archiviare una stringa di connessione SQL o qualsiasi altra informazione che è necessario conservare in modo sicuro, rendendola però disponibile per l'applicazione.

Digitare i comandi seguenti per creare un segreto nell'insieme di credenziali delle chiavi denominato **AppSecret**. Questo segreto archivierà il valore **MySecret**.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale
Visitare i collegamenti seguenti per creare una macchina virtuale Windows

[Interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-cli) 

[PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell)

[Portale](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal)

## <a name="assign-identity-to-virtual-machine"></a>Assegnare un'identità alla macchina virtuale
In questo passaggio si crea un'identità assegnata dal sistema per la macchina virtuale eseguendo questo comando nell'interfaccia della riga di comando di Azure

```
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Si noti l'elemento systemAssignedIdentity illustrato di seguito. L'output del comando precedente sarà 

```
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="give-virtual-machine-identity-permission-to-key-vault"></a>Concedere all'identità della macchina virtuale l'autorizzazione per Key Vault
È ora possibile concedere all'identità creata sopra l'autorizzazione per Key Vault eseguendo questo comando

```
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="login-to-the-virtual-machine"></a>Accedere alla macchina virtuale

È possibile seguire questa [esercitazione](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon)

## <a name="create-and-run-sample-python-app"></a>Creare ed eseguire un'app Python di esempio

Quello che segue è un file di esempio denominato "Sample.py". Usa la libreria [Requests](http://docs.python-requests.org/master/) per effettuare chiamate HTTP GET.

## <a name="edit-samplepy"></a>Modificare Sample.py
Dopo aver creato Sample.py, aprire il file e copiare il codice riportato di seguito

```
The below is a 2 step process. 
1. Fetch a token from the local MSI endpoint on the VM which in turn fetches a token from Azure Active Directory
2. Pass the token to Key Vault and fetch your secret 
```
    # importing the requests library 
    import requests 

    # Step 1: Fetch an access token from a Managed Identity enabled azure resource      
    # Note that the resource here is https://vault.azure.net for public cloud and api-version is 2018-02-01
    MSI_ENDPOINT = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net"
    r = requests.get(MSI_ENDPOINT, headers = {"Metadata" : "true"}) 
      
    # extracting data in json format 
    # This request gets a access_token from Azure Active Directory using the local MSI endpoint
    data = r.json() 
    
    # Step 2: Pass the access_token received from previous HTTP GET call to Key Vault
    KeyVaultURL = "https://prashanthwinvmvault.vault.azure.net/secrets/RandomSecret?api-version=2016-10-01"
    kvSecret = requests.get(url = KeyVaultURL, headers = {"Authorization": "Bearer " + data["access_token"]})
    
    print(kvSecret.json()["value"])
```

By running you should see the secret value 
```
python Sample.py
```

The above code shows you how to do operations with Azure Key Vault in an Azure Windows Virtual Machine. 

## Next steps

> [!div class="nextstepaction"]
> [Azure Key Vault REST API](https://docs.microsoft.com/rest/api/keyvault/)
