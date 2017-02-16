---
title: Gestire i criteri di backup di StorSimple | Microsoft Docs
description: "Viene illustrato come è possibile utilizzare il servizio StorSimple Manager per creare e gestire backup manuali, pianificazioni di backup e conservazione dei backup."
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: d1834bc8-d520-4463-82ae-3b32fabd021c
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/10/2016
ms.author: v-sharos
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: b43d5d867d6245a807f6eff5ed3a0c3df10c0dd7


---
# <a name="use-the-storsimple-manager-service-to-manage-backup-policies"></a>Per gestire i criteri di backup, è possibile usare il servizio StorSimple Manager.
[!INCLUDE [storsimple-version-selector-manage-backup-policies](../../includes/storsimple-version-selector-manage-backup-policies.md)]

## <a name="overview"></a>Panoramica
In questa esercitazione viene illustrato come utilizzare il servizio StorSimple Manager **criteri di Backup** pagina per controllare i processi di backup e memorizzazione dei backup per i volumi StorSimple. Viene inoltre descritto come eseguire un backup manuale.

Il **criteri di Backup** pagina consente di gestire i criteri di backup e pianificare locale e gli snapshot cloud. (Criteri di backup vengono utilizzati per configurare pianificazioni di backup e memorizzazione dei backup per un insieme di volumi). I criteri di backup consentono di creare uno snapshot di più volumi contemporaneamente. Questo significa che i backup creati con un criterio di backup saranno copie coerenti con l'arresto anomalo. Questa pagina vengono elencati i criteri di backup, i relativi tipi, i volumi associati, il numero di backup conservati e l'opzione per abilitare questi criteri.

Il **criteri di Backup** pagina consente inoltre di filtrare i criteri di backup esistenti da una o più dei seguenti campi:

* **Nome criterio** : il nome associato al criterio. I diversi tipi di criteri includono:
  
  * Criteri pianificati, vengono creati esplicitamente dall'utente.
  * Criteri automatici, che vengono creati quando il backup predefinito per questa opzione è stato abilitato al momento della creazione del volume. Questi criteri sono denominati VolumeName_Default dove nome Volume si riferisce al nome del volume StorSimple configurato dall'utente nel portale di Azure classico. I criteri automatici generare gli snapshot cloud giornalieri a partire da 22:30 ora del dispositivo.
  * Criteri importati, che sono stati originariamente creati in Gestione Snapshot StorSimple. Hanno un tag che descrive l'host di gestione Snapshot StorSimple che i criteri sono stati importati da.
* **Volumi** – i volumi associati al criterio. Tutti i volumi associati a un criterio di backup vengono raggruppati quando vengono creati i backup.
* **Ultimo backup completato** : la data e l'ora dell'ultimo backup riuscito è stato creato con questo criterio.
* **Backup successivo** : data e ora del successivo backup pianificato verrà avviato da questo criterio.
* **Pianificazioni** – il numero di pianificazioni associate al criterio di backup.

Le operazioni utilizzate di frequente che è possibile eseguire da questa pagina sono:

* Aggiungere un criterio di backup 
* Aggiungere o modificare una pianificazione 
* Eliminare un criterio di backup 
* Creazione di un backup manuale 
* Creare un criterio di backup personalizzato con più volumi e pianificazioni 

## <a name="add-a-backup-policy"></a>Aggiungere un criterio di backup
Aggiungere un criterio di backup per la pianificazione automatica dei backup. Eseguire i passaggi seguenti nel portale di Azure classico per aggiungere un criterio di backup per il dispositivo StorSimple. Dopo aver aggiunto i criteri, è possibile definire una pianificazione (vedere [Aggiungere o modificare una pianificazione](#add-or-modify-a-schedule)).

[!INCLUDE [storsimple-add-backup-policy](../../includes/storsimple-add-backup-policy.md)]

![Video disponibile](./media/storsimple-manage-backup-policies/Video_icon.png) **Video disponibile**

Per guardare un video che illustra come creare un criterio di backup cloud o locale, fare clic [qui](https://azure.microsoft.com/documentation/videos/create-storsimple-backup-policies/).

## <a name="add-or-modify-a-schedule"></a>Aggiungere o modificare una pianificazione
È possibile aggiungere o modificare una pianificazione che è collegata a un criterio di backup nel dispositivo StorSimple. Eseguire i passaggi seguenti nel portale di Azure classico per aggiungere o modificare un backup pianificato.

[!INCLUDE [storsimple-add-modify-backup-schedule](../../includes/storsimple-add-modify-backup-schedule.md)]

## <a name="delete-a-backup-policy"></a>Eliminare un criterio di backup
Eseguire i passaggi seguenti nel portale di Azure classico per eliminare un criterio di backup nel dispositivo StorSimple.

[!INCLUDE [storsimple-delete-backup-policy](../../includes/storsimple-delete-backup-policy.md)]

## <a name="take-a-manual-backup"></a>Creazione di un backup manuale
Eseguire i passaggi seguenti nel portale di Azure classico per creare un backup manuale su richiesta per un singolo volume.

[!INCLUDE [storsimple-create-manual-backup](../../includes/storsimple-create-manual-backup.md)]

## <a name="create-a-custom-backup-policy-with-multiple-volumes-and-schedules"></a>Creare un criterio di backup personalizzato con più volumi e pianificazioni
Eseguire i passaggi seguenti nel portale di Azure classico per creare un criterio di backup personalizzato che dispone di più volumi e pianificazioni.

[!INCLUDE [storsimple-create-custom-backup-policy](../../includes/storsimple-create-custom-backup-policy.md)]

## <a name="next-steps"></a>Passaggi successivi
Ulteriori informazioni sull’ [utilizzo del servizio StorSimple Manager per amministrare il dispositivo StorSimple](storsimple-manager-service-administration.md).




<!--HONumber=Nov16_HO3-->


