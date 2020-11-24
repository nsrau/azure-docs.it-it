---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: df572b88cf8a67163b28ec87154dcea01646b9a2
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2020
ms.locfileid: "95564248"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>Per installare gli aggiornamenti tramite il portale di Azure

1. Passare a Gestione dispositivi StorSimple e selezionare **Dispositivi**. Dall'elenco dei dispositivi connessi al servizio selezionare e fare clic sul dispositivo che si vuole aggiornare. 

    ![In recente, Device Manager MySSDevManager è evidenziato e selezionato, i dispositivi vengono evidenziati e selezionati e il dispositivo MYSSIS1103 è evidenziato e selezionato.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate1m.png) 

2. Nel pannello **Impostazioni** fare clic su **Aggiornamenti del dispositivo**. 

    ![Vengono visualizzate le informazioni per MYSSIS1103. In impostazioni gli aggiornamenti del dispositivo sono evidenziati.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate2m.png)  

3. Se sono disponibili aggiornamenti software, verrà visualizzato un messaggio. Per cercare gli aggiornamenti è anche possibile fare clic su **Analizza**.

    ![Nel riquadro aggiornamenti del dispositivo viene evidenziato il pulsante Analizza. È presente un messaggio a quattro righe: Ultima scansione/non analizzata/versione software/10.0.10280.0.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate3m.png)

    Si riceverà una notifica dell'avvio dell'analisi e del completamento.

    ![La notifica indica che l'analisi degli aggiornamenti per il dispositivo SSIS 1103.2:12 PM/ha completato l'operazione.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate5m.png)

4. Dopo aver eseguito l'analisi degli aggiornamenti fare clic su **Scarica aggiornamenti**. 

    ![Il riquadro aggiornamenti del dispositivo indica che sono disponibili nuovi aggiornamenti e "ultima analisi/11/3/2016 2:12 PM/versione software/10.0.10280.0". La versione è evidenziata.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate6m.png)

5. Nel pannello **Nuovi aggiornamenti** esaminare le informazioni sugli aggiornamenti scaricati e confermare l'installazione. Fare clic su **OK**.

    ![Il riquadro nuovi aggiornamenti indica che dopo il download degli aggiornamenti sarà necessario confermare l'installazione. Il pulsante OK è evidenziato.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate7m.png)

6. Si riceverà una notifica dell'avvio del caricamento e del completamento.

     ![La notifica indica "download degli aggiornamenti per il dispositivo SSIS... 2:13 PM ".](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate8m.png)

5. Nel pannello **Aggiornamenti del dispositivo** fare clic su **Installa**.

     ![Il riquadro aggiornamenti del dispositivo Visualizza il messaggio "conferma installazione aggiornamenti". Il pulsante Installa è evidenziato.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate11m.png)   

6. Nel pannello **Nuovi aggiornamenti** viene visualizzato un avviso che comunica che l'aggiornamento è problematico. Poiché l'array virtuale è un dispositivo a nodo singolo, il dispositivo viene riavviato dopo l'aggiornamento. Tutti gli IO in corso verranno interrotti. Fare clic su **OK** per installare gli aggiornamenti. 

    ![Il messaggio nel riquadro nuovo aggiornamenti è "il dispositivo avrà un tempo di inattività durante l'installazione di questi aggiornamenti". Il pulsante OK è evidenziato.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate12m.png) 

7. Si riceverà una notifica dell'avvio del processo di installazione. 

    ![La notifica indica che è in corso l'installazione degli aggiornamenti per il dispositivo MYSSIS1103. 2:15 PM.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate13m.png)

8.  Al termine del processo di installazione, fare clic sul collegamento **Visualizzare il processo** nel pannello **Aggiornamenti del dispositivo** per monitorare l'installazione. 

    ![Nel riquadro aggiornamenti del dispositivo il collegamento con etichetta "installazione di aggiornamenti. Visualizza processo "è evidenziato.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate15m.png)

    Viene visualizzato il pannello **Installa aggiornamenti**. Nel pannello sono visualizzate informazioni dettagliate sul processo.

    ![Nel riquadro Installa aggiornamenti sono disponibili informazioni sull'installazione, tra cui dispositivo, stato, durata, ora di inizio e ora di fine.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate16m.png)

9. Al termine dell'installazione degli aggiornamenti viene visualizzato un messaggio nel pannello Aggiornamenti del dispositivo. La versione del software diventerà **10.0.10288.0**. 

    ![Il riquadro aggiornamenti del dispositivo Mostra "il dispositivo è aggiornato" e fornisce la versione del software (10.0.10288.0).](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate17m.png)
