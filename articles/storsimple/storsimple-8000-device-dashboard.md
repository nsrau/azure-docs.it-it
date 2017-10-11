---
title: Usare il riepilogo per dispositivi StorSimple serie 8000 | Microsoft Docs
description: Descrive il riepilogo del dispositivo del servizio Gestione dispositivi StorSimple e illustra come usarlo per visualizzare le metriche di archiviazione e gli iniziatori connessi e trovare il numero di serie e il nome qualificato iSCSI.
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
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: 784d3ce9d8f926b00ac1c6fbf48a05c0b04f900a
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="use-the-device-summary-in-storsimple-device-manager-service"></a>Usare il riepilogo del dispositivo nel servizio Gestione dispositivi StorSimple

## <a name="overview"></a>Panoramica
Il pannello di riepilogo del dispositivo StorSimple offre una panoramica delle informazioni per un determinato dispositivo StorSimple, a differenza del pannello di riepilogo del servizio, che fornisce informazioni su tutti i dispositivi inclusi nella soluzione Microsoft Azure StorSimple.

Il pannello di riepilogo del dispositivo offre una vista di riepilogo di un dispositivo StorSimple serie 8000 registrato con un servizio Gestione dispositivi StorSimple, in modo da evidenziare eventuali problemi del dispositivo che richiedono attenzione da parte dell'amministratore di sistema. Questa esercitazione introduce il pannello di riepilogo del dispositivo, illustra il contenuto e la funzione e descrive le attività che è possibile eseguire da questo pannello.

Il pannello di riepilogo dispositivo contiene le informazioni seguenti:

![Pannello di riepilogo del dispositivo](./media/storsimple-8000-device-dashboard/device-summary1.png)

## <a name="management-command-bar"></a>Barra dei comandi di gestione

Il pannello del dispositivo StorSimple contiene le opzioni per la gestione del dispositivo StorSimple in uso. I comandi per la gestione vengono visualizzati nella parte superiore del pannello e sul lato sinistro. Usare queste opzioni per aggiungere condivisioni o volumi o per aggiornare o eseguire il failover del dispositivo.

![Barra dei comandi di gestione](./media/storsimple-8000-device-dashboard/device-summary2.png)

## <a name="essentials"></a>Informazioni di base

L'area relativa alle informazioni di base riporta alcune proprietà importanti, ad esempio lo stato, il modello, il nome qualificato iSCSI del dispositivo di destinazione e la versione del software. 

![Informazioni di base sui dispositivi](./media/storsimple-8000-device-dashboard/device-summary3.png)

## <a name="monitoring"></a>Monitoraggio

* Il riquadro **Avvisi** offre uno snapshot di tutti gli avvisi attivi per il dispositivo, raggruppati in base alla gravità.

    ![Riquadro avvisi](./media/storsimple-8000-device-dashboard/device-summary4.png)

    Fare clic sul riquadro per aprire il pannello **Avvisi**, quindi fare clic su un singolo avviso per visualizzare altri dettagli specifici, incluse tutte le operazioni consigliate. È inoltre possibile cancellare l'avviso se il problema è stato risolto.

    ![Fare clic sul riquadro degli avvisi](./media/storsimple-8000-device-dashboard/device-summary10.png)

* Il riquadro **Stato e integrità** offre informazioni dettagliate sull'integrità dei componenti hardware di un dispositivo, oltre allo stato del dispositivo. Lo stato del dispositivo può essere offline, online, disattivato o pronto per la configurazione.

    ![Riquadro Stato e integrità](./media/storsimple-8000-device-dashboard/device-summary5.png)

* Il riquadro **Volumi** offre un riepilogo del numero di volumi nel dispositivo, raggruppati per stato.

    ![Riquadro Volumi](./media/storsimple-8000-device-dashboard/device-summary6.png)

    Fare clic sul riquadro per aprire il pannello di elenco **Volumi** e quindi fare clic su un singolo volume per visualizzarne o modificarne le proprietà.
    
    ![Fare clic sul riquadro Volumi](./media/storsimple-8000-device-dashboard/device-summary9.png)
    
    Per altre informazioni, vedere come [gestire i volumi](storsimple-8000-manage-volumes-u2.md).

* Nel grafico **Utilizzo** è possibile visualizzare l'archiviazione primaria usata nel dispositivo e l'archiviazione cloud usata negli ultimi sette giorni, che corrispondono al periodo di tempo predefinito.

     ![Riquadro Utilizzo](./media/storsimple-8000-device-dashboard/device-summary7.png)
    
     Per scegliere una diversa scala cronologica, usare l'opzione **Modifica** nell'angolo superiore destro del grafico.

     ![Modificare il grafico sull'utilizzo](./media/storsimple-8000-device-dashboard/device-summary12.png)

     In questo grafico, è possibile visualizzare le metriche per l'archiviazione primaria totale (la quantità di dati scritti dall'host per il dispositivo) e l'archiviazione cloud totale utilizzata dal dispositivo in un periodo di tempo.
  
     In questo contesto, *archiviazione primaria* fa riferimento alla quantità totale di dati scritti dall'host e può essere suddivisa in base al tipo di volume: *archiviazione primaria a livelli* include sia i dati archiviati in locale sia quelli archiviati a livelli nel cloud, mentre *archiviazione primaria aggiunta in locale* include solo i dati archiviati in locale. L’*Archiviazione cloud*d'altra parte, è una misura della quantità totale di dati archiviati nel cloud. Questo tipo di archiviazione include i backup e i dati a più livelli. I dati archiviati nel cloud sono deduplicati e compressi, mentre l'archiviazione primaria indica la quantità di spazio di archiviazione usato prima che i dati vengano deduplicati e compressi. (È possibile confrontare i due numeri per avere un'idea del tasso di compressione). Per entrambe le archiviazioni, primaria e cloud, gli importi mostrati si basano sulla frequenza di rilevamento configurata. Se, ad esempio, si sceglie una frequenza settimanale, il grafico mostrerà i dati per ogni giorno della settimana precedente.

     Per visualizzare la quantità di archiviazione cloud usata nel corso del tempo, selezionare l'opzione **SPAZIO DI ARCHIVIAZIONE CLOUD UTILIZZATO**. Per visualizzare l'archiviazione totale scritta dall'host, selezionare le opzioni **PRIMARY TIERED STORAGE USED** (ARCHIVIAZIONE PRIMARIA A LIVELLI USATA) e **PRIMARY LOCALLY PINNED STORAGE USED** (ARCHIVIAZIONE PRIMARIA AGGIUNTA IN LOCALE USATA). 
     Per ulteriori informazioni, vedere [Utilizzare il servizio StorSimple Manager per monitorare il dispositivo StorSimple](storsimple-monitor-device.md).


* Il riquadro **Capacità** mostra l'archiviazione primaria di cui è stato eseguito il provisioning e quella rimanente nel dispositivo rispetto all'archiviazione totale disponibile per lo stesso dispositivo. **Provisioning** fa riferimento alla quantità di spazio di archiviazione preparata e allocata per l'uso; **Rimanente** fa riferimento alla capacità rimanente di cui è possibile eseguire il provisioning in questo dispositivo. 

    ![Riquadro Utilizzo](./media/storsimple-8000-device-dashboard/device-summary8.png)

    Fare clic su questo riquadro per visualizzare come viene effettuato il provisioning della capacità tra i volumi a livelli e i volumi aggiunti in locale. **Rimanente a livelli** è la capacità disponibile di cui è possibile effettuare il provisioning, incluso cloud, mentre **Rimanente locale** è la capacità rimanente sui dischi collegati a questo dispositivo.

    ![Fare clic sul grafico Utilizzo](./media/storsimple-8000-device-dashboard/device-summary13.png)


## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sul [pannello di riepilogo del servizio StorSimple](storsimple-8000-service-dashboard.md).
* Altre informazioni sull'[utilizzo del servizio Gestione dispositivi StorSimple per amministrare il dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

