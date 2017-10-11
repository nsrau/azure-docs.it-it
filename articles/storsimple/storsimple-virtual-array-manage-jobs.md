---
title: Visualizzare e gestire i processi dell'array virtuale StorSimple | Documentazione Microsoft
description: "Descrive la pagina Processi del servizio Gestione dispositivi StorSimple e illustra come è possibile tenere traccia dei processi recenti e correnti per l'array virtuale StorSimple."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 31879821-b599-4609-a7f4-d4b0f658a933
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 11/11/2016
ms.author: alkohli
ms.openlocfilehash: 3fd1c262a8ce94d8e98f2b066a8028d974b15b1d
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="use-the-storsimple-device-manager-service-to-view-jobs-for-the-storsimple-virtual-array"></a>Usare il servizio Gestione dispositivi StorSimple per visualizzare i processi per l'array virtuale StorSimple
## <a name="overview"></a>Overview
Il pannello **Processi** fornisce un unico portale centralizzato per la visualizzazione e la gestione dei processi avviati sugli array virtuali connessi al servizio Gestione dispositivi StorSimple. È possibile visualizzare i processi in esecuzione, completati e non riusciti per più dispositivi virtuali. I risultati vengono presentati in un formato tabulare.

![Pannello Processi](./media/storsimple-virtual-array-manage-jobs/ova-jobs-blade.png)

È possibile trovare rapidamente i processi desiderati filtrando i campi, ad esempio:

* **Intervallo di tempo**: i processi possono essere filtrati in base all'intervallo di data e ora.
* **Dispositivi** : i processi vengono avviati in un dispositivo specifico connesso al servizio. I processi filtrati vengono quindi catalogati in base ai seguenti attributi:
  
  * **Nome**: il nome del processo può essere **Tutto**, **Backup**, **Clona**, **Effettua il failover**, **Scarica aggiornamenti** o **Installa aggiornamenti**.
  * **Stato**: i processi possono essere **Tutto**, **In corso**, **Riuscito**, **Operazione non riuscita** o **Annullato**.
  * **Entità** : i processi possono essere associati a un volume, una condivisione o un dispositivo.
  * **Dispositivo** : il nome del dispositivo su cui è stato avviato il processo.
  * **Avviato alle** : l'ora di inizio del processo.
  * **Durata**: la durata di esecuzione del processo.
* **Stato** : è possibile cercare tutti i processi in esecuzione, completati o non riusciti.
* **Tipo di processo**: il processo può essere di tipo tutto, backup, ripristino, failover, scarica aggiornamenti o installa aggiornamenti.

L'elenco dei processi viene aggiornato ogni 30 secondi.

## <a name="view-job-details"></a>Visualizza i dettagli dei processi
Eseguire la procedura seguente per visualizzare i dettagli di qualsiasi processo.

#### <a name="to-view-job-details"></a>Per visualizzare i dettagli dei processi
1. Nel pannello **Processi** visualizzare i processi desiderati eseguendo una query con i filtri appropriati. È possibile cercare processi completati o in esecuzione.
2. Selezionare un processo nell'elenco tabulare dei processi.
   
    ![Pannello Processi](./media/storsimple-virtual-array-manage-jobs/ova-jobs-blade.png)
3. Nella parte inferiore della pagina, fare clic su **Dettagli**.
4. Nella finestra di dialogo **Dettagli** è possibile visualizzare statistiche temporali, sullo stato e sui dettagli. La figura seguente mostra un esempio della finestra di dialogo relativa ai **dettagli del processo di backup** :
   
    ![Dettagli del processo](./media/storsimple-virtual-array-manage-jobs/ova-jobs-details.png)

#### <a name="job-failures-when-the-virtual-machine-is-paused-in-the-hypervisor"></a>Errori di processo quando la macchina virtuale viene sospesa nell'hypervisor
Se nell'array virtuale StorSimple è in corso un processo e il dispositivo (macchina virtuale con provisioning in hypervisor) viene sospeso per più di 15 minuti, il processo ha esito negativo. Ciò è dovuto al fatto che l'ora dell'array virtuale StorSimple non è più sincronizzata con l'ora di Microsoft Azure. 

Verrà visualizzato un errore indicante che l'ora del dispositivo non è sincronizzata con l'ora di Microsoft Azure per un valore superiore a 15 minuti. Verificare che l'hypervisor e il dispositivo siano ora sincronizzati con un server NTP. Assicurarsi che non vi siano problemi di connettività. Per risolvere i problemi di connettività, eseguire i test diagnostici dall'interfaccia utente Web locale del dispositivo virtuale.

Questi errori possono verificarsi con processi di backup, ripristino, aggiornamento e failover. Se il provisioning della macchina virtuale viene eseguito in Hyper-V, l'ora della macchina virtuale alla fine si sincronizza con l'hypervisor. Quando ciò accade, è possibile riavviare il processo.

## <a name="next-steps"></a>Passaggi successivi
[Informazioni su come usare l'interfaccia utente Web locale per amministrare l'array virtuale StorSimple](storsimple-ova-web-ui-admin.md).

