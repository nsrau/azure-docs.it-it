---
title: Crittografia disco di Azure in una rete isolataAzure Disk Encryption on an isolated network
description: Questo articolo fornisce suggerimenti per la risoluzione dei problemi relativi a Crittografia disco di Microsoft Azure per macchine virtuali Linux.This article provides troubleshooting tips for Microsoft Azure Disk Encryption for Linux VMs.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 02/27/2020
ms.custom: seodec18
ms.openlocfilehash: f2b84427b9aad2d18368d808fc618f3bfbe774ec
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460121"
---
# <a name="azure-disk-encryption-on-an-isolated-network"></a>Crittografia disco di Azure in una rete isolataAzure Disk Encryption on an isolated network

Quando la connettività è limitata da un firewall, da un requisito del proxy o dalle impostazioni di gruppi di sicurezza di rete (NSG), l'estensione potrebbe non essere più in grado di eseguire le attività necessarie. È quindi possibile che vengano visualizzati messaggi del tipo "Stato estensione non disponibile nella macchina virtuale".

## <a name="package-management"></a>Gestione dei pacchetti

Crittografia disco di Azure dipende da una serie di componenti, che vengono in genere installati come parte dell'abilitazione ADE, se non già presente. Quando si è protetti da un firewall o altrimenti isolati da Internet, questi pacchetti devono essere preinstallati o disponibili localmente.

Ecco i pacchetti necessari per ogni distribuzione. Per un elenco completo delle distribuzioni e dei tipi di volume supportati, vedere [Macchine virtuali e sistemi operativi supportati.](disk-encryption-overview.md#supported-vms-and-operating-systems)

- **Ubuntu 14.04, 16.04, 18.04**: lsscsi, psmisc, at, cryptsetup-bin, python-parted, python-six, procps
- **CentOS 7.2 - 7.7**: lsscsi, psmisc, lvm2, uuid, at, patch, cryptsetup, cryptsetup-reencrypt, pyparted, procps-ng, util-linux
- **CentOS 6.8**: lsscsi, psmisc, lvm2, uuid, at, cryptsetup-reencrypt, pyparted, python-six
- **RedHat 7.2 - 7.7**: lsscsi, psmisc, lvm2, uuid, at, patch, cryptsetup, cryptsetup-reencrypt, procps-ng, util-linux
- **RedHat 6.8**: lsscsi, psmisc, lvm2, uuid, at, patch, cryptsetup-reencrypt
- **openSUSE 42.3, SLES 12-SP4, 12-SP3**: lsscsi, cryptsetup

In Red Hat, quando è necessario un proxy, è essenziale verificare che subscription-manager e yum siano configurati correttamente. Per altre informazioni, vedere [Risoluzione dei problemi relativi a subscription-manager e yum](https://access.redhat.com/solutions/189533).  

Quando i pacchetti vengono installati manualmente, devono anche essere aggiornati manualmente man mano che vengono rilasciate nuove versioni.

## <a name="network-security-groups"></a>Gruppi di sicurezza di rete
Tutte le impostazioni dei gruppi di sicurezza di rete devono consentire all'endpoint di soddisfare i prerequisiti di configurazione di rete documentati per la crittografia dei dischi.  Vedere Crittografia del disco di [Azure: requisiti di reteSee Azure Disk Encryption: Networking requirements](disk-encryption-overview.md#networking-requirements)

## <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Crittografia disco di Azure con Azure AD (versione precedente)Azure Disk Encryption with Azure AD (previous version)

Se si usa Crittografia disco di [Azure con Azure AD (versione precedente),](disk-encryption-overview-aad.md)la libreria di Azure Active [Directory](../../active-directory/azuread-dev/active-directory-authentication-libraries.md) dovrà essere installata manualmente per tutte le distribuzioni (oltre ai pacchetti appropriati per la distribuzione, come indicato [in precedenza).](#package-management)

Quando la crittografia è abilitata con le [credenziali di Azure AD](disk-encryption-linux-aad.md), la macchina virtuale di destinazione deve consentire la connettività sia sugli endpoint di Azure Active Directory sia sugli endpoint di Key Vault. Gli endpoint di autenticazione di Azure Active Directory correnti sono mantenuti nelle sezioni 56 e 59 della documentazione [URL e intervalli di indirizzi IP per Office 365](https://docs.microsoft.com/office365/enterprise/urls-and-ip-address-ranges). Le istruzioni di Key Vault sono fornite nella documentazione [Accedere ad Azure Key Vault protetto da firewall](../../key-vault/general/access-behind-firewall.md).

### <a name="azure-instance-metadata-service"></a>Servizio metadati dell'istanza di Azure 

La macchina virtuale deve essere in grado di accedere all'endpoint del [servizio metadati dell'istanza](instance-metadata-service.md) di Azure, che usa un noto indirizzo IP non instradabile (`169.254.169.254`) a cui è possibile accedere solo dall'interno della macchina virtuale.  Le configurazioni proxy che modificano il traffico HTTP locale a questo indirizzo (ad esempio, l'aggiunta di un'intestazione X-Forwarded-For) non sono supportate.

## <a name="next-steps"></a>Passaggi successivi

- Vedere altri passaggi per la [risoluzione dei](disk-encryption-troubleshooting.md) problemi relativi alla crittografia del disco di AzureSee more steps for Azure disk encryption troubleshooting
- [Crittografia dei dati inattivi in Azure](../../security/fundamentals/encryption-atrest.md)
