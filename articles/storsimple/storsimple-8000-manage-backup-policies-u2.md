---
title: Gestire i criteri di backup di StorSimple serie 8000 | Microsoft Docs
description: Illustra come usare il servizio Gestione dispositivi StorSimple per creare e gestire backup manuali, pianificazioni di backup e conservazione dei backup nei dispositivi StorSimple serie 8000.
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
ms.workload: TBD
ms.date: 07/05/2017
ms.author: alkohli
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 569dbfdeb7dcd526cb5a54b487ea1bfb59b13cc6
ms.contentlocale: it-it
ms.lasthandoff: 07/08/2017


---
# <a name="use-the-storsimple-device-manager-service-in-azure-portal-to-manage-backup-policies"></a>Usare il servizio Gestione dispositivi StorSimple nel portale di Azure per gestire i criteri di backup


## <a name="overview"></a>Panoramica

Questa esercitazione illustra come usare il pannello **criteri di Backup** del servizio Gestione dispositivi StorSimple per controllare i processi di backup e le regole di conservazione dei backup per i volumi StorSimple. Viene inoltre descritto come eseguire un backup manuale.

Quando si esegue il backup di un volume, è possibile scegliere di creare uno snapshot locale o uno snapshot nel cloud. Se si esegue il backup di un volume aggiunto in locale, è consigliabile specificare uno snapshot nel cloud. Se si crea un numero elevato di snapshot locali di un volume aggiunto in locale e tali snapshot sono associati a un set di dati che dispone di molte varianze, si determinerà una situazione favorevole all'esaurimento rapido dello spazio locale. Se si sceglie di creare snapshot locali, è consigliabile creare meno snapshot giornalieri per eseguire il backup dello stato più recente, conservarli per un giorno e quindi eliminarli.

Quando si crea uno snapshot nel cloud di un volume aggiunto in locale, copiare solo i dati modificati nel cloud, in cui è deduplicato e compresso.

## <a name="the-backup-policy-blade"></a>Pannello Criteri di backup

Il pannello **Criteri di Backup** del dispositivo StorSimple consente di gestire i criteri di backup e pianificare gli snapshot in locale e nel cloud. I criteri di backup vengono usati per configurare le pianificazioni dei backup e la regole di conservazione dei backup per una raccolta di volumi. I criteri di backup consentono di creare uno snapshot di più volumi contemporaneamente. Questo significa che i backup creati con un criterio di backup saranno copie coerenti con l'arresto anomalo.

L'elenco tabulare dei criteri di backup consente anche di filtrare i criteri di backup esistenti in base a uno o più dei seguenti campi:

* **Nome criterio** : il nome associato al criterio. I diversi tipi di criteri includono:

  * Criteri pianificati, vengono creati esplicitamente dall'utente.
  * Criteri importati, che sono stati originariamente creati in Gestione Snapshot StorSimple. Hanno un tag che descrive l'host di gestione Snapshot StorSimple che i criteri sono stati importati da.

  > [!NOTE]
  > I criteri di backup automatici o predefiniti non sono più abilitati al momento della creazione del volume.

* **Ultimo backup completato** : la data e l'ora dell'ultimo backup riuscito è stato creato con questo criterio.

* **Backup successivo** : data e ora del successivo backup pianificato verrà avviato da questo criterio.

* **Volumi** – i volumi associati al criterio. Tutti i volumi associati a un criterio di backup vengono raggruppati quando vengono creati i backup.

* **Pianificazioni** – il numero di pianificazioni associate al criterio di backup.

Le operazioni più comuni che è possibile eseguire con i criteri di backup sono:

* Aggiungere un criterio di backup
* Aggiungere o modificare una pianificazione
* Aggiungere o rimuovere un volume
* Eliminare un criterio di backup
* Creazione di un backup manuale

## <a name="add-a-backup-policy"></a>Aggiungere un criterio di backup

Aggiungere un criterio di backup per la pianificazione automatica dei backup. Al volume creato per la prima volta non è associato alcun criterio di backup predefinito. È necessario aggiungere e assegnare un criterio di backup per proteggere i dati del volume.

Attenersi alla procedura seguente nel portale di Azure per aggiungere un criterio di backup per il dispositivo StorSimple. Dopo aver aggiunto i criteri, è possibile definire una pianificazione (vedere [Aggiungere o modificare una pianificazione](#add-or-modify-a-schedule)).

[!INCLUDE [storsimple-8000-add-backup-policy-u2](../../includes/storsimple-8000-add-backup-policy-u2.md)]

## <a name="add-or-modify-a-schedule"></a>Aggiungere o modificare una pianificazione

È possibile aggiungere o modificare una pianificazione che è collegata a un criterio di backup nel dispositivo StorSimple. Attenersi alla procedura seguente nel portale di Azure per aggiungere o modificare una pianificazione.

[!INCLUDE [storsimple-8000-add-modify-backup-schedule](../../includes/storsimple-8000-add-modify-backup-schedule-u2.md)]


## <a name="add-or-remove-a-volume"></a>Aggiungere o rimuovere un volume

È possibile aggiungere o rimuovere un volume assegnato a un criterio di backup nel dispositivo StorSimple. Attenersi alla procedura seguente nel portale di Azure per aggiungere o rimuovere un volume.

[!INCLUDE [storsimple-8000-add-volume-backup-policy-u2](../../includes/storsimple-8000-add-remove-volume-backup-policy-u2.md)]


## <a name="delete-a-backup-policy"></a>Eliminare un criterio di backup

Attenersi alla procedura seguente nel portale di Azure per eliminare un criterio di backup nel dispositivo StorSimple.

[!INCLUDE [storsimple-8000-delete-backup-policy](../../includes/storsimple-8000-delete-backup-policy.md)]

## <a name="take-a-manual-backup"></a>Creazione di un backup manuale

Attenersi alla procedura seguente nel portale di Azure per creare un backup manuale su richiesta per un singolo volume.

[!INCLUDE [storsimple-8000-create-manual-backup](../../includes/storsimple-8000-create-manual-backup.md)]

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sull'[utilizzo del servizio Gestione dispositivi StorSimple per la gestione del dispositivo StorSimple](storsimple-8000-manager-service-administration.md).


