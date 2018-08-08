---
title: Crittografia del servizio di archiviazione di Azure per dati inattivi | Microsoft Docs
description: La funzionalità Crittografia del servizio di archiviazione di Azure consente di crittografare l'archivio BLOB di Azure sul lato del servizio durante l'archiviazione dei dati e di decrittografarlo durante il recupero dei dati.
services: storage
author: lakasa
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 08/01/2018
ms.author: lakasa
ms.openlocfilehash: f35697139a4be49be8a645cfd4d451ad8e3c8094
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2018
ms.locfileid: "39412356"
---
# <a name="azure-storage-service-encryption-for-data-at-rest"></a>Crittografia del servizio di archiviazione di Azure per dati inattivi
La crittografia del servizio di archiviazione di Azure per dati inattivi consente di proteggere i dati in modo da soddisfare i criteri di sicurezza e conformità dell'organizzazione. Con questa funzionalità, la piattaforma di archiviazione di Azure è in grado di crittografare automaticamente i dati prima del salvataggio permanente in Archiviazione BLOB di Azure, File di Azure o Archiviazione code di Azure e di decrittografarli prima del recupero. La gestione della crittografia, la crittografia di dati inattivi, la decrittografia e la gestione delle chiavi in Crittografia del servizio di archiviazione sono attività completamente trasparenti per gli utenti. Tutti i dati scritti nella piattaforma di archiviazione di Azure sono crittografati tramite [crittografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) a 256 bit, uno dei tipi di crittografia a blocchi più sicuri tra quelli disponibili.

Crittografia del servizio di archiviazione è abilitata per tutti gli account di archiviazione nuovi ed esistenti e non può essere disabilitata. Poiché i dati sono protetti per impostazione predefinita, non è necessario modificare il codice o le applicazioni per sfruttare i vantaggi offerti da Crittografia del servizio di archiviazione.

La funzionalità esegue automaticamente la crittografia dei dati nelle posizioni seguenti:

- Archiviazione BLOB di Azure, File di Azure, Archiviazione code di Azure, Archiviazione tabelle di Azure.  
- Entrambi i livelli di prestazioni (Standard e Premium).
- Entrambi i modelli di distribuzione (Azure Resource Manager e classico).

> [!Note]  
> La crittografia del servizio di archiviazione non è disponibile per [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md). È consigliabile usare un tipo di crittografia a livello di sistema operativo, ad esempio [Crittografia dischi di Azure](../../security/azure-security-disk-encryption-overview.md), che usa gli standard di settore [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) in Windows e [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) in Linux per offrire crittografia integrata con Key Vault.

La crittografia del servizio di archiviazione non influisce sulle prestazioni dei servizi di archiviazione di Azure.

È possibile usare con Crittografia del servizio di archiviazione le chiavi di crittografia gestite da Microsoft oppure le proprie chiavi di crittografia. Per altre informazioni sull'uso delle proprie chiavi, vedere [Crittografia del servizio di archiviazione con chiavi gestite dall'utente in Azure Key Vault](storage-service-encryption-customer-managed-keys.md).

## <a name="view-encryption-settings-in-the-azure-portal"></a>Visualizzare le impostazioni di crittografie nel portale di Azure
Per visualizzare le impostazioni di Crittografia del servizio di archiviazione, eseguire l'accesso al [portale di Azure](https://portal.azure.com) e selezionare un account di archiviazione. Nel riquadro **IMPOSTAZIONI** selezionare l'impostazione **Crittografia**.

![Screenshot del portale che visualizza l'impostazione Crittografia](./media/storage-service-encryption/image1.png)

## <a name="faq-for-storage-service-encryption"></a>Domande frequenti su Crittografia del servizio di archiviazione
**In che modo è possibile crittografare i dati in un account di archiviazione di Resource Manager?**  
La crittografia del servizio di archiviazione è abilitata per tutti gli account di archiviazione, sia quelli classici sia quelli di Resource Manager. Tutti i file esistenti nell'account di archiviazione creati prima dell'abilitazione della crittografia vengono crittografati retroattivamente tramite un processo di crittografia in background.

**La crittografia del servizio di archiviazione è abilitata per impostazione predefinita quando si crea un account di archiviazione?**  
Sì, la crittografia del servizio di archiviazione è abilitata per tutti gli account di archiviazione e per tutti i servizi di archiviazione di Azure.

**Con un account di archiviazione di Resource Manager è possibile abilitare Crittografia del servizio di archiviazione?**  
La crittografia del servizio di archiviazione è abilitata per impostazione predefinita per tutti gli account di archiviazione di Resource Manager esistenti. Ciò vale per Archiviazione BLOB di Azure, File di Azure, Archiviazione code di Azure, Archiviazione tabelle di Azure. 

**È possibile disabilitare la crittografia per l'account di archiviazione?**  
La crittografia è abilitata per impostazione predefinita e non ne è prevista la disabilitazione per l'account di archiviazione. 

**Qual è il costo aggiuntivo di Archiviazione di Azure se la crittografia del servizio di archiviazione è abilitata?**  
Non sono previsti costi aggiuntivi.

**È possibile usare le proprie chiavi di crittografia?**  
Sì, è possibile usare le proprie chiavi di crittografia. Per altre informazioni, vedere [Crittografia del servizio di archiviazione di Azure con chiavi gestite dal cliente in Azure Key Vault](storage-service-encryption-customer-managed-keys.md).

**È possibile revocare l'accesso alle chiavi di crittografia?**  
Sì, se si [usano le proprie chiavi di crittografia](storage-service-encryption-customer-managed-keys.md) in Azure Key Vault.

**La crittografia del servizio di archiviazione è disponibile per Azure Managed Disks?**  
No, la crittografia del servizio di archiviazione non è disponibile per [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md). È consigliabile usare un tipo di crittografia a livello di sistema operativo, ad esempio [Crittografia dischi di Azure](../../security/azure-security-disk-encryption-overview.md), che usa gli standard di settore [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) in Windows e [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) in Linux per offrire crittografia integrata con Key Vault.

**Quali sono le differenze tra la crittografia del servizio di archiviazione e Crittografia dischi di Azure?**  
Crittografia dischi di Azure offre l'integrazione tra soluzioni basate sul sistema operativo, ad esempio BitLocker e DM-Crypt, e Azure Key Vault. La crittografia del servizio di archiviazione offre la crittografia in modo nativo a livello piattaforma di archiviazione di Azure, al di sotto della macchina virtuale.

**Con un account di archiviazione classico è possibile abilitare Crittografia del servizio di archiviazione?**  
La crittografia del servizio di archiviazione è abilitata per tutti gli account di archiviazione, sia quelli classici sia quelli di Resource Manager.

**Come si esegue la crittografia dei dati nell'account di archiviazione classico?**  
Con la crittografia abilitata per impostazione predefinita, tutti i dati archiviati nei servizi di archiviazione di Azure vengono crittografati automaticamente. 

**È possibile creare account di archiviazione con la crittografia del servizio di archiviazione abilitata usando Azure PowerShell e l'interfaccia della riga di comando di Azure?**  
La crittografia del servizio di archiviazione è abilitata per impostazione predefinita al momento della creazione di un account di archiviazione (classico o di Resource Manager). È possibile verificare le proprietà dell'account con Azure PowerShell e tramite l'interfaccia della riga di comando di Azure.

**L'account di archiviazione è configurato per la replica con ridondanza geografica. Con Crittografia del servizio di archiviazione verrà crittografata anche la copia ridondante?**  
Sì, vengono crittografate tutte le copie dell'account di archiviazione. Sono supportate tutte le opzioni di ridondanza: archiviazione con ridondanza locale, archiviazione con ridondanza della zona, archiviazione con ridondanza geografica e archiviazione con ridondanza geografica e accesso in lettura.

**La crittografia del servizio di archiviazione è consentita solo in aree specifiche?**  
La crittografia del servizio di archiviazione è disponibile in tutte le aree.

**La crittografia del servizio di archiviazione è conforme a FIPS 140-2?**  
Sì, la crittografia del servizio di archiviazione è conforme a FIPS 140-2.

**In che modo è possibile contattare il personale addetto in caso di problemi o se si vogliono inviare commenti?**  
Per qualsiasi problema o per inviare commenti relativi alla crittografia del servizio di archiviazione, contattare [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com).

## <a name="next-steps"></a>Passaggi successivi
Archiviazione di Azure offre un set completo di funzionalità di sicurezza che consentono agli sviluppatori di creare applicazioni sicure. Per altre informazioni, vedere la [Guida alla sicurezza di Archiviazione di Azure](../storage-security-guide.md).