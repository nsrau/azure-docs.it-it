---
title: Crittografia del servizio di archiviazione di Azure per dati inattivi | Microsoft Docs
description: La funzionalità Crittografia del servizio di archiviazione di Azure consente di crittografare l'archivio BLOB di Azure sul lato del servizio durante l'archiviazione dei dati e di decrittografarlo durante il recupero dei dati.
services: storage
author: lakasa
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 03/14/2018
ms.author: lakasa
ms.openlocfilehash: d9df2218acc218a796e502fa4e3b94573af86ca8
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="azure-storage-service-encryption-for-data-at-rest"></a>Crittografia del servizio di archiviazione di Azure per dati inattivi

Crittografia del servizio di archiviazione di Azure per dati inattivi consente di proteggere i dati, in modo da soddisfare i criteri di sicurezza e conformità dell'organizzazione. Questa funzionalità consente ad Archiviazione di Azure di crittografare automaticamente i dati prima del salvataggio permanente nel servizio e di decrittografarli prima del recupero. La gestione della crittografia, la crittografia di dati inattivi, la decrittografia e la gestione delle chiavi in Crittografia del servizio di archiviazione sono attività completamente trasparenti per gli utenti. Tutti i dati scritti in Archiviazione di Azure sono crittografati attraverso la [crittografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) a 256 bit, una delle crittografie a blocchi più sicure tra quelle disponibili.

Crittografia del servizio di archiviazione è abilitata per tutti gli account di archiviazione nuovi ed esistenti e non può essere disabilitata. Poiché i dati sono protetti per impostazione predefinita, non è necessario modificare il codice o le applicazioni per sfruttare i vantaggi offerti da Crittografia del servizio di archiviazione.

La funzionalità esegue automaticamente la crittografia dei dati nelle posizioni seguenti:

- Entrambi i livelli di prestazioni (Standard e Premium).
- Entrambi i modelli di distribuzione (Azure Resource Manager e classico).
- Tutti i servizi di Archiviazione di Azure (Archiviazione BLOB, archiviazione code, archiviazione tabelle e File di Azure). 

Crittografia del servizio di archiviazione non influisce sulle prestazioni di Archiviazione di Azure.

È possibile usare con Crittografia del servizio di archiviazione le chiavi di crittografia gestite da Microsoft oppure le proprie chiavi di crittografia. Per altre informazioni sull'uso delle proprie chiavi, vedere [Crittografia del servizio di archiviazione con chiavi gestite dall'utente in Azure Key Vault](storage-service-encryption-customer-managed-keys.md).

## <a name="view-encryption-settings-in-the-azure-portal"></a>Visualizzare le impostazioni di crittografie nel portale di Azure

Per visualizzare le impostazioni di Crittografia del servizio di archiviazione, eseguire l'accesso al [portale di Azure](https://portal.azure.com) e selezionare un account di archiviazione. Nel riquadro **IMPOSTAZIONI** selezionare l'impostazione **Crittografia**.

![Screenshot del portale che visualizza l'impostazione Crittografia](./media/storage-service-encryption/image1.png)

## <a name="faq-for-storage-service-encryption"></a>Domande frequenti su Crittografia del servizio di archiviazione

**D: Con un account di archiviazione classico è possibile abilitare Crittografia del servizio di archiviazione?**

R: La funzionalità Crittografia del servizio di archiviazione è abilitata per impostazione predefinita per tutti gli account di archiviazione (classici e di Resource Manager).

**D: Come si crittografano i dati nell'account di archiviazione classico?**

R: Con la crittografia abilitata per impostazione predefinita, Archiviazione di Azure esegue automaticamente la crittografia dei nuovi dati. 

**D: Con un account di archiviazione di Resource Manager è possibile abilitare Crittografia del servizio di archiviazione?**

R: La funzionalità Crittografia del servizio di archiviazione è abilitata per impostazione predefinita per tutti gli account di archiviazione di Resource Manager esistenti. La funzionalità è supportata per Archiviazione BLOB, archiviazione code, archiviazione tabelle e File di Azure. 

**D: In che modo vengono crittografati i dati in un account di archiviazione di Resource Manager?**

R: La funzionalità Crittografia del servizio di archiviazione è abilitata per impostazione predefinita per tutti gli account di archiviazione, sia quelli classici sia quelli di Resource Manager. Tuttavia, i dati esistenti non vengono crittografati. Per crittografare i dati esistenti, è possibile copiarli con un altro nome o in un altro contenitore e quindi rimuoverne le versioni non crittografate. 

**D: È possibile creare account di archiviazione con la funzionalità Crittografia del servizio di archiviazione abilitata usando Azure PowerShell e l'interfaccia della riga di comando di Azure?**

R: La funzionalità Crittografia del servizio di archiviazione viene abilitata per impostazione predefinita al momento della creazione di un account di archiviazione (classico o di Resource Manager). È possibile verificare le proprietà dell'account con Azure PowerShell e tramite l'interfaccia della riga di comando di Azure.

**Q: Qual è il costo aggiuntivo di Archiviazione di Azure se la funzionalità Crittografia del servizio di archiviazione è abilitata?**

R: Non sono previsti costi aggiuntivi.

**D: È possibile usare le proprie chiavi di crittografia?**

R: Sì, è possibile usare le proprie chiavi di crittografia. Per altre informazioni, vedere [Crittografia del servizio di archiviazione di Azure con chiavi gestite dal cliente in Azure Key Vault](storage-service-encryption-customer-managed-keys.md).

**D: È possibile revocare l'accesso alle chiavi di crittografia?**

R: Sì, se si [usano le proprie chiavi di crittografia](storage-service-encryption-customer-managed-keys.md) in Azure Key Vault.

**D: Crittografia del servizio di archiviazione è abilitata per impostazione predefinita quando si crea un account di archiviazione?**

R: Sì, la funzionalità Crittografia del servizio di archiviazione, che usa chiavi gestite da Microsoft, è abilitata per impostazione predefinita per tutti gli account di archiviazione, sia quelli classici sia quelli di Azure Resource Manager. È abilitata anche per tutti i servizi (Archiviazione BLOB, archiviazione tabelle, archiviazione code e File di Azure).

**D: Quali sono le differenze rispetto alla Crittografia dischi di Azure?**

R: Crittografia dischi di Azure viene usata per crittografare i dischi del sistema operativo e i dischi dati nelle macchine virtuali IaaS. Per altre informazioni, vedere la [Guida alla sicurezza di Archiviazione di Azure](../storage-security-guide.md).

**D: Che cosa accade se si abilita Crittografia dischi di Azure su dischi dati personali?**

R: Non si verifica alcun problema. Entrambi i metodi eseguono la crittografia dei dati.

**D: L'account di archiviazione è configurato per la replica con ridondanza geografica. Con Crittografia del servizio di archiviazione verrà crittografata anche la copia ridondante?**

R: Sì, vengono crittografate tutte le copie dell'account di archiviazione. Sono supportate tutte le opzioni di ridondanza: archiviazione con ridondanza locale, archiviazione con ridondanza della zona, archiviazione con ridondanza geografica e archiviazione con ridondanza geografica e accesso in lettura.

**D: È possibile disabilitare la crittografia sull'account di archiviazione?**

R: La crittografia è abilitata per impostazione predefinita e non ne è prevista la disabilitazione per l'account di archiviazione. 

**D: Crittografia del servizio di archiviazione è consentita solo in aree specifiche?**

R: La funzionalità Crittografia del servizio di archiviazione è disponibile in tutte le aree per tutti i servizi. 

**D: In che modo è possibile contattare il personale addetto in caso di problemi o se si vogliono inviare commenti?**

D: Per qualsiasi problema o per inviare commenti relativi a Crittografia del servizio di archiviazione, contattare [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com).

## <a name="next-steps"></a>Passaggi successivi
Archiviazione di Azure offre un set completo di funzionalità di sicurezza che consentono agli sviluppatori di creare applicazioni sicure. Per altre informazioni, vedere la [Guida alla sicurezza di Archiviazione di Azure](../storage-security-guide.md).
