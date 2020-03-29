---
title: File di inclusione
description: File di inclusione
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: egeaney
ms.custom: include
ms.openlocfilehash: e5e1755e6351d308c041de5cefe943e9874c8ebd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372194"
---
### <a name="enable-customer-managed-keys"></a>Abilitare le chiavi gestite dal clienteEnable customer-managed keys

È necessario usare l'insieme di credenziali delle chiavi di Azure per archiviare le chiavi gestite dal cliente. È possibile creare chiavi personalizzate e archiviarle in un insieme di credenziali delle chiavi oppure usare le API dell'insieme di credenziali delle chiavi di Azure per generare le chiavi. La risorsa Servizi cognitivi e l'insieme di credenziali delle chiavi devono trovarsi nella stessa area e nello stesso tenant di Azure Active Directory (Azure AD), ma possono trovarsi in sottoscrizioni diverse. Per altre informazioni su Archiviazione delle chiavi di Azure, vedere [Che cos'è l'insieme di](https://docs.microsoft.com/azure/key-vault/key-vault-overview)credenziali delle chiavi di Azure?.

Una nuova risorsa servizi cognitivi viene sempre crittografata utilizzando chiavi gestite da Microsoft.A new Cognitive Services resource is always encrypted using Microsoft-managed keys. Non è possibile abilitare le chiavi gestite dal cliente al momento della creazione della risorsa. Le chiavi gestite dal cliente vengono archiviate nell'insieme di credenziali delle chiavi di Azure e è necessario eseguire il provisioning dell'insieme di credenziali delle chiavi con criteri di accesso che concedono le autorizzazioni chiave all'identità gestita associata alla risorsa Servizi cognitivi. L'identità gestita è disponibile solo dopo la creazione della risorsa tramite il piano tariffario per CMK.

Per informazioni su come usare le chiavi gestite dal cliente con l'insieme di credenziali delle chiavi di Azure per la crittografia dei servizi cognitivi, vedere:To learn how to use customer-managed keys with Azure Key Vault for Cognitive Services encryption, see:

- [Configurare le chiavi gestite dal cliente con Key Vault per la crittografia di Cognitive Services dal portale di AzureConfigure customer-managed keys with Key Vault for Cognitive Services encryption from the Azure portal](..\articles\cognitive-services\Encryption\cognitive-services-encryption-keys-portal.md)

L'abilitazione delle chiavi gestite dai clienti consentirà anche un'identità gestita assegnata dal sistema, una funzionalità di Azure AD. Una volta abilitata l'identità gestita assegnata dal sistema, questa risorsa verrà registrata con Azure Active Directory.Once the system assigned managed identity is enabled, this resource will be registered with Azure Active Directory. Dopo la registrazione, all'identità gestita verrà concesso l'accesso all'insieme di credenziali delle chiavi selezionato durante l'impostazione della chiave gestita dal cliente. Ulteriori informazioni sulle [identità gestite](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

> [!IMPORTANT]
> Se si disabilitano le identità gestite assegnate al sistema, l'accesso all'insieme di credenziali delle chiavi verrà rimosso e tutti i dati crittografati con le chiavi del cliente non saranno più accessibili. Tutte le funzioni dipendenti da questi dati smetteranno di funzionare.

> [!IMPORTANT]
> Le identità gestite attualmente non supportano gli scenari tra directory. Quando si configurano chiavi gestite dal cliente nel portale di Azure, un'identità gestita viene assegnata automaticamente sotto le coperte. Se successivamente si sposta la sottoscrizione, il gruppo di risorse o la risorsa da una directory di Azure AD a un'altra, l'identità gestita associata alla risorsa non viene trasferita al nuovo tenant, pertanto le chiavi gestite dal cliente potrebbero non funzionare più. Per altre informazioni, vedere Trasferimento di una sottoscrizione tra directory di Azure AD nelle domande frequenti e problemi noti con le identità gestite per le risorse di Azure.For more information, see **Transferring** a subscription between Azure AD directories in [FAQs and known issues with managed identities for Azure resources.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories)  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Archiviare le chiavi gestite dal cliente nell'insieme di credenziali delle chiavi di AzureStore customer-managed keys in Azure Key Vault

Per abilitare le chiavi gestite dal cliente, è necessario usare un insieme di credenziali delle chiavi di Azure per archiviare le chiavi. È necessario attivare entrambe le proprietà **Soft Delete** e **Do Not Purge** nell'insieme di credenziali delle chiavi.

Solo le chiavi RSA di dimensioni 2048 sono supportate con la crittografia di Servizi cognitivi. Per altre informazioni sulle chiavi, vedere **Chiavi dell'insieme di credenziali delle chiavi** in Informazioni su chiavi, segreti e certificati dell'insieme di credenziali delle chiavi di [Azure.](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)

### <a name="rotate-customer-managed-keys"></a>Ruotare le chiavi gestite dal cliente

È possibile ruotare una chiave gestita dal cliente in Archiviazione delle chiavi di Azure in base ai criteri di conformità. Quando la chiave viene ruotata, è necessario aggiornare la risorsa Servizi cognitivi per usare il nuovo URI della chiave. Per informazioni su come aggiornare la risorsa per usare una nuova versione della chiave nel portale di Azure, vedere la sezione relativa **all'aggiornamento della versione della chiave** in [Configurare le chiavi gestite dal cliente per Servizi cognitivi tramite il portale](..\articles\cognitive-services\Encryption\cognitive-services-encryption-keys-portal.md)di Azure.

La rotazione della chiave non attiva la ricrittografia dei dati nella risorsa. Non è richiesta alcuna ulteriore azione da parte dell'utente.

### <a name="revoke-access-to-customer-managed-keys"></a>Revoca dell'accesso alle chiavi gestite dal cliente

To revoke access to customer-managed keys, use PowerShell or Azure CLI. Per altre informazioni, vedere Azure Key Vault PowerShell o interfaccia della riga [di comando dell'insieme di credenziali delle chiavi](https://docs.microsoft.com/cli/azure/keyvault)di Azure.For more information, see Azure Key Vault [PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault//) or Azure Key Vault CLI . La revoca dell'accesso blocca in modo efficace l'accesso a tutti i dati nella risorsa Servizi cognitivi, poiché la chiave di crittografia non è accessibile da Servizi cognitivi.


