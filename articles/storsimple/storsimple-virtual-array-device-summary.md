---
title: Pannello di riepilogo dispositivo array virtuale StorSimple | Documentazione Microsoft
description: "Descrive il pannello di riepilogo dispositivo per Gestione dispositivi StorSimple e illustra come usarlo per monitorare l'integrità dell'array virtuale StorSimple."
services: storsimple
documentationcenter: 
author: manuaery
manager: syadav
editor: 
ms.assetid: a13c1ea7-6428-4234-84a6-0ebf51670a85
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2016
ms.author: manuaery
ms.openlocfilehash: 35413d597c3b6b1c7600241a78572b63f982d175
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-device-summary-blade-for-storsimple-device-manager-connected-to-storsimple-virtual-array"></a>Usare il pannello di riepilogo dispositivo per il servizio Gestione dispositivi StorSimple connesso all'array virtuale StorSimple

## <a name="overview"></a>Panoramica

Il pannello del dispositivo di Gestione dispositivi StorSimple visualizza un riepilogo di un array virtuale StorSimple registrato con uno specifico servizio Gestione dispositivi StorSimple, evidenziando i problemi relativi al dispositivo che richiedono attenzione da parte dell'amministratore di sistema. Questa esercitazione introduce il pannello di riepilogo del dispositivo, illustra il contenuto e la funzione e descrive le attività che è possibile eseguire da questo pannello.

Il pannello di riepilogo dispositivo contiene le informazioni seguenti:

![Pagina dashboard](./media/storsimple-virtual-array-device-summary/device-blade.png)



## <a name="management"></a>Gestione

Il pannello del dispositivo StorSimple contiene le opzioni per la gestione del dispositivo StorSimple in uso. I comandi per la gestione vengono visualizzati nella parte superiore del pannello e sul lato sinistro. Usare queste opzioni per aggiungere condivisioni o volumi, aggiornare o eseguire il failover dell'array virtuale.

L'area relativa alle informazioni di base riporta alcune proprietà importanti, ad esempio lo stato, il modello, la versione del software e un collegamento **all'interfaccia utente Web** dell'array. Se si usa una rete interna, è possibile avviare direttamente l'[interfaccia utente Web locale](storsimple-ova-web-ui-admin.md) per amministrare l'array virtuale.

![Informazioni di base sui dispositivi](./media/storsimple-virtual-array-device-summary/device-essentials.png)

## <a name="storsimple-device-summary"></a>Riepilogo dispositivo StorSimple

* Il riquadro **Avvisi** fornisce uno snapshot di tutti gli avvisi attivi per l'array virtuale, raggruppati in base alla gravità. Fare clic sul riquadro per aprire il pannello **Avvisi**, quindi fare clic su un singolo avviso per visualizzare altri dettagli specifici, incluse tutte le operazioni consigliate. È inoltre possibile cancellare l'avviso se il problema è stato risolto.

* Il riquadro **Capacità** mostra l'archiviazione primaria di cui è stato eseguito il provisioning e quella rimanente nel dispositivo virtuale rispetto all'archiviazione totale disponibile per lo stesso dispositivo. **Provisioning** fa riferimento alla quantità di spazio di archiviazione preparata e allocata per l'uso; **Rimanente** fa riferimento alla capacità rimanente di cui è possibile eseguire il provisioning in questo dispositivo. **Rimanente a livelli** è la capacità disponibile di cui è possibile eseguire il provisioning, tra cui il cloud, mentre **Rimanente locale** è la capacità rimanente sui dischi collegati a questo array virtuale.

* Nel grafico **Utilizzo** è possibile visualizzare l'archiviazione primaria usata in tutti gli array virtuali e l'archiviazione cloud usata negli ultimi sette giorni, il periodo di tempo predefinito. Usare l'opzione **Modifica** nell'angolo superiore destro del grafico per scegliere una scala cronologica differente.

* Il riquadro **Condivisioni** o **Volumi** fornisce un riepilogo del numero di condivisioni o volumi nel dispositivo raggruppati per stato. Fare clic sul riquadro per aprire il pannello di elenco **Condivisioni** o **Volumi** e quindi fare clic su una singola condivisione o un singolo volume per visualizzare o modificare le relative proprietà. Per altre informazioni, vedere l'articolo su come [gestire le condivisioni](storsimple-virtual-array-manage-shares.md) o [gestire i volumi](storsimple-virtual-array-manage-volumes.md).

## <a name="next-steps"></a>Passaggi successivi
È possibile passare agli argomenti seguenti:
- [Gestire condivisioni su un array virtuale StorSimple](storsimple-virtual-array-manage-shares.md)
    
- [Gestire volumi su un array virtuale StorSimple](storsimple-virtual-array-manage-volumes.md)

