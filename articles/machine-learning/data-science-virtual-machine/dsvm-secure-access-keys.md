---
title: Archiviare le credenziali di accesso in modo sicuro nella macchina virtuale di data science - Azure | Microsoft Docs
description: Informazioni su come archiviare le credenziali di accesso in modo sicuro nella Data Science Virtual Machine. Si apprenderà come usare le identità del servizio gestito e Azure Key Vault per archiviare le credenziali di accesso.
keywords: apprendimento avanzato, AI, strumenti di data science, macchina virtuale per data science, analisi geospaziale, processo di data science del team
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: vijetaj
ms.openlocfilehash: 1374cbef41f40ea270f3c4d84c68d08e7db095bc
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051610"
---
# <a name="store-access-credentials-securely-on-a-data-science-virtual-machine"></a>Archiviare in modo sicuro le credenziali di accesso in un Data Science Virtual Machine

Il codice nelle applicazioni cloud contiene spesso le credenziali per l'autenticazione nei servizi cloud. La gestione e la protezione di queste credenziali è una sfida nota nella creazione di applicazioni cloud. Idealmente, le credenziali non dovrebbero mai essere visualizzate nelle workstation degli sviluppatori o essere archiviate nel controllo del codice sorgente.

La funzionalità [identità gestite per le risorse di Azure](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview) semplifica la risoluzione di questo problema offrendo ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory (Azure ad). È possibile usare questa identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD senza dover inserire le credenziali nel codice.

Un modo per proteggere le credenziali consiste nell'usare Windows Installer (MSI) in combinazione con [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/), un servizio gestito di Azure per archiviare in modo sicuro i segreti e le chiavi crittografiche. È possibile accedere a un insieme di credenziali delle chiavi usando l'identità gestita e quindi recuperare le chiavi di crittografia e i segreti autorizzati dall'insieme di credenziali delle chiavi.

La documentazione sulle identità gestite per le risorse di Azure e Key Vault è costituita da una risorsa completa per informazioni approfondite su questi servizi. La parte restante di questo articolo illustra l'uso di base dell'identità del servizio gestita e di Key Vault nella macchina virtuale di data science per accedere alle risorse di Azure. 

## <a name="create-a-managed-identity-on-the-dsvm"></a>Creare un'identità gestita nella macchina virtuale di data science 


```
# Prerequisite: You have already created a Data Science VM in the usual way.

# Create an identity principal for the VM.
az vm assign-identity -g <Resource Group Name> -n <Name of the VM>
# Get the principal ID of the DSVM.
az resource list -n <Name of the VM> --query [*].identity.principalId --out tsv
```


## <a name="assign-key-vault-access-permissions-to-a-vm-principal"></a>Assegnare autorizzazioni di accesso Key Vault a un'entità VM
```
# Prerequisite: You have already created an empty Key Vault resource on Azure by using the Azure portal or Azure CLI.

# Assign only get and set permissions but not the capability to list the keys.
az keyvault set-policy --object-id <Principal ID of the DSVM from previous step> --name <Key Vault Name> -g <Resource Group of Key Vault>  --secret-permissions get set
```

## <a name="access-a-secret-in-the-key-vault-from-the-dsvm"></a>Accedere a un segreto in Key Vault dalla macchina virtuale di data science

```
# Get the access token for the VM.
x=`curl http://localhost:50342/oauth2/token --data "resource=https://vault.azure.net" -H Metadata:true`
token=`echo $x | python -c "import sys, json; print(json.load(sys.stdin)['access_token'])"`

# Access the key vault by using the access token.
curl https://<Vault Name>.vault.azure.net/secrets/SQLPasswd?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

## <a name="access-storage-keys-from-the-dsvm"></a>Accedere alle chiavi di archiviazione dalla macchina virtuale di data science

```
# Prerequisite: You have granted your VMs MSI access to use storage account access keys based on instructions at https://docs.microsoft.com/azure/active-directory/managed-service-identity/tutorial-linux-vm-access-storage. This article describes the process in more detail.

y=`curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true`
ytoken=`echo $y | python -c "import sys, json; print(json.load(sys.stdin)['access_token'])"`
curl https://management.azure.com/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup of Storage account>/providers/Microsoft.Storage/storageAccounts/<Storage Account Name>/listKeys?api-version=2016-12-01 --request POST -d "" -H "Authorization: Bearer $ytoken"

# Now you can access the data in the storage account from the retrieved storage account keys.
```
## <a name="access-the-key-vault-from-python"></a>Accedere a Key Vault da Python

```python
from azure.keyvault import KeyVaultClient
from msrestazure.azure_active_directory import MSIAuthentication

"""MSI Authentication example."""

# Get credentials.
credentials = MSIAuthentication(
    resource='https://vault.azure.net'
)

# Create a Key Vault client.
key_vault_client = KeyVaultClient(
    credentials
)

key_vault_uri = "https://<key Vault Name>.vault.azure.net/"

secret = key_vault_client.get_secret(
    key_vault_uri,  # Your key vault URL.
    # The name of your secret that already exists in the key vault.
    "SQLPasswd",
    ""              # The version of the secret; empty string for latest.
)
print("My secret value is {}".format(secret.value))
```

## <a name="access-the-key-vault-from-azure-cli"></a>Accedere a Key Vault dall'interfaccia della riga di comando di Azure

```
# With managed identities for Azure resources set up on the DSVM, users on the DSVM can use Azure CLI to perform the authorized functions. The following commands enable access to the key vault from Azure CLI without requiring login to an Azure account.
# Prerequisites: MSI is already set up on the DSVM as indicated earlier. Specific permissions, like accessing storage account keys, reading specific secrets, and writing new secrets, are provided to the MSI.

# Authenticate to Azure CLI without requiring an Azure account. 
az login --msi

# Retrieve a secret from the key vault. 
az keyvault secret show --vault-name <Vault Name> --name SQLPasswd

# Create a new secret in the key vault.
az keyvault secret set --name MySecret --vault-name <Vault Name> --value "Helloworld"

# List access keys for the storage account.
az storage account keys list -g <Storage Account Resource Group> -n <Storage Account Name>
```
