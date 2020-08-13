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
ms.custom: mvc, devx-track-python
ms.openlocfilehash: c0f98f8c77e4a9cd4271511e0169f07da1c52baa
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87875950"
---
# <a name="tutorial-use-azure-key-vault-with-a-virtual-machine-in-python"></a>Esercitazione: Usare Azure Key Vault con una macchina virtuale in Python

Azure Key Vault consente di proteggere chiavi, segreti e certificati, ad esempio chiavi API e stringhe di connessione del database.

In questa esercitazione viene configurata un'applicazione Python per leggere informazioni da Azure Key Vault tramite identità gestite per le risorse di Azure. Si apprenderà come:

> [!div class="checklist"]
> * Creare un insieme di credenziali delle chiavi
> * Archiviare un segreto nell'insieme di credenziali delle chiavi
> * Creare una macchina virtuale Linux di Azure
> * Abilitare un'[identità gestita](../../active-directory/managed-identities-azure-resources/overview.md) per la macchina virtuale
> * Concedere le autorizzazioni necessarie per l'applicazione console per la lettura di dati dall'insieme di credenziali delle chiavi
> * Recuperare un segreto dall'insieme di credenziali delle chiavi

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

## <a name="create-a-resource-group-and-key-vault"></a>Creare un gruppo di risorse e un insieme di credenziali delle chiavi

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

## <a name="populate-your-key-vault-with-a-secret"></a>Popolare l'insieme di credenziali delle chiavi con un segreto

[!INCLUDE [Create a secret](../../../includes/key-vault-create-secret.md)]

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

Creare una VM denominata **myVM** usando uno dei metodi seguenti:

| Linux | Windows |
|--|--|
| [Interfaccia della riga di comando di Azure](../../virtual-machines/linux/quick-create-cli.md) | [Interfaccia della riga di comando di Azure](../../virtual-machines/windows/quick-create-cli.md) |
| [PowerShell](../../virtual-machines/linux/quick-create-powershell.md) | [PowerShell](../../virtual-machines/windows/quick-create-powershell.md) |
| [Azure portal](../../virtual-machines/linux/quick-create-portal.md) | [Il portale di Azure](../../virtual-machines/windows/quick-create-portal.md) |

Per creare una VM Linux con l'interfaccia della riga di comando di Azure, usare il comando [az vm create](/cli/azure/vm).  L'esempio seguente aggiunge un account utente denominato *azureuser*. Il parametro `--generate-ssh-keys` viene usato per generare automaticamente una chiave SSH e inserito nella posizione predefinita della chiave ( *~/.ssh*). 

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Prendere nota del valore di `publicIpAddress` nell'output.

## <a name="assign-an-identity-to-the-vm"></a>Assegnare un'identità alla macchina virtuale

Creare un'identità assegnata dal sistema per la macchina virtuale con il comando [az vm identity assign](/cli/azure/vm/identity?view=azure-cli-latest#az-vm-identity-assign) dell'interfaccia della riga di comando di Azure:

```azurecli
az vm identity assign --name "myVM" --resource-group "myResourceGroup"
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
az keyvault set-policy --name "<your-unique-keyvault-name>" --object-id "<systemAssignedIdentity>" --secret-permissions get list
```

## <a name="log-in-to-the-vm"></a>Accedere alla macchina virtuale

Per accedere alla macchina virtuale, seguire le istruzioni riportate in [Connettersi e accedere a una macchina virtuale di Azure che esegue Linux](../../virtual-machines/linux/login-using-aad.md) oppure [Connettersi e accedere a una macchina virtuale di Azure che esegue Windows](../../virtual-machines/windows/connect-logon.md).


Per accedere a una macchina virtuale Linux, è possibile usare il comando ssh con il valore di "<publicIpAddress>" specificato nel passaggio [Creare una macchina virtuale](#create-a-virtual-machine):

```terminal
ssh azureuser@<PublicIpAddress>
```

## <a name="install-python-libraries-on-the-vm"></a>Installare le libreria Python nella VM

Nella macchina virtuale installare le due librerie Python che verranno usate nello script Python: `azure-keyvault-secrets` e `azure.identity`.  

In una macchina virtuale Linux, ad esempio, è possibile installarle usando `pip3`:

```bash
pip3 install azure-keyvault-secrets

pip3 install azure.identity
```

## <a name="create-and-edit-the-sample-python-script"></a>Creare e modificare lo script Python di esempio

Nella macchina virtuale creare un file Python denominato **sample.py**. Modificare il file in modo che contenga il codice seguente, sostituendo "<your-unique-keyvault-name>" con il nome dell'insieme di credenziali delle chiavi:

```python
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential

keyVaultName = "<your-unique-keyvault-name>"
KVUri = f"https://{keyVaultName}.vault.azure.net"
secretName = "mySecret"

credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)
retrieved_secret = client.get_secret(secretName)

print(f"The value of secret '{secretName}' in '{keyVaultName}' is: '{retrieved_secret.value}'")
```

## <a name="run-the-sample-python-app"></a>Eseguire l'app Python di esempio

Infine, eseguire **sample.py**. Se le impostazioni sono corrette, verrà restituito il valore del segreto:

```bash
python3 sample.py

The value of secret 'mySecret' in '<your-unique-keyvault-name>' is: 'Success!'
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile eliminare la macchina virtuale e l'insieme di credenziali delle chiavi.  È possibile eseguire rapidamente questa operazione eliminando semplicemente il gruppo di risorse a cui appartengono:

```azurecli
az group delete -g myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

[API REST di Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
