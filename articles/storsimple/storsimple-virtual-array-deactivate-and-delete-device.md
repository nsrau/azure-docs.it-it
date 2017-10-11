---
title: Disattivare ed eliminare un array virtuale Microsoft Azure StorSimple | Documentazione Microsoft
description: Viene descritto come rimuovere un dispositivo StorSimple dal servizio disattivandolo e poi eliminandolo.
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: a929f5bc-03e2-4b01-b925-973db236f19f
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: alkohli
ms.openlocfilehash: 8dea36f92b034f8c6cdb6875634848d37f4c6606
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="deactivate-and-delete-a-storsimple-virtual-array"></a>Disattivare ed eliminare un array virtuale StorSimple

## <a name="overview"></a>Overview

Quando si disattiva un array virtuale StorSimple, si interrompe la connessione tra il dispositivo e il servizio Gestione dispositivi StorSimple corrispondente. In questa esercitazione viene illustrato come:

* Disattivare un dispositivo 
* Eliminare un dispositivo disattivato

Le informazioni in questo articolo si applicano solo agli array virtuali StorSimple. Per informazioni sulla serie 8000, passare alla procedura per [la disattivazione o per l'eliminazione di un dispositivo](storsimple-deactivate-and-delete-device.md).

## <a name="when-to-deactivate"></a>Quando disattivare

La disattivazione è un'operazione permanente e non può essere annullata. Un dispositivo disattivato non può essere registrato di nuovo con il servizio Gestione dispositivi StorSimple. Negli scenari seguenti potrebbe essere necessario disattivare ed eliminare un array virtuale StorSimple:

* **Failover pianificato**: il dispositivo è in linea e si prevede di eseguirne il failover. Può essere necessario eseguire questa operazione se si prevede l'aggiornamento a un dispositivo di dimensioni superiori. Dopo il trasferimento della proprietà dei dati e il completamento del failover, il dispositivo di origine viene eliminato automaticamente.
* **Failover non pianificato**: il dispositivo è offline ed è necessario eseguirne il failover. Questo scenario può verificarsi durante un'emergenza dovuta a un'interruzione nel datacenter e quando il dispositivo primario è inattivo. Si pianifica di eseguire il failover del dispositivo su un dispositivo secondario. Dopo il trasferimento della proprietà dei dati e il completamento del failover, il dispositivo di origine viene eliminato automaticamente.
* **Rimozioni delle autorizzazioni**: si desidera rimuovere le autorizzazioni del dispositivo. Ciò richiede prima di tutto la disattivazione del dispositivo, quindi la sua eliminazione. Quando si disattiva un dispositivo, tutti i dati archiviati localmente non saranno più accessibili. È possibile solo accedere e recuperare i dati archiviati nel cloud. Se si pianifica cdi mantenere il dispositivo dopo la disattivazione, prima di effettuare tale operazione è necessario eseguire uno snapshot di tutti i dati nel cloud. In questo modo sarà possibile recuperare tutti i dati in una fase successiva.

## <a name="deactivate-a-device"></a>Disattivare un dispositivo

Per disattivare un dispositivo seguire questa procedura.

#### <a name="to-deactivate-the-device"></a>Per disattivare il dispositivo

1. Nel servizio passare a **Gestione > Dispositivi**. Nel pannello **Dispositivi** fare clic e selezionare il dispositivo che si desidera disattivare.
   
    ![Selezione del dispositivo disattivare](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete7.png)
2. Nel pannello **Device dashboard** (Dashboard dispositivo) fare clic su **… More** (... Altro) e, nell'elenco, selezionare **Disattiva**.
   
    ![Clic su Disattiva](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete8.png)
3. Nel pannello **Disattiva** digitare il nome del dispositivo e fare clic su **Disattiva**. 
   
    ![Conferma della disattivazione](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete1.png)
   
    Si avvia il processo di disattivazione il cui completamento richiede alcuni minuti.
   
    ![Disattivazione in corso](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete2.png)
4. Dopo la disattivazione, l'elenco dei dispositivi viene aggiornato.
   
    ![Disattivazione completata](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete3.png)
   
    È ora possibile eliminare il dispositivo.

## <a name="delete-the-device"></a>Eliminare il dispositivo

Per eliminare un dispositivo, è prima necessario disattivarlo. L’eliminazione di un dispositivo lo rimuove dall'elenco dei dispositivi connessi al servizio. Il servizio quindi non può più gestire il dispositivo eliminato. I dati associati al dispositivo rimangono comunque nel cloud. Su questi dati sono applicati degli addebiti.

Per eliminare il dispositivo, completare la procedura seguente.

#### <a name="to-delete-the-device"></a>Per eliminare il dispositivo

1. In Gestione dispositivi StorSimple passare a **Gestione > Dispositivi**. Nel pannello **Dispositivi** selezionare un dispositivo disattivato che si desidera eliminare.
2. Nel pannello **Device dashboard** (Dashboard dispositivo) fare clic su **… More** (Altro), quindi su**Elimina**.
   
   ![Selezione del dispositivo da eliminare](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete4.png)
3. Nel pannello **Elimina** digitare il nome del dispositivo per confermare l'eliminazione, quindi fare clic su **Elimina**. L'eliminazione del dispositivo non determina l'eliminazione dei dati a esso associati presenti nel cloud. 
   
   ![Conferma dell'eliminazione](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete5.png) 
4. Si avvia il processo di eliminazione il cui completamento richiede alcuni minuti.
   
   ![Eliminazione in corso](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete6.png)
   
    Dopo il completamento dell'eliminazione, è possibile visualizzare l'elenco aggiornato dei dispositivi.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su come eseguire il failover, fare riferimento all'articolo relativo a [failover e ripristino di emergenza per l'array virtuale StorSimple](storsimple-virtual-array-failover-dr.md).

* Per altre informazioni sull'uso del servizio Gestione dispositivi StorSimple, vedere l'articolo relativo all'[uso di Gestione dispositivi StorSimple per amministrare l'array virtuale StorSimple](storsimple-virtual-array-manager-service-administration.md). 

