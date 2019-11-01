---
title: Esercitazione - Usare una macchina virtuale Linux e un'applicazione Python per archiviare segreti in Azure Key Vault | Microsoft Docs
description: In questa esercitazione viene descritto come configurare un'applicazione Python per leggere un segreto da Azure Key Vault.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: 15650de776b481d1635b58f2b8ecf2bf2921d12f
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2019
ms.locfileid: "73242411"
---
# <a name="tutorial-use-a-linux-vm-and-a-python-app-to-store-secrets-in-azure-key-vault"></a>Esercitazione: Usare una macchina virtuale Linux e un'app Python per archiviare segreti in Azure Key Vault

Azure Key Vault facilita la protezione di segreti come le chiavi API e le stringhe di connessione di database necessarie per accedere ad applicazioni, servizi e risorse IT dell'organizzazione.

In questa esercitazione viene configurata un'applicazione Web di Azure per leggere informazioni da Azure Key Vault tramite identità gestite per risorse di Azure. Si apprenderà come:

> [!div class="checklist"]
> * Creare un insieme di credenziali delle chiavi
> * Archiviare un segreto nell'insieme di credenziali delle chiavi
> * Creare una macchina virtuale Linux
> * Abilitare un'[identità gestita](../active-directory/managed-identities-azure-resources/overview.md) per la macchina virtuale
> * Concedere le autorizzazioni necessarie per l'applicazione console per la lettura di dati dall'insieme di credenziali delle chiavi
> * Recuperare un segreto dall'insieme di credenziali delle chiavi

Prima di continuare, assicurarsi di acquisire familiarità con i [concetti di base su Key Vault](basic-concepts.md).

## <a name="prerequisites"></a>Prerequisiti

* [Git](https://git-scm.com/downloads).
* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
* [Interfaccia della riga di comando di Azure 2.0.4 o versioni successive](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) o Azure Cloud Shell.

[!INCLUDE [Azure Cloud Shell](../../includes/cloud-shell-try-it.md)]

## <a name="understand-managed-service-identity"></a>Informazioni sull'identità del servizio gestita

Con Azure Key Vault è possibile archiviare le credenziali in modo sicuro evitando di lasciarle nel codice. Per recuperarle, è necessario eseguire l'autenticazione in Azure Key Vault. Tuttavia, per eseguire l'autenticazione in Key Vault, sono necessarie le credenziali. Si tratta di un classico problema di bootstrap. Tramite Azure e Azure Active Directory (Azure AD), l'identità del servizio gestita offre un'identità di bootstrap che semplifica il processo iniziale.

Quando si abilita l'identità del servizio gestita per un servizio di Azure come Macchine virtuali, Servizio app o Funzioni, Azure crea un'entità servizio per l'istanza del servizio in Azure AD. Azure inserisce quindi le credenziali per l'entità servizio nell'istanza del servizio.

![Identità del servizio gestita](media/MSI.png)

Il codice chiama quindi un servizio di metadati locale disponibile nella risorsa di Azure per ottenere un token di accesso. Il codice usa il token di accesso ottenuto dall'endpoint dell'identità del servizio gestita locale per eseguire l'autenticazione con un servizio Azure Key Vault.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Per accedere ad Azure tramite l'interfaccia della riga di comando di Azure, immettere:

```azurecli-interactive
az login
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

Creare un gruppo di risorse usando il comando `az group create` nell'area Stati Uniti occidentali con il codice seguente. Sostituire `YourResourceGroupName` con il nome desiderato.

```azurecli-interactive
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Questo gruppo di risorse verrà usato durante tutta l'esercitazione.

## <a name="create-a-key-vault"></a>Creare un insieme di credenziali delle chiavi

Creare quindi un insieme di credenziali delle chiavi nel gruppo di risorse creato nel passaggio precedente. Specificare le informazioni seguenti:

* Nome dell'insieme di credenziali delle chiavi: il nome deve essere una stringa di 3-24 caratteri costituiti esclusivamente da 0-9, a-z, A-Z e trattini (-).
* Nome del gruppo di risorse.
* Percorso: **Stati Uniti occidentali**.

```azurecli-interactive
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

A questo punto, l'account Azure è l'unico autorizzato a eseguire qualsiasi operazione su questo nuovo insieme di credenziali.

## <a name="add-a-secret-to-the-key-vault"></a>Aggiungere un segreto all'insieme di credenziali delle chiavi

Verrà aggiunto un segreto per illustrare il funzionamento di questa operazione. Potrebbe essere necessario archiviare una stringa di connessione SQL o qualsiasi altra informazione che deve essere tenuta al sicuro e resa disponibile per l'applicazione.

Digitare i comandi seguenti per creare un segreto nell'insieme di credenziali delle chiavi denominato *AppSecret*. Questo segreto archivierà il valore **MySecret**.

```azurecli-interactive
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-linux-virtual-machine"></a>Creare una macchina virtuale Linux

Creare una macchina virtuale tramite il comando `az vm create`.

Nell'esempio seguente viene creata una macchina virtuale denominata **myVM** e aggiunto un account utente denominato **azureuser**. Il parametro `--generate-ssh-keys` genera automaticamente una chiave SSH e la inserisce nella posizione predefinita della chiave ( **~/.ssh**). Per creare un set specifico di chiavi, invece, usare l'opzione `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

La creazione della macchina virtuale e delle risorse di supporto richiede alcuni minuti. L'output di esempio seguente mostra che la creazione della macchina virtuale è riuscita:

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

```azurecli-interactive
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

L'output del comando è il seguente.

```azurecli
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

Prendere nota di `systemAssignedIdentity`. Verrà usata nel prossimo passaggio.

## <a name="give-the-vm-identity-permission-to-key-vault"></a>Assegnare all'identità della macchina virtuale l'autorizzazione per Key Vault

È ora possibile assegnare l'autorizzazione per Key Vault all'identità creata. Eseguire il comando seguente:

```azurecli-interactive
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-in-to-the-vm"></a>Accedere alla macchina virtuale

Accedere alla macchina virtuale usando un terminale.

```terminal
ssh azureuser@<PublicIpAddress>
```

## <a name="install-python-library-on-the-vm"></a>Installare la libreria Python nella macchina virtuale

Scaricare e installare la libreria Python [requests](https://pypi.org/project/requests/2.7.0/) per effettuare chiamate HTTP GET.

## <a name="create-edit-and-run-the-sample-python-app"></a>Creare, modificare ed eseguire l'app Python di esempio

Creare un file Python denominato **Sample.py**.

Aprire il file Sample.py e modificarlo in modo che contenga il codice seguente:

```python
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

Il codice precedente esegue un processo in due passaggi:

   1. Recupera un token dall'endpoint dell'identità del servizio gestita locale nella macchina virtuale. L'endpoint recupera quindi un token da Azure Active Directory.
   1. Passa il token all'insieme di credenziali delle chiavi e recupera il segreto.

Eseguire il comando indicato di seguito. Verrà visualizzato il valore del segreto.

```console
python Sample.py
```

In questa esercitazione è stato descritto come usare Azure Key Vault con un'app Python in esecuzione in una macchina virtuale Linux.

## <a name="clean-up-resources"></a>Pulire le risorse

Eliminare il gruppo di risorse, la macchina virtuale e tutte le risorse correlate quando non servono più. A tale scopo, selezionare il gruppo di risorse per la VM e selezionare **Elimina**.

Eliminare l'insieme di credenziali delle chiavi usando il comando `az keyvault delete`:

```azurecli-interactive
az keyvault delete --name
                   [--resource-group]
                   [--subscription]
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [API REST di Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
