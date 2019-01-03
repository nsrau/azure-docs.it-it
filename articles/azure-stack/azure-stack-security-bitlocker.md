---
title: Dati di crittografia dei dati inattivi in Azure Stack
description: Informazioni su come protezione dei dati con crittografia dei dati inattivi in Azure Stack
services: azure-stack
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 12/06/2018
ms.author: patricka
ms.reviewer: fiseraci
keywords: ''
ms.openlocfilehash: f9e76b255647f62b273fef8336ed845e365261cf
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2018
ms.locfileid: "53731741"
---
# <a name="data-at-rest-encryption-in-azure-stack"></a>Dati di crittografia dei dati inattivi in Azure Stack

Azure Stack consente di proteggere utente e i dati di infrastruttura a livello del sottosistema di archiviazione usando la crittografia a riposo. Il sottosistema di archiviazione di Azure Stack verrà crittografato usando BitLocker con crittografia AES a 128 bit. Le chiavi BitLocker vengono rese persistenti in un archivio segreto interno.

I dati di crittografia dei dati inattivi sono un requisito comune per molti i principali standard di conformità (ad esempio, PCI-DSS, FedRAMP, HIPAA). Azure Stack consente di soddisfare tali requisiti senza operazioni aggiuntive o configurazioni necessarie. Per altre informazioni su come Azure Stack ti aiuta a soddisfare gli standard di conformità, vedere la [Microsoft Service Trust Portal](https://aka.ms/AzureStackCompliance).

> [!NOTE]
> Crittografia dei dati inattivi consente di proteggere i dati in base a cui si accede da un utente fisicamente rubare uno o più dischi rigidi. I dati di crittografia dei dati inattivi non offre protezione contro i dati vengano intercettati attraverso la rete (i dati in transito), i dati attualmente in uso (dati in memoria) o più in generale, i dati in corso exfiltrated mentre il sistema sia attivo e in esecuzione.

## <a name="retrieving-bitlocker-recovery-keys"></a>Il recupero delle chiavi di ripristino di BitLocker

Chiavi di BitLocker di Stack di Azure per dati inattivi vengono gestite internamente. Non è necessario fornire loro per le normali operazioni o durante l'avvio del sistema. Tuttavia, scenari di supporto potrebbero richiedere le chiavi di ripristino BitLocker per riportare online il sistema.  

> [!WARNING]
> Recuperare le chiavi di ripristino di BitLocker e averle archiviate in una posizione sicura all'esterno di Azure Stack. Senza che sia le chiavi di ripristino durante determinati scenari di supporto può comportare la perdita di dati e richiedono un ripristino di sistema da un'immagine di backup.

Recuperare le chiavi di ripristino di BitLocker richiede l'accesso alla [privileged endpoint](azure-stack-privileged-endpoint.md) (PEP). Da una sessione PEP, eseguire il cmdlet Get-AzsRecoveryKeys.

```powershell
##This cmdlet retrieves the recovery keys for all the volumes that are encrypted with BitLocker.
Get-AzsRecoveryKeys
```

I parametri facoltativi per *Get-AzsRecoveryKeys* cmdlet:

| Parametro | DESCRIZIONE | type | Obbligatorio |
|---------|---------|---------|---------|
|*raw* | Restituisce i dati non elaborati del mapping tra la chiave di ripristino, nome del computer e ID password di ciascun volume crittografato  | opzione | Nessuna (progettato per scenari di supporto)|


## <a name="troubleshoot-issues"></a>Risolvere i problemi

In casi estremi, sbloccare BitLocker una richiesta potrebbe avere esito negativo risultante in un volume specifico per non eseguire l'avvio. A seconda della disponibilità di alcuni dei componenti dell'architettura, questo errore può comportare tempi di inattività e perdita di dati se non si dispongono di chiavi di ripristino di BitLocker.

> [!WARNING]
> Recuperare le chiavi di ripristino di BitLocker e averle archiviate in una posizione sicura all'esterno di Azure Stack. Senza che sia le chiavi di ripristino durante determinati scenari di supporto può comportare la perdita di dati e richiedono un ripristino di sistema da un'immagine di backup.

Se si ritiene che il sistema ha riscontrato problemi con BitLocker, ad esempio Azure Stack mancato avvio, contattare il supporto tecnico. Il supporto richiede le chiavi di ripristino di BitLocker. La maggior parte di BitLocker correlati problemi possono essere risolti con un'operazione FRU per VM/host/volume specifico. Per gli altri casi, può essere eseguita una procedura di sblocco manuale tramite chiavi di ripristino di BitLocker. Se le chiavi di ripristino di BitLocker non sono disponibili, l'unica opzione è eseguire il ripristino da un'immagine di backup. A seconda di quando è stato eseguito l'ultimo backup, è possibile riscontrare la perdita di dati.

## <a name="next-steps"></a>Passaggi successivi

- [Altre informazioni sulla sicurezza di Azure Stack](azure-stack-security-foundations.md)
- Per altre informazioni sul modo in cui BitLocker protegge i volumi condivisi cluster, vedere [la protezione del cluster condiviso volumi e delle reti SAN con BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/protecting-cluster-shared-volumes-and-storage-area-networks-with-bitlocker).