---
title: Esercitazione sul backup di StorSimple Virtual Array | Microsoft Docs
description: Si illustra come eseguire il backup delle condivisioni e dei volumi di StorSimple Virtual Array.
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
ms.date: 06/07/2016
ms.author: alkohli

---
# Backup dell'array virtuale StorSimple
## Panoramica
Questa esercitazione si applica solo a Microsoft Azure StorSimple Virtual Array (noto anche come dispositivo virtuale locale StorSimple o dispositivo virtuale StorSimple) che esegue la versione di disponibilità generale (GA) di marzo 2016 o una versione successiva.

StorSimple Virtual Array è un dispositivo virtuale locale di archiviazione cloud ibrido che può essere configurato come file server o server iSCSI. Può creare backup, ripristinare da backup ed eseguire failover del dispositivo nel caso in cui sia necessario il ripristino di emergenza. Quando viene configurato come file server, consente anche il ripristino a livello di elemento. Questa esercitazione illustra come usare il portale di Azure classico o l'interfaccia utente web StorSimple per creare backup pianificati e manuali di StorSimple Virtual Array.

## Eseguire il backup di condivisioni e volumi
I backup garantiscono la protezione temporizzata, migliorano la recuperabilità e riducono al minimo i tempi di ripristino per le condivisioni e i volumi. È possibile eseguire il backup di una condivisione o di un volume sul dispositivo StorSimple in due modi: **pianificato** o **manuale**. Ognuno di questi metodi è descritto nelle sezioni seguenti.

> [!NOTE]
> In questa versione, i backup pianificati vengono creati secondo un criterio predefinito che esegue il backup ogni giorno in un orario specificato di tutte le condivisioni o di tutti i volumi nel dispositivo. Al momento non è possibile creare criteri personalizzati per i backup pianificati.
> 
> 

## Modificare la pianificazione dei backup
Il dispositivo virtuale StorSimple dispone di un criterio di backup predefinito che si avvia in un orario specifico (22.30) ed esegue il backup di tutte le condivisioni o di tutti i volumi nel dispositivo una volta al giorno. È possibile modificare l'ora di avvio del backup, ma la frequenza e il periodo di conservazione (che specificano il numero di backup da conservare) non possono essere modificati. Durante questi backup, viene eseguito il backup di tutto il dispositivo virtuale, quindi si consiglia di non pianificare backup nelle ore di punta.

Eseguire i passaggi seguenti nel [portale di Azure classico](https://manage.windowsazure.com/) per modificare l'ora di inizio del backup predefinita.

#### Per modificare l'ora di inizio del criterio di backup predefinito
1. Passare alla scheda **Configurazione** del dispositivo.
2. Nella sezione **Backup**, specificare l'ora di inizio per il backup giornaliero.
3. Fare clic su **Save**.

### Creazione di un backup manuale
Oltre ai backup pianificati, è possibile eseguire un backup manuale (su richiesta) in qualsiasi momento.

#### Per creare un backup manuale (su richiesta)
1. Passare alla scheda **Condivisioni** o alla scheda **Volumi**.
2. Nella parte inferiore della pagina, fare clic su **Esegui backup di tutto**. Si richiederà di confermare se si desidera eseguire il backup in questo momento. Fare clic sull'icona di spunta ![icona del segno di spunta](./media/storsimple-ova-backup/image3.png) per procedere con il backup.
   
    ![conferma backup](./media/storsimple-ova-backup/image4.png)
   
    Si riceverà una notifica di avvio del processo di backup.
   
    ![avvio di backup](./media/storsimple-ova-backup/image5.png)
   
    Si riceverà una notifica in cui si comunica che il processo è stato creato correttamente.
   
    ![processo di backup creato](./media/storsimple-ova-backup/image7.png)
3. Per tenere traccia dell'avanzamento del processo, fare clic su **Visualizza processo**.
4. Una volta completato il processo di backup, andare alla scheda **Catalogo di backup**. Il backup completato deve essere visualizzato.
   
    ![Backup completato](./media/storsimple-ova-backup/image8.png)
5. Impostare le selezioni filtro per il dispositivo appropriato, il criterio di backup e l'intervallo di tempo, quindi fare clic sull'icona di spunta ![icona del segno di spunta](./media/storsimple-ova-backup/image3.png).
   
    Il backup dovrebbe comparire nell'elenco dei set di backup che viene visualizzato nel catalogo.

## Visualizzare i backup esistenti
Eseguire i passaggi seguenti nel portale di Azure classico per visualizzare i backup esistenti.

#### Per visualizzare i backup esistenti
1. Nella pagina del servizio StorSimple Manager, fare clic sulla scheda **Catalogo di backup**.
2. Procedura di selezione di un set di backup:
   
   1. Selezionare il dispositivo.
   2. Nell'elenco a discesa, scegliere la condivisione o il volume per il backup che si desidera selezionare.
   3. Specificare l'intervallo di tempo.
   4. Fare clic sull'icona del segno di spunta ![](./media/storsimple-ova-backup/image3.png) per eseguire la query.
      
      I backup associati alla condivisione o al volume selezionati devono essere visualizzati nell'elenco dei set di backup.

![video\_icon](./media/storsimple-ova-backup/video_icon.png) **Video disponibile**

Guardare il video per scoprire come è possibile creare condivisioni, eseguire il backup di condivisioni e il ripristino dei dati in StorSimple Virtual Array.

> [!VIDEO https://channel9.msdn.com/Blogs/Windows-Azure/Use-the-StorSimple-Virtual-Array/player]
> 
> 

## Passaggi successivi
Informazioni su come [amministrare StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

<!---HONumber=AcomDC_0622_2016-->