---
title: Stiamo spostando le immagini delle macchine virtuali (VM) nell'archiviazione su disco gestita in Azure Marketplace
description: Per fornire uno storage e un supporto più veloci e affidabili per le nuove funzionalità e funzionalità del marketplace, stiamo spostando le immagini delle macchine virtuali del marketplace nello spazio di archiviazione su disco gestito.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: 17b418ab82cc88b4368efe247b06c0c8bbb27453
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285113"
---
# <a name="were-moving-virtual-machine-vm-images-on-azure-marketplace-to-managed-disk-storage"></a>Stiamo spostando le immagini delle macchine virtuali (VM) in Azure Marketplace nell'archiviazione su disco gestita

Per fornire uno storage e un supporto più veloci e affidabili per le nuove funzionalità e funzionalità del marketplace, stiamo spostando le immagini delle macchine virtuali del marketplace nello spazio di archiviazione su disco gestito.

A partire dal 2 gennaio 2020, le immagini delle macchine virtuali verranno spostate nell'archiviazione su disco gestita in fasi. Nella prima fase, verranno spostate solo le immagini senza nuove distribuzioni o con macchine virtuali in esecuzione nei 90 giorni precedenti. Prima di spostare le immagini, invieremo un'e-mail per indire all'editore quali immagini verranno spostate e quando verranno spostate.

I publisher o gli utenti non devono intraprendere alcuna azione e gli utenti non saranno interessati. Le offerte del marketplace rimarranno disponibili e i clienti saranno comunque in grado di distribuire macchine virtuali gestite da queste immagini, durante e dopo lo spostamento.

Se avete domande, vi preghiamo di [contattarci](https://support.microsoft.com/supportforbusiness/productselection?sapId=48734891-ee9a-5d77-bf29-82bf8d8111ff).

## <a name="faqs"></a>Domande frequenti

### <a name="would-the-users-of-my-vm-images-experience-an-outage"></a>Gli utenti delle immagini della macchina virtuale subirebbero un'interruzione?

Gli utenti delle immagini della macchina virtuale non subiranno un'interruzione. 

Nella prima fase verranno spostate solo le immagini delle macchine virtuali senza macchine virtuali in esecuzione. Poiché non ci sono utenti per queste immagini, non ci sarà alcun impatto. Anche per le fasi successive, non ci sarà alcun impatto sugli utenti.

### <a name="how-long-does-it-take-for-the-process-to-complete"></a>Quanto tempo ci vuole per il completamento del processo?

Il completamento della migrazione potrebbe richiedere fino a 24 ore.

### <a name="do-i-need-to-take-any-action"></a>Devo intraprendere qualche azione?

No. Gli editori o gli utenti non devono intraprendere alcuna azione.

### <a name="do-i-have-to-update-my-system-to-call-the-cloud-portal-apis-in-a-different-way-after-they-are-moved-to-managed-disk-storage"></a>È necessario aggiornare il sistema per chiamare le API del portale cloud in modo diverso dopo essere state spostate nell'archiviazione su disco gestito?

No. Le chiamate API esistenti continueranno a funzionare.

### <a name="would-all-my-vm-images-be-moved-to-managed-disk-at-the-same-time"></a>Tutte le immagini della macchina virtuale verranno spostate contemporaneamente sul disco gestito?

Tutte le immagini della macchina virtuale verranno trasferite nello stesso giorno. Ti informeremo una volta che sono stati spostati.

### <a name="can-i-request-to-schedule-the-move-of-my-vm-images-to-a-later-time"></a>È possibile richiedere di pianificare lo spostamento delle immagini della macchina virtuale in un momento successivo?

Si consiglia di spostare le immagini alla data pianificata. Tuttavia, in caso di dubbi, contattaci per riprogrammare il trasferimento.

### <a name="can-i-publish-updates-to-my-vm-images-during-the-move"></a>È possibile pubblicare aggiornamenti alle immagini della macchina virtuale durante lo spostamento?

Non è possibile eseguire aggiornamenti alle immagini della macchina virtuale durante lo spostamento.

### <a name="will-the-publishing-process-change-after-my-vm-image-is-moved-to-managed-disk"></a>Il processo di pubblicazione cambierà dopo lo spostamento dell'immagine della macchina virtuale nel disco gestito?

No, il processo di pubblicazione rimarrà invariato. 

### <a name="can-the-publisher-move-their-offers-to-managed-disk"></a>L'editore può spostare le proprie offerte su Managed Disk?

No, gli editori non possono spostare le offerte su Disco gestito. Dovranno aspettare e le loro immagini verranno spostate automaticamente. Invieremo notifiche al publisher prima di apportare modifiche.
