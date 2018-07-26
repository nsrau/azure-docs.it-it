---
title: Usare il dashboard di connected factory - Azure | Microsoft Docs
description: Informazioni su come usare le funzionalità del dashboard di connected factory.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 07/10/2018
ms.author: dobett
ms.openlocfilehash: 4af93b5667181941585cc1ac89f5696379962026
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2018
ms.locfileid: "39075300"
---
# <a name="use-features-in-the-connected-factory-solution-accelerator-dashboard"></a>Usare le funzionalità nel dashboard dell'acceleratore di soluzioni di connected factory

L'avvio rapido [Deploy a cloud-based solution to manage my industrial IoT devices](quickstart-connected-factory-deploy.md) (Distribuzione di una soluzione basata sul cloud per gestire i dispositivi IoT industriali) ha illustrato come accedere al dashboard e rispondere agli avvisi. Questa guida pratica illustra alcune funzionalità aggiuntive del dashboard che è possibile usare per monitorare e gestire i dispositivi IoT industriali.

## <a name="apply-filters"></a>Applicare filtri

È possibile filtrare le informazioni visualizzate nel dashboard sia nel pannello **Località della fabbrica** o nel pannello **Avvisi**:

1. Fare clic sull'icona dell'**imbuto** per visualizzare un elenco dei filtri disponibili nel pannello delle località degli stabilimenti o nel pannello degli avvisi.

1. Verrà visualizzato il pannello dei filtri:

    [![Filtri dell'acceleratore di soluzioni di connected factory](./media/iot-accelerators-connected-factory-dashboard/filterpanel-inline.png)](./media/iot-accelerators-connected-factory-dashboard/filterpanel-expanded.png#lightbox)

1. Scegliere il filtro necessario e fare clic su **Applica**. È anche possibile digitare testo libero nei campi di filtro.

1. Il filtro verrà quindi applicato. L'icona del grafico a imbuto extra indica che è applicato un filtro:

    [![Filtro dell'acceleratore di soluzioni di connected factory applicato](./media/iot-accelerators-connected-factory-dashboard/filterapplied-inline.png)](./media/iot-accelerators-connected-factory-dashboard/filterapplied-expanded.png#lightbox)

    > [!NOTE]
    > Un filtro attivo non ha effetto sui valori di OEE e KPI visualizzati, filtra solo il contenuto dell'elenco.

1. Per cancellare un filtro, fare clic sul grafico a imbuto e quindi fare clic su **Cancella** nel pannello dei filtri.

## <a name="browse-an-opc-ua-server"></a>Esplorare un server OPC UA

Quando si distribuisce l'acceleratore di soluzioni, viene effettuato automaticamente il provisioning di un set di server OPC UA simulati che è possibile esplorare tramite il dashboard. I server simulati consentono di provare facilmente l'acceleratore di soluzioni senza la necessità di distribuire server reali. Per connettere un server OPC UA reale alla soluzione, vedere l'esercitazione [Connettere il dispositivo OPC UA all'acceleratore di soluzioni di connected factory](iot-accelerators-connected-factory-gateway-deployment.md).

1. Fare clic sull'**icona del browser** nella barra di spostamento del dashboard:

    [![Browser del server dell'acceleratore di soluzioni di connected factory](./media/iot-accelerators-connected-factory-dashboard/browser-inline.png)](./media/iot-accelerators-connected-factory-dashboard/browser-expanded.png#lightbox)

1. Nell'elenco che mostra i server distribuiti dall'acceleratore di soluzioni scegliere uno dei server:

    [![Elenco dei server dell'acceleratore di soluzioni di connected factory](./media/iot-accelerators-connected-factory-dashboard/serverlist-inline.png)](./media/iot-accelerators-connected-factory-dashboard/serverlist-expanded.png#lightbox)

1. Fare clic su **Connect** (Connetti). Verrà visualizzata una finestra di dialogo di sicurezza. Per la simulazione è possibile fare clic su **Proceed** (Continua).

1. Per espandere uno dei nodi dell'albero dei server, fare clic su di esso. I nodi che pubblicano i dati di telemetria hanno un segno di spunta accanto:

    [![Albero dei server dell'acceleratore di soluzioni di connected factory](./media/iot-accelerators-connected-factory-dashboard/servertree-inline.png)](./media/iot-accelerators-connected-factory-dashboard/servertree-expanded.png#lightbox)

1. Fare clic con il pulsante destro del mouse su un elemento per leggere, scrivere, pubblicare o chiamare il nodo. Le azioni disponibili variano a seconda delle autorizzazioni e degli attributi del nodo. L'opzione di lettura visualizza un pannello contestuale con il valore del nodo specifico. L'opzione di scrittura visualizza un pannello contestuale in cui è possibile immettere un nuovo valore. L'opzione di chiamata visualizza un nodo in cui è possibile immettere i parametri per la chiamata.

## <a name="publish-a-node"></a>Pubblicare un nodo

Quando si esplora un *server OPC UA simulato* è anche possibile scegliere di pubblicare nuovi nodi. È possibile analizzare i dati di telemetria da questi nodi nella soluzione. Questi *server OPC UA simulati* consentono di provare facilmente l'acceleratore di soluzioni senza distribuire dispositivi reali:

1. Passare a un nodo da pubblicare nell'albero del browser dei server OPC UA.

1. Fare clic con il pulsante destro del mouse sul nodo. Fare clic su **Pubblica**:

    [![Nodo di pubblicazione acceleratore di soluzioni di connected factory](./media/iot-accelerators-connected-factory-dashboard/publishnode-inline.png)](./media/iot-accelerators-connected-factory-dashboard/publishnode-expanded.png#lightbox)

1. Verrà visualizzato un pannello contestuale che indica che la pubblicazione ha avuto esito positivo. Il nodo viene visualizzato al livello delle postazioni con un segno di spunta accanto:

    [![Pubblicazione acceleratore di soluzioni di connected factory riuscita](./media/iot-accelerators-connected-factory-dashboard/publishsuccess-inline.png)](./media/iot-accelerators-connected-factory-dashboard/publishsuccess-expanded.png#lightbox)

## <a name="command-and-control"></a>Comando e controllo

La soluzione Connected Factory consente di comandare e controllare i dispositivi industriali direttamente dal cloud. È possibile usare questa funzionalità per rispondere agli avvisi generati dal dispositivo. È ad esempio possibile inviare un comando al dispositivo per aprire una valvola di rilascio della pressione. È possibile trovare i comandi disponibili nel nodo **StationCommands** dell'albero del browser dei server OPC UA. In questo scenario si apre una valvola di sfiato nella postazione di un gruppo di componenti di una linea di produzione situata a Monaco di Baviera. Per usare la funzionalità di comando e controllo è necessario il ruolo di **Amministratore** per la distribuzione dell'acceleratore di soluzioni:

1. Selezionare il nodo **StationCommands** nell'albero del browser del server OPC UA per Monaco, linea di produzione 0, stazione di assemblaggio.

1. Scegliere il comando che si vuole usare. Fare doppio clic sul nodo **OpenPressureReleaseValve**. Fare clic su **Call** (Chiama):

    [![Comando di chiamata dell'acceleratore di soluzioni di connected factory](./media/iot-accelerators-connected-factory-dashboard/callcommand-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callcommand-expanded.png#lightbox)

1. Viene visualizzato un pannello contestuale che indica il metodo che si sta per chiamare e i dettagli dei parametri. Fare clic su **Call** (Chiama):

    [![Parametri di chiamata dell'acceleratore di soluzioni di connected factory](./media/iot-accelerators-connected-factory-dashboard/callpanel-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callpanel-expanded.png#lightbox)

1. Il pannello contestuale viene aggiornato per indicare che la chiamata al metodo ha avuto esito positivo. È possibile verificare che la chiamata abbia avuto esito positivo leggendo il valore del nodo della pressione aggiornato in seguito alla chiamata.

    [![Chiamata riuscita dell'acceleratore di soluzioni di connected factory](./media/iot-accelerators-connected-factory-dashboard/callsuccess-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callsuccess-expanded.png#lightbox)

## <a name="behind-the-scenes"></a>Dietro le quinte

Quando si distribuisce un acceleratore di soluzioni, il processo di distribuzione crea più risorse nella sottoscrizione di Azure selezionata. È possibile visualizzare queste risorse nel [portale](https://portal.azure.com) di Azure. Il processo di distribuzione crea un **gruppo di risorse** con un nome basato sul nome scelto per l'acceleratore di soluzioni:

[![Gruppo di risorse dell'acceleratore di soluzioni di connected factory](./media/iot-accelerators-connected-factory-dashboard/resourcegroup-inline.png)](./media/iot-accelerators-connected-factory-dashboard/resourcegroup-expanded.png#lightbox)

È possibile visualizzare le impostazioni di ogni risorsa selezionandola nell'elenco di risorse nel gruppo di risorse.

È anche possibile visualizzare il codice sorgente dell'acceleratore di soluzioni di connected factory nel repository GitHub [azure-iot-connected-factory](https://github.com/Azure/azure-iot-connected-factory).

Al termine, è possibile eliminare l'acceleratore di soluzioni dalla sottoscrizione di Azure nel sito [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators#dashboard). Questo sito consente di eliminare facilmente tutte le risorse di cui è stato effettuato il provisioning quando si è creato l'acceleratore di soluzioni.

> [!NOTE]
> Per assicurarsi di eliminare tutti gli elementi correlati all'acceleratore di soluzioni, eseguire l'eliminazione dal sito [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators#dashboard). Non eliminare il gruppo di risorse nel portale.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver distribuito un acceleratore di soluzioni funzionante è possibile proseguire con l'introduzione agli acceleratori di soluzioni IoT leggendo gli articoli seguenti:

* [Procedura dettagliata dell'acceleratore di soluzioni di connected factory](iot-accelerators-connected-factory-sample-walkthrough.md)
* [Connect your device to the Connected Factory solution accelerator](iot-accelerators-connected-factory-gateway-deployment.md) (Connettere il dispositivo all'acceleratore di soluzioni di connected factory)
* [Autorizzazioni per il sito azureiotsolutions.com](iot-accelerators-permissions.md)
