---
title: Gestire i criteri di backup di StorSimple | Microsoft Docs
description: Viene illustrato come è possibile utilizzare il servizio StorSimple Manager per creare e gestire backup manuali, pianificazioni di backup e conservazione dei backup.
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: ''

ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/10/2016
ms.author: v-sharos

---
# Per gestire i criteri di backup è possibile usare il servizio StorSimple Manager (aggiornamento 2)
[!INCLUDE [storsimple-version-selector-manage-backup-policies](../../includes/storsimple-version-selector-manage-backup-policies.md)]

## Panoramica
In questa esercitazione viene illustrato come utilizzare il servizio StorSimple Manager **criteri di Backup** pagina per controllare i processi di backup e memorizzazione dei backup per i volumi StorSimple. Viene inoltre descritto come eseguire un backup manuale.

Quando si esegue il backup di un volume, è possibile scegliere di creare uno snapshot locale o uno snapshot nel cloud. Se si esegue il backup di un volume aggiunto in locale, è consigliabile specificare uno snapshot nel cloud. Se si crea un numero elevato di snapshot locali di un volume aggiunto in locale e tali snapshot sono associati a un set di dati che dispone di molte varianze, si determinerà una situazione favorevole all'esaurimento rapido dello spazio locale. Se si sceglie di creare snapshot locali, è consigliabile creare meno snapshot giornalieri per eseguire il backup dello stato più recente, conservarli per un giorno e quindi eliminarli.

Quando si crea uno snapshot nel cloud di un volume aggiunto in locale, copiare solo i dati modificati nel cloud, in cui è deduplicato e compresso.

## La pagina Criteri di Backup
Il **criteri di Backup** pagina consente di gestire i criteri di backup e pianificare locale e gli snapshot cloud. (Criteri di backup vengono utilizzati per configurare pianificazioni di backup e memorizzazione dei backup per un insieme di volumi). I criteri di backup consentono di creare uno snapshot di più volumi contemporaneamente. Questo significa che i backup creati con un criterio di backup saranno copie coerenti con l'arresto anomalo. La pagina **Criteri di backup**elenca i criteri di backup, i relativi tipi, i volumi associati, il numero di backup conservati e l'opzione per abilitare questi criteri.

Il **criteri di Backup** pagina consente inoltre di filtrare i criteri di backup esistenti da una o più dei seguenti campi:

* **Nome criterio** : il nome associato al criterio. I diversi tipi di criteri includono:
  
  * Criteri pianificati, vengono creati esplicitamente dall'utente.
  * Criteri automatici, che vengono creati quando il backup predefinito per questa opzione è stato abilitato al momento della creazione del volume. Questi criteri sono denominati *VolumeName*\_Default dove nome *VolumeName* si riferisce al nome del volume StorSimple configurato dall'utente nel portale di Azure classico. I criteri automatici generare gli snapshot cloud giornalieri a partire da 22:30 ora del dispositivo.
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

## Aggiungere un criterio di backup
Aggiungere un criterio di backup per la pianificazione automatica dei backup. Eseguire i passaggi seguenti nel portale di Azure classico per aggiungere un criterio di backup per il dispositivo StorSimple. Dopo aver aggiunto i criteri, è possibile definire una pianificazione (vedere [Aggiungere o modificare una pianificazione](#add-or-modify-a-schedule)).

[!INCLUDE [storsimple-add-backup-policy-u2](../../includes/storsimple-add-backup-policy-u2.md)]

![Video disponibile](./media/storsimple-manage-backup-policies-u2/Video_icon.png) **Video disponibile**

Per guardare un video che illustra come creare un criterio di backup cloud o locale, fare clic [qui](https://azure.microsoft.com/documentation/videos/create-storsimple-backup-policies/).

## Aggiungere o modificare una pianificazione
È possibile aggiungere o modificare una pianificazione che è collegata a un criterio di backup nel dispositivo StorSimple. Eseguire i passaggi seguenti nel portale di Azure classico per aggiungere o modificare un backup pianificato.

[!INCLUDE [storsimple-aggiungere-modifica-backup-pianificazione](../../includes/storsimple-add-modify-backup-schedule-u2.md)]

## Eliminare un criterio di backup
Eseguire i passaggi seguenti nel portale di Azure classico per eliminare un criterio di backup nel dispositivo StorSimple.

[!INCLUDE [eliminazione di storsimple-criteri di backup](../../includes/storsimple-delete-backup-policy.md)]

## Creazione di un backup manuale
Eseguire i passaggi seguenti nel portale di Azure classico per creare un backup manuale su richiesta per un singolo volume.

[!INCLUDE [storsimple-create-manual-backup](../../includes/storsimple-create-manual-backup.md)]

## Creare un criterio di backup personalizzato con più volumi e pianificazioni
Eseguire i passaggi seguenti nel portale di Azure classico per creare un criterio di backup personalizzato che dispone di più volumi e pianificazioni.

[!INCLUDE [storsimple-crea-custom--criteri di backup](../../includes/storsimple-create-custom-backup-policy-u2.md)]

## Passaggi successivi
Ulteriori informazioni sull’[utilizzo del servizio StorSimple Manager per amministrare il dispositivo StorSimple](storsimple-manager-service-administration.md).

<!---HONumber=AcomDC_0511_2016-->