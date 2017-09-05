---
title: Protezione delle applicazioni PaaS usando Archiviazione di Azure | Microsoft Docs
description: " Informazioni sulle procedure consigliate di sicurezza in Archiviazione di Azure per proteggere le applicazioni PaaS Web e per dispositivi mobili. "
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: 
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: TomShinder
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 62c7b6706268e3c6e329f90651125fe299f61d67
ms.contentlocale: it-it
ms.lasthandoff: 08/24/2017

---
# <a name="securing-paas-web-and-mobile-applications-using-azure-storage"></a>Protezione delle applicazioni PaaS Web e per dispositivi mobili usando Archiviazione di Azure
Questo articolo illustra un insieme di procedure consigliate di sicurezza in Archiviazione di Azure per la protezione delle applicazioni PaaS Web e per dispositivi mobili. Le procedure consigliate si basano sull'esperienza di tecnici e clienti con Azure.

La [Guida alla sicurezza di Archiviazione di Azure](../storage/common/storage-security-guide.md) è una fonte eccellente di informazioni dettagliate su Archiviazione di Azure e sulla sicurezza.  Questo articolo include ad un alto livello alcuni concetti illustrati nella Guida alla sicurezza con i relativi collegamenti, oltre ad altre fonti per altre informazioni.

## <a name="azure-storage"></a>Archiviazione di Azure
Azure consente di distribuire e usare le risorse di archiviazione in modi che non è facile ottenere in locale. Con archiviazione di Azure è possibile raggiungere livelli elevati di scalabilità e disponibilità con un lavoro richiesto minimo. Non solo archiviazione di Azure è la base per le macchine virtuali Windows e Linux di Azure, ma può supportare anche applicazioni distribuite di grandi dimensioni.

Archiviazione di Azure offre i quattro servizi seguenti: archiviazione BLOB, archiviazione tabelle, archiviazione code e archiviazione file. Per altre informazioni, vedere [Introduzione ad Archiviazione di Microsoft Azure](../storage/storage-introduction.md).

## <a name="best-practices"></a>Procedure consigliate
In questo articolo vengono illustrate le seguenti procedure consigliate:

- Protezione dell'accesso:
   - Firme di accesso condiviso
   - Disco gestito
   - Controllo degli accessi in base al ruolo

- Crittografia di archiviazione:
   - Crittografia lato client per i dati di valore elevato
   - Crittografia dischi di Azure per le macchine virtuali (VM)
   - Crittografia del servizio di archiviazione

## <a name="access-protection"></a>Protezione dell'accesso
### <a name="use-shared-access-signature-instead-of-a-storage-account-key"></a>Usare la firma di accesso condiviso anziché una chiave dell'account di archiviazione

In una soluzione IaaS, le macchine virtuali Windows Server o Linux vengono in genere protette dalle minacce di divulgazione e manomissione usando meccanismi di controllo di accesso. In Windows si userebbero gli [elenchi di controllo di accesso (ACL)](../virtual-network/virtual-networks-acl.md) e in Linux si userebbe probabilmente [chmod](https://en.wikipedia.org/wiki/Chmod). In pratica, ciò è esattamente ciò che si farebbe attualmente per proteggere i file in un server nel proprio data center.

La funzionalità PaaS è diversa. Uno dei modi più comuni per archiviare i file in Microsoft Azure consiste nell'usare [archiviazione BLOB di Azure](../storage/storage-dotnet-how-to-use-blobs.md). Una differenza tra archiviazione BLOB e altri tipi di archiviazione file è l'I/O di file e i metodi di protezione forniti con l'I/O dei file.

Il controllo di accesso è fondamentale. Per facilitare il controllo di accesso alle risorse di archiviazione di Azure, il sistema genera due chiavi dell'account di archiviazione a 512 bit quando si [crea un account di archiviazione](../storage/common/storage-create-storage-account.md). Il livello di ridondanza delle chiavi consente di evitare l'interruzione del servizio durante la normale rotazione delle chiavi.

Le chiavi di accesso alle risorse di archiviazione sono segreti ad alta priorità che devono essere accessibili solo ai responsabili del controllo di accesso alle risorse di archiviazione. Se persone sbagliate ottengono l'accesso a queste chiavi, disporranno del controllo completo delle risorse di archiviazione e potrebbero sostituire, eliminare o aggiungere file alle risorse di archiviazione. Sono inclusi il malware e altri tipi di contenuto che potrebbero compromettere l'organizzazione o i clienti.

È comunque necessario un modo per fornire l'accesso agli oggetti nella risorsa di archiviazione. Per fornire un accesso più granulare è possibile sfruttare la [firma di accesso condiviso](../storage/common/storage-dotnet-shared-access-signature-part-1.md) (SAS). La firma di accesso condiviso consente la condivisione di oggetti specifici nella risorsa di archiviazione per un intervallo di tempo predefinito e con autorizzazioni specifiche. Una firma di accesso condiviso consente di definire:

- L'intervallo di validità della firma di accesso condiviso, incluse l'ora di inizio e quella di scadenza.
- Le autorizzazioni concesse dalla firma di accesso condiviso. Ad esempio, una firma di accesso condiviso su un BLOB potrebbe concedere a un utente le autorizzazioni di lettura e di scrittura per questo BLOB, ma non eliminare le autorizzazioni.
- Un indirizzo IP o un intervallo di indirizzi IP facoltativo da cui Archiviazione di Azure accetta la firma di accesso condiviso. Ad esempio, è possibile specificare un intervallo di indirizzi IP appartenenti all'organizzazione. Fornisce un'altra misura di sicurezza per la firma di accesso condiviso.
- Il protocollo tramite cui Archiviazione di Azure accetta la firma di accesso condiviso. È possibile usare questo parametro facoltativo per limitare l'accesso ai client tramite HTTPS.

La firma di accesso condiviso consente di condividere il contenuto nel modo in cui si vuole condividerlo, senza fornire le chiavi dell'account di archiviazione. L'uso costante di una firma di accesso condiviso rappresenta un metodo sicuro per condividere le risorse di archiviazione senza compromettere le chiavi dell'account di archiviazione.

Per altre informazioni, vedere [Uso delle firme di accesso condiviso](../storage/common/storage-dotnet-shared-access-signature-part-1.md) (SAS). Per altre informazioni sui potenziali rischi e i consigli per ridurre tali rischi, vedere [Procedure consigliate per l'uso di SAS](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

### <a name="use-managed-disks-for-vms"></a>Usare dischi gestiti per le macchine virtuali

Quando si sceglie [Azure Managed Disks](../storage/storage-managed-disks-overview.md), Azure gestisce gli account di archiviazione utilizzati per i dischi della VM. È sufficiente scegliere il tipo di disco (Standard o Premium) e la relativa dimensione; Archiviazione di Azure eseguirà le altre operazioni. Non è necessario preoccuparsi dei limiti di scalabilità che altrimenti potrebbero essere richiesti per più account di archiviazione.

Per altre informazioni, vedere [Frequently asked questions about Azure IaaS VM disks and managed and unmanaged premium disks](../storage/storage-faq-for-disks.md) (Domande frequenti sui dischi e sui dischi Premium delle macchine virtuali IaaS di Azure: gestiti e non gestiti).

### <a name="use-role-based-access-control"></a>Usare il controllo degli accessi in base al ruolo

È già stato illustrato l'uso di una firma di accesso condiviso (SAS) per concedere un accesso limitato agli oggetti nell'account di archiviazione, senza esporre la chiave dell'account di archiviazione. Talvolta i rischi associati a una particolare operazione su un account di archiviazione superano i benefici derivanti dall'uso della firma di accesso condiviso. Talvolta è più semplice gestire l'accesso in modi diversi.

Un altro modo per gestire l'accesso è usare il [controllo degli accessi in base al ruolo](../active-directory/role-based-access-control-what-is.md). Nel controllo degli accessi in base al ruolo l'aspetto principale è concedere ai dipendenti esattamente le autorizzazioni di cui necessitano, in base ai principi dei privilegi minimi e sapere solo quando è necessario. un numero eccessivo di autorizzazioni può esporre un account agli attacchi. Un numero di autorizzazioni insufficiente ostacola l'efficienza del lavoro dei dipendenti. Il controllo degli accessi in base al ruolo di Azure consente di risolvere questo problema offrendo una gestione granulare degli accessi per Azure. Questo è fondamentale per le organizzazioni che intendono applicare criteri di sicurezza per l'accesso ai dati.

Si possono sfruttare i ruoli predefiniti del controllo degli accessi in base al ruolo in Azure per assegnare privilegi agli utenti. Si consiglia di usare Collaboratore Account di archiviazione per gli operatori cloud che devono gestire gli account di archiviazione e il ruolo Collaboratore Account di archiviazione classico per gestire gli account di archiviazione classici. Per gli operatori cloud che hanno necessità di gestire le VM ma non la rete virtuale o l'account di archiviazione a cui sono connesse, è possibile aggiungere il ruolo di Collaboratore Macchina virtuale.

Le organizzazioni che non applicano il controllo di accesso ai dati con funzionalità come Controllo degli accessi in base al ruolo potrebbero concedere più privilegi del necessario ai propri utenti. Questo può comportare la compromissione dei dati perché ad alcuni utenti potrebbe essere concesso l'accesso a dati di cui non dovrebbero disporre.

Per altre informazioni sul controllo degli accessi in base al ruolo, vedere:

- [Controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-control-configure.md)
- [Ruoli predefiniti per il controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-built-in-roles.md)
- [Azure Storage Security Guide](../storage/common/storage-security-guide.md) (Guida alla sicurezza di Archiviazione di Azure) per informazioni dettagliate su come proteggere l'account di archiviazione con il controllo degli accessi in base al ruolo

## <a name="storage-encryption"></a>Crittografia di archiviazione
### <a name="use-client-side-encryption-for-high-value-data"></a>Usare la crittografia lato client per i dati di valore elevato

La crittografia lato client consente di crittografare i dati in transito a livello di codice prima di caricarli in Archiviazione di Azure e decrittografarli a livello di codice quando vengono recuperati dalla risorsa di archiviazione.  Questo approccio consente la crittografia dei dati in transito, ma anche dei dati inattivi.  La crittografia dei dati lato client è il metodo più sicuro, ma richiede modifiche all'applicazione a livello di codice e l'implementazione della gestione delle chiavi.

La crittografia lato client consente anche il controllo esclusivo delle chiavi di crittografia.  È possibile generare e gestire chiavi di crittografia personalizzate.  La crittografia lato client usa una tecnica basata su busta in cui la libreria client di archiviazione di Azure genera una chiave di crittografia del contenuto (CEK) di cui viene eseguito il wrapping (crittografia) usando la chiave di crittografia della chiave (KEK). La chiave di crittografia della chiave è identificata con un identificatore di chiave e può essere costituita da una coppia di chiavi asimmetriche o da una chiave simmetrica. Può essere gestita localmente o archiviata in [Azure Key Vault](../key-vault/key-vault-whatis.md).

La crittografia lato client è incorporata nelle librerie client di archiviazione .NET e Java.  Vedere [Crittografia lato client e Azure Key Vault per Archiviazione di Microsoft Azure](../storage/storage-client-side-encryption.md) per informazioni sulla crittografia dei dati all'interno di applicazioni client e la generazione e gestione di chiavi di crittografia personalizzate.

### <a name="azure-disk-encryption-for-vms"></a>Crittografia dischi di Azure per le VM
Crittografia dischi di Azure è una funzionalità che consente di crittografare i dischi delle macchine virtuali IaaS Windows e Linux. Crittografia dischi di Azure si basa sulla funzionalità BitLocker standard di settore disponibile in Windows e sulla funzionalità DM-Crypt di Linux per offrire la crittografia del volume per i dischi dei dati e del sistema operativo. La soluzione è integrata con Azure Key Vault per consentire di controllare e gestire le chiavi di crittografia dei dischi e i segreti nella sottoscrizione dell'insieme di credenziali delle chiavi. Questa soluzione assicura anche che tutti i dati nei dischi delle macchine virtuali vengano crittografati quando inattivi in Archiviazione di Azure.

Vedere [Crittografia dischi di Azure per le macchine virtuali IaaS Windows e Linux](azure-security-disk-encryption.md).

### <a name="storage-service-encryption"></a>Crittografia del servizio di archiviazione
Quando è abilitata la [crittografia del servizio di archiviazione](../storage/storage-service-encryption.md) per archiviazione file, i dati vengono crittografati automaticamente usando l'algoritmo AES-256. Microsoft gestisce interamente la crittografia, la decrittografia e la gestione delle chiavi. Questa funzionalità è disponibile per i tipi di ridondanza archiviazione con ridondanza locale e con ridondanza geografica.

## <a name="next-steps"></a>Passaggi successivi
Questo articolo descrive una serie di procedure consigliate per la protezione delle applicazioni Web PaaS e delle applicazioni per dispositivi mobili mediante Archiviazione di Microsoft Azure. Per ulteriori informazioni sulla protezione delle distribuzioni PaaS, vedere:

- [Protezione delle distribuzioni PaaS](security-paas-deployments.md)
- [Protezione delle applicazioni Web e per dispositivi mobili in PaaS mediante i Servizi app di Azure](security-paas-applications-using-app-services.md)
- [Protezione di database PaaS in Azure](security-paas-applications-using-sql.md)

