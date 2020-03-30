---
title: Crittografia delle risorse sicure nell'automazione
description: L'automazione di Azure protegge gli asset protetti usando più livelli di crittografia. Per impostazione predefinita, la crittografia viene eseguita utilizzando chiavi gestite da Microsoft.By default, the encryption is done using Microsoft-managed keys. I clienti possono configurare i propri account di automazione per l'utilizzo di chiavi gestite dal cliente per la crittografia. In questo articolo vengono descritti i dettagli di entrambe le modalità di crittografia e come è possibile passare tra le due.
services: automation
ms.service: automation
ms.subservice: process-automation
author: snehithm
ms.author: snmuvva
ms.date: 01/11/2020
ms.topic: conceptual
manager: kmadnani
ms.openlocfilehash: 3c21e2fcdde9bffac91af56d49dfa0bf336e8c0c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78246242"
---
# <a name="secure-assets-in-azure-automation"></a>Proteggere gli asset nell'automazione di AzureSecure assets in Azure Automation

Gli asset sicuri in Automazione di Azure includono credenziali, certificati, connessioni e variabili crittografate. Queste risorse sono protette in Automazione di Azure usando più livelli di crittografia. In base alla chiave di primo livello utilizzata per la crittografia, sono disponibili due modelli per la crittografia:
-   Utilizzo di chiavi gestite da MicrosoftUsing Microsoft-managed keys
-   Utilizzo di chiavi gestite dal cliente

## <a name="microsoft-managed-keys"></a>Chiavi gestite da Microsoft

Per impostazione predefinita, l'account di automazione di Azure usa chiavi gestite da Microsoft.By default, your Azure Automation account uses Microsoft-managed keys.

Ogni asset sicuro viene crittografato e archiviato in Automazione di Azure usando una chiave univoca (chiave di crittografia dei dati) generata per ogni account di automazione. Queste chiavi vengono crittografate e archiviate in Automazione di Azure usando un'altra chiave univoca generata per ogni account denominato chiave di crittografia dell'account (AEK). Queste chiavi di crittografia dell'account sono crittografate e archiviate in Automazione di Azure usando le chiavi gestite da Microsoft.These account encryption keys encrypted and stored in Azure Automation using Microsoft-managed Keys. 

## <a name="customer-managed-keys-with-key-vault-preview"></a>Chiavi gestite dal cliente con Key Vault (anteprima)Customer-managed Keys with Key Vault (preview)

Puoi gestire la crittografia delle risorse sicure per il tuo account Automation con le tue chiavi. Quando si specifica una chiave gestita dal cliente a livello dell'account di automazione, tale chiave viene utilizzata per proteggere e controllare l'accesso alla chiave di crittografia dell'account per l'account di automazione. Questo a sua volta viene utilizzato per crittografare e decrittografare tutte le risorse sicure. Le chiavi gestite dal cliente offrono una maggiore flessibilità per creare, ruotare, disabilitare e revocare i controlli di accesso. È inoltre possibile controllare le chiavi di crittografia utilizzate per proteggere gli asset protetti.

Usare L'insieme di credenziali delle chiavi di Azure per archiviare le chiavi gestite dal cliente. È possibile creare chiavi personalizzate e archiviarle in un insieme di credenziali delle chiavi oppure usare le API dell'insieme di credenziali delle chiavi di Azure per generare le chiavi.  Per altre informazioni su Archiviazione delle chiavi di Azure, vedere [Che cos'è l'insieme di](../key-vault/key-vault-overview.md) credenziali delle chiavi di Azure.For more information about Azure Key Vault, see What is Azure Key Vault?

## <a name="enable-customer-managed-keys-for-an-automation-account"></a>Abilitare le chiavi gestite dal cliente per un account di automazioneEnable customer-managed keys for an Automation account

Quando si abilita la crittografia con chiavi gestite dal cliente per un account di automazione, Automazione di Azure esegue il wrapping della chiave di crittografia dell'account con la chiave gestita dal cliente nell'insieme di credenziali delle chiavi associato. L'abilitazione delle chiavi gestite dal cliente non influisce sulle prestazioni e l'account viene crittografato immediatamente con la nuova chiave, senza alcun ritardo.

Un nuovo account di automazione viene sempre crittografato utilizzando chiavi gestite da Microsoft.A new Automation account is always encrypted using Microsoft-managed keys. Non è possibile abilitare le chiavi gestite dal cliente al momento della creazione dell'account. Le chiavi gestite dal cliente vengono archiviate in Archivio chiavi di Azure e è necessario eseguire il provisioning dell'insieme di credenziali delle chiavi con criteri di accesso che concedono le autorizzazioni chiave all'identità gestita associata all'account di automazione. L'identità gestita è disponibile solo dopo la creazione dell'account di archiviazione.

Quando si modifica la chiave usata per la crittografia degli asset protetti di Automazione di Automazione di Azure, abilitando o disabilitando le chiavi gestite dal cliente, aggiornando la versione della chiave o specificando una chiave diversa, la crittografia della chiave di crittografia dell'account cambia, ma gli asset sicuri in Non è necessario crittografare nuovamente l'account di Automazione di Azure.Your Azure Automation account do not need to be re-encrypted.

Nelle tre sezioni seguenti vengono descritti i meccanismi di abilitazione delle chiavi gestite dal cliente per un account di automazione. 

> [!NOTE] 
> Per abilitare le chiavi gestite dal cliente, è necessario effettuare chiamate all'API REST di Automazione di Azure usando la versione API 2020-01-13-previewTo enable customer-managed keys, you need to make Azure Automation REST API calls using api version 2020-01-13-preview

### <a name="pre-requisites-for-using-customer-managed-keys-in-azure-automation"></a>Prerequisiti per l'uso di chiavi gestite dal cliente in Automazione di AzurePre-requisites for using Customer-managed keys in Azure Automation

Prima di abilitare le chiavi gestite dal cliente per un account di automazione, è necessario verificare che siano soddisfatti i prerequisiti seguenti:

 - La chiave con gestione del cliente viene archiviata in un insieme di credenziali delle chiavi di Azure.The customer-manged key is stored in an Azure Key Vault. 
 - Attivare entrambe le proprietà **Soft Delete** e Do **Not Purge** nell'insieme di credenziali delle chiavi. Queste funzionalità sono necessarie per consentire il ripristino delle chiavi in caso di cancellazione accidentale.
 - Solo le chiavi RSA sono supportate con la crittografia di automazione di Azure.Only RSA keys are supported with Azure Automation encryption. Per altre informazioni sulle chiavi, vedere [Informazioni su chiavi, segreti e certificati dell'insieme](../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)di credenziali delle chiavi di Azure.
- L'account di automazione e l'insieme di credenziali delle chiavi possono trovarsi in sottoscrizioni diverse, ma devono trovarsi nello stesso tenant di Azure Active Directory.The Automation account and the key vault can be in different subscriptions, but need to be in the same Azure Active Directory tenant.

### <a name="assign-an-identity-to-the-automation-account"></a>Assegnare un'identità all'account di automazione

Per usare le chiavi gestite dal cliente con un account di automazione, l'account di automazione deve eseguire l'autenticazione nell'insieme di credenziali delle chiavi in cui sono archiviate le chiavi gestite dal cliente. Azure Automation uses system assigned managed identities to authenticate the account with Azure Key Vault. Per altre informazioni sulle identità gestite, vedere [Informazioni sulle identità gestite per le risorse di Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

Configurare un'identità gestita assegnata dal sistema all'account di automazione usando la seguente chiamata all'API REST:

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

Corpo della richiesta:

```json
{ 
 "identity": 
 { 
  "type": "SystemAssigned" 
  } 
}
```

L'identità assegnata dal sistema per l'account di automazione viene restituita in una risposta simile alla seguente:

```json
{
 "name": "automation-account-name",
 "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name",
 ..
 "identity": {
    "type": "SystemAssigned",
    "principalId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
    "tenantId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb"
 },
..
}
```

### <a name="configure-the-key-vault-access-policy"></a>Configurare i criteri di accesso dell'insieme di credenziali delle chiavi

Una volta assegnata un'identità gestita all'account di automazione, configurare l'accesso all'insieme di credenziali delle chiavi che archivia le chiavi gestite dal cliente. Automazione di Azure richiede **get**, **recover** **, wrapKey**, **UnwrapKey** nelle chiavi gestite dal cliente.

Tali criteri di accesso possono essere impostati usando la chiamata all'API REST seguente:Such an access policy can be set using the following REST API call:

```http
PUT https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sample-group/providers/Microsoft.KeyVault/vaults/sample-vault/accessPolicies/add?api-version=2018-02-14
```

Corpo della richiesta:

```json
{
  "properties": {
    "accessPolicies": [
      {
        "tenantId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb",
        "objectId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
        "permissions": {
          "keys": [
            "get",
            "recover",
            "wrapKey",
            "unwrapKey"
          ],
          "secrets": [],
          "certificates": []
        }
      }
    ]
  }
}
```

> [!NOTE]
> I campi **tenantId** e **objectId** devono essere forniti con i valori di **identity.tenantId** e **identity.principalId** rispettivamente dalla risposta dell'identità gestita per l'account di automazione.

### <a name="change-the-configuration-of-automation-account-to-use-customer-managed-key"></a>Modificare la configurazione dell'account di automazione per utilizzare la chiave gestita dal cliente

Infine, è possibile cambiare l'account di automazione dalle chiavi gestite da Microsft alle chiavi gestite dal cliente, usando la seguente chiamata all'API REST:

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

Corpo della richiesta:

```json
 {
    "properties": {
      "encryption": {
        "keySource": "Microsoft.Keyvault",
        "keyvaultProperties": {
          "keyName": "sample-vault-key",
          "keyvaultUri": "https://sample-vault-key12.vault.azure.net",
          "keyVersion": "7c73556c521340209371eaf623cc099d"
        }
      }
    }
  }
```

Risposta di esempio

```json
{
  "name": "automation-account-name",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name",
  ..
  "properties": {
    ..
    "encryption": {
      "keyvaultProperties": {
         "keyName": "sample-vault-key",
          "keyvaultUri": "https://sample-vault-key12.vault.azure.net",
          "keyVersion": "7c73556c521340209371eaf623cc099d"
      },
      "keySource": "Microsoft.Keyvault"
    },
    ..
  }
}
```

## <a name="manage-customer-managed-keys-lifecycle"></a>Gestire il ciclo di vita delle chiavi gestite dal cliente

### <a name="rotate-customer-managed-keys"></a>Ruotare le chiavi gestite dal cliente

È possibile ruotare una chiave gestita dal cliente in Archiviazione delle chiavi di Azure in base ai criteri di conformità. Quando la chiave viene ruotata, è necessario aggiornare l'account di automazione per utilizzare il nuovo URI della chiave.

La rotazione della chiave non attiva la ricrittografia degli asset protetti nell'account di automazione. Non sono necessarie ulteriori azioni.

### <a name="revoke-access-to-customer-managed-keys"></a>Revoca dell'accesso alle chiavi gestite dal cliente

To revoke access to customer-managed keys, use PowerShell or the Azure CLI. Per altre informazioni, vedere Azure Key Vault PowerShell o interfaccia della riga [di comando dell'insieme di credenziali delle chiavi](https://docs.microsoft.com/cli/azure/keyvault)di Azure.For more information, see Azure Key Vault [PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault/) or Azure Key Vault CLI . La revoca dell'accesso blocca in modo efficace l'accesso a tutti gli asset protetti nell'account di automazione, poiché la chiave di crittografia non è accessibile da Automazione di Azure.Revoking access effectively blocks access to all secure assets in the Automation account, as the encryption key is inaccessible by Azure Automation.

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni sull'insieme di credenziali delle chiavi di Azure](../key-vault/key-vault-overview.md)

- [Asset di tipo certificato in Automazione di Azure](shared-resources/certificates.md)

- [Asset credenziali in Automazione di Azure](shared-resources/credentials.md)

- [Asset di tipo variabile in Automazione di Azure](shared-resources/variables.md)
