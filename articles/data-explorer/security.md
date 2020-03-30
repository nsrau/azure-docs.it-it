---
title: Proteggere i cluster di Azure Data Explorer in AzureSecure Azure Data Explorer clusters in Azure
description: Informazioni su come proteggere i cluster in Azure Data Explorer.Learn about how to secure clusters in Azure Data Explorer.
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
# <a name="secure-azure-data-explorer-clusters-in-azure"></a>Proteggere i cluster di Azure Data Explorer in AzureSecure Azure Data Explorer clusters in Azure

Questo articolo offre un'introduzione alla sicurezza in Azure Data Explorer per proteggere i dati e le risorse nel cloud e soddisfare le esigenze di sicurezza dell'azienda. È importante proteggere i cluster. La protezione dei cluster include una o più funzionalità di Azure che includono l'accesso e l'archiviazione sicure. In questo articolo vengono fornite informazioni che consentono di proteggere il cluster.

## <a name="managed-identities-for-azure-resources"></a>Identità gestite per le risorse di Azure

Una sfida comune quando si creano applicazioni cloud è la gestione delle credenziali nel codice per l'autenticazione ai servizi cloud. Proteggere le credenziali è un'attività importante. Le credenziali non devono essere archiviate nelle workstation di sviluppo o archiviate nel controllo del codice sorgente. Azure Key Vault consente di archiviare in modo sicuro le credenziali, i segreti e altre chiavi, ma è necessario autenticare il codice in Key Vault per recuperarle.

La funzionalità delle identità gestite di Azure Active Directory (Azure AD) per le risorse di Azure risolve questo problema. La funzionalità offre ai servizi di Azure un'identità gestita automaticamente in Azure AD. È possibile usare l'identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD, incluso Key Vault, senza inserire le credenziali nel codice. Per altre informazioni su questo servizio, vedere la pagina di panoramica delle identità gestite per le risorse di Azure.For more information about this service, see [managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) overview page.

## <a name="data-encryption"></a>Crittografia dei dati

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

[Crittografia disco](/azure/security/azure-security-disk-encryption-overview) di Azure consente di proteggere e proteggere i dati per soddisfare gli impegni di conformità e sicurezza dell'organizzazione. Fornisce la crittografia del volume per il sistema operativo e i dischi dati delle macchine virtuali del cluster. Azure Disk Encryption si integra anche con [Azure Key Vault](/azure/key-vault/), che consente di controllare e gestire le chiavi e i segreti di crittografia del disco e di garantire che tutti i dati nei dischi delle macchine virtuali siano crittografati. 

### <a name="customer-managed-keys-with-azure-key-vault"></a>Chiavi gestite dal cliente con l'insieme di credenziali delle chiavi di AzureCustomer-managed keys with Azure Key Vault

Per impostazione predefinita, i dati vengono crittografati con chiavi gestite da Microsoft.By default, data is encrypted with Microsoft-managed keys. Per un ulteriore controllo sulle chiavi di crittografia, è possibile fornire chiavi gestite dal cliente da utilizzare per la crittografia dei dati. È possibile gestire la crittografia dei dati a livello di archiviazione con le proprie chiavi. Una chiave gestita dal cliente viene utilizzata per proteggere e controllare l'accesso alla chiave di crittografia radice, che viene utilizzata per crittografare e decrittografare tutti i dati. Le chiavi gestite dal cliente offrono una maggiore flessibilità per creare, ruotare, disabilitare e revocare i controlli di accesso. È inoltre possibile controllare le chiavi di crittografia utilizzate per proteggere i dati.

Usare L'insieme di credenziali delle chiavi di Azure per archiviare le chiavi gestite dal cliente. È possibile creare chiavi personalizzate e archiviarle in un insieme di credenziali delle chiavi oppure usare un'API dell'insieme di credenziali delle chiavi di Azure per generare le chiavi. Il cluster Azure Data Explorer e l'insieme di credenziali delle chiavi di Azure devono trovarsi nella stessa area, ma possono trovarsi in sottoscrizioni diverse. Per altre informazioni su Archiviazione delle chiavi di Azure, vedere [Che cos'è l'insieme di](/azure/key-vault/key-vault-overview)credenziali delle chiavi di Azure?. Per una spiegazione dettagliata sulle chiavi gestite dal cliente, vedere [Chiavi gestite dal cliente con L'insieme](/azure/storage/common/storage-service-encryption)di credenziali delle chiavi di Azure . Configurare le chiavi gestite dal cliente nel cluster di Azure Data Explorer usando [C'è](/azure/data-explorer/customer-managed-keys-csharp) o il modello di [Azure Resource Manager](/azure/data-explorer/customer-managed-keys-resource-manager)

> [!Note]
> Le chiavi gestite dal cliente si basano sulle identità gestite per le risorse di Azure, una funzionalità di Azure Active Directory (Azure AD). Per configurare le chiavi gestite dal cliente nel portale di Azure, è necessario configurare un'identità gestita **SystemAssigned** nel cluster come descritto in Informazioni dettagliate in [Configurare le identità gestite per il cluster Azure Data Explorer.](/azure/data-explorer/managed-identities)

#### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Archiviare le chiavi gestite dal cliente nell'insieme di credenziali delle chiavi di AzureStore customer-managed keys in Azure Key Vault

Per abilitare le chiavi gestite dal cliente in un cluster, usare un insieme di credenziali delle chiavi di Azure per archiviare le chiavi. È necessario attivare entrambe le proprietà **Soft Delete** e **Do Not Purge** nell'insieme di credenziali delle chiavi. L'insieme di credenziali delle chiavi deve trovarsi nella stessa sottoscrizione del cluster. Azure Data Explorer usa le identità gestite per le risorse di Azure per l'autenticazione nell'insieme di credenziali delle chiavi per le operazioni di crittografia e decrittografia. Le identità gestite non supportano scenari tra directory.

#### <a name="rotate-customer-managed-keys"></a>Ruotare le chiavi gestite dal cliente

È possibile ruotare una chiave gestita dal cliente in Archiviazione delle chiavi di Azure in base ai criteri di conformità. Quando la chiave viene ruotata, è necessario aggiornare il cluster per utilizzare il nuovo URI della chiave. La rotazione della chiave non attiva la ricrittografia dei dati nel cluster. 

#### <a name="revoke-access-to-customer-managed-keys"></a>Revoca dell'accesso alle chiavi gestite dal cliente

To revoke access to customer-managed keys, use PowerShell or Azure CLI. Per altre informazioni, vedere Azure Key Vault PowerShell o interfaccia della riga [di comando dell'insieme di credenziali delle chiavi](/cli/azure/keyvault)di Azure.For more information, see Azure Key Vault [PowerShell](/powershell/module/az.keyvault/) or Azure Key Vault CLI . La revoca dell'accesso blocca l'accesso a tutti i dati nel livello di archiviazione del cluster, poiché la chiave di crittografia è di conseguenza inaccessibile da Azure Data Explorer.Revoking access blocks access to all data in the cluster's storage level, since the encryption key is sequently inaccessible by Azure Data Explorer.

> [!Note]
> Quando Azure Data Explorer identifica che l'accesso a una chiave gestita dal cliente viene revocato, sospenderà automaticamente il cluster per eliminare tutti i dati memorizzati nella cache. Una volta restituito l'accesso alla chiave, il cluster deve essere ripreso manualmente.

## <a name="role-based-access-control"></a>Controllo degli accessi in base al ruolo

Utilizzando il [controllo degli accessi in base al ruolo,](/azure/role-based-access-control/overview)è possibile separare i compiti all'interno del team e concedere solo l'accesso necessario agli utenti del cluster. Invece di concedere a tutti le autorizzazioni senza restrizioni nel cluster, è possibile consentire solo determinate azioni. È possibile configurare il controllo di [accesso per i database](/azure/data-explorer/manage-database-permissions) nel portale di [Azure](/azure/role-based-access-control/role-assignments-portal)usando l'interfaccia della riga di comando di [Azure](/azure/role-based-access-control/role-assignments-cli)o [Azure PowerShell.](/azure/role-based-access-control/role-assignments-powershell)

## <a name="next-steps"></a>Passaggi successivi

* [Proteggere il cluster in Azure Data Explorer - Portale](manage-cluster-security.md) abilitando la crittografia inattivi.
* [Configurare le identità gestite per il cluster di Azure Data ExplorerConfigure managed identities for your Azure Data Explorer cluster](managed-identities.md)
* [Configurare le chiavi gestite dal cliente usando il modello di Azure Resource ManagerConfigure customer-managed-keys using the Azure Resource Manager template](customer-managed-keys-resource-manager.md)
* [Configurare le chiavi gestite dal cliente usando CConfigure customer-managed-keys using C #](customer-managed-keys-csharp.md)

