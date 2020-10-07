---
title: 'Avvio rapido: Libreria client di Azure Key Vault per Python - Gestire i certificati'
description: Informazioni su come creare, recuperare ed eliminare certificati da Azure Key Vault con la libreria client per Python
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: b9ff7397ad29ac681e21c32608ade9c6ce557c37
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "89488627"
---
# <a name="quickstart-azure-key-vault-certificates-client-library-for-python"></a>Avvio rapido: Libreria client dei certificati di Azure Key Vault per Python

Introduzione alla libreria client di Azure Key Vault per Python Seguire questi passaggi per installare il pacchetto e provare il codice di esempio per le attività di base. Usando Key Vault per l'archiviazione dei certificati si evita di archiviarli nel codice, aumentando la sicurezza dell'app.

[Documentazione di riferimento delle API](/python/api/overview/azure/keyvault-certificates-readme?view=azure-python) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-certificates) | [Pacchetto (Indice dei pacchetti Python)](https://pypi.org/project/azure-keyvault-certificates)

## <a name="set-up-your-local-environment"></a>Configurare l'ambiente locale

[!INCLUDE [Set up your local environment](../../../includes/key-vault-python-qs-setup.md)]

7. Installare la libreria di certificati di Key Vault:

    ```terminal
    pip install azure-keyvault-certificates
    ```

## <a name="create-a-resource-group-and-key-vault"></a>Creare un gruppo di risorse e un insieme di credenziali delle chiavi

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

## <a name="give-the-service-principal-access-to-your-key-vault"></a>Concedere all'entità servizio l'accesso all'insieme di credenziali delle chiavi

Eseguire il comando [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) seguente per autorizzare le operazioni get, list e create nei certificati da parte dell'entità servizio.

# <a name="cmd"></a>[cmd](#tab/cmd)

```azurecli
az keyvault set-policy --name %KEY_VAULT_NAME% --spn %AZURE_CLIENT_ID% --resource-group KeyVault-PythonQS-rg --certificate-permissions delete get list create
```

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az keyvault set-policy --name $KEY_VAULT_NAME --spn $AZURE_CLIENT_ID --resource-group KeyVault-PythonQS-rg --certificate-permissions delete get list create 
```

---

Questo comando si basa sulle variabili di ambiente `KEY_VAULT_NAME` e `AZURE_CLIENT_ID` create nei passaggi precedenti.

Per altre informazioni, vedere [Assegnare un criterio di accesso - Interfaccia della riga di comando](../general/assign-access-policy-cli.md)

## <a name="create-the-sample-code"></a>Creare il codice di esempio

La libreria client di Azure Key Vault per Python consente di gestire i certificati e le risorse correlate, come i segreti e le chiavi crittografiche. L'esempio di codice seguente illustra come creare un client e come impostare, recuperare ed eliminare un segreto.

Creare un file denominato *kv_certificates.py* che contiene questo codice.

```python
import os
from azure.keyvault.certificates import CertificateClient, CertificatePolicy,CertificateContentType, WellKnownIssuerNames 
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = "https://" + keyVaultName + ".vault.azure.net"

credential = DefaultAzureCredential()
client = CertificateClient(vault_url=KVUri, credential=credential)

certificateName = input("Input a name for your certificate > ")

print(f"Creating a certificate in {keyVaultName} called '{certificateName}' ...")

policy = CertificatePolicy.get_default()
poller = client.begin_create_certificate(certificate_name=certificateName, policy=policy)
certificate = poller.result()

print(" done.")

print(f"Retrieving your certificate from {keyVaultName}.")

retrieved_certificate = client.get_certificate(certificateName)

print(f"Certificate with name '{retrieved_certificate.name}' was found'.")
print(f"Deleting your certificate from {keyVaultName} ...")

poller = client.begin_delete_certificate(certificateName)
deleted_certificate = poller.result()

print(" done.")
```

## <a name="run-the-code"></a>Eseguire il codice

Assicurarsi che il codice della sezione precedente sia incluso in un file denominato *kv_certificates.py*. Eseguire quindi il codice con il comando seguente:

```terminal
python kv_certificates.py
```

- Se si verificano errori relativi alle autorizzazioni, assicurarsi d i aver eseguito il comando [`az keyvault set-policy`](#give-the-service-principal-access-to-your-key-vault).
- Se il codice viene eseguito di nuovo con lo stesso nome della chiave si potrebbe verificare un errore con un messaggio analogo a "(Conflitto) Il certificato <name> si trova attualmente in uno stato eliminato ma recuperabile". Usare un nome di chiave diverso.

## <a name="code-details"></a>Dettagli del codice

### <a name="authenticate-and-create-a-client"></a>Autenticare e creare un client

Nel codice precedente l'oggetto [`DefaultAzureCredential`](/python/api/azure-identity/azure.identity.defaultazurecredential?view=azure-python) usa le variabili di ambiente create per l'entità servizio. Queste credenziali vengono fornite ogni volta che si crea un oggetto client da una libreria di Azure, ad esempio [`CertificateClient`](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?view=azure-python), insieme all'URI della risorsa da usare tramite il client:

```python
credential = DefaultAzureCredential()
client = CertificateClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-certificate"></a>Salvare un certificato

Una volta ottenuto l'oggetto client per l'insieme di credenziali delle chiavi, è possibile creare un certificato usando il metodo [begin_create_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?view=azure-python#begin-create-certificate-certificate-name--policy----kwargs-): 

```python
policy = CertificatePolicy.get_default()
poller = client.begin_create_certificate(certificate_name=certificateName, policy=policy)
certificate = poller.result()
```

In questo caso, il certificato richiede un criterio ottenuto con il metodo [CertificatePolicy.get_default](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificatepolicy?view=azure-python#get-default--).

La chiamata a un metodo `begin_create_certificate` genera una chiamata asincrona all'API REST di Azure per l'insieme di credenziali delle chiavi. La chiamata asincrona restituisce un oggetto poller. Per attendere il risultato dell'operazione, chiamare il metodo `result` dell'oggetto poller.

Durante la gestione della richiesta, Azure autentica l'identità del chiamante (l'entità servizio) usando l'oggetto credenziali fornito al client.

Verifica inoltre che il chiamante sia autorizzato a eseguire l'azione richiesta. Questa autorizzazione è stata concessa all'entità servizio in precedenza usando il comando [`az keyvault set-policy`](#give-the-service-principal-access-to-your-key-vault).

### <a name="retrieve-a-certificate"></a>Recuperare un certificato

Per leggere un certificato da Key Vault, usare il metodo [get_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?view=azure-python#get-certificate-certificate-name----kwargs-):

```python
retrieved_certificate = client.get_certificate(certificateName)
 ```

È anche possibile verificare che il certificato sia stato impostato con il comando [az keyvault certificate show](/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-show) dell'interfaccia della riga di comando di Azure.

### <a name="delete-a-certificate"></a>Eliminare un certificato

Per eliminare un certificato, usare il metodo [begin_delete_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?view=azure-python#begin-delete-certificate-certificate-name----kwargs-):

```python
poller = client.begin_delete_certificate(certificateName)
deleted_certificate = poller.result()
```

Il `begin_delete_certificate` metodo è asincrono e restituisce un oggetto poller. La chiamata al metodo `result` dell'oggetto poller attende il relativo completamento.

È possibile verificare che il certificato sia stato eliminato con il comando [az keyvault certificate show](/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-show) dell'interfaccia della riga di comando di Azure.

Una volta eliminato, un certificato rimane in uno stato eliminato ma recuperabile per un periodo di tempo. Se si esegue di nuovo il codice, usare un nome di certificato diverso.

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole sperimentare anche con [segreti](../secrets/quick-create-python.md) e [chiavi](../keys/quick-create-python.md), è possibile riutilizzare l'istanza di Key Vault creata in questo articolo.

In caso contrario, dopo aver finito di usare le risorse create nell'articolo, eseguire il comando seguente per eliminare il gruppo di risorse e tutte le risorse contenute al suo interno:

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica dell'insieme di credenziali chiave di Azure](../general/overview.md)
- [Guida per gli sviluppatori per Azure Key Vault](../general/developers-guide.md)
- [Procedure consigliate per Azure Key Vault](../general/best-practices.md)
- [Eseguire l'autenticazione con Key Vault](../general/authentication.md)
