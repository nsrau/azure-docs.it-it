---
title: Esercitazione - Usare Azure Key Vault con una macchina virtuale di Azure in Python | Microsoft Docs
description: In questa esercitazione verrà configurata un'applicazione Python per leggere un segreto da un insieme di credenziali delle chiavi
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
ms.openlocfilehash: 8c816d17807432d75b6102190fc37d25a525d7cf
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2019
ms.locfileid: "54244172"
---
# <a name="tutorial-use-azure-key-vault-with-an-azure-virtual-machine-in-python"></a>Esercitazione: Usare Azure Key Vault con una macchina virtuale di Azure in Python

Azure Key Vault facilita la protezione di segreti come le chiavi API e le stringhe di connessione di database necessarie per accedere ad applicazioni, servizi e risorse IT dell'organizzazione.

In questa esercitazione si eseguiranno i passaggi per ottenere un'applicazione Web di Azure per leggere le informazioni da Azure Key Vault usando le identità gestite per le risorse di Azure. Si apprenderà come:

> [!div class="checklist"]
> * Creare un insieme di credenziali delle chiavi.
> * Archiviare un segreto nell'insieme di credenziali delle chiavi.
> * Creare una macchina virtuale di Azure.
> * Abilitare un'[identità gestita](../active-directory/managed-identities-azure-resources/overview.md) per la macchina virtuale.
> * Concedere le autorizzazioni necessarie per l'applicazione console per la lettura dei dati dall'insieme di credenziali delle chiavi.
> * Recuperare un segreto dall'insieme di credenziali delle chiavi.

Prima di continuare, leggere i [concetti di base su Key Vault](key-vault-whatis.md#basic-concepts).

## <a name="prerequisites"></a>Prerequisiti
Per tutte le piattaforme, è necessario avere:

* Git ([download](https://git-scm.com/downloads)).
* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
* [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) versione 2.0.4 o versioni successive. Disponibile per Windows, Mac e Linux.

### <a name="managed-service-identity-and-how-it-works"></a>Identità del servizio gestita e relativo funzionamento
Questa esercitazione usa un'identità del servizio gestita.

Con Azure Key Vault è possibile archiviare le credenziali in modo sicuro evitando di lasciarle nel codice. Per recuperarle, è necessario eseguire l'autenticazione con Key Vault. E per eseguire l'autenticazione con Key Vault servono le credenziali. Un classico problema di bootstrap. Tramite Azure e Azure Active Directory (Azure AD), l'identità del servizio gestita offre una "identità di bootstrap" che semplifica il processo iniziale.

Quando si abilita l'identità del servizio gestita per un servizio di Azure, ad esempio Macchine virtuali, Servizio app o Funzioni, Azure crea un'[entità servizio](key-vault-whatis.md#basic-concepts) per l'istanza del servizio in Azure AD. Azure inserisce le credenziali per l'entità servizio nell'istanza del servizio. 

![Identità del servizio gestita](media/MSI.png)

Il codice chiama quindi un servizio di metadati locale disponibile nella risorsa di Azure per ottenere un token di accesso.
Il codice usa il token di accesso ottenuto dall'endpoint dell'identità del servizio gestita locale per eseguire l'autenticazione con un servizio Azure Key Vault. 

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

* Nome dell'insieme di credenziali delle chiavi: il nome deve essere una stringa di 3-24 caratteri costituiti esclusivamente da 0-9, a-z, A-Z e trattini (-).
* Nome del gruppo di risorse.
* Percorso: **Stati Uniti occidentali**.

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
A questo punto, l'account Azure è l'unico autorizzato a eseguire qualsiasi operazione su questo nuovo insieme di credenziali.

## <a name="add-a-secret-to-the-key-vault"></a>Aggiungere un segreto all'insieme di credenziali delle chiavi

Verrà aggiunto un segreto per illustrare il funzionamento di questa operazione. Si potrebbe archiviare una stringa di connessione SQL o qualsiasi altra informazione che è necessario conservare in modo sicuro, rendendola però disponibile per l'applicazione.

Digitare i comandi seguenti per creare un segreto nell'insieme di credenziali delle chiavi denominato *AppSecret*. Questo segreto archivierà il valore **MySecret**.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

Creare una VM con il comando [az vm create](/cli/azure/vm#az_vm_create).

Nell'esempio seguente viene creata una macchina virtuale denominata *myVM* e aggiunto un account utente denominato *azureuser*. Il parametro `--generate-ssh-keys` genera automaticamente una chiave SSH e la inserisce nella posizione predefinita della chiave (*~/.ssh*). Per usare invece un set specifico di chiavi, usare l'opzione `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

La creazione della macchina virtuale e delle risorse di supporto richiede alcuni minuti. L'output di esempio seguente mostra che la creazione della macchina virtuale è riuscita:

```
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

Si noti il valore `publicIpAddress` nell'output della macchina virtuale. Questo indirizzo viene usato per l'accesso alla macchina virtuale nei passaggi successivi.

## <a name="assign-an-identity-to-the-virtual-machine"></a>Assegnare un'identità alla macchina virtuale
In questo passaggio si crea un'identità assegnata dal sistema per la macchina virtuale. Eseguire il comando seguente nell'interfaccia della riga di comando di Azure:

```
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

L'output del comando è il seguente. Si noti il valore di **systemAssignedIdentity**. 

```
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="give-the-virtual-machine-identity-permission-to-the-key-vault"></a>Concedere all'identità della macchina virtuale l'autorizzazione per l'insieme di credenziali delle chiavi
A questo punto è possibile assegnare all'identità l'autorizzazione per l'insieme di credenziali delle chiavi. Eseguire il comando seguente:

```
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-in-to-the-virtual-machine"></a>Accedere alla macchina virtuale

Accedere alla macchina virtuale seguendo questa [esercitazione](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon).

## <a name="create-and-run-the-sample-python-app"></a>Creare ed eseguire l'app Python di esempio

Il file di esempio seguente è denominato *Sample.py*. Usa la libreria [requests](https://pypi.org/project/requests/2.7.0/) per effettuare chiamate HTTP GET.

## <a name="edit-samplepy"></a>Modificare Sample.py
Dopo aver creato il file Sample.py, aprirlo e copiare il codice seguente. Il codice è un processo in due passaggi che: 
1. Recupera un token dall'endpoint dell'identità del servizio gestita locale nella macchina virtuale. L'endpoint recupera quindi un token da Azure Active Directory.
2. Passa il token all'insieme di credenziali delle chiavi e recupera il segreto. 

```
    # importing the requests library 
    import requests 

    # Step 1: Fetch an access token from an MSI-enabled Azure resource      
    # Note that the resource here is https://vault.azure.net for the public cloud, and api-version is 2018-02-01
    MSI_ENDPOINT = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net"
    r = requests.get(MSI_ENDPOINT, headers = {"Metadata" : "true"}) 
      
    # Extracting data in JSON format 
    # This request gets an access token from Azure Active Directory by using the local MSI endpoint
    data = r.json() 
    
    # Step 2: Pass the access token received from the previous HTTP GET call to the key vault
    KeyVaultURL = "https://prashanthwinvmvault.vault.azure.net/secrets/RandomSecret?api-version=2016-10-01"
    kvSecret = requests.get(url = KeyVaultURL, headers = {"Authorization": "Bearer " + data["access_token"]})
    
    print(kvSecret.json()["value"])
```

Eseguendo il comando seguente verrà visualizzato il valore del segreto: 

```
python Sample.py
```

Il codice precedente mostra come eseguire operazioni con Azure Key Vault in una macchina virtuale Windows. 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [API REST di Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
