---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 48ce594fa5f4b736e69b5b4ef7a10011fe8031fa
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027220"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>Per installare gli aggiornamenti tramite il portale di Azure

1. Passare a Gestione dispositivi StorSimple e selezionare **Dispositivi**. Dall'elenco dei dispositivi connessi al servizio selezionare e fare clic sul dispositivo che si vuole aggiornare. 

    ![In recente, Device Manager MySSDevManager è evidenziato e selezionato, i dispositivi vengono evidenziati e selezionati e il dispositivo MYSSIS1103 è evidenziato e selezionato.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate1m.png) 

2. Nel pannello **Impostazioni** fare clic su **Aggiornamenti del dispositivo**. 

    ![Vengono visualizzate le informazioni relative a MYSSIS1103. In impostazioni gli aggiornamenti del dispositivo sono evidenziati.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate2m.png)  

3. Se sono disponibili aggiornamenti software, verrà visualizzato un messaggio. Per cercare gli aggiornamenti è anche possibile fare clic su **Analizza**.

    ![Il riquadro aggiornamenti del dispositivo indica che sono disponibili nuovi aggiornamenti e "ultima analisi/18/01/2017 2:42 PM/versione software/10.0.10288.0". La versione è evidenziata.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate3m1.png)

    Si riceverà una notifica dell'avvio dell'analisi e del completamento.

    ![La notifica indica "analisi degli aggiornamenti per il dispositivo MySSIS1103. 2:12 PM/completare correttamente l'operazione ".](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate5m.png)

4. Dopo aver eseguito l'analisi degli aggiornamenti fare clic su **Scarica aggiornamenti**. 

    ![Nel riquadro aggiornamenti del dispositivo è presente il messaggio "i nuovi aggiornamenti sono disponibili". Il pulsante Scarica aggiornamenti è evidenziato.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate6m.png)

5. Nel pannello **Nuovi aggiornamenti** esaminare le informazioni sugli aggiornamenti scaricati e confermare l'installazione. Fare clic su **OK**.

    ![Il riquadro nuovi aggiornamenti indica che dopo il download degli aggiornamenti sarà necessario confermare l'installazione. Il pulsante OK è evidenziato.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate7m.png)

6. Si riceverà una notifica dell'avvio del caricamento e del completamento.

     ![La notifica indica che il download degli aggiornamenti per il dispositivo MySSIS1... 2:13 PM ".](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate8m.png)

5. Nel pannello **Aggiornamenti del dispositivo** fare clic su **Installa**.

     ![Aggiornamenti del dispositivo: "conferma installazione aggiornamenti". È disponibile un pulsante Installa.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate11m1.png)   

6. Nel pannello **Nuovi aggiornamenti** viene visualizzato un avviso che comunica che l'aggiornamento è problematico. Poiché l'array virtuale è un dispositivo a nodo singolo, il dispositivo viene riavviato dopo l'aggiornamento. Tutti gli IO in corso verranno interrotti. Fare clic su **OK** per installare gli aggiornamenti. 

    ![Il messaggio nel riquadro nuovo aggiornamenti è "il dispositivo avrà un tempo di inattività durante l'installazione di questi aggiornamenti". Il pulsante OK è evidenziato.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate12m.png) 

7. Si riceverà una notifica dell'avvio del processo di installazione. 

    ![La notifica indica che è in corso l'installazione degli aggiornamenti per il dispositivo MySSIS1103. 2:15 PM ".](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate13m.png)

8.  Al termine del processo di installazione, fare clic sul collegamento **Visualizzare il processo** nel pannello **Aggiornamenti del dispositivo** per monitorare l'installazione. 

    ![Nel riquadro aggiornamenti del dispositivo è presente un collegamento con etichetta "installazione di aggiornamenti. Visualizza processo ".](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate15m1.png)

    Viene visualizzato il pannello **Installa aggiornamenti**. Nel pannello sono visualizzate informazioni dettagliate sul processo.

    ![Nel riquadro Installa aggiornamenti sono disponibili informazioni sull'installazione, tra cui dispositivo, stato, durata, ora di inizio e ora di fine.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate16m1.png)

9. Una volta che gli aggiornamenti sono stati installati correttamente, nel pannello **aggiornamenti del dispositivo** viene visualizzato un messaggio di questo effetto. 
