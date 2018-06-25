---
title: Creare un ambiente Azure Time Series Insights
description: Informazioni su come creare un ambiente Time Series Insights, popolato con i dati provenienti dai dispositivi simulati.
services: time-series-insights
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 06/04/2018
ms.author: bryanla
ms.openlocfilehash: b57e688b5f830c8c7d34315a68be94e4e4c99466
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2018
ms.locfileid: "36295848"
---
# <a name="tutorial-create-an-azure-time-series-insights-environment"></a>Esercitazione: Creare un ambiente Azure Time Series Insights

Questa esercitazione illustrerà come creare un ambiente Time Series Insight (TSI), popolato con i dati provenienti dai dispositivi simulati. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un ambiente TSI 
> * Creare una soluzione di simulazione di dispositivi contenente un hub IoT
> * Connettere l'ambiente TSI all'hub IoT
> * Eseguire una simulazione di dispositivi per trasmettere dati nell'ambiente TSI
> * Verificare i dati di telemetria simulati

## <a name="prerequisites"></a>prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare. 

L'account di accesso di Azure deve anche essere membro del ruolo "Proprietario" della sottoscrizione. Per maggiori dettagli, vedere [Aggiungere o modificare gli amministratori delle sottoscrizioni di Azure](/azure/billing/billing-add-change-azure-subscription-administrator).

## <a name="overview"></a>Panoramica

L'ambiente TSI è l'ambiente in cui vengono raccolti e archiviati i dati dei dispositivi. Dopo l'archiviazione nell'ambiente TSI, è possibile usare [TSI Explorer](time-series-quickstart.md) e l'[API di query TSI](/rest/api/time-series-insights/time-series-insights-reference-queryapi) per eseguire query e analizzare i dati.

Come tutti i dispositivi, simulati o fisici, l'hub IoT è il punto di connessione usato dai dispositivi per connettersi in modo sicuro e trasmettere dati al cloud di Azure. Come descritto nella [Panoramica su TSI](time-series-insights-overview.md), l'hub IoT funge anche da origine evento per trasmettere dati nell'ambiente TSI. 

Questa esercitazione usa anche un [acceleratore di soluzioni IoT](/azure/iot-accelerators/) per generare e trasmettere dati di telemetria di esempio all'hub IoT. Gli acceleratori di soluzioni IoT forniscono soluzioni aziendali preconfigurate che consentono di accelerare lo sviluppo di soluzioni IoT personalizzate. 

## <a name="create-a-tsi-environment"></a>Creare un ambiente TSI

Per prima cosa, creare un ambiente TSI nella sottoscrizione di Azure:

1. Accedere al [portale di Azure](https://portal.azure.com) usando l'account della sottoscrizione di Azure.  
2. Selezionare **+ Crea una risorsa** in alto a sinistra.  
3. Selezionare la categoria **Internet delle cose** e quindi **Time Series Insights**.  
   
   [![Selezionare la risorsa dell'ambiente Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png#lightbox)

4. Nella pagina **Ambiente Time Series Insights** specificare i parametri richiesti:
   
   Parametro|DESCRIZIONE
   ---|---
   **Nome ambiente** | Scegliere un nome univoco per l'ambiente TSI. Il nome viene usato da TSI Explorer e dall'API di query.
   **Sottoscrizione** | Le sottoscrizioni sono contenitori per le risorse di Azure. Scegliere la sottoscrizione in cui creare l'ambiente TSI.
   **Gruppo di risorse** | Un gruppo di risorse è un contenitore per le risorse di Azure. Scegliere un gruppo di risorse esistente per la risorsa dell'ambiente TSI oppure crearne uno nuovo.
   **Posizione** | Scegliere un'area del data center per l'ambiente TSI. Per evitare un aumento della latenza e dei costi della larghezza di banda, è consigliabile mantenere l'ambiente TSI nella stessa area delle altre risorse IoT.
   **SKU (Piano tariffario)** | Scegliere la velocità effettiva necessaria. Per la capacità iniziale e i costi minimi, selezionare S1.
   **Capacità** | Per Capacità si intende il moltiplicatore applicato alla velocità in ingresso, alla capacità di archiviazione e ai costi associati allo SKU selezionato.  È possibile modificare la capacità di un ambiente dopo la creazione. Per i costi minimi, selezionare una capacità pari a 1. 

   Al termine, fare clic su **Crea** per iniziare il processo di provisioning.

   ![Creare una risorsa dell'ambiente Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-params.png)

5. È possibile controllare il pannello **Notifiche** per monitorare il completamento della distribuzione, che dovrebbe richiedere meno di un minuto:  

   ![Completamento della distribuzione dell'ambiente Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-deployment-succeeded.png)

## <a name="create-a-device-simulation"></a>Creare una simulazione di dispositivi

Successivamente, creare la soluzione di simulazione di dispositivi, che genererà i dati di test per popolare l'ambiente TSI:

1. In una finestra o scheda separata andare a https://www.azureiotsolutions.com, eseguire l'accesso con lo stesso account della sottoscrizione di Azure e selezionare l'acceleratore "Simulazione dispositivi":

   ![Eseguire l'acceleratore Simulazione dispositivi](media/tutorial-create-populate-tsi-environment/sa-main.png)

2. Immettere i parametri richiesti nella pagina **Crea soluzione Simulazione dispositivi**:

   Parametro|DESCRIZIONE
   ---|---
   **Nome soluzione** | Un valore univoco usato per la creazione di un nuovo gruppo di risorse. Le risorse di Azure elencate vengono create e assegnate al gruppo di risorse.
   **Sottoscrizione** | Specificare la stessa sottoscrizione usata per la creazione dell'ambiente TSI nella sezione precedente.
   **Area** | Specificare la stessa area usata per la creazione dell'ambiente TSI nella sezione precedente. 
   **Distribuisci risorse di Azure facoltative** | Lasciare selezionato **Hub IoT**, che verrà usato dai dispositivi simulati per la connessione/trasmissione dei dati.

   Al termine, fare clic su **Crea soluzione** per eseguire il provisioning delle risorse di Azure della soluzione. L'operazione richiederà circa 6-7 minuti:

   ![Eseguire il provisioning della soluzione Simulazione dispositivi](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution.png)

3. Al termine del provisioning, il testo riportato sopra la nuova soluzione verrà modificato da "Provisioning..." a "Pronto":

   >[!IMPORTANT]
   > Non fare ancora clic sul pulsante **Avvia**, ma mantenere aperta questa pagina Web per tornarvi in un secondo momento.

   ![Completamento del provisioning della soluzione Simulazione dispositivi](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard-ready.png)

4. Tornare al portale di Azure e controllare le risorse appena create nella sottoscrizione. Nella pagina del portale **Gruppi di risorse** si noterà che è stato creato un nuovo gruppo di risorse con il **Nome soluzione** specificato nel passaggio precedente. Si notino inoltre tutte le risorse create per supportare la soluzione di simulazione di dispositivi:

   [![Risorse della soluzione Simulazione dispositivi](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png)](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png#lightbox)

## <a name="connect-the-tsi-environment-to-the-iot-hub"></a>Connettere l'ambiente TSI all'hub IoT 

A questo punto, si è appreso come creare due set di risorse, ognuno nel proprio gruppo di risorse: 
- Un ambiente TSI vuoto 
- Risorse della soluzione Simulazione dispositivi, tra cui un hub IoT, generate da un acceleratore di soluzioni

Tenere presente che i dispositivi simulati devono connettersi a un hub IoT per trasmettere i dati dei dispositivi. Per propagare i dati nell'ambiente TSI, è necessario apportare modifiche alla configurazione dell'hub IoT e dell'ambiente TSI. 

### <a name="iot-hub-configuration-define-a-consumer-group"></a>Configurazione dell'hub IoT: definire un gruppo di consumer

L'hub IoT fornisce vari endpoint per condividere funzionalità con altri attori. L'endpoint "Eventi" consente ad altre applicazioni di utilizzare i dati, mentre vengono trasmessi a un'istanza dell'hub IoT. In particolare, i "gruppi di consumer" permettono alle applicazioni di essere in ascolto ed eseguire il pull dei dati dall'hub IoT. 

Successivamente, si definisce una nuova proprietà "gruppo di consumer" nell'endpoint "Eventi" dell'hub IoT della soluzione Simulazione dispositivi. 

1. Nel portale di Azure passare alla pagina **Panoramica** del gruppo di risorse creato per la soluzione Simulazione dispositivi e quindi selezionare la risorsa dell'hub IoT:

   [![Gruppo di risorse della soluzione Simulazione dispositivi](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-view-rg.png)](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-view-rg.png#lightbox)

   Prendere nota anche del **Nome** della risorsa dell'hub IoT generata per la soluzione, poiché sarà necessario farvi riferimento in seguito.

2. Scorrere verso il basso e selezionare la pagina **Endpoint** e quindi selezionare l'endpoint **Eventi**. Nella pagina **Proprietà** dell'endpoint immettere un nome univoco per l'endpoint nel gruppo di consumer "$Default" e quindi fare clic su **Salva**:

   [![Endpoint dell'hub IoT della soluzione Simulazione dispositivi](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-create.png)](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-create.png#lightbox)

### <a name="tsi-environment-configuration-define-an-event-source"></a>Configurazione dell'ambiente TSI: definire un'origine evento

Connettere ora il nuovo endpoint dell'evento "gruppo di consumer" dell'hub IoT all'ambiente TSI, come "origine evento".

1. Passare alla pagina **Panoramica** del gruppo di risorse creato per l'ambiente TSI e quindi selezionare l'ambiente TSI:

   [![Ambiente e gruppo di risorse dell'ambiente TSI](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-view-rg.png)](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-view-rg.png#lightbox)

2. Nella pagina dell'ambiente TSI selezionare **Origini eventi** e quindi fare clic su **+ Aggiungi**:

   ![Panoramica dell'ambiente TSI](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add.png)

3. Immettere i parametri richiesti nella pagina **Nuova origine evento**: 

   Parametro|DESCRIZIONE
   ---|---
   **Nome origine evento** | Richiede un valore univoco, usato per assegnare un nome all'origine evento.
   **Origine** | Selezionare "Hub IoT".
   **Opzione di importazione** | Usare l'impostazione predefinita "Usare un hub IoT delle sottoscrizioni disponibili". Questa opzione comporterà l'inserimento delle sottoscrizioni disponibili nell'elenco a discesa successivo.
   **Sottoscrizione** | Selezionare la stessa sottoscrizione in cui sono stati creati l'ambiente TSI e le risorse di Simulazione dispositivi.
   **Nome hub IoT** | Dovrebbe essere impostato sul valore predefinito del nome dell'hub IoT annotato in precedenza. In caso contrario, selezionare l'hub IoT corretto.
   **Nome criteri hub IoT** | Lasciare il valore predefinito "iothubowner".
   **Gruppo di consumer dell'hub IoT** | Dovrebbe essere impostato sul valore predefinito del nome del gruppo di consumer dell'hub IoT creato in precedenza. In caso contrario, selezionare il nome del gruppo di consumer corretto. 
   **Formato di serializzazione eventi** | Lasciare il valore predefinito "JSON".
   **Nome della proprietà Timestamp** | Specificare "timestamp".

   Al termine, fare clic su **Crea** per aggiungere l'origine evento. Quando si torna alla pagina **Panoramica** del gruppo di risorse, insieme alla risorsa dell'ambiente TSI viene visualizzata una nuova risorsa "Origine evento Time Series Insights".

   ![Nuova origine evento dell'ambiente TSI](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add-event-source.png)

## <a name="run-a-device-simulation-to-stream-data-into-the-tsi-environment"></a>Eseguire una simulazione di dispositivi per trasmettere dati nell'ambiente TSI

Al termine di tutte le operazioni di configurazione, è necessario popolare l'ambiente TSI con dati di esempio provenienti dai dispositivi simulati.

Come descritto nella sezione [Creare una simulazione di dispositivi](#create-a-device-simulation), sono state create diverse risorse di Azure tramite l'acceleratore allo scopo di supportare la soluzione. Insieme all'hub IoT illustrato in precedenza, è stata generata un'applicazione Web di Servizio app di Azure per la creazione e la trasmissione dei dati di telemetria dei dispositivi simulati.

1. Tornare al [dashboard Acceleratori di soluzione](https://www.azureiotsolutions.com/Accelerators#dashboard). Se necessario, eseguire nuovamente l'accesso usando lo stesso account di Azure già usato in questa esercitazione. A questo punto, è possibile fare clic sul pulsante **Avvia** sotto la soluzione "Simulazione dispositivi":

     ![Dashboard Acceleratori di soluzione](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard.png)

2. Verrà quindi avviata l'app Web per la simulazione di dispositivi; l'operazione potrebbe richiedere alcuni secondi dopo il caricamento iniziale. Viene inoltre richiesto il consenso dell'utente per concedere all'applicazione Web l'autorizzazione "Sign you in and read your profile" (Accedi e visualizza il profilo personale). Questa autorizzazione consente all'applicazione di recuperare le informazioni sul profilo utente necessarie per supportare il funzionamento dell'applicazione:

     ![Consenso per l'applicazione Web per la simulazione di dispositivi](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png)

3. Al termine del caricamento della pagina **Simulation setup** (Configurazione simulazione), immettere i parametri richiesti: 

   Parametro|DESCRIZIONE
   ---|---
   **Target IoT Hub** (Hub IoT di destinazione) | Selezionare "Use pre-provisioned IoT Hub" (Usa l'hub IoT di cui è già stato eseguito il provisioning).
   **Device model** (Modello del dispositivo) | Selezionare "Chiller" (Refrigeratore).
   **Number of devices** (Numero di dispositivi)  | Immettere 1000 in **Amount** (Numero).
   **Telemetry frequency** (Frequenza telemetria) | Immettere 10 secondi.
   **Simulation duration** (Durata simulazione) | Selezionare **End in:** (Termina in) e immettere 5 minuti.

   Al termine, fare clic su **Start Simulation** (Avvia simulazione). La simulazione verrà eseguita per un totale di 5 minuti, generando dati da 1000 dispositivi simulati, ogni 10 secondi.  

   ![Configurazione Simulazione dispositivi](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png)

4. Mentre è in esecuzione la simulazione, si noterà che i campi **Total messages** (Totale messaggi) e **Messages per second** (Messaggi al secondo) vengono aggiornati, approssimativamente ogni 10 secondi. La simulazione terminerà dopo circa 5 minuti e si tornerà alla pagina **Simulation setup** (Configurazione simulazione).

   ![Simulazione dispositivi in esecuzione](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png)

## <a name="verify-the-telemetry-data"></a>Verificare i dati di telemetria

In questa sezione finale si verifica che i dati di telemetria siano stati generati e archiviati nell'ambiente TSI. Per verificare i dati, si usa Time Series Insights Explorer, che consente di eseguire query e analizzare i dati di telemetria.

1. Tornare alla pagina **Panoramica** del gruppo di risorse dell'ambiente TSI e quindi selezionare nuovamente l'ambiente TSI:

   [![Ambiente e gruppo di risorse dell'ambiente TSI](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png#lightbox)

2. Nella pagina **Panoramica** dell'ambiente TSI fare clic su **URL di Time Series Insights Explorer** per aprire TSI Explorer:

   [![TSI Explorer](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png#lightbox)

3. TSI Explorer verrà caricato e sarà eseguita l'autenticazione tramite l'account del portale di Azure. Dopo la visualizzazione iniziale, è possibile verificare nell'area grafico che l'ambiente TSI sia stato effettivamente popolato con i dati di telemetria simulati. Per filtrare un intervallo di tempo più ristretto, selezionare l'elenco a discesa in alto a sinistra. Immettere quindi un intervallo di tempo sufficientemente ampio da coprire l'intera durata della simulazione del dispositivo. Fare quindi clic sul pulsante con la lente di ingrandimento per la ricerca:

   [![Filtro dell'intervallo di tempo in TSI Explorer](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png#lightbox)

4. Limitando l'intervallo di tempo, all'interno del grafico vengono ingranditi i picchi distinti del trasferimento dei dati, l'hub IoT e l'ambiente TSI. Si noti anche il testo **Streaming completato** in alto a destra, che indica il numero totale di eventi trovati. È anche possibile trascinare il dispositivo di scorrimento **Dimensioni intervallo** per controllare la granularità del tracciato nel grafico:

   [![Visualizzazione filtrata dell'intervallo di tempo in TSI Explorer](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png#lightbox)

5. Infine, è anche possibile fare clic con il pulsante sinistro del mouse su un'area per filtrare un intervallo e quindi fare clic con il pulsante destro del mouse e usare **Esplora eventi** per mostrare i dettagli degli eventi nella visualizzazione tabulare **Eventi**:

   [![Visualizzazione filtrata ed eventi dell'intervallo di tempo in TSI Explorer](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png#lightbox)

## <a name="clean-up-resources"></a>Pulire le risorse

Questa esercitazione consente di creare diversi servizi di Azure in esecuzione per supportare l'ambiente TSI e la soluzione di simulazione di dispositivi. Se si vuole interrompere e/o posticipare il completamento di questa serie di esercitazioni, è consigliabile eliminare tutte le risorse per evitare costi non necessari. 

Dal menu a sinistra nel portale di Azure:

1. Fare clic sull'icona **Gruppi di risorse** e quindi selezionare il gruppo di risorse creato per l'ambiente TSI. Nella parte superiore della pagina fare clic su **Elimina gruppo di risorse**, immettere il nome del gruppo di risorse e quindi fare clic su **Elimina**. 
2. Fare clic sull'icona **Gruppi di risorse** e quindi selezionare il gruppo di risorse creato dall'acceleratore di soluzione Simulazione dispositivi. Nella parte superiore della pagina fare clic su **Elimina gruppo di risorse**, immettere il nome del gruppo di risorse e quindi fare clic su **Elimina**. 

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Creare un ambiente TSI 
> * Creare una soluzione di simulazione di dispositivi contenente un hub IoT
> * Connettere l'ambiente TSI all'hub IoT
> * Eseguire una simulazione di dispositivi per trasmettere dati nell'ambiente TSI
> * Verificare i dati di telemetria simulati

Ora che si è appreso come creare l'ambiente TSI, vedere come creare un'applicazione Web che utilizza dati di un ambiente TSI:

> [!div class="nextstepaction"]
> [Creare un'app Web a singola pagina Azure Time Series Insights](tutorial-create-tsi-sample-spa.md)


