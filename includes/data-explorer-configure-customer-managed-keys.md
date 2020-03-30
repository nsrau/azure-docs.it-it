---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 01/07/2020
ms.author: orspodek
ms.openlocfilehash: 7f5c02c6c009e8916ed063454e0ae6049892e95c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297885"
---
Azure Data Explorer crittografa tutti i dati in un account di archiviazione inattivi. Per impostazione predefinita, i dati vengono crittografati con chiavi gestite da Microsoft.By default, data is encrypted with Microsoft-managed keys. Per un ulteriore controllo sulle chiavi di crittografia, è possibile fornire chiavi gestite dal cliente da utilizzare per la crittografia dei dati. 

Le chiavi gestite dal cliente devono essere archiviate in un insieme di credenziali [delle chiavi](/azure/key-vault/key-vault-overview)di Azure . È possibile creare chiavi personalizzate e archiviarle in un insieme di credenziali delle chiavi oppure usare un'API dell'insieme di credenziali delle chiavi di Azure per generare le chiavi. Il cluster di Azure Data Explorer e l'insieme di credenziali delle chiavi devono trovarsi nella stessa area, ma possono trovarsi in sottoscrizioni diverse. Per una spiegazione dettagliata sulle chiavi gestite dal cliente, vedere [Chiavi gestite dal cliente con L'insieme](/azure/storage/common/storage-service-encryption)di credenziali delle chiavi di Azure . 

In questo articolo viene illustrato come configurare le chiavi gestite dal cliente.

## <a name="configure-azure-key-vault"></a>Configurare Azure Key Vault

Per configurare le chiavi gestite dal cliente con Azure Data Explorer, è necessario [impostare due proprietà nell'insieme di credenziali](/azure/key-vault/key-vault-ovw-soft-delete)delle chiavi: **Eliminazione temporanea** e **Non eliminazione**. Queste proprietà non sono abilitate per impostazione predefinita. Per abilitare queste proprietà, eseguire **Abilitazione dell'eliminazione temporanea** e **Abilitazione della protezione** dell'eliminazione in [PowerShell](/azure/key-vault/key-vault-soft-delete-powershell) o nell'interfaccia della riga [di comando](/azure/key-vault/key-vault-soft-delete-cli) di Azure in un insieme di credenziali delle chiavi nuovo o esistente. Sono supportate solo le chiavi RSA di dimensione 2048. Per ulteriori informazioni sulle chiavi, vedere [Chiavi dell'insieme di credenziali delle chiavi](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys).

> [!NOTE]
> La crittografia dei dati tramite chiavi gestite dai clienti non è supportata nei [cluster leader e follower.](/azure/data-explorer/follower) 

## <a name="assign-an-identity-to-the-cluster"></a>Assegnare un'identità al clusterAssign an identity to the cluster

Per abilitare le chiavi gestite dal cliente per il cluster, assegnare innanzitutto un'identità gestita assegnata dal sistema al cluster. Questa identità gestita verrà usata per concedere al cluster le autorizzazioni per accedere all'insieme di credenziali delle chiavi. Per configurare le identità gestite assegnate dal sistema, vedere [le identità gestite](/azure/data-explorer/managed-identities).