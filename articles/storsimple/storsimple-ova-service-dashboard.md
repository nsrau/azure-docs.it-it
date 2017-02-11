---
title: 'Dashboard del servizio StorSimple Manager: array virtuale | Microsoft Docs'
description: "Descrive il dashboard del servizio StorSimple Manager e illustra come usarlo per monitorare l&quot;integrità dell&quot;array virtuale StorSimple."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 36ac6eb4-a616-4bb1-8163-6862ac33da63
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 58aa9508e28a00efe69d0a9cbc8adb6837d085c4


---
# <a name="use-the-storsimple-manager-service-dashboard-for-the-storsimple-virtual-array"></a>Usare il dashboard del servizio StorSimple Manager per l'array virtuale StorSimple
## <a name="overview"></a>Panoramica
La pagina dashboard del servizio StorSimple Manager fornisce una visualizzazione di riepilogo degli array virtuali StorSimple (noti anche come dispositivi virtuali locali StorSimple o dispositivi virtuali) connessi al servizio StorSimple Manager, evidenziando quelli che richiedono attenzione da parte dell'amministratore di sistema. Questa esercitazione introduce la pagina dashboard, illustra il contenuto e la funzione del dashboard e descrive le attività che si possono eseguire da questa pagina.

![Dashboard del servizio](./media/storsimple-ova-service-dashboard/dashboard1.png)

Nel dashboard del servizio StorSimple Manager sono visualizzate le informazioni seguenti:

* Nell'area del **grafico** , nella parte superiore della pagina, è possibili visualizzare le metriche rilevanti per i dispositivi virtuali. È possibile visualizzare l'archiviazione primaria usata in tutti i dispositivi virtuali, nonché l'archiviazione cloud utilizzata dai dispositivi virtuali per un periodo di tempo. Usare i controlli nell'angolo in alto a destra del grafico per specificare l'utilizzo relativo o assoluto e per visualizzare una scala temporale di 1 settimana, 1 mese, 3 mesi o 1 anno. Usare il controllo di aggiornamento ![controllo di aggiornamento](./media/storsimple-ova-service-dashboard/refresh-control.png) per aggiornare il grafico.
* La **panoramica sull'uso** illustra l'archiviazione primaria fornita e usata da tutti i dispositivi virtuali rispetto all'archiviazione totale disponibile tra tutti i dispositivi virtuali. **Fornito** si riferisce alla quantità di spazio di archiviazione preparata e allocata per l'uso, **Utilizzato** si riferisce all'uso delle condivisioni o dei volumi come visualizzato dagli iniziatori connessi ai dispositivi virtuali e **Capacità massima** mostra la capacità massima di tutti i dispositivi virtuali.
* L’area **avvisi** fornisce uno snapshot di tutti gli avvisi attivi per tutti i dispositivi virtuali, raggruppati in base alla gravità. Cliccando sul livello di gravità si apre la pagina **avvisi** , la quale è stata programmata per mostrare gli avvisi. Sulla pagina **avvisi** , è possibile fare clic su un singolo avviso per visualizzare ulteriori dettagli su tale avviso, incluse tutte le operazioni consigliate. È inoltre possibile cancellare l'avviso se il problema è stato risolto.
* L’area **processi** fornisce uno snapshot dei processi recenti in tutti i dispositivi virtuali connessi al servizio. Sono disponibili dei collegamenti che possono essere utilizzati per esaminare i processi attualmente in corso e quelli che hanno avuto esito positivo o negativo nelle ultime 24 ore. 
* L’area di **riepilogo rapido** disponibile nella parte destra della pagina fornisce informazioni utili, ad esempio lo stato del servizio, il numero totale di dispositivi virtuali connessi al servizio, il percorso del servizio e i dettagli della sottoscrizione associata al servizio. Viene inoltre fornito un collegamento al log delle operazioni. Fare clic sul collegamento per visualizzare un elenco di tutte le operazioni completate del servizio StorSimple Manager. 

È possibile usare il servizio StorSimple Manager per eseguire le attività seguenti:

* Ottenere la chiave di registrazione del servizio.
* Visualizzare i log delle operazioni.

## <a name="get-the-service-registration-key"></a>Ottenere la chiave di registrazione del servizio
Il codice di registrazione del servizio viene usato per registrare un dispositivo virtuale StorSimple con il servizio StorSimple Manager, in modo che il dispositivo venga visualizzato nel portale di Azure classico per altre azioni di gestione. La chiave viene creata sul primo dispositivo virtuale ed è condivisa con i rimanenti dispositivi virtuali. 

Facendo clic su **Chiave di registrazione** nella parte inferiore della pagina, è possibile aprire la finestra di dialogo **Chiave di registrazione del servizio**, dalla quale è possibile copiare la chiave di registrazione del servizio corrente negli Appunti o rigenerare il codice di registrazione del servizio.

![chiave di registrazione](./media/storsimple-ova-service-dashboard/service-dashboard3.png)

La rigenerazione della chiave non influisce sui dispositivi virtuali registrati in precedenza: sarà valida solo per i dispositivi virtuali registrati con il servizio dopo la rigenerazione della chiave.

Per altre informazioni sulla chiave di registrazione, vedere il video su [come ottenere la chiave di registrazione del servizio](storsimple-ova-manage-service.md#get-the-service-registration-key).

## <a name="view-the-operations-logs"></a>Visualizzare i log delle operazioni
È possibile visualizzare il log delle operazioni facendo clic sul collegamento dei logo delle operazione disponibile nel riquadro **riepilogo rapido** del dashboard. Verrà visualizzata la pagina Servizi di gestione, dove è possibile filtrare e visualizzare i registri specifici per il servizio StorSimple Manager.

![Log delle operazioni](./media/storsimple-ova-service-dashboard/ops-log.png)

## <a name="next-steps"></a>Passaggi successivi
Scoprire come usare l'interfaccia utente Web locale per [amministrare l'array virtuale StorSimple](storsimple-ova-web-ui-admin.md).




<!--HONumber=Nov16_HO3-->


