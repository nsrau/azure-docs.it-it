---
title: includere il file
description: includere file
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c301ed2b612c2f3a7aca40ed5ed733800323adcc
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2020
ms.locfileid: "86231049"
---
È possibile scegliere di gestire la crittografia a livello di ogni disco gestito, con le proprie chiavi. La crittografia lato server per i dischi gestiti con chiavi gestite dal cliente offre un'esperienza integrata con Azure Key Vault. È possibile importare [le proprie chiavi RSA](../articles/key-vault/keys/hsm-protected-keys.md) nel proprio insieme di credenziali delle chiavi o generare nuove chiavi RSA in Azure Key Vault. 

I dischi gestiti di Azure gestiscono la crittografia e la decrittografia in modo completamente trasparente usando la [crittografia envelope](../articles/storage/common/storage-client-side-encryption.md#encryption-and-decryption-via-the-envelope-technique). I dati vengono crittografati usando una chiave di crittografia dei dati (DEK) basata su [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256 che è a sua volta protetta tramite le chiavi del cliente. Il servizio di archiviazione genera chiavi di crittografia dei dati e le crittografa con le chiavi gestite dal cliente usando la crittografia RSA. La crittografia envelope consente di ruotare (cambiare) periodicamente le chiavi in base ai criteri di conformità senza influire sulle macchine virtuali. Quando si ruotano le chiavi, il servizio di archiviazione esegue nuovamente la crittografia delle chiavi di crittografia dei dati con le nuove chiavi gestite dal cliente. 

#### <a name="full-control-of-your-keys"></a>Controllo completo delle chiavi

È necessario concedere l'accesso a Managed disks nel Key Vault per usare le chiavi per la crittografia e la decrittografia della chiave di crittografia. Ciò consente di avere il controllo completo di dati e chiavi. È possibile disabilitare le chiavi o revocare l'accesso ai dischi gestiti in qualsiasi momento. È anche possibile controllare l'uso della chiave di crittografia con il monitoraggio di Azure Key Vault per garantire che solo i dischi gestiti o altri servizi di Azure attendibili accedano alle chiavi.

Quando si disabilita o si elimina la chiave, tutte le VM con dischi che usano tale chiave verranno arrestate automaticamente. In seguito, le macchine virtuali non potranno essere usate, a meno che la chiave non sia abilitata di nuovo o si assegni una nuova chiave.

Per le unità SSD Premium, SSD Standard e HDD Standard: quando si disabilita o si elimina la chiave, tutte le macchine virtuali con dischi che usano quella chiave verranno arrestate automaticamente. In seguito, le macchine virtuali non potranno essere usate, a meno che la chiave non sia abilitata di nuovo o si assegni una nuova chiave.    

Per dischi Ultra: quando si disabilita o si elimina una chiave, le macchine virtuali con dischi Ultra con la chiave non vengono arrestate automaticamente. Dopo aver deallocato e riavviato le macchine virtuali, i dischi smetteranno di usare la chiave e quindi le macchine virtuali non torneranno online. Per riportare online le macchine virtuali, è necessario assegnare una nuova chiave o abilitare quella esistente.    

Il diagramma seguente illustra il modo in cui i dischi gestiti usano Azure Active Directory e Azure Key Vault per effettuare richieste usando la chiave gestita dal cliente:

:::image type="content" source="media/virtual-machines-managed-disks-description-customer-managed-keys/customer-managed-keys-sse-managed-disks-workflow.png" alt-text="Flusso di lavoro del disco gestito e delle chiavi gestite dal cliente. Un amministratore crea una Azure Key Vault, quindi crea un set di crittografia del disco e imposta il set di crittografia del disco. Il set è associato a una VM, che consente al disco di usare Azure AD per l'autenticazione":::

L'elenco seguente illustra il diagramma in maggiore dettaglio:

1. Un amministratore di Azure Key Vault crea le risorse dell'insieme di credenziali delle chiavi.
1. L'amministratore dell'insieme di credenziali delle chiavi importa le proprie chiavi RSA o genera nuove chiavi RSA nell'insieme di credenziali delle chiavi.
1. L'amministratore crea un'istanza della risorsa del set di crittografia dischi, specificando un ID di Azure Key Vault e un URL della chiave. Il set di crittografia dischi è una nuova risorsa introdotta per semplificare la gestione delle chiavi per i dischi gestiti. 
1. Quando si crea un set di crittografia dischi, in Azure Active Directory (AD) viene creata un'[identità gestita assegnata dal sistema](../articles/active-directory/managed-identities-azure-resources/overview.md) che viene associata al set di crittografia dischi. 
1. L'amministratore di Azure Key Vault concede quindi all'identità gestita l'autorizzazione per eseguire operazioni nell'insieme di credenziali delle chiavi.
1. Un utente della macchina virtuale crea dischi associandoli al set di crittografia dischi. L'utente della macchina virtuale può anche abilitare la crittografia lato server con chiavi gestite dal cliente per le risorse esistenti associandole al set di crittografia dischi. 
1. I dischi gestiti usano l'identità gestita per inviare richieste ad Azure Key Vault.
1. Per la lettura o la scrittura di dati, i dischi gestiti inviano richieste ad Azure Key Vault per crittografare (eseguire il wrapping) e decrittografare (annullare il wrapping) la chiave di crittografia dei dati al fine di eseguire la crittografia e la decrittografia dei dati. 

Per revocare l'accesso alle chiavi gestite dal cliente, vedere [PowerShell per Azure Key Vault](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) e [Interfaccia della riga di comando per Azure Key Vault](https://docs.microsoft.com/cli/azure/keyvault). La revoca dell'accesso blocca di fatto l'accesso a tutti i dati dell'account di archiviazione poiché il servizio Archiviazione di Azure non può accedere alla chiave di crittografia.
