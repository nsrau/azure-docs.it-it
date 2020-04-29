---
title: includere il file
description: File di inclusione
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: egeaney
ms.custom: include
ms.openlocfilehash: e5e1755e6351d308c041de5cefe943e9874c8ebd
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "79372194"
---
### <a name="enable-customer-managed-keys"></a>Abilita chiavi gestite dal cliente

È necessario utilizzare Azure Key Vault per archiviare le chiavi gestite dal cliente. È possibile creare chiavi personalizzate e archiviarle in un insieme di credenziali delle chiavi oppure usare le API Azure Key Vault per generare chiavi. La risorsa Servizi cognitivi e l'insieme di credenziali delle chiavi devono trovarsi nella stessa area e nello stesso tenant di Azure Active Directory (Azure AD), ma possono trovarsi in sottoscrizioni diverse. Per ulteriori informazioni su Azure Key Vault, vedere [che cos'è Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview).

Una nuova risorsa di servizi cognitivi è sempre crittografata usando chiavi gestite da Microsoft. Non è possibile abilitare le chiavi gestite dal cliente nel momento in cui viene creata la risorsa. Le chiavi gestite dal cliente vengono archiviate in Azure Key Vault e l'insieme di credenziali delle chiavi deve essere sottoposto a provisioning con criteri di accesso che concedono le autorizzazioni chiave all'identità gestita associata alla risorsa Servizi cognitivi. L'identità gestita è disponibile solo dopo la creazione della risorsa usando il piano tariffario per CMK.

Per informazioni su come usare le chiavi gestite dal cliente con Azure Key Vault per la crittografia dei servizi cognitivi, vedere:

- [Configurare chiavi gestite dal cliente con Key Vault per la crittografia dei servizi cognitivi dall'portale di Azure](..\articles\cognitive-services\Encryption\cognitive-services-encryption-keys-portal.md)

L'abilitazione delle chiavi gestite dal cliente consentirà anche un'identità gestita assegnata dal sistema, una funzionalità di Azure AD. Una volta abilitata l'identità gestita assegnata dal sistema, questa risorsa verrà registrata con Azure Active Directory. Dopo la registrazione, all'identità gestita verrà concesso l'accesso al Key Vault selezionato durante l'installazione della chiave gestita dal cliente. È possibile ottenere altre informazioni sulle [identità gestite](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

> [!IMPORTANT]
> Se si disabilitano le identità gestite assegnate dal sistema, l'accesso all'insieme di credenziali delle chiavi verrà rimosso e i dati crittografati con le chiavi del cliente non saranno più accessibili. Tutte le funzionalità che dipendono da questi dati smetteranno di funzionare.

> [!IMPORTANT]
> Le identità gestite attualmente non supportano gli scenari tra directory. Quando si configurano le chiavi gestite dal cliente nel portale di Azure, un'identità gestita viene assegnata automaticamente dietro le quinte. Se successivamente si sposta la sottoscrizione, il gruppo di risorse o la risorsa da una directory Azure AD a un'altra, l'identità gestita associata alla risorsa non viene trasferita al nuovo tenant, quindi le chiavi gestite dal cliente potrebbero non funzionare più. Per altre informazioni, vedere **trasferimento di una sottoscrizione tra Azure ad directory** nelle [domande frequenti e problemi noti relativi alle identità gestite per le risorse di Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories).  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Archiviare chiavi gestite dal cliente in Azure Key Vault

Per abilitare le chiavi gestite dal cliente, è necessario usare un Azure Key Vault per archiviare le chiavi. È necessario abilitare l' **eliminazione** temporanea e non **ripulire** le proprietà nell'insieme di credenziali delle chiavi.

Con la crittografia dei servizi cognitivi sono supportate solo le chiavi RSA di dimensioni 2048. Per ulteriori informazioni sulle chiavi, vedere **Key Vault chiavi** in [informazioni su Azure Key Vault chiavi, segreti e certificati](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys).

### <a name="rotate-customer-managed-keys"></a>Ruotare le chiavi gestite dal cliente

È possibile ruotare una chiave gestita dal cliente in Azure Key Vault in base ai criteri di conformità. Quando la chiave viene ruotata, è necessario aggiornare la risorsa Servizi cognitivi per usare il nuovo URI della chiave. Per informazioni su come aggiornare la risorsa per usare una nuova versione della chiave nella portale di Azure, vedere la sezione intitolata **aggiornare la versione della chiave** in [configurare chiavi gestite dal cliente per servizi cognitivi usando il portale di Azure](..\articles\cognitive-services\Encryption\cognitive-services-encryption-keys-portal.md).

La rotazione della chiave non attiva la nuova crittografia dei dati nella risorsa. Non sono necessarie altre azioni da parte dell'utente.

### <a name="revoke-access-to-customer-managed-keys"></a>Revocare l'accesso alle chiavi gestite dal cliente

Per revocare l'accesso alle chiavi gestite dal cliente, usare PowerShell o l'interfaccia della riga di comando di Azure. Per altre informazioni, vedere [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault//) o l'interfaccia della riga di comando di [Azure Key Vault](https://docs.microsoft.com/cli/azure/keyvault). La revoca dell'accesso blocca in modo efficace l'accesso a tutti i dati nella risorsa Servizi cognitivi, in quanto la chiave di crittografia non è accessibile dai servizi cognitivi.


