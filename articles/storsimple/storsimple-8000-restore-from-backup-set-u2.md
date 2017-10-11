---
title: Ripristinare un volume dal backup in un dispositivo StorSimple serie 8000 | Microsoft Docs
description: Viene illustrato come utilizzare il catalogo di backup del servizio Gestione dispositivi StorSimple per ripristinare un volume StorSimple da un set di backup.
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
ms.date: 05/23/2017
ms.author: alkohli
ms.openlocfilehash: aff0710ead4f76bb80c38e2d88fe9cd3ce6a7b48
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="restore-a-storsimple-volume-from-a-backup-set"></a>Ripristinare un volume StorSimple da un set di backup

## <a name="overview"></a>Panoramica

In questa esercitazione viene illustrata l'operazione di ripristino eseguita su un dispositivo StorSimple serie 8000 usando un set di backup esistente. Usare il pannello **Catalogo di backup** pannello per ripristinare un volume da un backup locale o nel cloud. Il pannello **Catalogo di backup** visualizzati tutti i set di backup creati quando si eseguono backup manuali o automatizzati. L'operazione di ripristino da un set di backup porta immediatamente il volume online, mentre i dati vengono scaricati in background.

Come metodo alternativo per avviare il ripristino, passare a **Dispositivi > [Dispositivo] > Volumi**. Nel pannello **Volumi**, selezionare un volume, fare clic con il pulsante destro del mouse per richiamare il menu di scelta rapida e quindi selezionare **Ripristina**.

## <a name="before-you-restore"></a>Prima di avviare il ripristino

Prima di avviare un'operazione di ripristino, consultare le avvertenze seguenti:

* **È necessario portare offline il volume**: portare offline il volume sia nel dispositivo che nell'host prima di avviare l'operazione di ripristino. Anche se l'operazione di ripristino riporta automaticamente online il volume nel dispositivo, è necessario portare il dispositivo online manualmente nell'host. È possibile portare online il volume nell'host non appena il volume è online sul dispositivo (non è necessario attendere fino al completamento dell'operazione di ripristino). Per le procedure, vedere [Portare un volume offline](storsimple-8000-manage-volumes-u2.md#take-a-volume-offline).

* **Tipo di volume dopo il ripristino** : i volumi eliminati vengono ripristinati in base al tipo nello snapshot; ovvero i volumi aggiunti in locale vengono ripristinati come volumi aggiunti in locale e i volumi a livelli vengono ripristinati come volumi a livelli.

    Per i volumi esistenti, il tipo di uso corrente del volume sovrascrive il tipo che viene archiviato nello snapshot. Ad esempio, se si ripristina un volume da uno snapshot che è stato eseguito quando il tipo di volume era a livelli e tale tipo di volume è ora aggiunto in locale (a causa di un'operazione di conversione eseguita), il volume verrà ripristinato come volume aggiunto in locale. Analogamente, se un volume aggiunto in locale è stato espanso e successivamente ripristinato da uno snapshot meno recente eseguito quando il volume era di dimensioni inferiori, il volume ripristinato manterrà la dimensione espansa corrente.

    Non è possibile convertire un volume da un volume a livelli a un volume aggiunto in locale o da un volume aggiunto in locale a un volume a livelli mentre il volume è in fase di ripristino. Attendere il completamento dell'operazione di ripristino e quindi convertire il volume in un altro tipo. Per informazioni sulla conversione di un volume, vedere l'articolo relativo alla [modifica di un tipo di volume](storsimple-8000-manage-volumes-u2.md#change-the-volume-type). 

* **La dimensione del volume si riflette nel volume ripristinato**: si tratta di un aspetto importante se si intende ripristinare un volume aggiunto in locale che è stato eliminato (perché i volumi aggiunti in locale vengono sottoposti a provisioning completo). Verificare che sia disponibile spazio sufficiente prima di tentare di ripristinare un volume aggiunto in locale eliminato in precedenza.

* **Durante il ripristino non è possibile espandere un volume** : attendere il completamento dell'operazione di ripristino prima di cercare di espandere il volume. Per altre informazioni sull'espansione di un volume, vedere [Modificare un volume](storsimple-8000-manage-volumes-u2.md#modify-a-volume).

* **È possibile eseguire un backup mentre si sta ripristinando un volume locale** : per le procedure, vedere l'articolo dedicato all' [uso del servizio Gestione dispositivi StorSimple per gestire i criteri di backup](storsimple-8000-manage-backup-policies-u2.md).

* **È possibile annullare un'operazione di ripristino** : se si annulla il processo di ripristino, il volume verrà riportato allo stato in cui si trovava prima di avviare l'operazione di ripristino. Per le procedure, vedere [Annullare un processo](storsimple-8000-manage-jobs-u2.md#cancel-a-job).

## <a name="how-does-restore-work"></a>Funzionamento del ripristino

Per i dispositivi che eseguono la versione Update 4 o successiva, viene implementato un ripristino basato su mappa termica. Quando le richieste di accesso ai dati da parte dell'host raggiungono il dispositivo, queste richieste vengono rilevate e viene creata una mappa termica. Un'elevata frequenza delle richieste produce blocchi di dati con heat maggiore, mentre una frequenza minore si traduce in blocchi con heat minore. È necessario accedere ai dati almeno due volte perché vengano contrassegnati come _ad accesso frequente_. Anche un file modificato viene contrassegnato come _ad accesso frequente_. Dopo aver avviato il ripristino, viene eseguita l'attivazione proattiva dei dati in base alla mappa termica. Per le versioni precedenti ad Update 4, i dati vengono scaricati durante il ripristino in base al solo accesso.

Le avvertenze seguenti si applicano a ripristini basati su mappa termica:

* Il rilevamento basato su mappa termica è abilitato solo per i volumi a livelli, mentre i volumi aggiunti in locale non sono supportati.

* Il ripristino basato su mappa termica non è supportato quando si clona un volume in un altro dispositivo. 

* In presenza di un ripristino sul posto e se nel dispositivo esiste uno snapshot locale per il volume da ripristinare, la riattivazione non viene eseguita (perché i dati sono già disponibili in locale). 

* Per impostazione predefinita, durante il ripristino vengono avviati i processi di riattivazione, che riattivano in modo proattivo i dati in base alla mappa termica. 

Nella versione Update 4 è possibile usare i cmdlet di Windows PowerShell per eseguire una query sui processi di riattivazione in esecuzione, annullare un processo di riattivazione e ottenere lo stato del processo di riattivazione.

* `Get-HcsRehydrationJob`: questo cmdlet ottiene lo stato del processo di riattivazione. Viene generato un singolo processo di riattivazione per un volume.

* `Set-HcsRehydrationJob`: questo cmdlet permette di sospendere, arrestare e riprendere il processo di riattivazione, quando questo è in corso.

Per altre informazioni sui cmdlet di riattivazione, vedere le [informazioni di riferimento sui cmdlet di Windows PowerShell per StorSimple](https://technet.microsoft.com/library/dn688168.aspx).

Con la riattivazione automatica, le prestazioni di lettura temporanee sono in genere più elevate. L'effettiva entità dei miglioramenti dipende da diversi fattori, tra cui modello di accesso, varianza dei dati e tipo di dati. 

Per annullare un processo di riattivazione, è possibile usare il cmdlet di PowerShell. Se si vuole disabilitare in modo permanente i processi di riattivazione per tutti i ripristini successivi, [contattare il supporto tecnico Microsoft](storsimple-8000-contact-microsoft-support.md).

## <a name="how-to-use-the-backup-catalog"></a>Come utilizzare il catalogo di backup

Il pannello **Catalogo di backup** fornisce una query che consente di limitare la selezione del set di backup. È possibile filtrare i set di backup che vengono recuperati in base ai parametri seguenti:

* **Intervallo di tempo**: intervallo di data e ora di creazione del set di backup.
* **Dispositivo** : il dispositivo in cui è stato creato il set di backup.
* **Criterio di backup** o **Volume**: il criterio di backup o volume associato a questo set di backup.

I set di backup filtrati vengono quindi catalogati in base ai seguenti attributi:

* **Nome** : nome del criterio di backup o volume associato al set di backup.
* **Tipo** : i set di backup possono essere snapshot in locale o del cloud. Uno snapshot locale è un backup di tutti i dati di volume archiviati localmente sul dispositivo, mentre uno snapshot del cloud si riferisce al backup dei dati di volume che risiedono nel cloud. Gli snapshot in locale forniscono un accesso più rapido, mentre gli snapshot del cloud vengono scelti per la resilienza dei dati.
* **Dimensioni** : dimensione effettiva del set di backup.
* **Creato il** : data e ora di creazione dei backup. 
* **Volumi**: il numero di volumi associati al set di backup.
* **Avviato** : i backup possono essere avviati automaticamente in base a una pianificazione o manualmente dall'utente. Per pianificare i backup, è possibile utilizzare un criterio di backup. In alternativa, è possibile utilizzare l’opzione **Esegui backup** per eseguire un backup interattivo o su richiesta.

## <a name="how-to-restore-your-storsimple-volume-from-a-backup"></a>Come ripristinare il volume di StorSimple da un backup

È possibile usare il pannello **Catalogo di backup** per ripristinare il volume StorSimple da un backup specifico. Tenere presente che, effettuando il ripristino, lo stato di un volume torna a quello in cui si trovava al momento dell'esecuzione del backup. Tutti i dati aggiunti dopo l'operazione di backup andranno persi.

> [!WARNING]
> Il ripristino da un backup sostituisce i volumi esistenti dal backup. Ciò può comportare la perdita dei dati scritti dopo l'esecuzione del backup.


### <a name="to-restore-your-volume"></a>Per ripristinare il volume
1. Passare al servizio Gestione dispositivi StorSimple e fare clic su **Catalogo di backup**.

2. Procedura di selezione di un set di backup:
   
   1. Specificare l'intervallo di tempo.
   2. Selezionare un dispositivo appropriato.
   3. Nell'elenco a discesa, scegliere il volume o il criterio di backup per il backup che si desidera selezionare.
   4. Fare clic su **Applica** per eseguire la query.

    I backup associati al volume selezionato o al criterio di backup dovrebbero essere visualizzati nell'elenco dei set di backup.
   
    ![Elenco di set di backup](./media/storsimple-8000-restore-from-backup-set-u2/bucatalog.png)     
     
3. Espandere il set di backup per visualizzare i volumi associati. Questi volumi devono essere disconnessi nell'host e nel dispositivo prima di poterli ripristinare. Accedere ai volumi nel pannello **Volumi** del dispositivo, quindi attenersi alla procedura descritta in [Disattivare un volume](storsimple-8000-manage-volumes-u2.md#take-a-volume-offline) per disattivarli.
   
   > [!IMPORTANT]
   > Assicurarsi, in primo luogo, di aver disattivato tutti i volumi sull'host, prima di disattivarli sul dispositivo. Se non si eseguono i volumi offline nell'host, si potrebbe verificare un danneggiamento dei dati.
   
4. Tornare alla scheda **Catalogo di backup** e selezionare un set di backup. Fare clic con il pulsante destro del mouse e scegliere **Ripristina** dal menu di scelta rapida.

    ![Elenco di set di backup](./media/storsimple-8000-restore-from-backup-set-u2/restorebu1.png)

5. Verrà richiesto di confermare. Esaminare le informazioni di ripristino e quindi selezionare la casella di controllo di conferma.
   
    ![Pagina di conferma](./media/storsimple-8000-restore-from-backup-set-u2/restorebu2.png)

7.  Fare clic su **Ripristina**. In questo modo viene avviato un processo di ripristino che è possibile visualizzare accedendo alla pagina **Processi**.

    ![Pagina di conferma](./media/storsimple-8000-restore-from-backup-set-u2/restorebu5.png)

8. Dopo aver completato il ripristino, verificare che i contenuti dei volumi siano sostituiti dai volumi dal backup.


## <a name="if-the-restore-fails"></a>Se il ripristino ha esito negativo

Se l'operazione di ripristino ha esito negativo per qualsiasi motivo, si riceverà un avviso. Se questo si verifica, aggiornare l'elenco dei backup per verificare che il backup sia ancora valido. Se il backup è valido e si esegue il ripristino dal cloud, il problema potrebbe essere causato da problemi di connettività.

Per completare l'operazione di ripristino, portare offline il volume nell'host e ripetere l'operazione di ripristino. Si noti che eventuali modifiche eseguite sui dati del volume durante il processo di ripristino andranno perse.

## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come [gestire i volumi StorSimple](storsimple-8000-manage-volumes-u2.md).
* Informazioni su come [usare il servizio Gestione dispositivi StorSimple per gestire il dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

