---
title: Funzionalità di sicurezza per Archiviazione di Azure | Microsoft Docs
description: Questo articolo offre informazioni generali sulle funzionalità di sicurezza principali di Azure che possono essere usate con Archiviazione di Azure.
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
ms.date: 10/29/2018
ms.author: terrylan
ms.openlocfilehash: 759de1c62cb9cb4aa642fa564378b1ed551d7dae
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2018
ms.locfileid: "50239356"
---
# <a name="azure-storage-security-overview"></a>Panoramica della sicurezza di Archiviazione di Azure

Archiviazione di Azure è la soluzione di archiviazione cloud per le applicazioni moderne basate su durata, disponibilità e scalabilità per soddisfare le esigenze dei clienti. Archiviazione di Azure offre un set completo di funzionalità di sicurezza. È possibile:

* Proteggere l'account di archiviazione con il controllo degli accessi in base al ruolo (RBAC) e Azure Active Directory.
* Proteggere i dati in transito tra un'applicazione e Azure usando la crittografia lato client, HTTPS o SMB 3.0.
* Impostare i dati per la crittografia automatica quando vengono scritti in Archiviazione di Azure con la crittografia del servizio di archiviazione di Azure.
* Impostare i dischi dei dati e del sistema operativo usati dalle macchine virtuali (VM) per la crittografia con Crittografia dischi di Azure.
* Concedere l'accesso delegato agli oggetti dati nell'archiviazione di Azure usando le firme di accesso condiviso (SAS).
* Usare l'analisi per rilevare il metodo di autenticazione usato da un utente durante l'accesso ad Archiviazione di Azure.

Per un'analisi più approfondita della sicurezza in Archiviazione di Azure, vedere la [Guida alla sicurezza di Archiviazione di Azure](../storage/common/storage-security-guide.md). Questa guida fornisce un approfondimento sulle funzionalità di sicurezza dell'Archiviazione di Azure. Queste funzionalità includono chiavi di account di archiviazione, la crittografia dei dati in transito e inattivi e analisi di archiviazione.


Questo articolo fornisce informazioni generali sulle funzionalità di sicurezza di Azure che possono essere usate con Archiviazione di Azure. I collegamenti agli articoli forniscono informazioni dettagliate su ogni funzionalità.

## <a name="role-based-access-control"></a>Controllo degli accessi in base al ruolo

È possibile proteggere l'account di archiviazione con il controllo degli accessi in base al ruolo. Per le organizzazioni che intendono applicare criteri di sicurezza per l'accesso ai dati è fondamentale limitare l'accesso in base a principi di [riservatezza](https://en.wikipedia.org/wiki/Need_to_know) e [privilegi minimi](https://en.wikipedia.org/wiki/Principle_of_least_privilege). Questi diritti di accesso vengono concessi assegnando il ruolo di controllo degli accessi appropriato a gruppi e applicazioni in un ambito specifico. È possibile usare i [ruoli predefiniti del controllo degli accessi in base al ruolo](../role-based-access-control/built-in-roles.md), ad esempio Collaboratore Account di archiviazione, per assegnare privilegi agli utenti.

Altre informazioni:

* [Controllo degli accessi in base al ruolo di Azure Active Directory](../role-based-access-control/role-assignments-portal.md)

## <a name="delegated-access-to-storage-objects"></a>Accesso delegato agli oggetti di archiviazione

Una firma di accesso condiviso fornisce accesso delegato controllato alle risorse dell'account di archiviazione. La firma di accesso condiviso consente di concedere a un client autorizzazioni limitate per gli oggetti nell'account di archiviazione per un periodo di tempo e con un set di autorizzazioni specificati. È possibile concedere queste autorizzazioni limitate senza la necessità di condividere le chiavi di accesso all'account. 

La firma di accesso condiviso è un URI che racchiude nei parametri di query tutte le informazioni necessarie per l'accesso autenticato a una risorsa di archiviazione. Per accedere alle risorse di archiviazione con la firma di accesso condiviso, il client deve solo passare la firma al costruttore o al metodo appropriato.

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

Per molte organizzazioni, [la crittografia dei dati inattivi](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) è un passaggio obbligatorio per assicurare la privacy dei dati, la conformità e la sovranità dei dati. Tre funzionalità di Azure consentono di crittografare dati inattivi:

* [Crittografia del servizio di archiviazione](../storage/common/storage-security-guide.md#encryption-at-rest) consente di richiedere che il servizio di archiviazione crittografi automaticamente i dati durante la scrittura in Archiviazione di Azure.
* [Crittografia lato client](../storage/common/storage-security-guide.md#client-side-encryption) offre anche la funzionalità di crittografia dei dati inattivi.
* [Crittografia dischi di Azure](../storage/common/storage-security-guide.md#using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines) consente di crittografare i dischi dei dati e del sistema operativo usati da una macchina virtuale IaaS.

Altre informazioni su Crittografia del servizio di archiviazione:

* La [crittografia del servizio di archiviazione di Azure](https://azure.microsoft.com/services/storage/) è disponibile per [Archiviazione BLOB di Azure](https://azure.microsoft.com/services/storage/blobs/). Per informazioni su altri tipi di archiviazione di Azure, vedere [File di Azure](https://azure.microsoft.com/services/storage/files/), [Disco (Archiviazione Premium)](https://azure.microsoft.com/services/storage/premium-storage/), [Archiviazione tabelle](https://azure.microsoft.com/services/storage/tables/) e [Archiviazione code](https://azure.microsoft.com/services/storage/queues/).
* [Crittografia del servizio di archiviazione di Azure per dati inattivi](../storage/common/storage-service-encryption.md)

## <a name="azure-disk-encryption"></a>Azure Disk Encryption

Crittografia dischi di Azure per le macchine virtuali consente di soddisfare i requisiti di sicurezza e conformità dell'organizzazione. Esegue la crittografia dei dischi delle macchine virtuali, inclusi i dischi di avvio e di dati, tramite chiavi e criteri che è possibile controllare in [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

Crittografia dischi per le macchine virtuali funziona con sistemi operativi sia Linux, sia Windows. Usa l'insieme di credenziali delle chiavi per proteggere, gestire e controllare l'uso delle chiavi di crittografia dei dischi. Tutti i dati nei dischi delle macchine virtuali vengono crittografati mentre sono inattivi, usando una tecnologia di crittografia standard del settore negli account di archiviazione di Azure. La soluzione Crittografia dischi per Windows è basata su [Crittografia unità BitLocker](https://technet.microsoft.com/library/cc732774.aspx) di Microsoft e la soluzione Linux è basata su [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt).

Altre informazioni

* [Crittografia dischi di Azure per le macchine virtuali IaaS Windows e Linux](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)

## <a name="firewalls-and-virtual-networks"></a>Firewall e reti virtuali

Archiviazione di Azure consente di abilitare le regole del firewall per gli account di archiviazione. Una volta abilitate, le regole bloccheranno le richieste in ingresso dei dati, incluse quelle provenienti da altri servizi di Azure. È possibile configurare eccezioni per consentire il traffico. Le regole del firewall possono essere abilitate per gli account di archiviazione esistenti oppure durante la fase di creazione.

È consigliabile usare questa funzionalità per proteggere gli account di archiviazione limitandoli a un set specifico di reti consentite.

Per altre informazioni sui firewall e sulle reti virtuali di Archiviazione di Azure, vedere l'articolo [Configurare i firewall e le reti virtuali di Archiviazione di Azure](../storage/common/storage-network-security.md).

## <a name="azure-data-box"></a>Azure Data Box

I dispositivi Data Box, Data Box Disk e Data Box Heavy consentono di trasferire quantità elevate di dati in Azure quando non è possibile usare la rete. Questi dispositivi per il trasferimento dei dati offline vengono spediti tra l'organizzazione e il data center di Azure. Usano la crittografia AES per la protezione dei dati in transito e vengono sottoposti a un processo completo di bonifica dopo il caricamento per l'eliminazione dei dati.

Data Box Edge e Data Box Gateway sono prodotti per il trasferimento dei dati online che fungono da gateway di archiviazione di rete per gestire i dati tra il sito e Azure. Data Box Edge, un dispositivo di rete locale, trasferisce i dati verso e da Azure e usa risorse di calcolo perimetrali abilitate per intelligenza artificiale per elaborare i dati. Data Box Gateway è un'appliance virtuale con funzionalità di gateway di archiviazione.

Altre informazioni:

* [Azure Data Box](https://azure.microsoft.com/services/storage/databox/)
* [Azure Data Box Edge](../databox-online/data-box-edge-overview.md)
* [Azure Data Box Gateway](..//databox-online/data-box-gateway-overview.md)

## <a name="advanced-threat-protection"></a>Advanced Threat Protection

Archiviazione di Azure offre la funzionalità Advanced Threat Protection per un livello aggiuntivo di intelligence di sicurezza in grado di rilevare tentativi insoliti e potenzialmente dannosi di accesso o sfruttamento dell'account di archiviazione. Advanced Threat Protection esegue il monitoraggio dei log di diagnostica di Archiviazione di Azure per determinare la presenza di eventuali richieste di lettura, scrittura o eliminazione alle risorse di archiviazione BLOB. 

Gli avvisi di Advanced Threat Protection possono essere visualizzati dal [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/), che offre informazioni dettagliate sulle attività sospette rilevate e consiglia azioni per analizzare e correggere la potenziale minaccia. 

Altre informazioni:

* [Panoramica di Advanced Threat Protection di Archiviazione di Azure](../storage/common/storage-advanced-threat-protection.md)

## <a name="azure-key-vault"></a>Azure Key Vault

Crittografia dischi di Azure usa [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) per semplificare il controllo e la gestione dei segreti e delle chiavi di crittografia dei dischi nella sottoscrizione dell'insieme di credenziali delle chiavi. Questa soluzione assicura anche che tutti i dati nei dischi delle macchine virtuali vengano crittografati quando inattivi in Archiviazione di Azure. È opportuno usare l'insieme di credenziali delle chiavi per controllare le chiavi e l'utilizzo di criteri.

Altre informazioni

* [Cos'è l'insieme di credenziali chiave di Azure?](../key-vault/key-vault-whatis.md)
* [Introduzione all'insieme di credenziali delle chiavi di Azure](../key-vault/key-vault-get-started.md)
