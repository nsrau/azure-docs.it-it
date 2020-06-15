---
title: Crittografia di asset protetti in Automazione di Azure
description: Questo articolo illustra i concetti per la configurazione dell'account di Automazione per l'uso della crittografia.
services: automation
ms.service: automation
ms.subservice: process-automation
author: snehithm
ms.author: snmuvva
ms.date: 01/11/2020
ms.topic: conceptual
manager: kmadnani
ms.openlocfilehash: 1cb70109657343f41a1b3a19f3426377d97e261e
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83830124"
---
# <a name="encryption-of-secure-assets-in-azure-automation"></a>Crittografia di asset protetti in Automazione di Azure

Gli asset sicuri in Automazione di Azure includono credenziali, certificati, connessioni e variabili crittografate. Questi asset sono protetti in Automazione di Azure con più livelli di crittografia. A seconda della chiave di primo livello usata per la crittografia, sono disponibili due modelli crittografici:

- Uso di chiavi gestite da Microsoft
- Uso di chiavi gestite dall'utente

## <a name="microsoft-managed-keys"></a>Chiavi gestite da Microsoft

Per impostazione predefinita, l'account di Automazione di Azure usa chiavi gestite da Microsoft.

Ogni asset sicuro viene crittografato e archiviato in Automazione di Azure tramite una chiave univoca (chiave di crittografia dei dati) generata per ogni account di Automazione. Queste chiavi vengono a loro volta crittografate e archiviate in Automazione di Azure usando un'altra chiave univoca generata per ogni account e denominata chiave di crittografia dell'account (AEK). Queste chiavi di crittografia dell'account vengono crittografate e archiviate in Automazione di Azure tramite chiavi gestite da Microsoft. 

## <a name="keys-that-you-manage-with-key-vault-preview"></a>Chiavi gestite dall'utente con Key Vault (anteprima)

È possibile gestire la crittografia degli asset sicuri per l'account di Automazione con le proprie chiavi. Quando si specifica una chiave gestita dal cliente al livello dell'account di Automazione, questa chiave viene usata per proteggere e controllare l'accesso alla chiave di crittografia dell'account per l'account di Automazione. Questa chiave viene a sua volta usata per crittografare e decrittografare tutti gli asset sicuri. Le chiavi gestite dal cliente offrono maggiore flessibilità per creare, ruotare, disabilitare e revocare i controlli di accesso. È anche possibile controllare le chiavi di crittografia usate per proteggere gli asset sicuri.

Usare Azure Key Vault per archiviare le chiavi gestite dal cliente. È possibile creare chiavi personalizzate e archiviarle in un insieme di credenziali delle chiavi oppure usare le API Azure Key Vault per generare chiavi.  Per altre informazioni sull'insieme di credenziali di Azure, vedere [Cos'è l'insieme di credenziali delle chiavi di Azure?](../key-vault/general/overview.md)

## <a name="use-of-customer-managed-keys-for-an-automation-account"></a>Uso delle chiavi gestite dal cliente per un account di Automazione

Quando si usa la crittografia con le chiavi gestite dal cliente per un account di Automazione, Automazione di Azure esegue il wrapping della chiave di crittografia dell'account con la chiave gestita dal cliente nell'insieme di credenziali delle chiavi associato. L'abilitazione delle chiavi gestite dal cliente non influisce sulle prestazioni e l'account viene crittografato con la nuova chiave immediatamente, senza alcun ritardo.

Un nuovo account di Automazione viene sempre crittografato con le chiavi gestite da Microsoft. Non è possibile abilitare le chiavi gestite dal cliente al momento della creazione dell'account. Le chiavi gestite dal cliente vengono archiviate in Azure Key Vault e l'insieme di credenziali delle chiavi deve essere sottoposto a provisioning con criteri di accesso che concedono le autorizzazioni all'identità gestita associata all'account di Automazione. L'identità gestita è disponibile solo dopo la creazione dell'account di archiviazione.

Quando si modifica la chiave usata per la crittografia degli asset sicuri di Automazione di Azure, abilitando o disabilitando le chiavi gestite dal cliente, aggiornando la versione della chiave o specificando una chiave diversa, la crittografia della chiave di crittografia dell'account viene modificata, ma non è necessario crittografare di nuovo le risorse sicure nell'account di Automazione di Azure.

> [!NOTE] 
> Per abilitare le chiavi gestite dal cliente è necessario eseguire chiamate API REST di Automazione di Azure usando l'API versione 2020-01-13-Preview

### <a name="prerequisites-for-using-customer-managed-keys-in-azure-automation"></a>Prerequisiti per l'uso delle chiavi gestite dal cliente in Automazione di Azure

Prima di abilitare le chiavi gestite dal cliente per un account di Automazione, è necessario verificare che siano soddisfatti i seguenti prerequisiti:

 - La chiave gestita dal cliente è archiviata in un'istanza di Azure Key Vault. 
 - Abilitare le proprietà **Eliminazione temporanea** e **Protezione dall'eliminazione** nell'insieme di credenziali delle chiavi. Queste funzionalità sono necessarie per consentire il ripristino delle chiavi in caso di eliminazione accidentale.
 - Con la crittografia di Automazione di Azure sono supportate solo le chiavi RSA. Per altre informazioni sulle chiavi, vedere [Informazioni su chiavi, segreti e certificati di Azure Key Vault](../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).
- L'account di Automazione e l'insieme di credenziali delle chiavi possono trovarsi in sottoscrizioni diverse, ma devono essere nello stesso tenant Azure Active Directory.

### <a name="assignment-of-an-identity-to-the-automation-account"></a>Assegnazione di un'identità all'account di Automazione

Per usare chiavi gestite dal cliente con un account di Automazione, l'account di Automazione deve eseguire l'autenticazione nell'insieme di credenziali delle chiavi che archivia le chiavi gestite dal cliente. Automazione di Azure usa identità gestite assegnate dal sistema per autenticare l'account con Azure Key Vault. Per altre informazioni sulle identità gestite, vedere [Informazioni sulle identità gestite per le risorse di Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

Configurare un'identità gestita assegnata dal sistema per l'account di Automazione usando la chiamata API REST seguente:

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

L'identità assegnata dal sistema per l'account di Automazione viene restituita in una risposta simile alla seguente:

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

### <a name="configuration-of-the-key-vault-access-policy"></a>Configurazione dei criteri di accesso di Key Vault

Dopo che un'identità gestita è stata assegnata all'account di Automazione, configurare l'accesso all'insieme di credenziali delle chiavi che archivia le chiavi gestite dal cliente. Automazione di Azure richiede le autorizzazioni **get**, **recover**, **wrapKey**, **UnwrapKey** per le chiavi gestite dal cliente.

È possibile impostare un criterio di accesso di questo tipo usando la chiamata API REST seguente:

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
> Per i campi **tenantId** e **objectId** è necessario specificare rispettivamente i valori di **identity.tenantId** e **identity.principalId** dalla risposta dell'identità gestita per l'account di Automazione.

### <a name="change-the-configuration-of-automation-account-to-use-customer-managed-key"></a>Modificare la configurazione dell'account di Automazione per usare la chiave gestita dal cliente

È possibile infine cambiare l'account di Automazione da chiavi gestite da Microsoft a chiavi gestite dal cliente, usando la chiamata API REST seguente:

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

## <a name="rotation-of-a-customer-managed-key"></a>Rotazione di una chiave gestita dal cliente

È possibile ruotare una chiave gestita dal cliente in Azure Key Vault in base ai criteri di conformità. Quando la chiave viene ruotata, è necessario aggiornare l'account di Automazione per usare il nuovo URI della chiave.

La rotazione della chiave non attiva la nuova crittografia degli asset sicuri nell'account di Automazione. Non sono necessarie altre azioni.

## <a name="revocation-of-access-to-a-customer-managed-key"></a>Revoca dell'accesso a una chiave gestita dal cliente

Per revocare l'accesso alle chiavi gestite dal cliente, usare PowerShell o l'interfaccia della riga di comando di Azure. Per altre informazioni, vedere [PowerShell per Azure Key Vault](https://docs.microsoft.com/powershell/module/az.keyvault/) o [Interfaccia della riga di comando per Azure Key Vault](https://docs.microsoft.com/cli/azure/keyvault). La revoca dell'accesso blocca di fatto l'accesso a tutti gli asset sicuri dell'account di Automazione perché la chiave di crittografia non può accedere ad Automazione di Azure.

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica di Azure Key Vault, vedere [Informazioni su Azure Key Vault](../key-vault/general/overview.md).
- Per usare i certificati, vedere [Gestire i certificati in Automazione di Azure](shared-resources/certificates.md).
- Per gestire le credenziali, vedere [Gestire le credenziali in Automazione di Azure](shared-resources/credentials.md).
- Per usare le variabili di Automazione, vedere [Gestire variabili in Automazione di Azure](shared-resources/variables.md).
- Per informazioni sull'uso delle connessioni, vedere [Gestire le connessioni in Automazione di Azure](automation-connections.md).
