---
title: 'Avvio rapido: Libreria client di Azure Key Vault per Python - Gestire i certificati'
description: Informazioni su come creare, recuperare ed eliminare certificati da Azure Key Vault con la libreria client per Python
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: devx-track-python, devx-track-azurecli
ms.openlocfilehash: 05b1ea8e6daa9fae0588535534c4a8be45ab91d5
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93285264"
---
# <a name="quickstart-azure-key-vault-certificate-client-library-for-python"></a>Avvio rapido: Libreria client dei certificati di Azure Key Vault per Python

Introduzione alla libreria client dei certificati di Azure Key Vault per Python. Seguire questi passaggi per installare il pacchetto e provare il codice di esempio per le attività di base. Usando Key Vault per l'archiviazione dei certificati si evita di archiviarli nel codice, aumentando la sicurezza dell'app.

[Documentazione di riferimento delle API](/python/api/overview/azure/keyvault-certificates-readme) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-certificates) | [Pacchetto (Indice dei pacchetti Python)](https://pypi.org/project/azure-keyvault-certificates)

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure: [creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python 2.7 o 3.5.3 e versioni successive](/azure/developer/python/configure-local-development-environment)
- [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli)

Questa Guida di avvio rapido presuppone l'esecuzione dell'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) in una finestra del terminale Linux.

## <a name="set-up-your-local-environment"></a>Configurare l'ambiente locale

Questo argomento di avvio rapido usa la libreria di identità di Azure con l'interfaccia della riga di comando di Azure per autenticare l'utente nei servizi di Azure. Gli sviluppatori possono anche usare Visual Studio o Visual Studio Code per autenticare le chiamate. Per altre informazioni, vedere [Autenticare il client con la libreria client Azure Identity](/java/api/overview/azure/identity-readme).

### <a name="sign-in-to-azure"></a>Accedere ad Azure

1. Eseguire il comando `login`.

    ```azurecli-interactive
    az login
    ```

    Se l'interfaccia della riga di comando può aprire il browser predefinito, eseguirà questa operazione e caricherà una pagina di accesso di Azure.

    In caso contrario, aprire una pagina del browser all'indirizzo [https://aka.ms/devicelogin](https://aka.ms/devicelogin) e immettere il codice di autorizzazione visualizzato nel terminale.

2. Accedere con le credenziali dell'account nel browser.

### <a name="install-the-packages"></a>Installare i pacchetti

1. In un terminale o un prompt dei comandi creare una cartella di progetto appropriata e quindi creare e attivare un ambiente virtuale Python come descritto in [Usare ambienti virtuali Python](/azure/developer/python/configure-local-development-environment?tabs=cmd#use-python-virtual-environments)

1. Installare la libreria di identità di Azure Active Directory:

    ```terminal
    pip install azure.identity
    ```


1. Installare la libreria client dei certificati di Key Vault:

    ```terminal
    pip install azure-keyvault-certificates
    ```

### <a name="create-a-resource-group-and-key-vault"></a>Creare un gruppo di risorse e un insieme di credenziali delle chiavi

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

### <a name="grant-access-to-your-key-vault"></a>Concedere l'accesso all'insieme di credenziali delle chiavi

Creare un criterio di accesso per l'insieme di credenziali delle chiavi che concede l'autorizzazione per il segreto all'account utente

```console
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --secret-permissions delete get list set
```

#### <a name="set-environment-variables"></a>Impostare le variabili di ambiente

Questa applicazione usa il nome dell'insieme di credenziali delle chiavi come variabile di ambiente denominata `KEY_VAULT_NAME`.

Windows
```cmd
set KEY_VAULT_NAME=<your-key-vault-name>
````
Windows PowerShell
```powershell
$Env:KEY_VAULT_NAME=<your-key-vault-name>
```

macOS o Linux
```cmd
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="create-the-sample-code"></a>Creare il codice di esempio

La libreria client dei certificati di Azure Key Vault per Python consente di gestire i certificati. L'esempio di codice seguente mostra come creare un client e come impostare, recuperare ed eliminare un certificato.

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

- Se si verificano errori relativi alle autorizzazioni, assicurarsi d i aver eseguito il comando [`az keyvault set-policy`](#grant-access-to-your-key-vault).
- Se il codice viene eseguito di nuovo con lo stesso nome della chiave si potrebbe verificare un errore con un messaggio analogo a "(Conflitto) Il certificato <name> si trova attualmente in uno stato eliminato ma recuperabile". Usare un nome di chiave diverso.

## <a name="code-details"></a>Dettagli del codice

### <a name="authenticate-and-create-a-client"></a>Autenticare e creare un client

In questo argomento di avvio rapido viene usato l'utente connesso per eseguire l'autenticazione in Key Vault, che è il metodo preferito per lo sviluppo locale. Per le applicazioni distribuite in Azure, l'identità gestita deve essere assegnata al servizio app o alla macchina virtuale. Per altre informazioni, vedere [Informazioni sulle identità gestite per le risorse di Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

Nell'esempio seguente, il nome dell'insieme di credenziali delle chiavi viene esteso al relativo URI, nel formato "https://\<your-key-vault-name\>.vault.azure.net". Questo esempio usa la classe ['DefaultAzureCredential()'](/python/api/azure-identity/azure.identity.defaultazurecredential), che consente di usare lo stesso codice in ambienti diversi con opzioni diverse per specificare l'identità. Per altre informazioni, vedere [Autenticazione DefaultAzureCredential](https://docs.microsoft.com/python/api/overview/azure/identity-readme). 

```python
credential = DefaultAzureCredential()
client = CertificateClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-certificate"></a>Salvare un certificato

Una volta ottenuto l'oggetto client per l'insieme di credenziali delle chiavi, è possibile creare un certificato usando il metodo [begin_create_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?#begin-create-certificate-certificate-name--policy----kwargs-): 

```python
policy = CertificatePolicy.get_default()
poller = client.begin_create_certificate(certificate_name=certificateName, policy=policy)
certificate = poller.result()
```

In questo caso, il certificato richiede un criterio ottenuto con il metodo [CertificatePolicy.get_default](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificatepolicy?#get-default--).

La chiamata a un metodo `begin_create_certificate` genera una chiamata asincrona all'API REST di Azure per l'insieme di credenziali delle chiavi. La chiamata asincrona restituisce un oggetto poller. Per attendere il risultato dell'operazione, chiamare il metodo `result` dell'oggetto poller.

Durante la gestione della richiesta, Azure autentica l'identità del chiamante (l'entità servizio) usando l'oggetto credenziali fornito al client.


### <a name="retrieve-a-certificate"></a>Recuperare un certificato

Per leggere un certificato da Key Vault, usare il metodo [get_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?#get-certificate-certificate-name----kwargs-):

```python
retrieved_certificate = client.get_certificate(certificateName)
 ```

È anche possibile verificare che il certificato sia stato impostato con il comando [az keyvault certificate show](/cli/azure/keyvault/certificate?#az-keyvault-certificate-show) dell'interfaccia della riga di comando di Azure.

### <a name="delete-a-certificate"></a>Eliminare un certificato

Per eliminare un certificato, usare il metodo [begin_delete_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?#begin-delete-certificate-certificate-name----kwargs-):

```python
poller = client.begin_delete_certificate(certificateName)
deleted_certificate = poller.result()
```

Il `begin_delete_certificate` metodo è asincrono e restituisce un oggetto poller. La chiamata al metodo `result` dell'oggetto poller attende il relativo completamento.

È possibile verificare che il certificato sia stato eliminato con il comando [az keyvault certificate show](/cli/azure/keyvault/certificate?#az-keyvault-certificate-show) dell'interfaccia della riga di comando di Azure.

Una volta eliminato, un certificato rimane in uno stato eliminato ma recuperabile per un periodo di tempo. Se si esegue di nuovo il codice, usare un nome di certificato diverso.

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole sperimentare anche con [segreti](../secrets/quick-create-python.md) e [chiavi](../keys/quick-create-python.md), è possibile riutilizzare l'istanza di Key Vault creata in questo articolo.

In caso contrario, dopo aver finito di usare le risorse create nell'articolo, eseguire il comando seguente per eliminare il gruppo di risorse e tutte le risorse contenute al suo interno:

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica dell'insieme di credenziali chiave di Azure](../general/overview.md)
- [Proteggere l'accesso a un insieme di credenziali delle chiavi](../general/secure-your-key-vault.md)
- [Guida per gli sviluppatori per Azure Key Vault](../general/developers-guide.md)
- [Procedure consigliate per Azure Key Vault](../general/best-practices.md)
- [Eseguire l'autenticazione con Key Vault](../general/authentication.md)