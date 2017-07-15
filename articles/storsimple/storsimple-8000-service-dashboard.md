---
title: Usare il riepilogo per dispositivi StorSimple serie 8000 | Microsoft Docs
description: "Descrive il pannello di riepilogo del servizio StorSimple e illustra come usarlo per monitorare l'integrità della soluzione StorSimple."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2017
ms.author: alkohli
ms.translationtype: HT
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: d987a4ae170f21532a886552cbe1eb5a0d25fc3f
ms.contentlocale: it-it
ms.lasthandoff: 07/12/2017

---
# Usare il pannello di riepilogo del servizio per dispositivi StorSimple serie 8000
<a id="use-the-service-summary-blade-for-storsimple-8000-series-device" class="xliff"></a>

## Panoramica
<a id="overview" class="xliff"></a>

Il pannello di riepilogo del servizio Gestione dispositivi StorSimple offre una visualizzazione di riepilogo di tutti i dispositivi connessi a tale servizio, evidenziando quelli che richiedono attenzione da parte dell'amministratore di sistema. Questa esercitazione illustra il pannello di riepilogo del servizio e descrive il contenuto e la funzione del dashboard nonché le attività che è possibile eseguire da questa pagina.

![Riepilogo del servizio](./media/storsimple-8000-service-dashboard/service-summary1.png)


## Comandi di gestione
<a id="management-commands" class="xliff"></a>

Nel pannello di riepilogo del servizio StorSimple vengono visualizzate le opzioni per gestire il servizio Gestione dispositivi StorSimple e i dispositivi StorSimple serie 8000 in esso registrati. I comandi per la gestione vengono visualizzati nella parte superiore del pannello e sul lato sinistro.

![Barra dei comandi](./media/storsimple-8000-service-dashboard/service-summary2.png)

Usare queste opzioni per eseguire diverse operazioni, ad esempio aggiungere volumi o condivisioni, oppure monitorare i vari processi in esecuzione nei dispositivi StorSimple.


## Informazioni di base
<a id="essentials" class="xliff"></a>

L'area relativa alle informazioni di base riporta alcune proprietà importanti, ad esempio il gruppo di risorse, il percorso e la sottoscrizione in cui è stato creato il servizio Gestione dispositivi StorSimple.

![Informazioni di base](./media/storsimple-8000-service-dashboard/service-summary3.png)

## Riepilogo servizio di Gestione dispositivi StorSimple
<a id="storsimple-device-manager-service-summary" class="xliff"></a>

* Il riquadro **Avvisi** offre uno snapshot di tutti gli avvisi attivi per tutti i dispositivi, raggruppati in base alla gravità.

    ![Riquadro Avvisi](./media/storsimple-8000-service-dashboard/service-summary4.png)

    Facendo clic sul riquadro viene aperto il pannello **Avvisi**, in cui è possibile fare clic su un singolo avviso per visualizzare altri dettagli specifici, incluse tutte le operazioni consigliate. È inoltre possibile cancellare l'avviso se il problema è stato risolto.

    ![Fare clic sul riquadro Avvisi](./media/storsimple-8000-service-dashboard/service-summary8.png)

* Il riquadro **Capacità** mostra lo spazio di archiviazione primario di cui è stato effettuato il provisioning e quello rimanente in tutti i dispositivi rispetto allo spazio di archiviazione totale in essi disponibile. **Provisioning eseguito** fa riferimento allo spazio di archiviazione preparato e allocato per l'uso, mentre **Rimanente** fa riferimento alla capacità rimanente di cui è possibile effettuare il provisioning in tutti i dispositivi.

    ![Riquadro Capacità](./media/storsimple-8000-service-dashboard/service-summary6.png)

    La capacità **Rimanente - A livelli** è quella disponibile per il provisioning includendo il cloud, mentre **Rimanente - Locale** è la capacità rimanente nei dischi collegati ai dispositivi StorSimple serie 8000.


* Nel grafico **Utilizzo** vengono visualizzate le metriche rilevanti per i dispositivi. È possibile visualizzare lo spazio di archiviazione primario usato in tutti i dispositivi e lo spazio di archiviazione cloud utilizzato dai dispositivi negli ultimi 7 giorni, il periodo di tempo predefinito. 

    ![Riquadro Utilizzo](./media/storsimple-8000-service-dashboard/service-summary7.png) 

    Per scegliere una diversa scala cronologica, usare l'opzione **Modifica** nell'angolo superiore destro del grafico.

     ![Fare clic sul riquadro Utilizzo](./media/storsimple-8000-service-dashboard/service-summary10.png)

     ![Esportare i dati del grafico](./media/storsimple-8000-service-dashboard/service-summary11.png)

* Il riquadro **Dispositivi** contiene un riepilogo del numero dei dispositivi StorSimple serie 8000 in Gestione dispositivi StorSimple, raggruppati in base allo stato del dispositivo. 

    ![Riquadro Dispositivi](./media/storsimple-8000-service-dashboard/service-summary5.png)

    Fare clic su questo riquadro per aprire l'elenco **Dispositivi** e fare clic su un singolo dispositivo per esaminarne in dettaglio il riepilogo. È anche possibile eseguire azioni specifiche relative a un dispositivo dal pannello di riepilogo di un determinato dispositivo. Per altre informazioni sul pannello di riepilogo dispositivo, vedere [Pannello di riepilogo dispositivo](storsimple-8000-device-dashboard.md).

    ![Fare clic sul riquadro Dispositivi](./media/storsimple-8000-service-dashboard/service-summary9.png)

## Visualizzare i log attività
<a id="view-the-activity-logs" class="xliff"></a>

Per visualizzare le varie operazioni eseguite in Gestione dispositivi StorSimple, fare clic sul collegamento **Log attività** sul lato sinistro del pannello di riepilogo servizio StorSimple. Viene visualizzato il pannello **Log attività** in cui è possibile visualizzare un riepilogo delle operazioni recenti eseguite.

![Log attività](./media/storsimple-8000-service-dashboard/activity-logs1.png)
## Passaggi successivi
<a id="next-steps" class="xliff"></a>

* Altre informazioni su come [usare il servizio Gestione dispositivi StorSimple per amministrare un dispositivo StorSimple](storsimple-8000-manager-service-administration.md).


