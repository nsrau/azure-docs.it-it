---
title: Proteggere i cluster di Azure Esplora dati in Azure
description: Informazioni su come proteggere i cluster in Azure Esplora dati.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 786950011f10e25d6bcb72061212c1878e79d45a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77373362"
---
# <a name="secure-azure-data-explorer-clusters-in-azure"></a>Proteggere i cluster di Azure Esplora dati in Azure

Questo articolo fornisce un'introduzione alla sicurezza in Azure Esplora dati per aiutare a proteggere i dati e le risorse nel cloud e soddisfare le esigenze di sicurezza dell'azienda. È importante proteggere i cluster. La protezione dei cluster include una o più funzionalità di Azure che includono l'accesso sicuro e l'archiviazione. Questo articolo fornisce informazioni che consentono di proteggere il cluster.

## <a name="managed-identities-for-azure-resources"></a>Identità gestite per le risorse di Azure

Un problema comune durante la creazione di applicazioni cloud è la gestione delle credenziali nel codice per l'autenticazione nei servizi cloud. Proteggere le credenziali è un'attività importante. Le credenziali non devono essere archiviate nelle workstation degli sviluppatori o archiviate nel controllo del codice sorgente. Azure Key Vault consente di archiviare in modo sicuro le credenziali, i segreti e altre chiavi, ma è necessario autenticare il codice in Key Vault per recuperarle.

La funzionalità identità gestite di Azure Active Directory (Azure AD) per le risorse di Azure risolve questo problema. La funzionalità offre ai servizi di Azure un'identità gestita automaticamente in Azure AD. È possibile usare l'identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD, incluso Key Vault, senza inserire le credenziali nel codice. Per altre informazioni su questo servizio, vedere la pagina Panoramica [delle identità gestite per le risorse di Azure](/azure/active-directory/managed-identities-azure-resources/overview) .

## <a name="data-encryption"></a>Crittografia dei dati

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

[Crittografia dischi di Azure](/azure/security/azure-security-disk-encryption-overview) consente di proteggere e salvaguardare i dati per soddisfare gli impegni di sicurezza e conformità dell'organizzazione. Fornisce la crittografia del volume per il sistema operativo e i dischi dati delle macchine virtuali del cluster. Crittografia dischi di Azure si integra anche con [Azure Key Vault](/azure/key-vault/), che consente di controllare e gestire le chiavi e i segreti di crittografia del disco e di assicurarsi che tutti i dati nei dischi delle macchine virtuali siano crittografati. 

### <a name="customer-managed-keys-with-azure-key-vault"></a>Chiavi gestite dal cliente con Azure Key Vault

Per impostazione predefinita, i dati vengono crittografati con le chiavi gestite da Microsoft. Per un maggiore controllo sulle chiavi di crittografia, è possibile fornire chiavi gestite dal cliente da usare per la crittografia dei dati. È possibile gestire la crittografia dei dati a livello di archiviazione con le proprie chiavi. Una chiave gestita dal cliente viene usata per proteggere e controllare l'accesso alla chiave di crittografia radice, usata per crittografare e decrittografare tutti i dati. Le chiavi gestite dal cliente offrono maggiore flessibilità per creare, ruotare, disabilitare e revocare i controlli di accesso. È anche possibile controllare le chiavi di crittografia usate per proteggere i dati.

Usare Azure Key Vault per archiviare le chiavi gestite dal cliente. È possibile creare chiavi personalizzate e archiviarle in un insieme di credenziali delle chiavi oppure è possibile usare un'API Azure Key Vault per generare chiavi. Il cluster Azure Esplora dati e il Azure Key Vault devono trovarsi nella stessa area, ma possono trovarsi in sottoscrizioni diverse. Per ulteriori informazioni su Azure Key Vault, vedere [che cos'è Azure Key Vault?](/azure/key-vault/key-vault-overview). Per una spiegazione dettagliata delle chiavi gestite dal cliente, vedere [chiavi gestite dal cliente con Azure Key Vault](/azure/storage/common/storage-service-encryption). Configurare chiavi gestite dal cliente nel cluster di Esplora dati Azure con [C#](/azure/data-explorer/customer-managed-keys-csharp) o il [modello di Azure Resource Manager](/azure/data-explorer/customer-managed-keys-resource-manager)

> [!Note]
> Le chiavi gestite dal cliente si basano sulle identità gestite per le risorse di Azure, una funzionalità di Azure Active Directory (Azure AD). Per configurare le chiavi gestite dal cliente nel portale di Azure, è necessario configurare un'identità gestita di **SystemAssigned** nel cluster, come descritto in [configurare le identità gestite per il cluster Azure Esplora dati](/azure/data-explorer/managed-identities).

#### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Archiviare chiavi gestite dal cliente in Azure Key Vault

Per abilitare le chiavi gestite dal cliente in un cluster, usare un Azure Key Vault per archiviare le chiavi. È necessario abilitare l' **eliminazione** temporanea e non **ripulire** le proprietà nell'insieme di credenziali delle chiavi. L'insieme di credenziali delle chiavi deve trovarsi nella stessa sottoscrizione del cluster. Azure Esplora dati usa le identità gestite per le risorse di Azure per l'autenticazione nell'insieme di credenziali delle chiavi per le operazioni di crittografia e decrittografia. Le identità gestite non supportano scenari tra directory diverse.

#### <a name="rotate-customer-managed-keys"></a>Ruotare le chiavi gestite dal cliente

È possibile ruotare una chiave gestita dal cliente in Azure Key Vault in base ai criteri di conformità. Quando la chiave viene ruotata, è necessario aggiornare il cluster per usare il nuovo URI della chiave. La rotazione della chiave non attiva la nuova crittografia dei dati nel cluster. 

#### <a name="revoke-access-to-customer-managed-keys"></a>Revocare l'accesso alle chiavi gestite dal cliente

Per revocare l'accesso alle chiavi gestite dal cliente, usare PowerShell o l'interfaccia della riga di comando di Azure. Per altre informazioni, vedere [Azure Key Vault PowerShell](/powershell/module/az.keyvault/) o l'interfaccia della riga di comando di [Azure Key Vault](/cli/azure/keyvault). La revoca dell'accesso blocca l'accesso a tutti i dati nel livello di archiviazione del cluster, poiché la chiave di crittografia non è di conseguenza accessibile da parte di Azure Esplora dati.

> [!Note]
> Quando Azure Esplora dati identifica che l'accesso a una chiave gestita dal cliente viene revocato, il cluster verrà automaticamente sospeso per eliminare i dati memorizzati nella cache. Una volta che l'accesso alla chiave viene restituito, il cluster deve essere ripreso manualmente.

## <a name="role-based-access-control"></a>Controllo di accesso in base ai ruoli

Usando il [controllo degli accessi in base al ruolo](/azure/role-based-access-control/overview), è possibile separare i compiti all'interno del team e concedere solo l'accesso necessario agli utenti del cluster. Anziché concedere a tutti le autorizzazioni senza restrizioni per il cluster, è possibile consentire solo determinate azioni. È possibile configurare il [controllo di accesso per i database](/azure/data-explorer/manage-database-permissions) nel [portale di Azure](/azure/role-based-access-control/role-assignments-portal)usando l' [interfaccia](/azure/role-based-access-control/role-assignments-cli)della riga di comando di Azure o [Azure PowerShell](/azure/role-based-access-control/role-assignments-powershell).

## <a name="next-steps"></a>Passaggi successivi

* [Proteggere il cluster in Azure Esplora dati-Portal](manage-cluster-security.md) abilitando la crittografia dei computer inattivi.
* [Configurare le identità gestite per il cluster di Azure Esplora dati](managed-identities.md)
* [Configurare chiavi gestite dal cliente usando il modello di Azure Resource Manager](customer-managed-keys-resource-manager.md)
* [Configurare chiavi gestite dal cliente con C #](customer-managed-keys-csharp.md)

