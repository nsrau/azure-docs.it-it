---
title: 'Esercitazione: Creare un ambiente - Azure Time Series Insights | Microsoft Docs'
description: Informazioni su come creare un ambiente Time Series Insights, popolato con i dati provenienti dai dispositivi simulati.
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 12/04/2019
ms.custom: seodec18
ms.openlocfilehash: 387f3df7313c26c5cd8f7fee7c9b58ec3b5c4552
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872410"
---
# <a name="tutorial-create-an-azure-time-series-insights-environment"></a>Esercitazione: Creare un ambiente Azure Time Series Insights

Questa esercitazione illustra come creare un ambiente Azure Time Series Insights, popolato con i dati provenienti da dispositivi simulati. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un ambiente Time Series Insights
> * Creare una soluzione di simulazione di dispositivi contenente un hub IoT.
> * Connettere l'ambiente Time Series Insights all'hub IoT.
> * Eseguire una simulazione di dispositivi per trasmettere dati nell'ambiente Time Series Insights.
> * Verificare i dati di telemetria simulati.

> [!IMPORTANT]
> Registrarsi per creare una [sottoscrizione di Azure gratuita](https://azure.microsoft.com/free/), se non è già stato fatto.

## <a name="prerequisites"></a>Prerequisiti

* L'account di accesso di Azure deve anche essere membro del ruolo **Proprietario** della sottoscrizione. Per altre informazioni, vedere [Gestire l'accesso mediante il controllo degli accessi in base al ruolo e il portale di Azure](../role-based-access-control/role-assignments-portal.md).

## <a name="review-video"></a>Vedere il video

### <a name="learn-how-to-use-an-azure-iot-solution-accelerator-to-generate-data-and-get-started-with-time-series-insights-br"></a>Questo video illustra come usare un acceleratore di soluzione di Azure IoT per generare dati da usare per iniziare a usare Time Series Insights. </br>

> [!VIDEO https://www.youtube.com/embed/6ehNf6AJkFo]

## <a name="overview"></a>Panoramica

L'ambiente Time Series Insights è l'ambiente in cui vengono raccolti e archiviati i dati dei dispositivi. Dopo l'archiviazione, è possibile usare [Azure Time Series Insights Explorer](time-series-quickstart.md) e l'[API di query di Time Series Insights](/rest/api/time-series-insights/ga-query-api) per eseguire query e analizzare i dati.

L'hub IoT di Azure è l'origine evento usata da tutti i dispositivi (simulati o fisici) nell'esercitazione per connettersi in modo sicuro e trasmettere dati al cloud di Azure.

Questa esercitazione usa anche un [acceleratore di soluzione IoT](https://www.azureiotsolutions.com) per generare e trasmettere dati di telemetria di esempio all'hub IoT.

>[!TIP]
> Gli [acceleratori di soluzioni IoT](https://www.azureiotsolutions.com) offrono soluzioni preconfigurate di livello aziendale utilizzabili per accelerare lo sviluppo di soluzioni IoT personalizzate.

## <a name="create-a-device-simulation"></a>Creare una simulazione di dispositivi

Creare prima di tutto la soluzione di simulazione di dispositivi che genera i dati di test per popolare l'ambiente Time Series Insights.

1. In una finestra o scheda separata passare a [azureiotsolutions.com](https://www.azureiotsolutions.com). Accedere con lo stesso account della sottoscrizione di Azure e selezionare l'acceleratore **Simulazione dispositivi**.

   [![Eseguire l'acceleratore Simulazione dispositivi](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-landing-page.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-landing-page.png#lightbox)

1.  Selezionare **Prova adesso**. Immettere i parametri necessari nella pagina di **creazione della soluzione Simulazione dispositivi**.

   Parametro|DESCRIZIONE
   ---|---
   **Nome distribuzione** | Questo valore univoco viene usato per creare un nuovo gruppo di risorse. Le risorse di Azure elencate vengono create e assegnate al gruppo di risorse.
   **Sottoscrizione di Azure** | Specificare la stessa sottoscrizione usata per creare l'ambiente Time Series Insights nella sezione precedente.
   **Opzioni di distribuzione** | Selezionare **Provision new IoT Hub** (Effettuare il provisioning di un nuovo hub IoT) per creare un nuovo hub IoT specifico per questa esercitazione.
   **Località di Azure** | Specificare la stessa area usata per creare l'ambiente Time Series Insights nella sezione precedente.

   Al termine, selezionare **Crea** per effettuare il provisioning delle risorse di Azure della soluzione. Il completamento del processo può richiedere fino a 20 minuti.

   [![Eseguire il provisioning della soluzione Simulazione dispositivi](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-configuration.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-configuration.png#lightbox)

1. Al termine del provisioning, verranno visualizzati due aggiornamenti che mostrano che lo stato della distribuzione è passato da **Provisioning** a **Pronto**. 

   >[!IMPORTANT]
   > Non immettere ancora l'acceleratore di soluzione, ma mantenere aperta questa pagina Web per tornarvi in un secondo momento.

   [![Completamento del provisioning della soluzione Simulazione dispositivi](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png#lightbox)

1. Esaminare ora le risorse appena create nel portale di Azure. Nella pagina **Gruppi di risorse** si noti che è stato creato un nuovo gruppo di risorse con il **Nome soluzione** specificato nel passaggio precedente. Prendere nota delle risorse create per la simulazione di dispositivi.

   [![Risorse per la simulazione di dispositivi](media/tutorial-create-populate-tsi-environment/tsi-device-sim-solution-resources.png)](media/tutorial-create-populate-tsi-environment/tsi-device-sim-solution-resources.png#lightbox)

## <a name="create-an-environment"></a>Creare un ambiente

Creare quindi un ambiente Time Series Insights nella sottoscrizione di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com) usando l'account della sottoscrizione di Azure. 
1. Selezionare **+ Crea una risorsa** in alto a sinistra. 
1. Selezionare la categoria **Internet delle cose** e quindi **Time Series Insights**. 

   [![Selezionare la risorsa dell'ambiente Time Series Insights](media/tutorial-create-populate-tsi-environment/tsi-create-new-environment.png)](media/tutorial-create-populate-tsi-environment/tsi-create-new-environment.png#lightbox)

1. Nella pagina **Ambiente Time Series Insights** specificare i parametri richiesti.

   Parametro|DESCRIZIONE
   ---|---
   **Nome ambiente** | Scegliere un nome univoco per l'ambiente di Time Series Insights. I nomi vengono usati da Time Series Insights Explorer e dalle [API di query](https://docs.microsoft.com/rest/api/time-series-insights/ga-query).
   **Sottoscrizione** | Le sottoscrizioni sono contenitori per le risorse di Azure. Scegliere una sottoscrizione in cui creare l'ambiente Time Series Insights.
   **Gruppo di risorse** | Un gruppo di risorse è un contenitore per le risorse di Azure. Scegliere un gruppo di risorse esistente o crearne uno nuovo per la risorsa dell'ambiente di Time Series Insights.
   **Posizione** | Scegliere un'area del data center per l'ambiente di Time Series Insights. Per evitare un aumento della latenza, creare l'ambiente Time Series Insights nella stessa area delle altre risorse IoT.
   **Livello** | Scegliere la velocità effettiva necessaria. Selezionare **S1**.
   **Capacity** | Per capacità si intende il moltiplicatore applicato alla velocità in ingresso e alla capacità di archiviazione associate allo SKU selezionato. È possibile cambiare la capacità dopo la creazione. Selezionare una capacità pari a **1**.

   Al termine, selezionare **Avanti: Origine evento** per procedere con il passaggio successivo.

   [![Creare una risorsa dell'ambiente Time Series Insights](media/tutorial-create-populate-tsi-environment/tsi-create-resource-tsi-params.png)](media/tutorial-create-populate-tsi-environment/tsi-create-resource-tsi-params.png#lightbox)

1. Connettere ora l'ambiente Time Series Insights all'hub IoT creato dall'acceleratore di soluzione. Impostare **Selezionare un hub** su `Select existing`. Scegliere quindi l'hub IoT creato dall'acceleratore di soluzione al momento dell'impostazione del **Nome hub IoT**.

   [![Connettere l'ambiente Time Series Insights all'hub IoT creato](media/tutorial-create-populate-tsi-environment/tsi-create-resource-iot-hub.png)](media/tutorial-create-populate-tsi-environment/tsi-create-resource-iot-hub.png#lightbox)

   Selezionare infine **Rivedi e crea**.

1. Controllare il riquadro **Notifiche** per monitorare il completamento della distribuzione. 

   [![Distribuzione dell'ambiente Time Series Insights riuscita](media/tutorial-create-populate-tsi-environment/create-resource-tsi-deployment-succeeded.png)](media/tutorial-create-populate-tsi-environment/create-resource-tsi-deployment-succeeded.png#lightbox)

## <a name="run-device-simulation"></a>Eseguire una simulazione di dispositivi

Ora che la distribuzione e la configurazione iniziale sono state completate, popolare l'ambiente Time Series Insights con i dati di esempio dai [dispositivi simulati creati dall'acceleratore](#create-a-device-simulation).

Insieme all'hub IoT è stata generata un'applicazione Web del servizio app di Azure per la creazione e la trasmissione di dati di telemetria dei dispositivi simulati.

1. Tornare al [dashboard Acceleratori di soluzione](https://www.azureiotsolutions.com/Accelerators#dashboard). Se necessario, eseguire nuovamente l'accesso usando lo stesso account di Azure già usato in questa esercitazione. Selezionare la "soluzione per dispositivi" e quindi **passare all'acceleratore di soluzione** per avviare la soluzione distribuita.

   [![Dashboard Acceleratori di soluzione](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png#lightbox)

1. All'inizio, l'app Web di simulazione dei dispositivi richiede il consenso dell'utente per la concessione all'applicazione Web dell'autorizzazione **Accedi e visualizza il profilo personale**. Questa autorizzazione consente di recuperare le informazioni sul profilo utente necessarie per supportare il funzionamento dell'applicazione:

   [![Consenso per l'applicazione Web per la simulazione di dispositivi](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png)](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png#lightbox)

1. Selezionare **+ Nuova simulazione**. Al termine del caricamento della pagina **Simulation setup** (Configurazione simulazione), immettere i parametri richiesti.

   Parametro|DESCRIZIONE
   ---|---
   **Target IoT Hub** (Hub IoT di destinazione) | Selezionare **Use pre-provisioned IoT Hub** (Usa l'hub IoT di cui è già stato eseguito il provisioning).
   **Device model** (Modello del dispositivo) | Selezionare **Chiller** (Refrigeratore).
   **Number of devices** (Numero di dispositivi)  | Immettere `10` in **Amount** (Quantità).
   **Telemetry frequency** (Frequenza telemetria) | Immettere `10` secondi.
   **Simulation duration** (Durata simulazione) | Selezionare **End in:** (Termina in) e immettere `5` minuti.

   Al termine, selezionare **Avvia simulazione**. La simulazione viene eseguita per un totale di 5 minuti. Genera i dati da 1.000 dispositivi simulati ogni 10 secondi. 

   [![Configurazione Simulazione dispositivi](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png#lightbox)

1. Mentre è in esecuzione la simulazione, si noterà che i campi **Total messages** (Totale messaggi) e **Messages per second** (Messaggi al secondo) vengono aggiornati, approssimativamente ogni 10 secondi. La simulazione terminerà dopo circa 5 minuti e si tornerà alla pagina **Simulation setup** (Configurazione simulazione).

   [![Simulazione dispositivi in esecuzione](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png#lightbox)

## <a name="verify-the-telemetry-data"></a>Verificare i dati di telemetria

In questa sezione finale si verifica che i dati di telemetria siano stati generati e archiviati nell'ambiente Time Series Insights. Per verificare i dati, si usa Time Series Insights Explorer, che consente di eseguire query e analizzare i dati di telemetria.

1. Tornare nella pagina **Panoramica** del gruppo di risorse dell'ambiente Time Series Insights. Selezionare l'ambiente Time Series Insights.

   [![Gruppo di risorse e ambiente dell'ambiente Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png#lightbox)

1. Nella pagina **Panoramica** dell'ambiente Time Series Insights selezionare l'**URL dello strumento di esplorazione di Time Series Insights** per aprire lo strumento di esplorazione di Time Series Insights.

   [![Strumento di esplorazione di Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png#lightbox)

1. Lo strumento di esplorazione di Time Series Insights verrà caricato e sarà eseguita l'autenticazione tramite l'account del portale di Azure. Dopo la visualizzazione iniziale, è possibile verificare nell'area grafico che l'ambiente Time Series Insights sia stato popolato con i dati di telemetria simulati. Per filtrare un intervallo di tempo più ristretto, selezionare l'elenco a discesa nell'angolo in alto a sinistra. Immettere un intervallo di tempo sufficientemente ampio da coprire l'intera durata della simulazione del dispositivo. Selezionare quindi la lente di ingrandimento Cerca.

   [![Filtro dell'intervallo di tempo dello strumento di esplorazione di Azure Time Series Insights](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png#lightbox)

1. Limitando l'intervallo di tempo, all'interno del grafico vengono ingranditi i picchi distinti del trasferimento dei dati, l'hub IoT e l'ambiente Time Series Insights. Si noti anche il testo **Streaming completato** nell'angolo in alto a destra, che indica il numero totale di eventi trovati. È anche possibile trascinare il dispositivo di scorrimento **Dimensioni intervallo** per controllare la granularità del tracciato nel grafico.

   [![Vista dell'intervallo di tempo filtrato dello strumento di esplorazione di Time Series Insights](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png#lightbox)

1. Infine, è anche possibile fare clic con il pulsante sinistro del mouse su un'area per filtrare un intervallo. Quindi fare clic con il pulsante destro del mouse e usare **Esplora eventi** per mostrare i dettagli degli eventi nella visualizzazione tabulare **Eventi**.

   [![Eventi e vista dell'intervallo di tempo filtrato dello strumento di esplorazione di Time Series Insights](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png#lightbox)

## <a name="clean-up-resources"></a>Pulire le risorse

Questa esercitazione consente di creare diversi servizi di Azure in esecuzione per supportare l'ambiente Time Series Insights e la soluzione di simulazione di dispositivi. Per rimuoverli, tornare al portale di Azure.

Dal menu a sinistra nel portale di Azure:

1. Selezionare l'icona **Gruppi di risorse**. Quindi selezionare il gruppo di risorse creato per l'ambiente Time Series Insights. Nella parte superiore della pagina selezionare **Elimina gruppo di risorse**, immettere il nome del gruppo di risorse e quindi selezionare **Elimina**.

1. Selezionare l'icona **Gruppi di risorse**. Quindi selezionare il gruppo di risorse creato dall'acceleratore di soluzione per la simulazione del dispositivo. Nella parte superiore della pagina selezionare **Elimina gruppo di risorse**, immettere il nome del gruppo di risorse e quindi selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Creare un ambiente Time Series Insights
> * Creare una soluzione di simulazione di dispositivi contenente un hub IoT.
> * Connettere l'ambiente Time Series Insights all'hub IoT.
> * Eseguire una simulazione di dispositivi per trasmettere dati nell'ambiente Time Series Insights.
> * Verificare i dati di telemetria simulati.

Ora che si è appreso come creare l'ambiente Time Series Insights, vedere come creare un'applicazione Web che usa dati di un ambiente Time Series Insights:

> [!div class="nextstepaction"]
> [Vedere esempi di visualizzazione del SDK client ospitato](https://tsiclientsample.azurewebsites.net/)
