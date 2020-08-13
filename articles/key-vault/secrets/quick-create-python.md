---
title: Avvio rapido - Libreria client di Azure Key Vault per Python - Gestire i segreti
description: Informazioni su come creare, recuperare ed eliminare segreti da Azure Key Vault usando la libreria client Python
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/20/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: b31f22f9a3909df308fdcfa994833887828f2539
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87876360"
---
# <a name="quickstart-azure-key-vault-secrets-client-library-for-python"></a>Avvio rapido: Libreria client dei segreti di Azure Key Vault per Python

Introduzione alla libreria client di Azure Key Vault per Python Seguire questi passaggi per installare il pacchetto e provare il codice di esempio per le attività di base.

L'insieme di credenziali delle chiavi di Azure consente di proteggere le chiavi e i segreti di crittografia usati da servizi e applicazioni cloud. Usare la libreria client di Azure Key Vault per Python per:

- Aumentare la sicurezza e il controllo di chiavi e password.
- Creare e importare chiavi di crittografia in pochi minuti.
- Ridurre la latenza con ridondanza globale e su scala cloud.
- Semplificare e automatizzare le attività per i certificati TLS/SSL.
- Usare moduli di protezione hardware convalidati in base agli standard FIPS 140-2 livello 2.

[Documentazione di riferimento delle API](/python/api/overview/azure/keyvault-secrets-readme?view=azure-python) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault) | [Pacchetto (Indice dei pacchetti Python)](https://pypi.org/project/azure-keyvault/)

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure: [creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Python 2.7, 3.5.3 o versione successiva
- [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) o [Azure PowerShell](/powershell/azure/)

Questa Guida di avvio rapido presuppone l'esecuzione dell'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) in una finestra del terminale Linux.

## <a name="setting-up"></a>Configurazione

### <a name="install-the-package"></a>Installare il pacchetto

Nella finestra della console installare la libreria dei segreti di Azure Key Vault per Python.

```console
pip install azure-keyvault-secrets
```

Per questa Guida di avvio rapido, sarà necessario installare anche il pacchetto azure.identity package:

```console
pip install azure.identity
```

### <a name="create-a-resource-group-and-key-vault"></a>Creare un gruppo di risorse e un insieme di credenziali delle chiavi

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

### <a name="create-a-service-principal"></a>Creare un'entità servizio

[!INCLUDE [Create a service principal](../../../includes/key-vault-sp-creation.md)]

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Concedere all'entità servizio l'accesso all'insieme di credenziali delle chiavi

[!INCLUDE [Give the service principal access to your key vault](../../../includes/key-vault-sp-kv-access.md)]

#### <a name="set-environmental-variables"></a>Impostare le variabili di ambiente

[!INCLUDE [Set environmental variables](../../../includes/key-vault-set-environmental-variables.md)]

## <a name="object-model"></a>Modello a oggetti

La libreria client di Azure Key Vault per Python consente di gestire le chiavi e le risorse correlate, come i certificati e i segreti. Gli esempi di codice riportati di seguito illustrano come creare un client e come impostare, recuperare ed eliminare un segreto.

L'intera app console è disponibile all'indirizzo https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app.

## <a name="code-examples"></a>Esempi di codice

### <a name="add-directives"></a>Aggiungere le direttive

Aggiungere le direttive seguenti all'inizio del codice:

```python
import os
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential
```

### <a name="authenticate-and-create-a-client"></a>Autenticare e creare un client

L'autenticazione rispetto all'insieme di credenziali delle chiavi e la creazione di un client dell'insieme di credenziali delle chiavi dipendono dalle variabili di ambiente indicate nel passaggio precedente [Impostare le variabili di ambiente](#set-environmental-variables). Il nome dell'insieme di credenziali delle chiavi viene espanso nell'URI relativo, nel formato "https://<nome-insieme-credenziali-chiave-personale>.vault.azure.net".

```python
credential = DefaultAzureCredential()

client = SecretClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-secret"></a>Salvare un segreto

Ora che l'applicazione è stata autenticata, è possibile inserire un segreto nell'insieme di credenziali delle chiavi usando il metodo client.SetSecret](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync). A tale scopo è necessario un nome per il segreto e in questo esempio viene usato "mySecret".  

```python
client.set_secret(secretName, secretValue)
```

È possibile verificare che il segreto sia stato impostato con il comando [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show):

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Recuperare un segreto

È ora possibile recuperare il valore impostato in precedenza con il [metodo client.GetSecret](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync).

```python
retrieved_secret = client.get_secret(secretName)
 ```

Il segreto è ora salvato come `retrieved_secret.value`.

### <a name="delete-a-secret"></a>consente di eliminare un segreto

Eliminare infine il segreto dall'insieme di credenziali delle chiavi con il [metodo client.DeleteSecret](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync).

```python
client.delete_secret(secretName)
```

È possibile verificare che il segreto non sia più dotato del comando [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show):

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, è possibile usare l'interfaccia della riga di comando di Azure o Azure PowerShell per rimuovere l'insieme di credenziali delle chiavi e il gruppo di risorse corrispondente.

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>Codice di esempio

```python
import os
import cmd
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = f"https://{keyVaultName}.vault.azure.net"

credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)

secretName = "mySecret"

print("Input the value of your secret > ")
secretValue = raw_input()

print(f"Creating a secret in {keyVaultName} called '{secretName}' with the value '{secretValue}` ...")

client.set_secret(secretName, secretValue)

print(" done.")

print("Forgetting your secret.")
secretValue = ""
print(f"Your secret is {secretValue}.")

print(f"Retrieving your secret from {keyVaultName}.")

retrieved_secret = client.get_secret(secretName)

print(f"Your secret is '{retrieved_secret.value}'.")
print(f"Deleting your secret from {keyVaultName} ...")

client.delete_secret(secretName)

print(" done.")
```

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stato creato un insieme di credenziali delle chiavi, quindi è stato archiviato e recuperato un segreto. Per altre informazioni sul servizio Key Vault e su come integrarlo nelle applicazioni, continuare con gli articoli seguenti.

- Leggere una [panoramica di Azure Key Vault](../general/overview.md)
- Vedere la [Guida per gli sviluppatori per Azure Key Vault](../general/developers-guide.md)
- Esaminare le [procedure consigliate per Azure Key Vault](../general/best-practices.md)
