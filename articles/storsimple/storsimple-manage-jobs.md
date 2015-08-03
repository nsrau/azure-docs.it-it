<properties 
   pageTitle="Consente di visualizzare e gestire i processi di StorSimple | Microsoft Azure"
   description="Descrive la pagina processi del servizio StorSimple Manager e come utilizzarla."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carolz"
   editor=""/>
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="07/20/2015"
   ms.author="alkohli" />

# Consente di visualizzare e gestire i processi di StorSimple

## Panoramica

La pagina**processi**fornisce un unico portale centralizzato per la visualizzazione e la gestione di processi avviati sui dispositivi connessi al servizio StorSimple Manager. È possibile visualizzare i processi pianificati, in esecuzione, completati e non riusciti per più dispositivi. I risultati vengono presentati in un formato tabulare.

![Pagina dei processi](./media/storsimple-manage-jobs/HCS_JobsPage.png)

È possibile trovare rapidamente i processi desiderati filtrando i campi, ad esempio:

- **Stato**– i processi possono essere in esecuzione, pianificati, non riusciti, completati, in fase di annullamento o annullati.

- **Tipo**– i processi possono essere creati come risultato di un backup su richiesta o pianificato (* * eseguire Backup * *), di una clonazione, di un ripristino del dispositivo o di un'operazione di aggiornamento.

- **Dispositivi**: i processi vengono avviati in un determinato dispositivo connesso al servizio.

- **Da e a**: i processi possono essere filtrati in base all'intervallo di data e ora.

I processi filtrati vengono quindi elaborati in base ai seguenti attributi:

- **Tipo**: Backup, clonazione, ripristino, failover o aggiornamento.

- **Stato**: in esecuzione, pianificato, non è riuscito, completato, in fase di annullamento o annullato.

- **Entità**: I processi possono essere associati a un volume, un criterio di backup o un dispositivo. Un processo di clonazione è associato a un volume, mentre un processo di backup pianificato è associato a un criterio di backup. Viene creato un processo di dispositivo a causa di un ripristino di emergenza (DR) o di un'operazione di ripristino.

- **Dispositivo**: il nome del dispositivo su cui è stato avviato il processo.

- **Avviato su**: l'ora di inizio del processo.

- **Stato di avanzamento**: la percentuale di completamento di un processo in esecuzione. Per un processo completato deve sempre essere 100%.

L'elenco dei processi viene aggiornato ogni 30 secondi.

In questa pagina è possibile eseguire le seguenti azioni relative ai processi:

- Visualizza i dettagli dei processi

- Annullare un processo

## Visualizza i dettagli dei processi

Eseguire la procedura seguente per visualizzare i dettagli di qualsiasi processo.

#### Per visualizzare i dettagli dei processi

1. Nella pagina**processi**, visualizzare i processi desiderati eseguendo una query con filtri appropriati. È possibile cercare processi completati, in esecuzione o annullati.

2. Selezionare un processo.

3. Nella parte inferiore della pagina, fare clic su **Dettagli**.

4. Nella finestra di dialogo**Dettagli dei processi di Backup**è possibile visualizzare la stato, i dettagli, le statistiche temporali e le statistiche sui dati.

## Annullare un processo

Eseguire la procedura seguente per annullare un processo in esecuzione.

### Per annullare un processo

1. Nella pagina**processi**, visualizzare i processi in esecuzione che si desidera annullare eseguendo una query con filtri appropriati.

1. Selezionare il processo.

1. Nella parte inferiore della pagina fare clic su **Annulla**.

1. Alla richiesta di conferma fare clic su **Sì**.

Questo processo ora viene annullato.

## Passaggi successivi

[Informazioni su come gestire i criteri di backup di StorSimple](storsimple-manage-backup-policies.md).

<!---HONumber=July15_HO4-->