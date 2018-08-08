---
title: File di inclusione
description: File di inclusione
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 07/18/2018
ms.author: alkohli
ms.custom: include file
ms.openlocfilehash: a83095b8330ccf08d777e48389c17058c6d29527
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2018
ms.locfileid: "39347983"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>Per installare gli aggiornamenti tramite il portale di Azure

1. Passare a Gestione dispositivi StorSimple e selezionare **Dispositivi**. Dall'elenco dei dispositivi connessi al servizio selezionare e fare clic sul dispositivo che si vuole aggiornare.

2. In **Impostazioni** fare clic su **Aggiornamenti del dispositivo**.  

3. Se sono disponibili aggiornamenti software, verrà visualizzato un messaggio. Per cercare gli aggiornamenti è anche possibile fare clic su **Analizza**. Prendere nota della versione del software in esecuzione. 

    ![aggiornamento dispositivo](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate3m1.png)

    Si riceverà una notifica dell'avvio dell'analisi e del completamento.
 
4. Dopo aver eseguito l'analisi degli aggiornamenti fare clic su **Scarica aggiornamenti**. Leggere le note sulla versione in **Nuovi aggiornamenti**. Si noti inoltre che dopo il download degli aggiornamenti sarà necessario confermare l'installazione. Fare clic su **OK**.

    ![aggiornamento dispositivo](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate6m.png)

    Si riceverà una notifica dell'avvio del caricamento e del completamento.

5. In **Aggiornamenti del dispositivo** fare clic su **Installa**.

     ![aggiornamento dispositivo](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate11m1.png)

6. In **Nuovi aggiornamenti** un avviso informa che l'aggiornamento comporta interruzioni. Poiché l'array virtuale è un dispositivo a nodo singolo, il dispositivo viene riavviato dopo l'aggiornamento. Tutti gli IO in corso verranno interrotti. Fare clic su **OK** per installare gli aggiornamenti.

    ![aggiornamento dispositivo](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate12m.png)

    Si riceverà una notifica dell'avvio del processo di installazione.

7.  Dopo il completamento del processo di installazione, fare clic sul collegamento **Visualizza processo**. Viene visualizzato il pannello **Installa aggiornamenti**. Nel pannello sono visualizzate informazioni dettagliate sul processo. 

    ![aggiornamento dispositivo](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate16m1.png)

8. Se all'inizio l'array virtuale eseguiva la versione del software corrispondente all'aggiornamento 1 (10.0.10296.0), è ora in esecuzione l'aggiornamento 1.1 e l'operazione di aggiornamento è completata. È possibile ignorare i passaggi rimanenti. 

    Se all'inizio l'array virtuale eseguiva la versione del software corrispondente all'aggiornamento 0.6 (10.0.10293.0), è stato installato l'aggiornamento 1.0. Verrà visualizzato un messaggio che indica che sono disponibili degli aggiornamenti. Ripetere i passaggi da 4 a 7 per installare l'aggiornamento 1.1.

    

