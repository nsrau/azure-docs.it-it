---
title: Ripristinare un volume StorSimple dal backup | Microsoft Docs
description: Viene illustrato come utilizzare la pagina del catalogo di backup del servizio StorSimple Manager per ripristinare un volume StorSimple da un set di backup.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 6f289c39-96c7-4d57-b68a-4bc2e99aef9d
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: 76fa3dd8fa2f9775dc166686e699a8dd66399aff
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2017
---
# <a name="restore-a-storsimple-volume-from-a-backup-set-update-2"></a>Ripristinare un volume StorSimple da un set di backup (aggiornamento 2)
> [!NOTE]
> Il portale classico per StorSimple è deprecato. Le istanze di Gestione dispositivi StorSimple passeranno automaticamente al nuovo portale di Azure in base alla pianificazione per la funzionalità deprecata. Si riceveranno un messaggio di posta elettronica e una notifica del portale relativi a questo passaggio. Anche questo documento verrà ritirato a breve. Per la versione di questo articolo per il nuovo portale di Azure, vedere [Ripristinare un volume StorSimple da un set di backup (aggiornamento 2)](storsimple-8000-restore-from-backup-set-u2.md). Per domande relative al passaggio, vedere [Domande frequenti: spostamento nel portale di Azure](storsimple-8000-move-azure-portal-faq.md).

[!INCLUDE [storsimple-version-selector-restore-from-backup](../../includes/storsimple-version-selector-restore-from-backup.md)]

## <a name="overview"></a>Panoramica
Nella pagina **Catalogo di backup** vengono visualizzati tutti i set di backup creati quando si eseguono backup manuali o automatizzati. Usare questa pagina per elencare e gestire i backup, eseguire il ripristino da un set di backup o clonare un volume.

 ![Pagina catalogo di backup](./media/storsimple-restore-from-backup-set-u2/restore.png)

In questa esercitazione viene illustrato come utilizzare la pagina **Catalogo di backup** per ripristinare il dispositivo da un set di backup.

È possibile ripristinare un volume da un backup locale o cloud. In entrambi i casi, l'operazione di ripristino porta immediatamente il volume online, mentre i dati vengono scaricati in background. 

## <a name="before-you-restore"></a>Prima di avviare il ripristino
Prima di avviare un'operazione di ripristino, è necessario tenere presenti le indicazioni seguenti:

* **È necessario portare offline il volume**: portare offline il volume nell'host e nel dispositivo prima di avviare l'operazione di ripristino. Anche se l'operazione di ripristino riporta automaticamente online il volume nel dispositivo, è necessario portare il dispositivo online manualmente nell'host. È possibile portare online il volume nell'host non appena il volume è online sul dispositivo (non è necessario attendere fino al completamento dell'operazione di ripristino). Per le procedure, vedere [Portare un volume offline](storsimple-manage-volumes-u2.md#take-a-volume-offline).
* **Tipo di volume dopo il ripristino**: i volumi eliminati vengono ripristinati in base al tipo nello snapshot. I volumi aggiunti in locale vengono ripristinati come volumi aggiunti in locale, mentre i volumi a livelli vengono ripristinati come volumi a livelli.
  
    Per i volumi esistenti, il tipo di uso corrente del volume sovrascrive il tipo che viene archiviato nello snapshot. Se, ad esempio, si ripristina un volume da uno snapshot acquisito quando il tipo di volume era a livelli e questo tipo di volume è ora aggiunto in locale (a causa di un'operazione di conversione), il volume verrà ripristinato come volume aggiunto in locale. Analogamente, se un volume aggiunto in locale viene espanso e successivamente ripristinato da uno snapshot meno recente acquisito quando il volume aveva dimensioni minori, il volume ripristinato manterrà la dimensione espansa corrente.
  
    Non è possibile convertire un volume da volume a livelli a volume aggiunto in locale o _viceversa_ mentre è in corso il ripristino del volume. Attendere il completamento dell'operazione di ripristino e quindi convertire il volume in un altro tipo. Per informazioni sulla conversione di un volume, vedere l'articolo relativo alla [modifica di un tipo di volume](storsimple-manage-volumes-u2.md#change-the-volume-type). 
* **La dimensione del volume si riflette nel volume ripristinato**: si tratta di un aspetto importante se si intende ripristinare un volume aggiunto in locale che è stato eliminato (perché i volumi aggiunti in locale vengono sottoposti a provisioning completo). Verificare che sia disponibile spazio sufficiente prima di tentare di ripristinare un volume aggiunto in locale eliminato in precedenza. 
* **Durante il ripristino non è possibile espandere un volume** : attendere il completamento dell'operazione di ripristino prima di cercare di espandere il volume. Per altre informazioni sull'espansione di un volume, vedere [Modificare un volume](storsimple-manage-volumes-u2.md#modify-a-volume).
* **È possibile eseguire un backup mentre si sta ripristinando un volume locale** : per le procedure, vedere l'articolo dedicato all' [uso del servizio StorSimple Manager per gestire i criteri di backup](storsimple-manage-backup-policies.md).
* **È possibile annullare un'operazione di ripristino**: se si annulla il processo di ripristino, viene eseguito il rollback del volume allo stato in cui si trovava prima di avviare l'operazione di ripristino. Per le procedure, vedere [Annullare un processo](storsimple-manage-jobs-u2.md#cancel-a-job).

## <a name="how-does-restore-work"></a>Funzionamento del ripristino
Per i dispositivi che eseguono la versione Update 4 o successiva, viene implementato un ripristino basato su mappa termica. Quando le richieste di accesso ai dati da parte dell'host raggiungono il dispositivo, queste richieste vengono rilevate e viene creata una mappa termica. Un'elevata frequenza delle richieste produce blocchi di dati con heat maggiore, mentre una frequenza minore si traduce in blocchi con heat minore. È necessario accedere ai dati almeno due volte perché vengano contrassegnati come _ad accesso frequente_. Anche un file modificato viene contrassegnato come _ad accesso frequente_. Dopo aver avviato il ripristino, viene eseguita l'attivazione proattiva dei dati in base alla mappa termica. Per le versioni precedenti ad Update 4, i dati vengono scaricati durante il ripristino in base al solo accesso. 

Il rilevamento basato su mappa termica è abilitato solo per i volumi a livelli, mentre i volumi aggiunti in locale non sono supportati. Il ripristino basato su mappa termica non è supportato anche quando si clona un volume in un altro dispositivo. In presenza di un ripristino sul posto e se nel dispositivo esiste uno snapshot locale per il volume da ripristinare, la riattivazione non viene eseguita (perché i dati sono già disponibili in locale). Per impostazione predefinita, durante il ripristino vengono avviati i processi di riattivazione, che riattivano in modo proattivo i dati in base alla mappa termica. Nella versione Update 4 è possibile usare i cmdlet di Windows PowerShell per eseguire una query sui processi di riattivazione in esecuzione, annullare un processo di riattivazione e ottenere lo stato del processo di riattivazione.

* `Get-HcsRehydrationJob`: questo cmdlet ottiene lo stato del processo di riattivazione. Viene generato un singolo processo di riattivazione per un volume.
* `Set-HcsRehydrationJob`: questo cmdlet permette di sospendere, arrestare e riprendere il processo di riattivazione, quando questo è in corso. 

Per altre informazioni sui cmdlet di riattivazione, vedere le [informazioni di riferimento sui cmdlet di Windows PowerShell per StorSimple](https://technet.microsoft.com/library/dn688168.aspx).

Con la riattivazione automatica, le prestazioni di lettura temporanee sono in genere più elevate. L'effettiva entità dei miglioramenti dipende da diversi fattori, tra cui modello di accesso, varianza dei dati e tipo di dati. Per annullare un processo di riattivazione, è possibile usare il cmdlet di PowerShell. Se si vuole disabilitare in modo permanente i processi di riattivazione per tutti i ripristini successivi, contattare il supporto Microsoft.

## <a name="how-to-use-the-backup-catalog"></a>Come utilizzare il catalogo di backup
La pagina **Catalogo di backup** fornisce una query che consente di limitare la selezione del set di backup. È possibile filtrare i set di backup che vengono recuperati in base ai parametri seguenti:

* **Dispositivo** : il dispositivo in cui è stato creato il set di backup.
* **Criterio di backup** o **Volume**: il criterio di backup o volume associato a questo set di backup.
* **Da** e **A**: intervallo di data e ora di creazione del set di backup.

I set di backup filtrati vengono quindi catalogati in base ai seguenti attributi:

* **Nome** : nome del criterio di backup o volume associato al set di backup.
* **Dimensioni** : dimensione effettiva del set di backup.
* **Creato il** : data e ora di creazione dei backup. 
* **Tipo** : i set di backup possono essere snapshot in locale o del cloud. Uno snapshot locale è un backup di tutti i dati dei volumi archiviati localmente nel dispositivo. Per snapshot cloud si intende il backup dei dati dei volumi che si trovano nel cloud. Gli snapshot in locale forniscono un accesso più rapido, mentre gli snapshot del cloud vengono scelti per la resilienza dei dati.
* **Avviato da**: i backup possono essere avviati automaticamente in base a una pianificazione o manualmente dall'utente. Per pianificare i backup, è possibile utilizzare un criterio di backup. In alternativa, è possibile utilizzare l’opzione **Esegui backup** per eseguire un backup interattivo.

## <a name="how-to-restore-your-storsimple-volume-from-a-backup"></a>Come ripristinare il volume di StorSimple da un backup
È possibile usare la pagina **Catalogo di backup** per ripristinare il volume StorSimple da un backup specifico. Tenere presente, tuttavia, che il ripristino di un volume comporta il ripristino dello stato del volume al momento dell'esecuzione del backup. Tutti i dati aggiunti dopo l'operazione di backup andranno persi.

> [!WARNING]
> Il ripristino da un backup sostituisce i volumi esistenti dal backup. Ciò può comportare la perdita dei dati scritti dopo l'esecuzione del backup.
> 
> 

### <a name="to-restore-your-volume"></a>Per ripristinare il volume
1. Nella pagina del servizio StorSimple Manager, fare clic sulla scheda **Catalogo di backup** .
   
    ![Catalogo di backup](./media/storsimple-restore-from-backup-set-u2/restore.png)
2. Procedura di selezione di un set di backup:
   
   1. Selezionare un dispositivo appropriato.
   2. Nell'elenco a discesa, scegliere il volume o il criterio di backup per il backup che si desidera selezionare.
   3. Specificare l'intervallo di tempo.
   4. Fare clic sull'icona del segno di spunta  ![icona del segno di spunta](./media/storsimple-restore-from-backup-set-u2/HCS_CheckIcon.png) per eseguire la query.
      
      I backup associati al volume selezionato o al criterio di backup dovrebbero essere visualizzati nell'elenco dei set di backup.
3. Espandere il set di backup per visualizzare i volumi associati. Questi volumi devono essere disconnessi nell'host e nel dispositivo prima di poterli ripristinare. Accedere ai volumi sulla pagina **Contenitori di volumi** , quindi attenersi alla procedura descritta in [Disattivare un volume](storsimple-manage-volumes-u2.md#take-a-volume-offline) per disattivarli.
   
   > [!IMPORTANT]
   > Assicurarsi, in primo luogo, di aver disattivato tutti i volumi sull'host, prima di disattivarli sul dispositivo. Se non si eseguono i volumi offline nell'host, si potrebbe verificare un danneggiamento dei dati.
   > 
   > 
4. Tornare alla scheda **Catalogo di backup** e selezionare un set di backup.
5. Fare clic su **Ripristina** nella parte inferiore della pagina.
6. Viene chiesto di confermare l'operazione. Esaminare le informazioni di ripristino e quindi selezionare la casella di controllo di conferma.
   
    ![Pagina di conferma](./media/storsimple-restore-from-backup-set-u2/ConfirmRestore.png)
7. Fare clic sull'icona del segno di spunta ![icona del segno di spunta](./media/storsimple-restore-from-backup-set-u2/HCS_CheckIcon.png). Viene avviato un processo di ripristino. È possibile visualizzare il processo accedendo alla pagina **Processi**. 
8. Dopo aver completato il ripristino, è possibile verificare che i contenuti dei volumi siano sostituiti dai volumi dal backup.

![Video disponibile](./media/storsimple-restore-from-backup-set-u2/Video_icon.png) **Video disponibile**

Per guardare un video che illustra come è possibile usare le funzionalità di clonazione e ripristino in StorSimple per ripristinare file eliminati, fare clic [qui](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/).

## <a name="if-the-restore-fails"></a>Se il ripristino ha esito negativo
Se l'operazione di ripristino non riesce per qualsiasi motivo, viene visualizzato un avviso. Se questo si verifica, aggiornare l'elenco dei backup per verificare che il backup sia ancora valido. Se il backup è valido e si esegue il ripristino dal cloud, il problema potrebbe essere causato da problemi di connettività. 

Per completare l'operazione di ripristino, portare offline il volume nell'host e ripetere l'operazione di ripristino. Tutte le modifiche apportate ai dati del volume durante il processo di ripristino andranno perse.

## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come [gestire i volumi StorSimple](storsimple-manage-volumes-u2.md).
* Informazioni su come [usare il servizio StorSimple Manager per gestire il dispositivo StorSimple](storsimple-manager-service-administration.md).

