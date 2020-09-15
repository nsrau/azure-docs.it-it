---
title: 'Avvio rapido: Libreria client di Azure Key Vault per Python - Gestire le chiavi'
description: Informazioni su come creare, recuperare ed eliminare chiavi da Azure Key Vault con la libreria client per Python
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: 44942067756f82c224decc218de17bf7dbc69734
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2020
ms.locfileid: "89482130"
---
# <a name="quickstart-azure-key-vault-keys-client-library-for-python"></a>Avvio rapido: Libreria client delle chiavi di Azure Key Vault per Python

Introduzione alla libreria client di Azure Key Vault per Python Seguire questi passaggi per installare il pacchetto e provare il codice di esempio per le attività di base. Usando Key Vault per l'archiviazione delle chiavi crittografiche si evita di archiviarle nel codice, aumentando la sicurezza dell'app.

[Documentazione di riferimento delle API](/python/api/overview/azure/keyvault-keys-readme?view=azure-python) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-keys) | [Pacchetto (Indice dei pacchetti Python)](https://pypi.org/project/azure-keyvault-keys/)

## <a name="set-up-your-local-environment"></a>Configurare l'ambiente locale

[!INCLUDE [Set up your local environment](../../../includes/key-vault-python-qs-setup.md)]

7. Installare la libreria di chiavi di Key Vault:

    ```terminal
    pip install azure-keyvault-keys
    ```

## <a name="create-a-resource-group-and-key-vault"></a>Creare un gruppo di risorse e un insieme di credenziali delle chiavi

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

## <a name="give-the-service-principal-access-to-your-key-vault"></a>Concedere all'entità servizio l'accesso all'insieme di credenziali delle chiavi

Eseguire il comando [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) seguente per autorizzare le operazioni get, list e create nelle chiavi da parte dell'entità servizio. 

# <a name="cmd"></a>[cmd](#tab/cmd)

```azurecli
az keyvault set-policy --name %KEY_VAULT_NAME% --spn %AZURE_CLIENT_ID% --resource-group KeyVault-PythonQS-rg --key-permissions delete get list create
```

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az keyvault set-policy --name $KEY_VAULT_NAME --spn $AZURE_CLIENT_ID --resource-group KeyVault-PythonQS-rg --key-permissions delete get list create
```

---

Questo comando si basa sulle variabili di ambiente `KEY_VAULT_NAME` e `AZURE_CLIENT_ID` create nei passaggi precedenti.

Per altre informazioni, vedere [Assegnare un criterio di accesso - Interfaccia della riga di comando](../general/assign-access-policy-cli.md)

## <a name="create-the-sample-code"></a>Creare il codice di esempio

La libreria client di Azure Key Vault per Python consente di gestire le chiavi crittografiche e gli asset correlati, come i certificati e i segreti. L'esempio di codice seguente illustra come creare un client e come impostare, recuperare ed eliminare un segreto.

Creare un file denominato *kv_keys.py* che contiene questo codice.

```python
import os
from azure.keyvault.keys import KeyClient
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = "https://" + keyVaultName + ".vault.azure.net"

credential = DefaultAzureCredential()
client = KeyClient(vault_url=KVUri, credential=credential)

keyName = input("Input a name for your key > ")

print(f"Creating a key in {keyVaultName} called '{keyName}' ...")

rsa_key = client.create_rsa_key(keyName, size=2048)

print(" done.")

print(f"Retrieving your key from {keyVaultName}.")

retrieved_key = client.get_key(keyName)

print(f"Key with name '{retrieved_key.name}' was found.")
print(f"Deleting your key from {keyVaultName} ...")

poller = client.begin_delete_key(keyName)
deleted_key = poller.result()

print(" done.")
```

## <a name="run-the-code"></a>Eseguire il codice

Assicurarsi che il codice della sezione precedente sia incluso in un file denominato *kv_keys.py*. Eseguire quindi il codice con il comando seguente:

```terminal
python kv_keys.py
```

- Se si verificano errori relativi alle autorizzazioni, assicurarsi d i aver eseguito il [comando `az keyvault set-policy`](#give-the-service-principal-access-to-your-key-vault).
- Se il codice viene eseguito di nuovo con lo stesso nome della chiave si potrebbe verificare un errore con un messaggio analogo a "(Conflitto) La chiave <name> si trova attualmente in uno stato eliminato ma recuperabile". Usare un nome di chiave diverso.

## <a name="code-details"></a>Dettagli del codice

### <a name="authenticate-and-create-a-client"></a>Autenticare e creare un client

Nel codice precedente l'oggetto [`DefaultAzureCredential`](/python/api/azure-identity/azure.identity.defaultazurecredential?view=azure-python) usa le variabili di ambiente create per l'entità servizio. Queste credenziali vengono fornite ogni volta che si crea un oggetto client da una libreria di Azure, ad esempio [`KeyClient`](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python), insieme all'URI della risorsa da usare tramite il client:

```python
credential = DefaultAzureCredential()
client = KeyClient(vault_url=KVUri, credential=credential)
```

## <a name="save-a-key"></a>Salvare una chiave

Una volta ottenuto l'oggetto client per l'insieme di credenziali delle chiavi, è possibile archiviare una chiave usando il metodo [set_secret](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python#create-rsa-key-name----kwargs-): 

```python
rsa_key = client.create_rsa_key(keyName, size=2048)
```

È anche possibile usare [create_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python#create-key-name--key-type----kwargs-) o [create_ec_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python#create-ec-key-name----kwargs-).

La chiamata a un metodo `create` genera una chiamata all'API REST di Azure per l'insieme di credenziali delle chiavi.

Durante la gestione della richiesta, Azure autentica l'identità del chiamante (l'entità servizio) usando l'oggetto credenziali fornito al client.

Verifica inoltre che il chiamante sia autorizzato a eseguire l'azione richiesta. Questa autorizzazione è stata concessa all'entità servizio in precedenza usando il [comando `az keyvault set-policy`](#give-the-service-principal-access-to-your-key-vault).

## <a name="retrieve-a-key"></a>Recuperare una chiave

Per leggere una chiave da Key Vault, usare il metodo [get_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python#get-key-name--version-none----kwargs-):

```python
retrieved_key = client.get_key(keyName)
 ```

È anche possibile verificare che la chiave sia stata impostata con il comando [az keyvault key show](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-show) dell'interfaccia della riga di comando di Azure.

### <a name="delete-a-key"></a>Eliminare una chiave

Per eliminare una chiave, usare il metodo [begin_delete_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python#begin-delete-key-name----kwargs-):

```python
poller = client.begin_delete_key(keyName)
deleted_key = poller.result()
```

Il `begin_delete_key` metodo è asincrono e restituisce un oggetto poller. La chiamata al metodo `result` dell'oggetto poller attende il relativo completamento.

Per verificare se la chiave è stata eliminata usare il comando [az keyvault key show](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-show) dell'interfaccia della riga di comando di Azure.

Una volta eliminata, una chiave rimane in uno stato eliminato ma recuperabile per un periodo di tempo. Se si esegue di nuovo il codice, usare un nome di chiave diverso.

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole sperimentare anche con [certificati](../certificates/quick-create-python.md) e [segreti](../secrets/quick-create-python.md), è possibile riutilizzare l'istanza di Key Vault creata in questo articolo.

In caso contrario, dopo aver finito di usare le risorse create nell'articolo, eseguire il comando seguente per eliminare il gruppo di risorse e tutte le risorse contenute al suo interno:

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica dell'insieme di credenziali chiave di Azure](../general/overview.md)
- [Guida per gli sviluppatori per Azure Key Vault](../general/developers-guide.md)
- [Procedure consigliate per Azure Key Vault](../general/best-practices.md)
- [Eseguire l'autenticazione con Key Vault](../general/authentication.md)
