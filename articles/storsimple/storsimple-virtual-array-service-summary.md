---
title: Pannello di riepilogo servizio array virtuale StorSimple | Documentazione Microsoft
description: "Descrive il pannello di riepilogo servizio per Gestione dispositivi StorSimple e illustra come usarlo per monitorare l&quot;integrità dell&quot;array virtuale StorSimple."
services: storsimple
documentationcenter: 
author: manuaery
manager: syadav
editor: 
ms.assetid: 8a2b9a84-b0e6-48b9-b366-d16f004241a5
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
translationtype: Human Translation
ms.sourcegitcommit: c78eda33a64d630ba18aba6a174db372eb41dde9
ms.openlocfilehash: 284e404c44505a98d9e0ed5abe87cd945415af56

---
# <a name="use-the-service-summary-blade-for-storsimple-device-manager-connected-to-storsimple-virtual-array"></a>Usare il pannello di riepilogo servizio per il servizio Gestione dispositivi StorSimple connesso all'array virtuale StorSimple
## <a name="overview"></a>Panoramica
Il pannello di riepilogo servizio per Gestione dispositivi StorSimple visualizza un riepilogo degli array virtuali StorSimple, noti anche come dispositivi virtuali locali StorSimple o dispositivi virtuali, connessi al servizio, evidenziando quelli che richiedono attenzione da parte dell'amministratore di sistema. Questa esercitazione introduce il pannello di riepilogo servizio, illustra il contenuto e la funzione e descrive le attività che è possibile eseguire da questo pannello.

![Dashboard del servizio](./media/storsimple-virtual-array-service-summary/service-blade.png)

## <a name="management-commands-and-essentials"></a>Informazioni di base e comandi di gestione
Il pannello di riepilogo StorSimple visualizza le opzioni per la gestione del servizio Gestione dispositivi StorSimple e gli array virtuali registrati per questo servizio. I comandi per la gestione vengono visualizzati nella parte superiore del pannello e sul lato sinistro.

Usare queste opzioni per eseguire diverse operazioni, ad esempio aggiungere volumi o condivisioni, o monitorare i vari processi in esecuzione sugli array virtuali.

L'area relativa alle informazioni di base riporta alcune proprietà importanti, ad esempio il gruppo di risorse, il percorso e la sottoscrizione in cui è stato creato il servizio Gestione dispositivi StorSimple.

## <a name="storsimple-device-manager-service-summary"></a>Riepilogo servizio di Gestione dispositivi StorSimple
* Il riquadro **Avvisi** fornisce uno snapshot di tutti gli avvisi attivi per tutti i dispositivi virtuali, raggruppati in base alla gravità. Facendo clic sul riquadro viene aperto il pannello **Avvisi**, in cui è possibile fare clic su un singolo avviso per visualizzare altri dettagli specifici, incluse tutte le operazioni consigliate. È inoltre possibile cancellare l'avviso se il problema è stato risolto.
* II riquadro **Capacità** mostra l'archiviazione primaria di cui è stato eseguito il provisioning e quella rimanente in tutti i dispositivi virtuali rispetto all'archiviazione totale disponibile tra tutti i dispositivi virtuali. **Provisioning** fa riferimento alla quantità di archiviazione preparata e allocata per l'uso; **Rimanente** fa riferimento alla capacità rimanente di cui è possibile eseguire il provisioning in tutti i dispositivi virtuali. **Rimanente a livelli** è la capacità disponibile di cui è possibile eseguire il provisioning, tra cui il cloud, mentre **Rimanente locale ** è la capacità rimanente sui dischi collegati agli array virtuali.
* Nel grafico **Utilizzo** è possibile visualizzare le metriche rilevanti per i dispositivi virtuali. È possibile visualizzare l'archiviazione primaria usata in tutti i dispositivi virtuali e l'archiviazione cloud usata dai dispositivi virtuali negli ultimi sette giorni, il periodo di tempo predefinito. Usare l'opzione **Modifica** nell'angolo superiore destro del grafico per scegliere una scala cronologica differente.
* Il riquadro **Dispositivi** fornisce un riepilogo del numero di array virtuali in Gestione dispositivi StorSimple raggruppati per stato del dispositivo. Fare clic su questo riquadro per aprire l'elenco **Dispositivi** e fare clic su un singolo dispositivo per esaminarne in dettaglio il riepilogo. È anche possibile eseguire azioni specifiche relative a un dispositivo dal pannello di riepilogo di uno specifico dispositivo. Per altre informazioni sul pannello di riepilogo dispositivo, vedere [Pannello di riepilogo dispositivo](storsimple-virtual-array-device-summary.md).

## <a name="view-the-activity-logs"></a>Visualizzare i log attività
Per visualizzare le varie operazioni eseguite in Gestione dispositivi StorSimple, fare clic sul collegamento **Log attività** sul lato sinistro del pannello di riepilogo servizio StorSimple. Viene visualizzato il pannello **Log attività** in cui è possibile visualizzare un riepilogo delle operazioni recenti eseguite.

![Log attività](./media/storsimple-virtual-array-service-summary/activity-log.png)

## <a name="next-steps"></a>Passaggi successivi
Scoprire come usare l'interfaccia utente Web locale per [amministrare l'array virtuale StorSimple](storsimple-ova-web-ui-admin.md).




<!--HONumber=Jan17_HO4-->


