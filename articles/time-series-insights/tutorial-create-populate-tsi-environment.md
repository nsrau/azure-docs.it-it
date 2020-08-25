---
title: 'Esercitazione: Creare un ambiente - Azure Time Series Insights | Microsoft Docs'
description: Informazioni su come creare un ambiente Azure Time Series Insights popolato con dati provenienti da dispositivi simulati.
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 08/14/2020
ms.custom: seodec18
ms.openlocfilehash: 36687f6394ef03a3fceb70c73601680dd262d90c
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/15/2020
ms.locfileid: "88245401"
---
# <a name="tutorial-create-an-azure-time-series-insights-gen1-environment"></a>Esercitazione: Creare un ambiente Azure Time Series Insights Gen1

Questa esercitazione illustra come creare un ambiente Azure Time Series Insights, popolato con i dati provenienti da dispositivi simulati. In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
>
> * Creare un ambiente Azure Time Series Insights.
> * Creare una soluzione di simulazione di dispositivi contenente un hub IoT.
> * Connettere l'ambiente Azure Time Series Insights all'hub IoT.
> * Eseguire una simulazione di dispositivi per trasmettere dati nell'ambiente Azure Time Series Insights.
> * Verificare i dati di telemetria simulati.

> [!IMPORTANT]
> Registrarsi per creare una [sottoscrizione di Azure gratuita](https://azure.microsoft.com/free/), se non è già stato fatto.

## <a name="prerequisites"></a>Prerequisiti

* L'account di accesso di Azure deve anche essere membro del ruolo **Proprietario** della sottoscrizione. Per altre informazioni, vedere [Gestire l'accesso mediante il controllo degli accessi in base al ruolo e il portale di Azure](../role-based-access-control/role-assignments-portal.md).

## <a name="review-video"></a>Vedere il video

Questo video illustra come usare un acceleratore di soluzione di Azure IoT per generare dati e iniziare a usare Azure Time Series Insights.

> [!VIDEO https://www.youtube.com/embed/6ehNf6AJkFo]

## <a name="overview"></a>Panoramica

Nell'ambiente Azure Time Series Insights vengono raccolti e archiviati i dati dei dispositivi. Dopo l'archiviazione, è possibile usare lo [strumento di esplorazione di Azure Time Series Insights](time-series-quickstart.md) e l'[API di query di Azure Time Series Insights](/rest/api/time-series-insights/gen1-query-api) per eseguire query e analizzare i dati.

L'hub IoT di Azure è l'origine evento usata da tutti i dispositivi (simulati o fisici) nell'esercitazione per connettersi in modo sicuro e trasmettere dati al cloud di Azure.

Questa esercitazione usa anche un [acceleratore di soluzione IoT](https://www.azureiotsolutions.com) per generare e trasmettere dati di telemetria di esempio all'hub IoT.

>[!TIP]
> Gli [acceleratori di soluzioni IoT](https://www.azureiotsolutions.com) offrono soluzioni preconfigurate di livello aziendale utilizzabili per accelerare lo sviluppo di soluzioni IoT personalizzate.

## <a name="create-a-device-simulation"></a>Creare una simulazione di dispositivi

Creare prima di tutto la soluzione di simulazione di dispositivi, che genera i dati di test per popolare l'ambiente Azure Time Series Insights.

1. In una finestra o scheda separata passare a [azureiotsolutions.com](https://www.azureiotsolutions.com). Accedere con lo stesso account della sottoscrizione di Azure e selezionare l'acceleratore **Simulazione dispositivi**.

   [![Eseguire l'acceleratore Simulazione dispositivi](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-landing-page.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-landing-page.png#lightbox)

1. Selezionare **Prova adesso**. Immettere i parametri necessari nella pagina di **creazione della soluzione Simulazione dispositivi**.

   Parametro|Descrizione
   ---|---
   **Nome distribuzione** | Questo valore univoco viene usato per creare un nuovo gruppo di risorse. Le risorse di Azure elencate vengono create e assegnate al gruppo di risorse.
   **Sottoscrizione di Azure** | Specificare la stessa sottoscrizione usata per creare l'ambiente Azure Time Series Insights nella sezione precedente.
   **Opzioni di distribuzione** | Selezionare **Provision new IoT Hub** (Effettuare il provisioning di un nuovo hub IoT) per creare un nuovo hub IoT specifico per questa esercitazione.
   **Località di Azure** | Specificare la stessa area usata per creare l'ambiente Azure Time Series Insights nella sezione precedente.

   Al termine, selezionare **Crea** per effettuare il provisioning delle risorse di Azure della soluzione. Il completamento del processo può richiedere fino a 20 minuti.

   [![Eseguire il provisioning della soluzione Simulazione dispositivi](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-configuration.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-configuration.png#lightbox)

1. Al termine del provisioning, verranno visualizzati due aggiornamenti che mostrano che lo stato della distribuzione è passato da **Provisioning** a **Pronto**.

   >[!IMPORTANT]
   > Non immettere ancora l'acceleratore di soluzione, ma mantenere aperta questa pagina Web per tornarvi in un secondo momento.

   [![Completamento del provisioning della soluzione Simulazione dispositivi](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png#lightbox)

1. Esaminare ora le risorse appena create nel portale di Azure. Nella pagina **Gruppi di risorse** si noti che è stato creato un nuovo gruppo di risorse con il **Nome soluzione** specificato nel passaggio precedente. Prendere nota delle risorse create per la simulazione di dispositivi.

   [![Risorse per la simulazione di dispositivi](media/tutorial-create-populate-tsi-environment/tsi-device-sim-solution-resources.png)](media/tutorial-create-populate-tsi-environment/tsi-device-sim-solution-resources.png#lightbox)

## <a name="create-an-environment"></a>Creare un ambiente

Creare quindi un ambiente Azure Time Series Insights nella sottoscrizione di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com) usando l'account della sottoscrizione di Azure.
1. Selezionare **+ Crea una risorsa** in alto a sinistra.
1. Selezionare la categoria **Internet delle cose** e quindi **Time Series Insights**.

   [![Selezionare la risorsa dell'ambiente Azure Time Series Insights](media/tutorial-create-populate-tsi-environment/tsi-create-new-environment.png)](media/tutorial-create-populate-tsi-environment/tsi-create-new-environment.png#lightbox)

1. Nella pagina **Ambiente Time Series Insights** specificare i parametri richiesti.

   Parametro|Descrizione
   ---|---
   **Nome ambiente** | Scegliere un nome univoco per l'ambiente Azure Time Series Insights. I nomi vengono usati dallo strumento di esplorazione di Azure Time Series Insights e dalle [API di query](https://docs.microsoft.com/rest/api/time-series-insights/gen1-query).
   **Sottoscrizione** | Le sottoscrizioni sono contenitori per le risorse di Azure. Scegliere una sottoscrizione per creare l'ambiente Azure Time Series Insights.
   **Gruppo di risorse** | Un gruppo di risorse è un contenitore per le risorse di Azure. Scegliere un gruppo di risorse esistente o crearne uno nuovo per la risorsa dell'ambiente Azure Time Series Insights.
   **Posizione** | Scegliere un'area del data center per l'ambiente Azure Time Series Insights. Per evitare un aumento della latenza, creare l'ambiente Azure Time Series Insights nella stessa area delle altre risorse IoT.
   **Livello** | Scegliere la velocità effettiva necessaria. Selezionare **S1**.
   **Capacity** | Per capacità si intende il moltiplicatore applicato alla velocità in ingresso e alla capacità di archiviazione associate allo SKU selezionato. È possibile cambiare la capacità dopo la creazione. Selezionare una capacità pari a **1**.

   Al termine, selezionare **Avanti: Origine evento** per procedere con il passaggio successivo.

   [![Creare una risorsa ambiente Azure Time Series Insights](media/tutorial-create-populate-tsi-environment/tsi-create-resource-tsi-params.png)](media/tutorial-create-populate-tsi-environment/tsi-create-resource-tsi-params.png#lightbox)

1. Connettere ora l'ambiente Azure Time Series Insights all'hub IoT creato dall'acceleratore di soluzione. Impostare **Selezionare un hub** su `Select existing`. Scegliere quindi l'hub IoT creato dall'acceleratore di soluzione al momento dell'impostazione del **Nome hub IoT**.

   [![Connettere l'ambiente Azure Time Series Insights all'hub IoT creato](media/tutorial-create-populate-tsi-environment/tsi-create-resource-iot-hub.png)](media/tutorial-create-populate-tsi-environment/tsi-create-resource-iot-hub.png#lightbox)

   Selezionare infine **Rivedi e crea**.

1. Controllare il riquadro **Notifiche** per monitorare il completamento della distribuzione.

   [![Distribuzione dell'ambiente Azure Time Series Insights riuscita](media/tutorial-create-populate-tsi-environment/create-resource-tsi-deployment-succeeded.png)](media/tutorial-create-populate-tsi-environment/create-resource-tsi-deployment-succeeded.png#lightbox)

## <a name="run-device-simulation"></a>Eseguire una simulazione di dispositivi

Ora che la distribuzione e la configurazione iniziale sono state completate, popolare l'ambiente Azure Time Series Insights con i dati di esempio provenienti dai [dispositivi simulati creati dall'acceleratore](#create-a-device-simulation).

Insieme all'hub IoT è stata generata un'applicazione Web del servizio app di Azure per la creazione e la trasmissione di dati di telemetria dei dispositivi simulati.

1. Tornare al [dashboard Acceleratori di soluzione](https://www.azureiotsolutions.com/Accelerators#dashboard). Se necessario, eseguire nuovamente l'accesso usando lo stesso account di Azure già usato in questa esercitazione. Selezionare la "soluzione per dispositivi" e quindi **passare all'acceleratore di soluzione** per avviare la soluzione distribuita.

   [![Dashboard Acceleratori di soluzione](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png#lightbox)

1. All'inizio, l'app Web di simulazione dei dispositivi richiede il consenso dell'utente per la concessione all'applicazione Web dell'autorizzazione **Accedi e visualizza il profilo personale**. Questa autorizzazione consente di recuperare le informazioni sul profilo utente necessarie per supportare il funzionamento dell'applicazione:

   [![Consenso per l'applicazione Web per la simulazione di dispositivi](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png)](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png#lightbox)

1. Selezionare **+ Nuova simulazione**. Al termine del caricamento della pagina **Simulation setup** (Configurazione simulazione), immettere i parametri richiesti.

   Parametro|Descrizione
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

In questa sezione finale verrà verificato che i dati di telemetria siano stati generati e archiviati nell'ambiente Azure Time Series Insights. Per verificare i dati, usare lo strumento di esplorazione di Azure Time Series Insights, che consente di eseguire query e sui dati di telemetria e di analizzarli.

1. Tornare nella pagina **Panoramica** del gruppo di risorse dell'ambiente Azure Time Series Insights. Selezionare l'ambiente Azure Time Series Insights.

   [![Gruppo di risorse e ambiente dell'ambiente Azure Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png#lightbox)

1. Nella pagina **Panoramica** dell'ambiente Azure Time Series Insights selezionare l'**URL dello strumento di esplorazione di Azure Time Series Insights** per aprire tale strumento.

   [![Strumento di esplorazione di Azure Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png#lightbox)

1. Lo strumento di esplorazione di Azure Time Series Insights verrà caricato e verrà eseguita l'autenticazione tramite l'account del portale di Azure. Verranno visualizzati inizialmente l'area del grafico con cui è stato popolato l'ambiente Azure Time Series Insights e i dati di telemetria simulati. Per filtrare un intervallo di tempo più ristretto, selezionare l'elenco a discesa nell'angolo in alto a sinistra. Immettere un intervallo di tempo sufficientemente ampio da coprire l'intera durata della simulazione del dispositivo. Selezionare quindi la lente di ingrandimento Cerca.

   [![Filtro dell'intervallo di tempo dello strumento di esplorazione di Azure Time Series Insights](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png#lightbox)

1. Riducendo l'intervallo di tempo, all'interno del grafico vengono ingranditi i picchi distinti del trasferimento di dati nell'hub IoT e l'ambiente Azure Time Series Insights. Si noti anche il testo **Streaming completato** nell'angolo in alto a destra, che indica il numero totale di eventi trovati. È anche possibile trascinare il dispositivo di scorrimento **Dimensioni intervallo** per controllare la granularità del tracciato nel grafico.

   [![Visualizzazione filtrata dell'intervallo di tempo dello strumento di esplorazione di Azure Time Series Insights](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png#lightbox)

1. Infine, è anche possibile fare clic con il pulsante sinistro del mouse su un'area per filtrare un intervallo. Quindi fare clic con il pulsante destro del mouse e usare **Esplora eventi** per mostrare i dettagli degli eventi nella visualizzazione tabulare **Eventi**.

   [![Eventi e visualizzazione filtrata dell'intervallo di tempo dello strumento di esplorazione di Azure Time Series Insights](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png#lightbox)

## <a name="clean-up-resources"></a>Pulire le risorse

Questa esercitazione consente di creare diversi servizi di Azure in esecuzione per supportare l'ambiente Azure Time Series Insights e la soluzione di simulazione di dispositivi. Per rimuoverli, tornare al portale di Azure.

Dal menu a sinistra nel portale di Azure:

1. Selezionare l'icona **Gruppi di risorse**. Selezionare quindi il gruppo di risorse creato per l'ambiente Azure Time Series Insights. Nella parte superiore della pagina selezionare **Elimina gruppo di risorse**, immettere il nome del gruppo di risorse e quindi selezionare **Elimina**.

1. Selezionare l'icona **Gruppi di risorse**. Quindi selezionare il gruppo di risorse creato dall'acceleratore di soluzione per la simulazione del dispositivo. Nella parte superiore della pagina selezionare **Elimina gruppo di risorse**, immettere il nome del gruppo di risorse e quindi selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
>
> * Creare un ambiente Azure Time Series Insights.
> * Creare una soluzione di simulazione di dispositivi contenente un hub IoT.
> * Connettere l'ambiente Azure Time Series Insights all'hub IoT.
> * Eseguire una simulazione di dispositivi per trasmettere dati nell'ambiente Azure Time Series Insights.
> * Verificare i dati di telemetria simulati.

Ora che si è appreso come creare un ambiente Azure Time Series Insights, è possibile passare alla creazione di un'applicazione Web che usa dati di un ambiente Azure Time Series Insights:

> [!div class="nextstepaction"]
> [Vedere esempi di visualizzazione del SDK client ospitato](https://tsiclientsample.azurewebsites.net/)
