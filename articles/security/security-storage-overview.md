---
title: "Funzionalità di sicurezza per Archiviazione di Azure | Documentazione Microsoft"
description: " Questo articolo offre informazioni generali sulle funzionalità di sicurezza principali di Azure che possono essere usate con Archiviazione di Azure. "
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2017
ms.author: terrylan
ms.openlocfilehash: da28cbf5f6f91df1f89114a63bc3f2ebac0f6d73
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2017
---
# <a name="azure-storage-security-overview"></a>Panoramica sulla sicurezza di Archiviazione di Azure
Archiviazione di Azure è la soluzione di archiviazione cloud per le applicazioni moderne basate su durata, disponibilità e scalabilità per soddisfare le esigenze dei clienti. Archiviazione di Azure offre un set completo di funzionalità di sicurezza:

* L'account di archiviazione può essere protetto con il controllo degli accessi in base al ruolo e Azure Active Directory.
* È possibile proteggere i dati in transito tra un'applicazione e Azure usando la crittografia lato client, HTTPS o SMB 3.0.
* I dati possono essere impostati per la crittografia automatica quando vengono scritti in Archiviazione di Azure con Crittografia del servizio di archiviazione di Azure.
* I dischi di dati e del sistema operativo usati dalle macchine virtuali possono essere impostati per la crittografia con Crittografia dischi di Azure.
* È possibile concedere l'accesso delegato agli oggetti dati in Archiviazione di Azure usando le firme di accesso condiviso.
* Il metodo di autenticazione usato da un utente quando accede alla risorsa di archiviazione può essere monitorato con Analisi archiviazione.

Per un'analisi più approfondita della sicurezza in Archiviazione di Azure, vedere la [Guida alla sicurezza di Archiviazione di Azure](../storage/common/storage-security-guide.md). Questa guida offre approfondimenti sulle funzionalità di sicurezza di Archiviazione di Azure, ad esempio chiavi dell'account di archiviazione, crittografia dei dati in transito e inattivi e Analisi archiviazione.

Questo articolo offre informazioni generali sulle funzionalità di sicurezza di Azure che possono essere usate con Archiviazione di Azure. Per altre informazioni sono disponibili collegamenti ad articoli di approfondimento su ogni funzionalità.

Qui di seguito sono elencati gli argomenti trattati in questo articolo:

* Controllo degli accessi in base al ruolo
* Accesso delegato agli oggetti di archiviazione
* Crittografia in transito
* Crittografia di dati inattivi/Crittografia del servizio di archiviazione
* Azure Disk Encryption
* Insieme di credenziali chiave Azure

## <a name="role-based-access-control-rbac"></a>Controllo degli accessi in base al ruolo
È possibile proteggere l'account di archiviazione con il controllo degli accessi in base al ruolo. Per le organizzazioni che intendono applicare criteri di sicurezza per l'accesso ai dati è fondamentale limitare l'accesso in base a principi di [riservatezza](https://en.wikipedia.org/wiki/Need_to_know) e [privilegi minimi](https://en.wikipedia.org/wiki/Principle_of_least_privilege). Questi diritti di accesso vengono concessi assegnando il ruolo di controllo degli accessi appropriato a gruppi e applicazioni in un ambito specifico. È possibile usare i [ruoli predefiniti del controllo degli accessi in base al ruolo](../active-directory/role-based-access-built-in-roles.md), ad esempio Collaboratore Account di archiviazione, per assegnare privilegi agli utenti.

Altre informazioni:

* [Controllo degli accessi in base al ruolo di Azure Active Directory](../active-directory/role-based-access-control-configure.md)

## <a name="delegated-access-to-storage-objects"></a>Accesso delegato agli oggetti di archiviazione
Una firma di accesso condiviso (SAS) fornisce accesso delegato alle risorse nell'account di archiviazione. Questa firma di accesso condiviso significa che è possibile concedere a un client autorizzazioni limitate per BLOB, code o tabelle per un periodo di tempo specificato e con un set di autorizzazioni. È possibile concedere queste autorizzazioni limitate senza la necessità di condividere le chiavi di accesso all'account. La firma di accesso condiviso è un URI che racchiude nei parametri di query tutte le informazioni necessarie per l'accesso autenticato a una risorsa di archiviazione. Per accedere alle risorse di archiviazione con la firma di accesso condiviso, il client deve solo passare la firma al costruttore o al metodo appropriato.

Altre informazioni:

* [Informazioni sul modello di firma di accesso condiviso](../storage/common/storage-dotnet-shared-access-signature-part-1.md)
* [Creare e usare una firma di accesso condiviso con l'archiviazione BLOB](../storage/blobs/storage-dotnet-shared-access-signature-part-2.md)

## <a name="encryption-in-transit"></a>Crittografia in transito
La crittografia in transito è un meccanismo di protezione dei dati durante la trasmissione tra le reti. Con Archiviazione di Azure è possibile proteggere i dati con:

* [Crittografia a livello di trasporto](../storage/common/storage-security-guide.md#encryption-in-transit), ad esempio HTTPS quando si trasferiscono dati all'interno o all'esterno di Archiviazione di Azure.
* [Crittografia di rete](../storage/common/storage-security-guide.md#using-encryption-during-transit-with-azure-file-shares), ad esempio la crittografia SMB 3.0 per le condivisioni file di Azure.
* [Crittografia lato client](../storage/common/storage-security-guide.md#using-client-side-encryption-to-secure-data-that-you-send-to-storage), per crittografare i dati prima che siano trasferiti nella risorsa di archiviazione e decrittografarli dopo il trasferimento dalla risorsa di archiviazione.

Altre informazioni sulla crittografia lato client:

* [Crittografia lato client per Archiviazione di Microsoft Azure](https://blogs.msdn.microsoft.com/windowsazurestorage/2015/04/28/client-side-encryption-for-microsoft-azure-storage-preview/)
* [Serie di controlli della sicurezza del cloud: crittografia dei dati in transito](http://blogs.microsoft.com/cybertrust/2015/08/10/cloud-security-controls-series-encrypting-data-in-transit/)

## <a name="encryption-at-rest"></a>Crittografia di dati inattivi
Per molte organizzazioni, [la crittografia dei dati inattivi](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) è un passaggio obbligatorio per assicurare la privacy dei dati, la conformità e la sovranità dei dati. Esistono tre funzionalità di Azure che consentono di crittografare dati inattivi:

* [Crittografia del servizio di archiviazione](../storage/common/storage-security-guide.md#encryption-at-rest) consente di richiedere che il servizio di archiviazione crittografi automaticamente i dati durante la scrittura in Archiviazione di Azure.
* [Client-side Encryption](../storage/common/storage-security-guide.md#client-side-encryption) offre anche la funzionalità di crittografia dei dati inattivi.
* [Crittografia dischi di Azure](../storage/common/storage-security-guide.md#using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines) consente di crittografare i dischi dati e del sistema operativo usati da una macchina virtuale IaaS.

Altre informazioni su Crittografia del servizio di archiviazione:

* La [crittografia del servizio di archiviazione di Azure](https://azure.microsoft.com/services/storage/) è disponibile per [Archiviazione BLOB di Azure](https://azure.microsoft.com/services/storage/blobs/). Per informazioni su altri tipi di archiviazione di Azure, vedere [File](https://azure.microsoft.com/services/storage/files/), [Disco (Archiviazione Premium)](https://azure.microsoft.com/services/storage/premium-storage/), [Tabella](https://azure.microsoft.com/services/storage/tables/) e [Coda](https://azure.microsoft.com/services/storage/queues/).
* [Crittografia del servizio di archiviazione di Azure per dati inattivi](../storage/common/storage-service-encryption.md)

## <a name="azure-disk-encryption"></a>Azure Disk Encryption
Crittografia dischi di Azure per le macchine virtuali consente di soddisfare i requisiti di conformità e sicurezza dell'organizzazione, grazie alla possibilità di crittografare i dischi delle macchine virtuali, inclusi i dischi di avvio e di dati, con chiavi e criteri gestiti in [Insieme di credenziali delle chiavi di Azure](https://azure.microsoft.com/services/key-vault/).

Crittografia dischi per le macchine virtuali funziona con sistemi operativi sia Linux, sia Windows. Usa l'insieme di credenziali delle chiavi per proteggere, gestire e controllare l'uso delle chiavi di crittografia dei dischi. Tutti i dati nei dischi delle macchine virtuali vengono crittografati mentre sono inattivi, usando una tecnologia di crittografia standard del settore negli account di archiviazione di Azure. La soluzione Crittografia dischi per Windows è basata su [Crittografia unità BitLocker](https://technet.microsoft.com/library/cc732774.aspx) di Microsoft e la soluzione Linux è basata su [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt).

Altre informazioni:

* [Crittografia dischi di Azure per le macchine virtuali IaaS Windows e Linux](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)

## <a name="azure-key-vault"></a>Insieme di credenziali chiave Azure
Crittografia dischi di Azure usa [Insieme di credenziali delle chiavi di Azure](https://azure.microsoft.com/services/key-vault/) per facilitare il controllo e la gestione delle chiavi di crittografia dei dischi e dei segreti nella sottoscrizione dell'insieme di credenziali delle chiavi, assicurando al tempo stesso che tutti i dati nei dischi delle macchine virtuali siano crittografati quando inattivi in Archiviazione di Azure. È opportuno usare l'insieme di credenziali delle chiavi per controllare le chiavi e l'utilizzo di criteri.

Altre informazioni:

* [Cos'è l'insieme di credenziali chiave di Azure?](../key-vault/key-vault-whatis.md)
* [Introduzione all'insieme di credenziali delle chiavi di Azure](../key-vault/key-vault-get-started.md)
