---
title: Consente di visualizzare e gestire i processi di StorSimple | Microsoft Docs
description: Descrive la pagina relativa ai processi del servizio StorSimple Manager e come utilizzarlo per tenere traccia dei processi di backup recenti, correnti e pianificati.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''

ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/17/2016
ms.author: alkohli

---
# Utilizzare il servizio StorSimple Manager per visualizzare e gestire i processi di StorSimple (aggiornamento 2)
[!INCLUDE [storsimple-version-selector-manage-jobs](../../includes/storsimple-version-selector-manage-jobs.md)]

## Overview
La pagina**processi**fornisce un unico portale centralizzato per la visualizzazione e la gestione di processi avviati sui dispositivi connessi al servizio StorSimple Manager. È possibile visualizzare i processi pianificati, in esecuzione, completati, annullati e non riusciti per più dispositivi. I risultati vengono presentati in un formato tabulare.

![Pagina dei processi](./media/storsimple-manage-jobs-u2/jobs.png)

È possibile trovare rapidamente i processi desiderati filtrando i campi, ad esempio:

* **Stato**: i processi possono essere in esecuzione, completati, annullati, non riusciti, in fase di annullamento o completati con errori.
* **Da e a**: i processi possono essere filtrati in base all'intervallo di data e ora.
* **Tipo** – il tipo di processo può essere backup, backup manuale, ripristino, clonazione, failover del dispositivo, creazione di un volume aggiunto in locale, modifica del volume, aggiornamento, pacchetto di supporto o provisioning del dispositivo virtuale.
* **Dispositivi**: i processi vengono avviati in un determinato dispositivo connesso al servizio. I processi filtrati vengono quindi elaborati in base ai seguenti attributi:
  
  * **Tipo** : backup, backup manuale, ripristino, clonazione, failover del dispositivo, creazione di un volume aggiunto in locale, modifica del volume, aggiornamento, pacchetto di supporto o provisioning del dispositivo virtuale.
  * **Stato**: in esecuzione, completati, annullati, non riusciti, in fase di annullamento o completati con errori.
  * **Entità**: i processi possono essere associati a un volume, un criterio di backup o un dispositivo. Ad esempio, un processo di clonazione è associato a un volume, mentre un processo di backup pianificato è associato a un criterio di backup. Viene creato un processo di dispositivo a causa di un ripristino di emergenza (DR) o di un'operazione di ripristino.
  * **Dispositivo**: il nome del dispositivo su cui è stato avviato il processo.
  * **Avviato alle**: l'ora di inizio del processo.
  * **Stato di avanzamento**: la percentuale di completamento di un processo in esecuzione. Per un processo completato deve sempre essere 100%.

L'elenco dei processi viene aggiornato ogni 30 secondi.

In questa pagina è possibile eseguire le seguenti azioni relative ai processi:

* Visualizza i dettagli dei processi
* Annullare un processo

## Visualizza i dettagli dei processi
Eseguire la procedura seguente per visualizzare i dettagli di qualsiasi processo.

#### Per visualizzare i dettagli dei processi
1. Nella pagina**Processi**, visualizzare i processi desiderati eseguendo una query con filtri appropriati. È possibile cercare processi completati, in esecuzione o annullati.
2. Selezionare un processo.
3. Nella parte inferiore della pagina, fare clic su **Dettagli**.
4. Nella finestra di dialogo **Dettagli del processo di Backup** è possibile visualizzare la stato, i dettagli, le statistiche temporali e le statistiche sui dati.
   
    ![Pagina dettagli del processo](./media/storsimple-manage-jobs-u2/JobDetails.png)

## Annullare un processo
Eseguire la procedura seguente per annullare un processo in esecuzione.

> [!NOTE]
> Alcuni processi, ad esempio la modifica di un volume per cambiare il tipo di volume o espandere un volume, non possono essere annullati.
> 
> 

### Per annullare un processo
1. Nella pagina **Processi**, visualizzare i processi in esecuzione che si desidera annullare eseguendo una query con filtri appropriati.
2. Selezionare il processo.
3. Nella parte inferiore della pagina fare clic su **Annulla**.
4. Alla richiesta di conferma fare clic su **Sì**.

Questo processo ora viene annullato.

## Passaggi successivi
* Informazioni su come [gestire i criteri di backup di StorSimple](storsimple-manage-backup-policies.md).
* Informazioni su come [utilizzare il servizio StorSimple Manager per amministrare il dispositivo StorSimple](storsimple-manager-service-administration.md).

<!---HONumber=AcomDC_0817_2016-->