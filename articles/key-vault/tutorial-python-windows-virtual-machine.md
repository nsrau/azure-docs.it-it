---
title: Esercitazione - Usare Azure Key Vault con una macchina virtuale Windows in Python | Microsoft Docs
description: In questa esercitazione si configura un'applicazione ASP.NET Core per la lettura di un segreto dall'insieme di credenziali delle chiavi.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: ae9aaea91f62a7e79835e65a5a9f9c76017afdb1
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934393"
---
# <a name="tutorial-use-azure-key-vault-with-a-windows-virtual-machine-in-python"></a>Esercitazione: Usare Azure Key Vault con una macchina virtuale Windows in Python

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

Selezionare il nome di un gruppo di risorse e compilare il segnaposto. L'esempio seguente crea un gruppo di risorse nell'area Stati Uniti occidentali:

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

* [L’interfaccia della riga di comando di Azure](../virtual-machines/windows/quick-create-cli.md)
* [PowerShell](../virtual-machines/windows/quick-create-powershell.md)
* [Il portale di Azure](../virtual-machines/windows/quick-create-portal.md)

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

Per accedere alla macchina virtuale, seguire le istruzioni riportate in [Come connettersi e accedere a una macchina virtuale di Azure che esegue Windows](../virtual-machines/windows/connect-logon.md).

## <a name="create-and-run-a-sample-python-app"></a>Creare ed eseguire un'app Python di esempio

Nella prossima sezione è riportato un file di esempio denominato *Sample.py*. Usa la libreria [requests](http://docs.python-requests.org/en/master/) per effettuare chiamate HTTP GET.

## <a name="edit-samplepy"></a>Modificare Sample.py

Dopo aver creato il file *Sample.py*, aprirlo e copiare il codice riportato in questa sezione. 

Il codice è un processo in due passaggi che:
1. Recupera un token dall'endpoint dell'identità del servizio gestita locale nella macchina virtuale.  
  Anche questa operazione recupera un token da Azure AD.
1. Passa il token all'insieme di credenziali delle chiavi e recupera il segreto. 

```
    # importing the requests library 
    import requests 

    # Step 1: Fetch an access token from a Managed Identity enabled azure resource.      
    # Note that the resource here is https://vault.azure.net for public cloud and api-version is 2018-02-01
    MSI_ENDPOINT = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net"
    r = requests.get(MSI_ENDPOINT, headers = {"Metadata" : "true"}) 
      
    # extracting data in json format 
    # This request gets an access_token from Azure AD by using the local MSI endpoint.
    data = r.json() 
    
    # Step 2: Pass the access_token received from previous HTTP GET call to your key vault.
    KeyVaultURL = "https://prashanthwinvmvault.vault.azure.net/secrets/RandomSecret?api-version=2016-10-01"
    kvSecret = requests.get(url = KeyVaultURL, headers = {"Authorization": "Bearer " + data["access_token"]})
    
    print(kvSecret.json()["value"])
```

Eseguendo il codice seguente verrà visualizzato il valore del segreto: 

```
python Sample.py
```

Il codice precedente mostra come eseguire operazioni con Azure Key Vault in una macchina virtuale Windows. 

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile eliminare la macchina virtuale e l'insieme di credenziali delle chiavi.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [API REST di Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
