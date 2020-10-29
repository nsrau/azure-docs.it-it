---
title: Crittografia dischi di Azure in una rete isolata
description: In questo articolo vengono fornite informazioni sui suggerimenti per la risoluzione dei problemi per Microsoft Azure crittografia del disco nelle macchine virtuali Linux.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 02/27/2020
ms.custom: seodec18
ms.openlocfilehash: 1d7e019e7759e22e945bddee477a4cb77f17350b
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913824"
---
# <a name="azure-disk-encryption-on-an-isolated-network"></a>Crittografia dischi di Azure in una rete isolata

Quando la connettività è limitata da un firewall, da un requisito del proxy o dalle impostazioni di gruppi di sicurezza di rete (NSG), l'estensione potrebbe non essere più in grado di eseguire le attività necessarie. È quindi possibile che vengano visualizzati messaggi del tipo "Stato estensione non disponibile nella macchina virtuale".

## <a name="package-management"></a>Gestione dei pacchetti

Crittografia dischi di Azure dipende da diversi componenti, che in genere vengono installati come parte dell'abilitazione ADE se non sono già presenti. Quando si è protetti da un firewall o altrimenti isolati da Internet, questi pacchetti devono essere pre-installati o disponibili localmente.

Ecco i pacchetti necessari per ogni distribuzione. Per un elenco completo delle distribuzioni e dei tipi di volume supportati, vedere [macchine virtuali e sistemi operativi supportati](disk-encryption-overview.md#supported-vms-and-operating-systems).

- **Ubuntu 14,04, 16,04, 18,04** : lsscsi, psmisc, at, cryptsetup-bin, Python-PARTD, Python-Six, procps, GRUB-PC-bin
- **CentOS 7,2-7,7** : lsscsi, psmisc, lvm2, uuid, at, patch, cryptsetup, cryptsetup-ReEncrypt, pyparted, procps-ng, util-linux
- **CentOS 6,8** : lsscsi, psmisc, lvm2, uuid, at, cryptsetup-ReEncrypt, pyparted, Python-Six
- **RedHat 7,2-7,7** : lsscsi, psmisc, lvm2, uuid, at, patch, cryptsetup, cryptsetup-ReEncrypt, procps-ng, util-linux
- **RedHat 6,8** : lsscsi, psmisc, lvm2, uuid, at, patch, cryptsetup-ReEncrypt
- **openSUSE 42,3, SLES 12-SP4, 12-SP3** : lsscsi, cryptsetup

In Red Hat, quando è necessario un proxy, è essenziale verificare che subscription-manager e yum siano configurati correttamente. Per altre informazioni, vedere [Risoluzione dei problemi relativi a subscription-manager e yum](https://access.redhat.com/solutions/189533).  

Quando i pacchetti vengono installati manualmente, è necessario aggiornarli manualmente anche quando vengono rilasciate nuove versioni.

## <a name="network-security-groups"></a>Gruppi di sicurezza di rete
Tutte le impostazioni dei gruppi di sicurezza di rete devono consentire all'endpoint di soddisfare i prerequisiti di configurazione di rete documentati per la crittografia dei dischi.  Vedere [crittografia dischi di Azure: requisiti di rete](disk-encryption-overview.md#networking-requirements)

## <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Crittografia dischi di Azure con Azure AD (versione precedente)

Se si usa [crittografia dischi di Azure con Azure ad (versione precedente)](disk-encryption-overview-aad.md), è necessario installare la [libreria Azure Active Directory](../../active-directory/azuread-dev/active-directory-authentication-libraries.md) manualmente per tutte le distribuzioni (oltre ai pacchetti appropriati per la distribuzione, come [indicato in precedenza](#package-management)).

Quando la crittografia è abilitata con le [credenziali di Azure AD](disk-encryption-linux-aad.md), la macchina virtuale di destinazione deve consentire la connettività sia sugli endpoint di Azure Active Directory sia sugli endpoint di Key Vault. Gli endpoint di autenticazione Azure Active Directory correnti sono conservati nelle sezioni 56 e 59 della documentazione relativa agli [url Microsoft 365 e agli intervalli di indirizzi IP](/microsoft-365/enterprise/urls-and-ip-address-ranges) . Le istruzioni di Key Vault sono fornite nella documentazione [Accedere ad Azure Key Vault protetto da firewall](../../key-vault/general/access-behind-firewall.md).

### <a name="azure-instance-metadata-service"></a>Servizio metadati dell'istanza di Azure 

La macchina virtuale deve essere in grado di accedere all'endpoint del [servizio metadati dell'istanza di Azure](instance-metadata-service.md) , che usa un indirizzo IP non instradabile noto ( `169.254.169.254` ) a cui è possibile accedere solo dall'interno della macchina virtuale.  Non sono supportate le configurazioni proxy che modificano il traffico HTTP locale a questo indirizzo, ad esempio aggiungendo un'intestazione X-Forwarded-For.

## <a name="next-steps"></a>Passaggi successivi

- Vedere altri passaggi per la [risoluzione dei problemi di crittografia dischi di Azure](disk-encryption-troubleshooting.md)
- [Crittografia dei dati inattivi in Azure](../../security/fundamentals/encryption-atrest.md)
