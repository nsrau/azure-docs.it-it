---
title: Domande frequenti sullo spostamento dal portale classico al portale di Azure | Microsoft Docs
description: Questo articolo risponde ad alcune domande frequenti sullo spostamento dei dispositivi StorSimple dal portale classico al portale di Azure.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/28/2017
ms.author: alkohli
ms.openlocfilehash: 06e8d99aa2ad4eb11e594a729c6dab39d5cd1eb6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="move-storsimple-device-manager-service-from-classic-to-azure-portal-frequently-asked-questions-faq"></a>Spostare il servizio Gestione dispositivi StorSimple dal portale classico al portale di Azure: domande frequenti

## <a name="overview"></a>Panoramica

Di seguito sono riepilogate alcune domande frequenti sullo spostamento del servizio Gestione dispositivi StorSimple in esecuzione nel portale classico di Azure al portale di Azure.

Le domande e le risposte sono suddivise nelle categorie seguenti:

* Spostamento del servizio Gestione dispositivi StorSimple
* Spostamento degli account di archiviazione
* Uso di cmdlet basati su Azure Resource Manager
* Uso del servizio Gestore dati StorSimple
* Miscellaneous

## <a name="moving-storsimple-device-manager-service"></a>Spostamento del servizio Gestione dispositivi StorSimple

### <a name="once-i-have-moved-to-azure-portal-can-i-still-create-a-storsimple-manager-service-in-the-classic-portal"></a>Dopo essere passati al portale di Azure, è ancora possibile creare un servizio StorSimple Manager nel portale classico?

No. Dopo la migrazione del servizio StorSimple Manager al portale di Azure, non è possibile creare un nuovo servizio nel portale classico. Non è neanche possibile gestire il dispositivo tramite il portale classico. Per altre informazioni, vedere [Spostare un servizio nel portale di Azure](storsimple-8000-manage-service.md#move-a-service-to-azure-portal).

### <a name="i-have-multiple-storsimple-managers-running-in-the-classic-portal-can-i-choose-which-ones-to-move-to-the-azure-portal"></a>Si dispone di più istanze di StorSimple Manager in esecuzione nel portale classico. È possibile scegliere quali migrare al portale di Azure?

No. Non è possibile scegliere quali istanze di StorSimple Manager migrare al portale di Azure. Vengono spostate tutte le istanze di StorSimple Manager presenti nella sottoscrizione.


### <a name="i-initiated-the-migration-of-my-service-to-the-new-azure-portal-should-i-delete-the-storsimple-manager-from-the-classic-portal"></a>È stata avviata la migrazione del servizio al nuovo portale di Azure. È necessario eliminare StorSimple Manager dal portale classico? 

No. Non tentare di eliminare alcun servizio spostato dal portale classico. Attendere il completamento della migrazione e, dopo che tutte le istanze di StorSimple Manager sono state spostate nel nuovo portale, non sarà necessario eliminare alcun servizio dal portale classico. Alla fine il portale classico sarà deprecato.

### <a name="can-i-rename-my-storsimple-device-manager-service-in-the-azure-portal"></a>È possibile rinominare il servizio Gestione dispositivi StorSimple nel portale di Azure?

No. Il nome non può essere modificato dopo la creazione del servizio nel portale di Azure. Lo stesso vale anche per altre entità, ad esempio dispositivi, volumi, contenitori dei volumi e criteri di backup.

### <a name="can-i-create-the-80108020-storsimple-cloud-appliances-with-azure-resource-manager-deployment-model"></a>È possibile creare appliance cloud StorSimple 8010/8020 con il modello di distribuzione Azure Resource Manager?

Sì. È possibile creare nuove appliance cloud StorSimple 8010/8020 nel modello di distribuzione Azure Resource Manager. Anche le macchine virtuali sottostanti per queste appliance cloud sono nel modello di distribuzione Azure Resource Manager.

### <a name="when-we-migrate-subscriptions-to-the-azure-portal-will-the-underlying-vms-for-the-storsimple-cloud-appliances-also-migrate-to-azure-resource-management-deployment-model"></a>Quando si esegue la migrazione delle sottoscrizioni nel portale di Azure, anche le macchine virtuali sottostanti per le appliance cloud StorSimple verranno spostate nel modello di distribuzione Azure Resource Manager?

Lo spostamento del servizio StorSimple è indipendente dalla gestione delle macchine virtuali per le appliance cloud StorSimple. Si possono gestire completamente le macchine virtuali per le appliance cloud StorSimple sia nel portale classico che in quello di Azure anche attualmente.

### <a name="can-i-manage-existing-classic-80108020-storsimple-cloud-appliance-from-the-azure-portal"></a>È possibile gestire le appliance cloud StorSimple 8010/8020 classiche esistenti dal portale di Azure?

Sì. Le macchine virtuali associate alle appliance cloud 8010/8020 esistenti possono essere gestite dal portale di Azure.

Se è stato creato un modello di appliance cloud StorSimple 8010/8020 che esegue l'aggiornamento 3.0 e versioni successive, non ci saranno ripercussioni in seguito allo spostamento del servizio nel nuovo portale di Azure. Dovrebbe essere possibile gestire completamente le appliance cloud senza problemi. 

Se si dispone di appliance cloud che eseguono versioni precedenti all'aggiornamento 3.0 nel portale classico, saranno disponibili solo con funzionalità limitate. Per altre informazioni, vedere l'[elenco delle operazioni non supportate per i dispositivi che eseguono versioni precedenti all'aggiornamento 3](storsimple-8000-manage-service.md#move-a-service-to-azure-portal).

Non è possibile aggiornare un'appliance cloud. Usare la versione più recente del software per creare una nuova appliance cloud e quindi effettuare il failover dei contenitori dei volumi esistenti nella nuova appliance cloud creata. Per altre informazioni, vedere [Effettuare il failover nell'appliance cloud](storsimple-8000-cloud-appliance-u2.md#fail-over-to-the-cloud-appliance).


### <a name="my-storsimple-8000-series-device-is-running-update-20-i-migrated-my-service-to-new-azure-portal-my-device-connected-successfully-but-it-seems-that-i-am-not-able-to-fully-manage-my-device-how-do-i-resolve-this-behavior"></a>Il dispositivo StorSimple serie 8000 esegue l'aggiornamento 2.0. È stata eseguita la migrazione del servizio al nuovo portale di Azure. Il dispositivo si è connesso correttamente ma sembra che non sia possibile gestire completamente il dispositivo. Come si può risolvere il problema?

Il nuovo portale di Azure è supportato solo per i dispositivi StorSimple che eseguono l'aggiornamento 3.0 e versioni successive. Se nel dispositivo è in esecuzione l'aggiornamento 2.0, sono disponibili solo funzionalità limitate per questo dispositivo. Per altre informazioni, vedere l'[elenco delle operazioni non supportate per i dispositivi che eseguono versioni precedenti all'aggiornamento 3](storsimple-8000-manage-service.md#move-a-service-to-azure-portal).

Per gestire completamente il dispositivo, installare l'aggiornamento più recente nel dispositivo. Per altre informazioni, vedere [Installare l'aggiornamento 5](storsimple-8000-install-update-5.md).

### <a name="i-just-moved-my-storsimple-manager-service-to-the-azure-portal-i-am-seeing-some-alerts-related-to-my-device-is-this-behavior-related-to-the-move"></a>È stata eseguita la migrazione del servizio StorSimple Manager al portale di Azure. Vengono visualizzati alcuni avvisi relativi al dispositivo. Questo comportamento è correlato allo spostamento?

No. Lo spostamento in sé non dovrebbe comportare errori o avvisi. Seguire le indicazioni sugli avvisi per risolverli.

### <a name="i-am-using-a-storsimple-50007000-series-device-are-these-also-supported-in-the-azure-portal"></a>È in uso un dispositivo StorSimple serie 5000/7000. Anche questi dispositivi sono supportati nel portale di Azure?

No. I dispositivi StorSimple serie 5000/7000 non sono supportati nel portale di Azure.

### <a name="i-am-planning-to-migrate-data-from-storsimple-50007000-series-device-to-a-storsimple-8000-series-device-how-does-moving-a-service-to-azure-portal-impact-my-data-migration"></a>Si intende eseguire la migrazione dei dati da dispositivi StorSimple serie 5000/7000 a un dispositivo StorSimple serie 8000. In che modo lo spostamento di un servizio nel portale di Azure si ripercuote sulla migrazione dei dati? 

È possibile eseguire la migrazione dei dati da un dispositivo StorSimple serie 5000/7000 a un dispositivo StorSimple serie 8000 in esecuzione nel portale di Azure. Per consentire la migrazione dei dati dalla serie 5000/7000 alla serie 8000, è necessario [registrare un ticket di supporto con il supporto tecnico Microsoft](storsimple-8000-contact-microsoft-support.md).


## <a name="moving-subscriptions-storage-accounts-resource-groups"></a>Spostamento di sottoscrizioni, account di archiviazione e gruppi di risorse

### <a name="can-i-move-storsimple-device-manager-from-one-subscription-to-another-subscription-in-the-azure-portal"></a>È possibile spostare Gestione dispositivi StorSimple da una sottoscrizione a un'altra nel portale di Azure?

No. Lo spostamento del servizio Gestione dispositivi StorSimple da una sottoscrizione a un'altra non è supportato. È possibile eseguire un processo manuale costituito dai passaggi seguenti:

* Eseguire la migrazione dei dati dal dispositivo StorSimple.
* Eseguire un ripristino delle impostazioni predefinite del dispositivo per eliminare i dati locali sul dispositivo.
* Registrare il dispositivo con la nuova sottoscrizione in un servizio Gestione dispositivi StorSimple.
* Eseguire la migrazione dei dati al dispositivo.

### <a name="do-i-have-to-migrate-the-storage-account-to-azure-resource-manager-deployment-model"></a>È necessario eseguire la migrazione dell'account di archiviazione al modello di distribuzione Azure Resource Manager?

No. Gli account di archiviazione classici possono essere completamente gestiti dal portale di Azure. Quando si sposta il servizio StorSimple nel portale di Azure, l'account di archiviazione continua a funzionare come prima.

### <a name="what-happens-to-the-storage-account-after-the-service-is-migrated-to-the-azure-portal"></a>Cosa accade all'account di archiviazione dopo che viene eseguita la migrazione del servizio al portale di Azure?

Lo spostamento del servizio non è correlato alla gestione dell'account di archiviazione. Gli account di archiviazione classici e di Azure Resource Manager possono essere entrambi completamente gestiti all'interno del portale di Azure. Dopo che il servizio è stato spostato nel portale di Azure, il dispositivo dovrebbe continuare a funzionare con questo account di archiviazione e non dovrebbero esserci ripercussioni sui dati.

### <a name="can-i-migrate-storsimple-device-manager-from-one-resource-group-to-another"></a>È possibile migrare Gestione dispositivi StorSimple da un gruppo di risorse a un altro?

No. Non è possibile spostare un'istanza di Gestione dispositivi StorSimple creata con un gruppo di risorse in un altro gruppo di risorse.

## <a name="using-azure-resource-manager-based-cmdlets"></a>Uso di cmdlet basati su Azure Resource Manager

### <a name="i-moved-to-the-new-azure-portal-now-my-scripts-based-on-azure-classic-deployment-model-powershell-cmdlets-are-not-working-what-do-i-need-to-do"></a>È stata eseguita la migrazione al nuovo portale di Azure. Ora gli script basati sui cmdlet di PowerShell del modello di distribuzione classica di Azure non funzionano. Cosa occorre fare?

I cmdlet di PowerShell esistenti del modello distribuzione classica di Azure non sono supportati nel portale di Azure. Aggiornare gli script per gestire i dispositivi tramite Azure Resource Manager. Per altre informazioni sull'aggiornamento degli script, vedere gli [esempi per il nuovo portale di Azure](https://github.com/anoobbacker/storsimpledevicemgmttools).

### <a name="i-just-moved-to-the-azure-portal-and-needed-to-roll-over-the-service-data-encryption-key-where-is-this-option-in-the-azure-portal"></a>È stata eseguita la migrazione al portale di Azure ed è necessario rinnovare la chiave di crittografia dei dati del servizio. Dove si trova questa opzione nel portale di Azure?

L'opzione per il rinnovo della chiave di crittografia dei dati del servizio non è disponibile nel portale di Azure. Per altre informazioni su come effettuare il rinnovo nel portale di Azure, vedere [Modificare la chiave DEK del servizio](storsimple-8000-manage-service.md#change-the-service-data-encryption-key).

### <a name="i-am-using-windows-powershell-for-storsimple-cmdlets-on-the-storsimple-device-to-perform-operations-such-extract-a-support-package-are-these-cmdlets-affected-when-i-move-to-the-new-azure-portal"></a>Vengono usati i cmdlet di Windows PowerShell per StorSimple nel dispositivo StorSimple per eseguire operazioni come l'estrazione di un pacchetto di supporto. Questi cmdlet risultano interessati dallo spostamento al nuovo portale di Azure?

No. Con la migrazione del servizio al nuovo portale di Azure, non dovrebbero esserci ripercussioni sui cmdlet di Windows PowerShell per StorSimple associati al dispositivo StorSimple locale (che a sua volta non è interessato dallo spostamento). È possibile continuare a usare questi cmdlet per creare un pacchetto di supporto senza problemi anche nel portale di Azure. Solo i cmdlet di PowerShell del modello di distribuzione classica di Azure sono interessati da questo spostamento.

## <a name="moving-storsimple-data-manager-service"></a>Spostamento del servizio Gestore dati StorSimple

### <a name="i-am-using-storsimple-data-manager-service-how-should-i-proceed-with-this-move"></a>È in uso il servizio Gestore dati StorSimple. Come occorre procedere con questo spostamento?

Se si usa il servizio Gestore dati StorSimple, è necessario spostare innanzitutto le istanze di Gestione dispositivi StorSimple nel portale di Azure. Al termine dello spostamento, è possibile creare nuove istanze del Gestore dati StorSimple nel portale di Azure. Le istanze del Gestore dati StorSimple create prima dello spostamento non funzionano.

Per altre informazioni sulla migrazione del servizio Gestione dispositivi StorSimple, vedere [Spostare un servizio nel portale di Azure](storsimple-8000-manage-service.md#move-a-service-to-azure-portal). Per altre informazioni sulla creazione del Gestore dati StorSimple, vedere [Creare un servizio StorSimple Data Manager](storsimple-data-manager-ui.md).

## <a name="miscellaneous"></a>Miscellaneous

### <a name="i-am-running-storsimple-snapshot-manager-for-my-8000-series-device-is-there-any-impact-on-storsimple-snapshot-manager-when-i-move-to-azure-portal"></a>È in esecuzione StorSimple Snapshot Manager per il dispositivo serie 8000. Ci sono ripercussioni su StorSimple Snapshot Manager in seguito alla migrazione al portale di Azure?

No. Non ci sono ripercussioni su StorSimple Snapshot Manager in seguito alla migrazione del servizio al portale di Azure. Il dispositivo e StorSimple Snapshot Manager continuano a funzionare come prima.

### <a name="can-i-rename-my-storsimple-device-volume-containers-or-volumes"></a>È possibile rinominare il dispositivo StorSimple, i contenitori dei volumi o i volumi?

No. Non è possibile rinominare i dispositivi, i volumi, i contenitori dei volumi o i criteri di backup nel portale di Azure.

## <a name="next-steps"></a>Passaggi successivi

Informazioni dettagliate su come [spostare il servizio Gestione dispositivi StorSimple nel portale di Azure](storsimple-8000-manage-service.md#move-a-service-to-azure-portal).



