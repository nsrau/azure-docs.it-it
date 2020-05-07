---
title: Crittografia in backup di Azure
description: Informazioni sul modo in cui le funzionalità di crittografia di backup di Azure consentono di proteggere i dati di backup e soddisfare le esigenze di sicurezza dell'azienda.
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: 1f7e0f26df0f8bd70a10b36f658dcfebe897b475
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82765799"
---
# <a name="encryption-in-azure-backup"></a>Crittografia in backup di Azure

Tutti i dati di cui è stato eseguito il backup vengono crittografati automaticamente quando vengono archiviati nel cloud usando la crittografia di archiviazione di Azure, che consente di soddisfare gli impegni di sicurezza e conformità. I dati inattivi vengono crittografati usando la crittografia AES a 256 bit, una delle crittografie a blocchi più solide disponibili ed è conforme a FIPS 140-2.

Oltre alla crittografia dei dati inattivi, tutti i dati di backup in transito vengono trasferiti tramite HTTPS. Rimane sempre nella rete backbone di Azure.

Per altre informazioni, vedere [crittografia di archiviazione di Azure per dati](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)inattivi. Vedere le domande [frequenti su backup di Azure](https://docs.microsoft.com/azure/backup/backup-azure-backup-faq#encryption) per rispondere a eventuali domande sulla crittografia.

## <a name="encryption-of-backup-data-using-platform-managed-keys"></a>Crittografia dei dati di backup tramite chiavi gestite dalla piattaforma

Per impostazione predefinita, tutti i dati vengono crittografati usando chiavi gestite dalla piattaforma. Non è necessario eseguire alcuna azione esplicita da parte dell'utente per abilitare questa crittografia e si applica a tutti i carichi di lavoro di cui viene eseguito il backup nell'insieme di credenziali di servizi di ripristino.

## <a name="encryption-of-backup-data-using-customer-managed-keys"></a>Crittografia dei dati di backup tramite chiavi gestite dal cliente

Quando si esegue il backup delle macchine virtuali di Azure, è ora possibile crittografare i dati usando le chiavi di proprietà e gestite dall'utente. Backup di Azure consente di usare le chiavi RSA archiviate nel Azure Key Vault per la crittografia dei backup. La chiave di crittografia usata per crittografare i backup può essere diversa da quella usata per l'origine. I dati vengono protetti usando una chiave di crittografia dei dati basata su AES 256, che a sua volta è protetta usando le chiavi. In questo modo si ottiene il controllo completo sui dati e sulle chiavi. Per consentire la crittografia, è necessario che all'insieme di credenziali dei servizi di ripristino sia concesso l'accesso alla chiave di crittografia nel Azure Key Vault. È possibile disabilitare la chiave o revocare l'accesso ogni volta che è necessario. Tuttavia, è necessario abilitare la crittografia usando le chiavi prima di provare a proteggere gli elementi nell'insieme di credenziali.

>[!NOTE]
>Questa funzionalità è attualmente in disponibilità limitata. Compila [questo sondaggio](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapURE9TTDRIUEUyNFhNT1lZS1BNVDdZVllHWi4u) e invia un messaggio di posta AskAzureBackupTeam@microsoft.com elettronica all'indirizzo se desideri crittografare i dati di backup usando chiavi gestite dal cliente. Si noti che la possibilità di usare questa funzionalità è soggetta all'approvazione del servizio backup di Azure.

## <a name="backup-of-managed-disk-vms-encrypted-using-customer-managed-keys"></a>Backup di macchine virtuali con dischi gestiti crittografate con chiavi gestite dal cliente

Backup di Azure consente anche di eseguire il backup delle macchine virtuali di Azure che usano la chiave per la crittografia lato server. La chiave usata per crittografare i dischi è archiviata nel Azure Key Vault e gestita dall'utente. La crittografia lato server con chiavi gestite dal cliente è diversa da quella di crittografia dischi di Azure, perché ADE USA BitLocker (per Windows) e DM-crypt (per Linux) per eseguire la crittografia in-Guest, SSE crittografa i dati nel servizio di archiviazione, consentendo di usare qualsiasi sistema operativo o immagine per le macchine virtuali. Per altri dettagli, vedere [crittografia dei dischi gestiti con chiavi gestite dal cliente](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption#customer-managed-keys) .

## <a name="backup-of-vms-encrypted-using-ade"></a>Backup di macchine virtuali crittografate tramite ADE

Con backup di Azure è anche possibile eseguire il backup delle macchine virtuali di Azure con il sistema operativo o i dischi dati crittografati tramite crittografia dischi di Azure. ADE USA BitLocker per le macchine virtuali Windows e DM-Crypt per le macchine virtuali Linux per eseguire la crittografia nel guest. Per informazioni dettagliate, vedere backup [e ripristino di macchine virtuali crittografate con backup di Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption).

## <a name="next-steps"></a>Passaggi successivi

- [Eseguire il backup e il ripristino di una macchina virtuale di Azure crittografata](backup-azure-vms-encryption.md)
