<properties 
   pageTitle="Gestire i criteri di backup di StorSimple | Microsoft Azure"
   description="Viene illustrato come è possibile utilizzare il servizio StorSimple Manager per creare e gestire processi di backup e pianificazioni di backup."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="adinah"
   editor=""/>
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="06/17/2015"
   ms.author="v-sharos"/>

# Gestire i criteri di backup di StorSimple

## Panoramica

In questa esercitazione viene illustrato come utilizzare il servizio StorSimple Manager **criteri di Backup** pagina per controllare i processi di backup e memorizzazione dei backup per i volumi StorSimple. Viene inoltre descritto come eseguire un backup manuale.

Il **criteri di Backup** pagina consente di gestire i criteri di backup e pianificare locale e gli snapshot cloud. \(Criteri di backup vengono utilizzati per configurare pianificazioni di backup e memorizzazione dei backup per un insieme di volumi\). I criteri di backup consentono di creare uno snapshot di più volumi contemporaneamente. Questo significa che i backup creati con un criterio di backup saranno copie coerenti con l'arresto anomalo. Questa pagina vengono elencati i criteri di backup, i relativi tipi, i volumi associati, il numero di backup conservati e l'opzione per abilitare questi criteri.

Il **criteri di Backup** pagina consente inoltre di filtrare i criteri di backup esistenti da una o più dei seguenti campi:

- **Nome criterio** : il nome associato al criterio. I diversi tipi di criteri includono:

   - Criteri pianificati, vengono creati esplicitamente dall'utente.
   - Criteri automatici, che vengono creati quando il backup predefinito per questa opzione è stato abilitato al momento della creazione del volume. Questi criteri sono denominati VolumeName\_Default dove nome Volume si riferisce al nome del volume StorSimple configurato dall'utente nel portale di gestione. I criteri automatici generare gli snapshot cloud giornalieri a partire da 22:30 ora del dispositivo.
   - Criteri importati, che sono stati originariamente creati in Gestione Snapshot StorSimple. Hanno un tag che descrive l'host di gestione Snapshot StorSimple che i criteri sono stati importati da.

- **Volumi** – i volumi associati al criterio. Tutti i volumi associati a un criterio di backup vengono raggruppati quando vengono creati i backup.

- **Ultimo backup completato** : la data e l'ora dell'ultimo backup riuscito è stato creato con questo criterio.

- **Backup successivo** : data e ora del successivo backup pianificato verrà avviato da questo criterio.

- **Pianificazioni** – il numero di pianificazioni associate al criterio di backup.

Le operazioni utilizzate di frequente che è possibile eseguire da questa pagina sono:

- Aggiungere un criterio di backup 
- Aggiungere o modificare una pianificazione 
- Eliminare un criterio di backup 
- Creazione di un backup manuale 
- Creare un criterio di backup personalizzato con più volumi e pianificazioni 

## Aggiungere un criterio di backup

Aggiungere un criterio di backup per la pianificazione automatica dei backup. Eseguire i passaggi seguenti nel portale di gestione per aggiungere un criterio di backup per il dispositivo StorSimple. Dopo aver aggiunto i criteri, è possibile definire una pianificazione \(vedere [aggiungere o modificare una pianificazione](#add-or-modify-a-schedule).

[AZURE.INCLUDE [storsimple-aggiungere-backup-policy](../../includes/storsimple-add-backup-policy.md)]


## Aggiungere o modificare una pianificazione

È possibile aggiungere o modificare una pianificazione che è collegata a un criterio di backup nel dispositivo StorSimple. Eseguire i passaggi seguenti nel portale di gestione per creare un backup pianificato.

[AZURE.INCLUDE [storsimple-aggiungere-modifica-backup-pianificazione](../../includes/storsimple-add-modify-backup-schedule.md)]

## Eliminare un criterio di backup

Eseguire i passaggi seguenti nel portale di gestione per eliminare un criterio di backup nel dispositivo StorSimple.

[AZURE.INCLUDE [eliminazione di storsimple-criteri di backup](../../includes/storsimple-delete-backup-policy.md)]


## Creazione di un backup manuale

Eseguire i passaggi seguenti nel portale di gestione per creare un backup manuale su richiesta per un singolo volume nel dispositivo StorSimple.

[AZURE.INCLUDE [storsimple-create-manual-backup](../../includes/storsimple-create-manual-backup.md)]

## Creare un criterio di backup personalizzato con più volumi e pianificazioni

Eseguire i passaggi seguenti nel portale di gestione per creare un criterio di backup personalizzato che dispone di più volumi e pianificazioni.

[AZURE.INCLUDE [storsimple-crea-custom--criteri di backup](../../includes/storsimple-create-custom-backup-policy.md)]


## Passaggi successivi

Informazioni su come gestire i backup di StorSimple con il [Gestione Snapshot StorSimple](https://msdn.microsoft.com/library/azure/dn772365.aspx).

<!---HONumber=58_postMigration-->