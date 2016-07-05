<properties 
   pageTitle="Visualizzare e gestire i processi dell'array virtuale StorSimple | Microsoft Azure"
   description="Descrive la pagina Processi del servizio StorSimple Manager e illustra come utilizzarla per tenere traccia dei processi recenti e correnti dell'array virtuale StorSimple."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor=""/>
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="06/07/2016"
   ms.author="alkohli" />

# Usare il servizio StorSimple Manager per visualizzare i processi per l'array virtuale StorSimple

## Panoramica

La pagina **Processi** fornisce un unico portale centralizzato per la visualizzazione e la gestione dei processi avviati su array virtuali (noti anche come dispositivi virtuali locali) connessi al servizio StorSimple Manager. È possibile visualizzare i processi in esecuzione, completati e non riusciti per più dispositivi virtuali. I risultati vengono presentati in un formato tabulare.

![Pagina dei processi](./media/storsimple-ova-manage-jobs/ovajobs1.png)

È possibile trovare rapidamente i processi desiderati filtrando i campi, ad esempio:

- **Stato**: è possibile cercare tutti i processi in esecuzione, completati o non riusciti.
- **Da e a**: i processi possono essere filtrati in base all'intervallo di data e ora.
- **Tipo**: il tipo di processo può essere tutto, backup, ripristino, failover, download aggiornamenti o installazione aggiornamenti.
- **Dispositivi**: i processi vengono avviati in un dispositivo specifico connesso al servizio. I processi filtrati vengono quindi elaborati in base ai seguenti attributi:

    - **Tipo**: il tipo di processo può essere tutto, backup, ripristino, failover, download aggiornamenti o installazione aggiornamenti.

    - **Stato**: lo stato può essere tutto, in esecuzione, completato o non riuscito.

    - **Entità**: i processi possono essere associati a un volume, una condivisione o un dispositivo.

    - **Dispositivo**: il nome del dispositivo su cui è stato avviato il processo.

    - **Avviato alle**: l'ora di inizio del processo.

    - **Stato di avanzamento**: la percentuale di completamento di un processo in esecuzione. Per un processo completato deve sempre essere 100%.

L'elenco dei processi viene aggiornato ogni 30 secondi.

## Visualizza i dettagli dei processi

Eseguire la procedura seguente per visualizzare i dettagli di qualsiasi processo.

#### Per visualizzare i dettagli dei processi

1. Nella pagina**Processi**, visualizzare i processi desiderati eseguendo una query con filtri appropriati. È possibile cercare processi completati o in esecuzione.

2. Selezionare un processo nell'elenco tabulare dei processi.

3. Nella parte inferiore della pagina, fare clic su **Dettagli**.

4. Nella finestra di dialogo **Dettagli** è possibile visualizzare statistiche temporali, sullo stato e sui dettagli. La figura seguente mostra un esempio della finestra di dialogo relativa ai **dettagli del processo di backup**:
 
    ![Pagina dettagli del processo](./media/storsimple-ova-manage-jobs/ovajobs2.png)

#### Errori di processo quando la macchina virtuale viene sospesa nell'hypervisor

Se un processo è in corso nell'array virtuale StorSimple e il dispositivo (macchina virtuale con provisioning in hypervisor) viene sospeso per più di 15 minuti, il processo ha esito negativo. Ciò è dovuto al fatto che l'ora dell'array virtuale StorSimple non è più sincronizzata con l'ora di Microsoft Azure. Nella schermata seguente è illustrato un esempio di errore di un processo di ripristino.

![Il processo di ripristino non è riuscito.](./media/storsimple-ova-manage-jobs/restorejobfailure.png)

Questi errori possono verificarsi con processi di backup, ripristino, aggiornamento e failover. Se il provisioning della macchina virtuale viene eseguito in Hyper-V, l'ora della macchina virtuale alla fine si sincronizzerà con l'hypervisor. Quando ciò accade, è possibile riavviare il processo.

## Passaggi successivi

[Informazioni su come usare l'interfaccia utente Web locale per amministrare l'array virtuale StorSimple](storsimple-ova-web-ui-admin.md).

<!---HONumber=AcomDC_0622_2016-->