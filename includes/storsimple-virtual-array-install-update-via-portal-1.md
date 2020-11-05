---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: e64db93347794ccfc85252205c6d92a68e904635
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375834"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>Per installare gli aggiornamenti tramite il portale di Azure

1. Passare a Gestione dispositivi StorSimple e selezionare **Dispositivi**. Dall'elenco dei dispositivi connessi al servizio selezionare e fare clic sul dispositivo che si vuole aggiornare.

    ![aggiornamento dispositivo](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate1m.png) 

2. Nel pannello **Impostazioni** fare clic su **Aggiornamenti del dispositivo**.

    ![aggiornare il dispositivo 2](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate2m.png)  

3. Se sono disponibili aggiornamenti software, verrà visualizzato un messaggio. Per cercare gli aggiornamenti è anche possibile fare clic su **Analizza**. Prendere nota della versione del software in esecuzione. 

    ![aggiornare il dispositivo 3](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate3m1.png)

    Si riceverà una notifica dell'avvio dell'analisi e del completamento.

    ![aggiornare il dispositivo 4](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate5m.png)

4. Dopo aver eseguito l'analisi degli aggiornamenti fare clic su **Scarica aggiornamenti**.

    ![aggiornare il dispositivo 5](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate6m.png)

5. Leggere le note sulla versione nel pannello **Nuovi aggiornamenti**. Si noti inoltre che dopo il download degli aggiornamenti sarà necessario confermare l'installazione. Fare clic su **OK**.

    ![aggiornare il dispositivo 6](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate7m.png)

6. Si riceverà una notifica dell'avvio del caricamento e del completamento.

     ![aggiornare il dispositivo 7](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate8m.png)

5. Nel pannello **Aggiornamenti del dispositivo** fare clic su **Installa**.

     ![aggiornare il dispositivo 8](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate11m1.png)

6. Nel pannello **Nuovi aggiornamenti** viene visualizzato un avviso che comunica che l'aggiornamento è problematico. Poiché l'array virtuale è un dispositivo a nodo singolo, il dispositivo viene riavviato dopo l'aggiornamento. Tutti gli IO in corso verranno interrotti. Fare clic su **OK** per installare gli aggiornamenti.

    ![aggiornare il dispositivo 9](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate12m.png)

7. Si riceverà una notifica dell'avvio del processo di installazione.

    ![aggiornare il dispositivo 10](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate13m.png)

8.  Al termine del processo di installazione, fare clic sul collegamento **Visualizzare il processo** nel pannello **Aggiornamenti del dispositivo** per monitorare l'installazione. 

    ![aggiornare il dispositivo 11](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate15m1.png)

    Viene visualizzato il pannello **Installa aggiornamenti**. Nel pannello sono visualizzate informazioni dettagliate sul processo.

    ![aggiornare il dispositivo 12](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate16m1.png)

9. Se si è iniziato con un array virtuale che eseguiva l'aggiornamento 0.6 (10.0.10293.0) della versione del software, ora verrà eseguito l'aggiornamento 1 e l'operazione di aggiornamento è completata. È possibile ignorare i passaggi rimanenti. Se si è iniziato con un array virtuale che eseguiva una versione del software precedente all'aggiornamento 0.6 (10.0.10293.0), ora è stato installato l'aggiornamento 0.6. Verrà visualizzato un messaggio che indica che sono disponibili degli aggiornamenti. Ripetere i passaggi da 4 a 8 per installare l'aggiornamento 1.

    ![aggiornare il dispositivo 13](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate17.png)

