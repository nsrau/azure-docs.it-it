---
title: Avvio rapido - Libreria client di Azure Key Vault per Python - Gestire i segreti
description: Informazioni su come creare, recuperare ed eliminare segreti da Azure Key Vault usando la libreria client Python
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: cd8a5751c018b9b3b3b2ef96765545f2edab685b
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "89489205"
---
# <a name="quickstart-azure-key-vault-secrets-client-library-for-python"></a>Avvio rapido: Libreria client dei segreti di Azure Key Vault per Python

Introduzione alla libreria client di Azure Key Vault per Python Seguire questi passaggi per installare il pacchetto e provare il codice di esempio per le attività di base. Usando Key Vault per l'archiviazione dei segreti si evita di archiviarli nel codice, aumentando la sicurezza dell'app.

[Documentazione di riferimento delle API](/python/api/overview/azure/keyvault-secrets-readme?view=azure-python) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-secrets) | [Pacchetto (Indice dei pacchetti Python)](https://pypi.org/project/azure-keyvault-secrets/)

## <a name="set-up-your-local-environment"></a>Configurare l'ambiente locale

[!INCLUDE [Set up your local environment](../../../includes/key-vault-python-qs-setup.md)]

7. Installare la libreria di segreti di Key Vault:

    ```terminal
    pip install azure-keyvault-secrets
    ```

## <a name="create-a-resource-group-and-key-vault"></a>Creare un gruppo di risorse e un insieme di credenziali delle chiavi

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

## <a name="give-the-service-principal-access-to-your-key-vault"></a>Concedere all'entità servizio l'accesso all'insieme di credenziali delle chiavi

Eseguire il comando [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) seguente per autorizzare le operazioni get, list e create nei segreti da parte dell'entità servizio. Questo comando si basa sulle variabili di ambiente `KEY_VAULT_NAME` e `AZURE_CLIENT_ID` create nei passaggi precedenti.

# <a name="cmd"></a>[cmd](#tab/cmd)

```azurecli
az keyvault set-policy --name %KEY_VAULT_NAME% --spn %AZURE_CLIENT_ID% --resource-group KeyVault-PythonQS-rg --secret-permissions delete get list set 
```

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az keyvault set-policy --name $KEY_VAULT_NAME --spn $AZURE_CLIENT_ID --resource-group KeyVault-PythonQS-rg --secret-permissions delete get list set 
```

---

Questo comando si basa sulle variabili di ambiente `KEY_VAULT_NAME` e `AZURE_CLIENT_ID` create nei passaggi precedenti.

Per altre informazioni, vedere [Assegnare un criterio di accesso - Interfaccia della riga di comando](../general/assign-access-policy-cli.md)

## <a name="create-the-sample-code"></a>Creare il codice di esempio

La libreria client di Azure Key Vault per Python consente di gestire i segreti e gli asset correlati, come i certificati e le chiavi crittografiche. L'esempio di codice seguente illustra come creare un client e come impostare, recuperare ed eliminare un segreto.

Creare un file denominato *kv_secrets.py* che contiene questo codice.

```python
import os
import cmd
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = f"https://{keyVaultName}.vault.azure.net"

credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)

secretName = input("Input a name for your secret > ")
secretValue = input("Input a value for your secret > ")

print(f"Creating a secret in {keyVaultName} called '{secretName}' with the value '{secretValue}' ...")

client.set_secret(secretName, secretValue)

print(" done.")

print(f"Retrieving your secret from {keyVaultName}.")

retrieved_secret = client.get_secret(secretName)

print(f"Your secret is '{retrieved_secret.value}'.")
print(f"Deleting your secret from {keyVaultName} ...")

poller = client.begin_delete_secret(secretName)
deleted_secret = poller.result()

print(" done.")
```

## <a name="run-the-code"></a>Eseguire il codice

Assicurarsi che il codice della sezione precedente sia incluso in un file denominato *kv_secrets.py*. Eseguire quindi il codice con il comando seguente:

```terminal
python kv_secrets.py
```

- Se si verificano errori relativi alle autorizzazioni, assicurarsi d i aver eseguito il comando [`az keyvault set-policy`](#give-the-service-principal-access-to-your-key-vault).
- Se il codice viene eseguito di nuovo con lo stesso nome del segreto si potrebbe verificare un errore con un messaggio analogo a "(Conflitto) Il segreto <name> si trova attualmente in uno stato eliminato ma recuperabile". Usare un nome di segreto diverso.

## <a name="code-details"></a>Dettagli del codice

### <a name="authenticate-and-create-a-client"></a>Autenticare e creare un client

Nel codice precedente l'oggetto [`DefaultAzureCredential`](/python/api/azure-identity/azure.identity.defaultazurecredential?view=azure-python) usa le variabili di ambiente create per l'entità servizio. Queste credenziali vengono fornite ogni volta che si crea un oggetto client da una libreria di Azure, ad esempio [`SecretClient`](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python), insieme all'URI della risorsa da usare tramite il client:

```python
credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-secret"></a>Salvare un segreto

Una volta ottenuto l'oggetto client per l'insieme di credenziali delle chiavi, è possibile archiviare un segreto usando il metodo [set_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python#set-secret-name--value----kwargs-): 

```python
client.set_secret(secretName, secretValue)
```

La chiamata a `set_secret` genera una chiamata all'API REST di Azure per l'insieme di credenziali delle chiavi.

Durante la gestione della richiesta, Azure autentica l'identità del chiamante (l'entità servizio) usando l'oggetto credenziali fornito al client.

Verifica inoltre che il chiamante sia autorizzato a eseguire l'azione richiesta. Questa autorizzazione è stata concessa all'entità servizio in precedenza usando il comando [`az keyvault set-policy`](#give-the-service-principal-access-to-your-key-vault).

### <a name="retrieve-a-secret"></a>Recuperare un segreto

Per leggere un segreto da Key Vault, usare il metodo [get_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python#get-secret-name--version-none----kwargs-):

```python
retrieved_secret = client.get_secret(secretName)
 ```

Il valore del segreto è contenuto in `retrieved_secret.value`.

È anche possibile recuperare un segreto con il comando [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) dell'interfaccia della riga di comando di Azure.

### <a name="delete-a-secret"></a>consente di eliminare un segreto

Per eliminare un segreto, usare il metodo [begin_delete_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python#begin-delete-secret-name----kwargs-):

```python
poller = client.begin_delete_secret(secretName)
deleted_secret = poller.result()
```

Il `begin_delete_secret` metodo è asincrono e restituisce un oggetto poller. La chiamata al metodo `result` dell'oggetto poller attende il relativo completamento.

Per verificare se il segreto è stato rimosso, è possibile usare il comando [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) dell'interfaccia della riga di comando di Azure.

Una volta eliminato, un segreto rimane in uno stato eliminato ma recuperabile per un periodo di tempo. Se si esegue di nuovo il codice, usare un nome di segreto diverso.

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole sperimentare anche con [certificati](../certificates/quick-create-python.md) e [chiavi](../keys/quick-create-python.md), è possibile riutilizzare l'istanza di Key Vault creata in questo articolo.

In caso contrario, dopo aver finito di usare le risorse create nell'articolo, eseguire il comando seguente per eliminare il gruppo di risorse e tutte le risorse contenute al suo interno:

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica dell'insieme di credenziali chiave di Azure](../general/overview.md)
- [Guida per gli sviluppatori per Azure Key Vault](../general/developers-guide.md)
- [Procedure consigliate per Azure Key Vault](../general/best-practices.md)
- [Eseguire l'autenticazione con Key Vault](../general/authentication.md)
