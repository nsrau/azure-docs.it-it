---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 3129bbe171329ecf37f8712394cedf9b70188220
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2020
ms.locfileid: "96005818"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>Per installare gli aggiornamenti tramite il portale di Azure

1. Passare a Gestione dispositivi StorSimple e selezionare **Dispositivi**. Dall'elenco dei dispositivi connessi al servizio selezionare e fare clic sul dispositivo che si vuole aggiornare.

    ![In recente, Device Manager MySSDevManager è evidenziato e selezionato, i dispositivi vengono evidenziati e selezionati e il dispositivo MYSSIS1103 è evidenziato e selezionato.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate1m.png) 

2. Nel pannello **Impostazioni** fare clic su **Aggiornamenti del dispositivo**.

    ![Vengono visualizzate le informazioni relative a MYSSIS1103. In impostazioni gli aggiornamenti del dispositivo sono evidenziati.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate2m.png)  

3. Se sono disponibili aggiornamenti software, verrà visualizzato un messaggio. Per cercare gli aggiornamenti è anche possibile fare clic su **Analizza**. Prendere nota della versione del software in esecuzione. 

    ![Il riquadro aggiornamenti del dispositivo indica che sono disponibili nuovi aggiornamenti e "ultima analisi/11/01/2017 10:56 AM/Software Version/10.0.10289.0". La versione è evidenziata.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate3m1.png)

    Si riceverà una notifica dell'avvio dell'analisi e del completamento.

    ![La notifica indica "analisi degli aggiornamenti per il dispositivo MySVAFS110.... 10:57 AM/operazione in corso. "](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate5m.png)

4. Dopo aver eseguito l'analisi degli aggiornamenti fare clic su **Scarica aggiornamenti**.

    ![Nel riquadro aggiornamenti del dispositivo è presente il messaggio "i nuovi aggiornamenti sono disponibili". Il pulsante Scarica aggiornamenti è evidenziato.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate6m.png)

5. Leggere le note sulla versione nel pannello **Nuovi aggiornamenti**. Si noti inoltre che dopo il download degli aggiornamenti sarà necessario confermare l'installazione. Fare clic su **OK**.

    ![Il riquadro nuovi aggiornamenti indica che dopo il download degli aggiornamenti sarà necessario confermare l'installazione. Il pulsante OK è evidenziato.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate7m.png)

6. Si riceverà una notifica dell'avvio del caricamento e del completamento.

     ![La notifica indica che il download degli aggiornamenti per il dispositivo MySVAFS... 11:07 AM ".](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate8m.png)

5. Nel pannello **Aggiornamenti del dispositivo** fare clic su **Installa**.

     ![Aggiornamenti del dispositivo: "conferma installazione aggiornamenti". Il pulsante Installa è evidenziato.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate11m1.png)

6. Nel pannello **Nuovi aggiornamenti** viene visualizzato un avviso che comunica che l'aggiornamento è problematico. Poiché l'array virtuale è un dispositivo a nodo singolo, il dispositivo viene riavviato dopo l'aggiornamento. Tutti gli IO in corso verranno interrotti. Fare clic su **OK** per installare gli aggiornamenti.

    ![Il messaggio nel riquadro nuovo aggiornamenti è "il dispositivo avrà un tempo di inattività durante l'installazione di questi aggiornamenti". Il pulsante OK è evidenziato.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate12m.png)

7. Si riceverà una notifica dell'avvio del processo di installazione.

    ![La notifica indica che l'installazione degli aggiornamenti per il dispositivo MySVAFS110.... 11:09 AM ".](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate13m.png)

8.  Al termine del processo di installazione, fare clic sul collegamento **Visualizzare il processo** nel pannello **Aggiornamenti del dispositivo** per monitorare l'installazione. 

    ![Nel riquadro aggiornamenti del dispositivo è presente un collegamento con etichetta "installazione di aggiornamenti. Visualizza processo ". Il pulsante Installa è evidenziato.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate15m1.png)

    Viene visualizzato il pannello **Installa aggiornamenti**. Nel pannello sono visualizzate informazioni dettagliate sul processo.

    ![Nel riquadro Installa aggiornamenti sono disponibili informazioni sull'installazione, tra cui dispositivo, stato, durata, ora di inizio e ora di fine.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate16m1.png)

9. Se si è iniziato con un array virtuale che eseguiva l'aggiornamento 0.6 (10.0.10293.0) della versione del software, ora verrà eseguito l'aggiornamento 1 e l'operazione di aggiornamento è completata. È possibile ignorare i passaggi rimanenti. Se si è iniziato con un array virtuale che eseguiva una versione del software precedente all'aggiornamento 0.6 (10.0.10293.0), ora è stato installato l'aggiornamento 0.6. Verrà visualizzato un messaggio che indica che sono disponibili degli aggiornamenti. Ripetere i passaggi da 4 a 8 per installare l'aggiornamento 1.

    ![Il riquadro aggiornamenti del dispositivo indica che sono disponibili nuovi aggiornamenti e "ultima analisi/11/1/2017 10:56 AM/Software Version/10.0.10293.0".](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate17.png)

