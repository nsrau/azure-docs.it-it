---
title: 'Avvio rapido: Libreria client di Azure Key Vault per Python - Gestire i certificati'
description: Informazioni su come creare, recuperare ed eliminare certificati da Azure Key Vault con la libreria client per Python
author: msmbaldwin
ms.author: mbaldwin
ms.date: 3/30/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: 86ce2b95cce82bd6d6877f83c3bc0b0496804c83
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87875337"
---
# <a name="quickstart-azure-key-vault-certificates-client-library-for-python"></a>Avvio rapido: Libreria client dei certificati di Azure Key Vault per Python

Introduzione alla libreria client di Azure Key Vault per Python Seguire questi passaggi per installare il pacchetto e provare il codice di esempio per le attività di base.

L'insieme di credenziali delle chiavi di Azure consente di proteggere le chiavi e i segreti di crittografia usati da servizi e applicazioni cloud. Usare la libreria client di Azure Key Vault per Python per:

- Aumentare la sicurezza e il controllo di chiavi e password.
- Creare e importare chiavi di crittografia in pochi minuti.
- Ridurre la latenza con ridondanza globale e su scala cloud.
- Semplificare e automatizzare le attività per i certificati TLS/SSL.
- Usare moduli di protezione hardware convalidati in base agli standard FIPS 140-2 livello 2.

[Documentazione di riferimento delle API](/python/api/overview/azure/keyvault-certificates-readme?view=azure-python) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault) | [Pacchetto (Indice dei pacchetti Python)](https://pypi.org/project/azure-keyvault/)

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure: [creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Python 2.7, 3.5.3 o versione successiva
- [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) o [Azure PowerShell](/powershell/azure/)

Questa Guida di avvio rapido presuppone l'esecuzione dell'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) in una finestra del terminale Linux.

## <a name="setting-up"></a>Configurazione

### <a name="install-the-package"></a>Installare il pacchetto

Nella finestra della console installare la libreria dei certificati di Azure Key Vault per Python.

```console
pip install azure-keyvault-certificates
```

Per questa Guida di avvio rapido, sarà necessario installare anche il pacchetto azure.identity package:

```console
pip install azure.identity
```

### <a name="create-a-resource-group-and-key-vault"></a>Creare un gruppo di risorse e un insieme di credenziali delle chiavi

In questo argomento di avvio rapido si usa un'istanza creata in precedenza di Azure Key Vault. È possibile creare un insieme di credenziali delle chiavi seguendo la procedura descritta negli argomenti di avvio rapido per l'[interfaccia della riga di comando di Azure](quick-create-cli.md), [Azure PowerShell](quick-create-powershell.md) o il [portale di Azure](quick-create-portal.md). In alternativa, è possibile eseguire i comandi dell'interfaccia della riga di comando di Azure riportati di seguito.

> [!Important]
> Ogni insieme di credenziali delle chiavi deve avere un nome univoco. Negli esempi seguenti sostituire <your-unique-keyvault-name> con il nome dell'insieme di credenziali delle chiavi in uso.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

### <a name="create-a-service-principal"></a>Creare un'entità servizio

Il modo più semplice per autenticare un'applicazione .NET basata sul cloud consiste nell'usare un'identità gestita. Per informazioni dettagliate, vedere [Usare un'identità gestita del servizio app per accedere ad Azure Key Vault](../general/managed-identity.md).

Per semplicità, tuttavia, in questo avvio rapido viene creata un'applicazione desktop, per la quale sono richiesti un'entità servizio e un criterio di controllo di accesso. L'entità servizio richiede un nome univoco nel formato "http://&lt;nome-univoco-entità-servizio&gt;".

Creare un'entità servizio usando il comando [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) dell'interfaccia della riga di comando di Azure:

```azurecli
az ad sp create-for-rbac -n "http://&lt;my-unique-service-principal-name&gt;" --sdk-auth
```

Questa operazione restituisce una serie di coppie chiave-valore. 

```console
{
  "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
  "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
  "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
  "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Prendere nota dei valori clientId e clientSecret, perché verranno usati nel passaggio [Impostare le variabili di ambiente](#set-environmental-variables) di seguito.

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Concedere all'entità servizio l'accesso all'insieme di credenziali delle chiavi

Creare un criterio di accesso per l'insieme di credenziali delle chiavi che conceda l'autorizzazione all'entità servizio, passando il valore di clientId al comando [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy). Concedere all'entità servizio le autorizzazioni get, list e create per i certificati.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --certificate-permissions delete get list create 
```

#### <a name="set-environmental-variables"></a>Impostare le variabili di ambiente

Il metodo DefaultAzureCredential usato nell'applicazione si basa sulle tre variabili di ambiente `AZURE_CLIENT_ID`, `AZURE_CLIENT_SECRET` e `AZURE_TENANT_ID`. Impostare queste variabili sui valori clientId, clientSecret e tenantId annotati nel passaggio [Creare un'entità servizio](#create-a-service-principal) usando il formato `export VARNAME=VALUE`. Questo metodo imposta solo le variabili per la shell corrente e per i processi creati dalla shell. Per aggiungere in modo permanente queste variabili all'ambiente, modificare il file `/etc/environment `. 

Sarà anche necessario salvare il nome dell'insieme di credenziali delle chiavi come una variabile di ambiente denominata `KEY_VAULT_NAME`.

```console
export AZURE_CLIENT_ID=<your-clientID>

export AZURE_CLIENT_SECRET=<your-clientSecret>

export AZURE_TENANT_ID=<your-tenantId>

export KEY_VAULT_NAME=<your-key-vault-name>
````

## <a name="object-model"></a>Modello a oggetti

La libreria client di Azure Key Vault per Python consente di gestire le chiavi e le risorse correlate, come i certificati e i segreti. Gli esempi di codice riportati di seguito illustrano come creare un client e come creare, recuperare ed eliminare un certificato.

## <a name="code-examples"></a>Esempi di codice

### <a name="add-directives"></a>Aggiungere le direttive

Aggiungere le direttive seguenti all'inizio del codice:

```python
import os
from azure.keyvault.certificates import CertificateClient, CertificatePolicy,CertificateContentType, WellKnownIssuerNames 
from azure.identity import DefaultAzureCredential
```

### <a name="authenticate-and-create-a-client"></a>Autenticare e creare un client

L'autenticazione rispetto all'insieme di credenziali delle chiavi e la creazione di un client dell'insieme di credenziali delle chiavi dipendono dalle variabili di ambiente indicate nel passaggio precedente [Impostare le variabili di ambiente](#set-environmental-variables). Il nome dell'insieme di credenziali delle chiavi viene espanso nell'URI relativo, nel formato "https://<nome-insieme-credenziali-chiave-personale>.vault.azure.net".

```python
credential = DefaultAzureCredential()

client = CertificateClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-certificate"></a>Salvare un certificato

Ora che l'applicazione è autenticata, è possibile inserire un certificato autofirmato nell'insieme di credenziali delle chiavi. 

```python
certificate_operation_poller = client.begin_create_certificate(
    certificate_name=certificateName, policy=CertificatePolicy.get_default()
)
certificate = certificate_operation_poller.result()
```

È possibile verificare che il certificato sia stato impostato con il comando [az keyvault certificate show](/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-show):

```azurecli
az keyvault certificate show --vault-name <your-unique-keyvault-name> --name myCertificate
```

### <a name="retrieve-a-certificate"></a>Recuperare un certificato

È ora possibile recuperare il certificato creato in precedenza.

```python
retrieved_certificate = client.get_certificate(certificateName)
 ```

Il certificato è ora salvato come `retrieved_certificate`.

### <a name="delete-a-certificate"></a>Eliminare un certificato

A questo punto è possibile eliminare il certificato dall'insieme di credenziali delle chiavi.

```python
client.delete_certificate(certificateName)
```

È possibile verificare che il certificato sia stato eliminato con il comando [az keyvault certificate show](/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-show):

```azurecli
az keyvault certifcate show --vault-name <your-unique-keyvault-name> --name myCertificate
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
from azure.keyvault.certificates import CertificateClient, CertificatePolicy,CertificateContentType, WellKnownIssuerNames 
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = "https://" + keyVaultName + ".vault.azure.net"

credential = DefaultAzureCredential()
client = CertificateClient(vault_url=KVUri, credential=credential)

certificateName = "myCertificate"

print("Creating a certificate in " + keyVaultName + " called '" + certificateName  + "` ...")

certificate_operation_poller = client.begin_create_certificate(
    certificate_name=certificateName, policy=CertificatePolicy.get_default()

certificate = certificate_operation_poller.result()

print(" done.")

print("Retrieving your certificate from " + keyVaultName + ".")

retrieved_certificate = client.get_certificate(certificateName)

print("Certificate with name '{0}' was found'.".format(retrieved_certificate.name))
print("Deleting your certificate from " + keyVaultName + " ...")

client.delete_certificate(certificateName)

print(" done.")
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido è stato creato un insieme di credenziali delle chiavi, quindi è stato archiviato e recuperato un certificato. Per altre informazioni sul servizio Key Vault e su come integrarlo nelle applicazioni, continuare con gli articoli seguenti.

- Leggere una [panoramica di Azure Key Vault](../general/overview.md)
- Vedere la [Guida per gli sviluppatori per Azure Key Vault](../general/developers-guide.md)
- Esaminare le [procedure consigliate per Azure Key Vault](../general/best-practices.md)
