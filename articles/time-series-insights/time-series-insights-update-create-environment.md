---
title: 'Esercitazione: Configurare un ambiente di anteprima di Azure Time Series Insights | Microsoft Docs'
description: Informazioni su come configurare l'ambiente nella versione di anteprima di Azure Time Series Insights.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 10/02/2019
ms.custom: seodec18
ms.openlocfilehash: e42f6d7c5e3deff3eb5851f3ea192b4756d2fe04
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553484"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>Esercitazione: Configurare un ambiente di anteprima di Azure Time Series Insights

Questa esercitazione illustra il processo di creazione di un ambiente di anteprima con pagamento in base al consumo di Azure Time Series Insights. 

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un ambiente di anteprima di Azure Time Series Insights.
> * Connettere l'ambiente di anteprima di Azure Time Series Insights a un hub eventi in Hub eventi di Azure.
> * Eseguire un esempio di acceleratore di soluzione per trasmettere dati nell'ambiente di anteprima di Azure Time Series Insights.
> * Eseguire un'analisi di base sui dati.
> * Definire un tipo di modello di serie temporale e una gerarchia e associarli alle istanze.

>[!TIP]
> Gli [acceleratori di soluzioni IoT](https://www.azureiotsolutions.com/Accelerators) offrono soluzioni preconfigurate di livello aziendale utilizzabili per accelerare lo sviluppo di soluzioni IoT personalizzate.

Registrarsi per creare una [sottoscrizione di Azure gratuita](https://azure.microsoft.com/free/), se non è già stato fatto.

## <a name="prerequisites"></a>Prerequisiti

* L'account di accesso di Azure deve anche essere membro del ruolo **Proprietario** della sottoscrizione. Per altre informazioni, vedere [Gestire l'accesso mediante il controllo degli accessi in base al ruolo e il portale di Azure](../role-based-access-control/role-assignments-portal.md).

## <a name="create-a-device-simulation"></a>Creare una simulazione di dispositivi

In questa sezione vengono creati tre dispositivi simulati che inviano dati a un'istanza dell'hub IoT di Azure.

1. Accedere alla [pagina degli acceleratori di soluzione di Azure IoT](https://www.azureiotsolutions.com/Accelerators). La pagina mostra diversi esempi predefiniti. Accedere tramite l'account di Azure personale. Quindi, selezionare **Simulazione dispositivi**.

   [![Pagina degli acceleratori di soluzione Azure IoT](media/v2-update-provision/device-one-accelerator.png)](media/v2-update-provision/device-one-accelerator.png#lightbox)

   Selezionare **Prova adesso**.

1. Nella pagina della soluzione **Crea simulazione dispositivo** impostare i parametri seguenti:

    | Parametro | Azione |
    | --- | --- |
    | **Nome della distribuzione** | Immettere un valore univoco per un nuovo gruppo di risorse. Le risorse di Azure elencate vengono create e assegnate al gruppo di risorse. |
    | **Sottoscrizione di Azure** | Selezionare la sottoscrizione usata per creare l'ambiente Time Series Insights. |
    | **Località di Azure** | Selezionare l'area usata per creare l'ambiente Time Series Insights. |
    | **Opzioni di distribuzione** | Selezionare **Effettuare il provisioning di un nuovo hub IoT**. |
 
    Selezionare **Crea soluzione**. Il completamento della distribuzione della soluzione potrebbe richiedere fino a 20 minuti.

    [![Pagina Crea soluzione Simulazione dispositivi](media/v2-update-provision/device-two-create.png)](media/v2-update-provision/device-two-create.png#lightbox)

## <a name="create-a-preview-payg-environment"></a>Creare un ambiente di anteprima con pagamento in base al consumo

Questa sezione descrive come creare un ambiente di anteprima di Azure Time Series Insights e connetterlo all'hub IoT creato dall'acceleratore di soluzione IoT usando il [portale di Azure](https://portal.azure.com/).

1. Accedere al portale di Azure usando l'account della sottoscrizione.

1. Selezionare **Crea una risorsa** > **Internet delle cose** > **Time Series Insights**.

   [![Selezionare Internet delle cose e quindi Time Series Insights](media/v2-update-provision/payg-one-azure.png)](media/v2-update-provision/payg-one-azure.png#lightbox)

1. Nel riquadro **Crea ambiente Time Series Insights** della scheda **Generale** impostare i parametri seguenti:

    | Parametro | Azione |
    | --- | ---|
    | **Nome ambiente** | Immettere un nome univoco per l'ambiente di anteprima di Azure Time Series Insights. |
    | **Sottoscrizione** | Immettere la sottoscrizione in cui si vuole creare l'ambiente di anteprima di Azure Time Series Insights. È consigliabile usare la stessa sottoscrizione delle altre risorse IoT create dal simulatore di dispositivi. |
    | **Gruppo di risorse** | Selezionare un gruppo di risorse esistente per la risorsa dell'ambiente di anteprima di Azure Time Series Insights oppure crearne uno nuovo. Un gruppo di risorse è un contenitore per le risorse di Azure. È consigliabile usare lo stesso gruppo di risorse delle altre risorse IoT create dal simulatore di dispositivi. |
    | **Posizione** | Selezionare un'area del data center per l'ambiente di anteprima di Azure Time Series Insights. Per evitare un aumento della latenza, è consigliabile creare un ambiente di anteprima di Azure Time Series Insights nella stessa area delle altre risorse IoT. |
    | **Livello** |  Selezionare **PAYG** (*pagamento a consumo*). Si tratta dello SKU per il prodotto di anteprima di Azure Time Series Insights. |
    | **ID proprietà** | Immettere un valore che identifichi in modo univoco l'istanza della serie temporale. Il valore immesso nella casella **ID proprietà** non è modificabile. Non è possibile modificarlo in un secondo momento. Per questa esercitazione immettere **iothub-connection-device-id**. Per altre informazioni sull'ID serie temporale, vedere [Procedure consigliate per la scelta di un ID serie temporale](./time-series-insights-update-how-to-id.md). |
    | **Nome account di archiviazione** | Immettere un nome univoco globale per un nuovo account di archiviazione da creare. |
   
   Selezionare **Avanti: Origine evento**.

   [![Riquadro per la creazione di un ambiente Time Series Insights](media/v2-update-provision/payg-two-create.png)](media/v2-update-provision/payg-two-create.png#lightbox)

1. Nella scheda **Origine evento** impostare i parametri seguenti:

   | Parametro | Azione |
   | --- | --- |
   | **Create an event source?** (Creare un origine evento?) | Selezionare **Sì**.|
   | **Nome** | Immettere un valore univoco per il nome dell'origine evento. |
   | **Tipo di origine** | Selezionare **Hub IoT**. |
   | **Selezionare un hub** | Scegliere **Seleziona esistente**. |
   | **Sottoscrizione** | Selezionare la sottoscrizione usata per il simulatore di dispositivo. |
   | **Nome hub IoT** | Selezionare il nome dell'hub IoT creato per il simulatore di dispositivo. |
   | **Criteri di accesso dell'hub IoT** | selezionare **iothubowner**. |
   | **Gruppo di consumer dell'hub IoT** | Selezionare **Nuovo**, immettere un nome univoco e quindi selezionare **Aggiungi**. Il gruppo di consumer deve essere un valore univoco nell'anteprima di Azure Time Series Insights. |
   | **Timestamp property** (Proprietà timestamp) | Questo valore viene usato per identificare la proprietà **Timestamp** nei dati di telemetria in ingresso. Per questa esercitazione, lasciare vuota questa casella. Questo simulatore usa il timestamp in ingresso dall'hub IoT predefinito di Time Series Insights. |

   Selezionare **Rivedi e crea**.

   [![Configurare un'origine evento](media/v2-update-provision/payg-five-event-source.png)](media/v2-update-provision/payg-five-event-source.png#lightbox)

1. Nella scheda **Rivedi e crea** rivedere le selezioni e quindi selezionare **Crea**.

    [![Pagina Rivedi e crea, con pulsante Crea](media/v2-update-provision/payg-six-review.png)](media/v2-update-provision/payg-six-review.png#lightbox)

    È possibile visualizzare lo stato della distribuzione:

    [![Notifica del completamento della distribuzione](media/v2-update-provision/payg-seven-deploy.png)](media/v2-update-provision/payg-seven-deploy.png#lightbox)

1. Se si è proprietari del tenant, si avrà accesso all'ambiente di anteprima di Azure Time Series Insights. Assicurarsi di avere accesso:

   1. Cercare il gruppo di risorse e quindi selezionare l'ambiente di anteprima di Azure Time Series Insights:

      [![Ambiente selezionato](media/v2-update-provision/payg-eight-environment.png)](media/v2-update-provision/payg-eight-environment.png#lightbox)

   1. Nella pagina di anteprima di Azure Time Series Insights selezionare **Criteri di accesso ai dati**:

      [![Criteri di accesso ai dati](media/v2-update-provision/payg-nine-data-access.png)](media/v2-update-provision/payg-nine-data-access.png#lightbox)

   1. Verificare che le credenziali siano elencate:

      [![Credenziali elencate](media/v2-update-provision/payg-ten-verify.png)](media/v2-update-provision/payg-ten-verify.png#lightbox)

   Se le credenziali non sono elencate, è necessario concedere a se stessi l'autorizzazione per accedere all'ambiente. Per altre informazioni sull'impostazione delle autorizzazioni, leggere [Concedere l'accesso ai dati](./time-series-insights-data-access.md).

## <a name="stream-data"></a>Trasmettere i dati

Ora che è stato distribuito l'ambiente di Time Series Insights, è possibile trasmettervi i dati per l'analisi.

1. Tornare alla [pagina degli acceleratori di soluzione di Azure IoT](https://www.azureiotsolutions.com/Accelerators). Collocare la soluzione nel dashboard dell'acceleratore di soluzione. Selezionare quindi **Avvio**:

    [![Avviare la soluzione Simulazione dispositivi](media/v2-update-provision/device-three-launch.png)](media/v2-update-provision/device-three-launch.png#lightbox)

1. Si verrà reindirizzati alla pagina **Microsoft Azure IoT Device Simulation** (Simulazione dispositivi di Microsoft Azure IoT). Nell'angolo in alto a destra della pagina selezionare **New simulation** (Nuova simulazione).

    [![Pagina di simulazione di Azure IoT](media/v2-update-provision/device-four-iot-sim-page.png)](media/v2-update-provision/device-four-iot-sim-page.png#lightbox)

1. Nel riquadro **Simulation setup** (Configurazione simulazione) impostare i parametri seguenti:

    | Parametro | Azione |
    | --- | --- |
    | **Nome** | Immettere un nome univoco per un simulatore. |
    | **Descrizione** | Immettere una definizione. |
    | **Simulation duration** (Durata simulazione) | Impostare su **Run indefinitely** (Esecuzione a tempo indefinito). |
    | **Device model** (Modello del dispositivo) | **Nome**: immettere **Chiller** (Refrigeratore). <br />**Amount** (Quantità): immettere **3**. |
    | **Target IoT Hub** (Hub IoT di destinazione) | Impostare su **Use pre-provisioned IoT Hub** (Usa l'hub IoT di cui è già stato eseguito il provisioning). |

    [![Parametri da impostare](media/v2-update-provision/device-five-params.png)](media/v2-update-provision/device-five-params.png#lightbox)

    Selezionare **Start simulation** (Avvia simulazione).

    Nel dashboard di simulazione del dispositivo prendere nota delle informazioni visualizzate per **Active devices** (Dispositivi attivi) e **Messages per second** (Messaggi al secondo).

    [![Dashboard di simulazione di Azure IoT](media/v2-update-provision/device-seven-dashboard.png)](media/v2-update-provision/device-seven-dashboard.png#lightbox)

## <a name="analyze-data"></a>Analizzare i dati

In questa sezione verranno eseguite attività di analisi di base sui dati delle serie temporali usando lo [strumento di esplorazione aggiornato dell'anteprima di Azure Time Series Insights](./time-series-insights-update-explorer.md).

1. Passare allo strumento di esplorazione dell'anteprima di Azure Time Series Insights selezionando l'URL nella pagina delle risorse nel [portale di Azure](https://portal.azure.com/).

    [![URL dello strumento di esplorazione dell'anteprima di Time Series Insights](media/v2-update-provision/analyze-one-portal.png)](media/v2-update-provision/analyze-one-portal.png#lightbox)

1. Nello strumento di esplorazione selezionare il nodo **Time Series Instances** (Istanze di Time Series) per visualizzare tutte le istanze della versione di anteprima di Azure Time Series Insights nell'ambiente.

    [![Elenco di istanze senza padre](media/v2-update-provision/analyze-two-unparented.png)](media/v2-update-provision/analyze-two-unparented.png#lightbox)

1. Selezionare la prima istanza della serie temporale. Selezionare quindi **Show pressure** (Mostra pressione).

    [![Istanza della serie temporale selezionata con il comando di menu per visualizzare la pressione media](media/v2-update-provision/analyze-three-show-pressure.png)](media/v2-update-provision/analyze-three-show-pressure.png#lightbox)

    Viene visualizzato un grafico della serie temporale. Modificare l'**intervallo** impostandolo su **15s**.

    [![Grafico serie temporale](media/v2-update-provision/analyze-four-chart.png)](media/v2-update-provision/analyze-four-chart.png#lightbox)

1. Ripetere il passaggio 3 con le altre due istanze della serie temporale. È possibile visualizzare tutte le istanze della serie temporale come illustrato in questo grafico:

    [![Grafico relativo a tutte le serie temporali](media/v2-update-provision/analyze-five-chart.png)](media/v2-update-provision/analyze-five-chart.png#lightbox)

1. Nella casella di opzione **Timeframe** (Intervallo di tempo) modificare l'intervallo di tempo per visualizzare le tendenze nelle serie temporali dell'ultima ora:

    [![Impostare l'intervallo di tempo su un'ora](media/v2-update-provision/analyze-six-time.png)](media/v2-update-provision/analyze-six-time.png#lightbox)

## <a name="define-and-apply-a-model"></a>Definire e applicare un modello

In questa sezione si applica un modello per strutturare i dati. Per completare il modello, vengono definiti i tipi, le gerarchie e le istanze. Per altre informazioni sulla modellazione dei dati, vedere [Modello Time Series](./time-series-insights-update-tsm.md).

1. Nella finestra di esplorazione, selezionare la scheda **Modello**:

   [![Scheda Modello nello strumento di esplorazione](media/v2-update-provision/define-one-model.png)](media/v2-update-provision/define-one-model.png#lightbox)

1. Selezionare **Add** (Aggiungi) per aggiungere un tipo:

   [![Pulsante di aggiunta per i tipi](media/v2-update-provision/define-two-add.png)](media/v2-update-provision/define-two-add.png#lightbox)

1. Definire quindi tre variabili per il tipo, ovvero *pressure* (pressione), *temperature* (temperatura) e *humidity* (umidità). Nel riquadro **Add a Type** (Aggiungi un tipo) impostare i parametri seguenti:

    | Parametro | Azione |
    | --- | ---|
    | **Nome** | Immettere **Chiller** (Refrigeratore). |
    | **Descrizione** | Immettere **This is a type definition of Chiller** (Definizione del tipo per Refrigeratore). |

   * Per definire la *pressione*, in **Variables** (Variabili) impostare i parametri seguenti:

     | Parametro | Azione |
     | --- | ---|
     | **Nome** | Immettere **Avg Pressure** (Pressione media). |
     | **Valore** | Selezionare **pressione (doppia)** . Il popolamento automatico del campo **Value** (Valore) potrebbe richiedere alcuni minuti dopo l'inizio della ricezione di eventi nell'anteprima di Azure Time Series Insights. |
     | **Operazione di aggregazione** | Selezionare **AVG** (MEDIA). |

      [![Selezioni per la definizione della pressione](media/v2-update-provision/define-three-variable.png)](media/v2-update-provision/define-three-variable.png#lightbox)

      Per aggiungere la variabile successiva, selezionare **Add Variable** (Aggiungi variabile).

   * Definire la *temperatura*:

     | Parametro | Azione |
     | --- | ---|
     | **Nome** | Immettere **Avg Temperature** (Temperatura media). |
     | **Valore** | Selezionare **temperatura (doppia)** . Il popolamento automatico del campo **Value** (Valore) potrebbe richiedere alcuni minuti dopo l'inizio della ricezione di eventi nell'anteprima di Azure Time Series Insights. |
     | **Operazione di aggregazione** | Selezionare **AVG** (MEDIA).|

      [![Selezioni per la definizione della temperatura](media/v2-update-provision/define-four-avg.png)](media/v2-update-provision/define-four-avg.png#lightbox)

      Per aggiungere la variabile successiva, selezionare **Add Variable** (Aggiungi variabile).

   * Definire l'*umidità*:

      | | |
      | --- | ---|
      | **Nome** | Immettere **Max Humidity** (Umidità massima). |
      | **Valore** | Selezionare **umidità (doppia)** . Il popolamento automatico del campo **Value** (Valore) potrebbe richiedere alcuni minuti dopo l'inizio della ricezione di eventi nell'anteprima di Azure Time Series Insights. |
      | **Operazione di aggregazione** | Selezionare **MAX** (MASSIMA).|

      [![Selezioni per la definizione della temperatura](media/v2-update-provision/define-five-humidity.png)](media/v2-update-provision/define-five-humidity.png#lightbox)

    Selezionare **Create** (Crea).

    È possibile visualizzare il tipo che è stato aggiunto:

    [![Informazioni sul tipo aggiunto](media/v2-update-provision/define-six-type.png)](media/v2-update-provision/define-six-type.png#lightbox)

1. Il passaggio successivo consiste nell'aggiungere una gerarchia. In **Hierarchies** (Gerarchie) selezionare **Add** (Aggiungi):

    [![Scheda delle gerarchie con pulsante di aggiunta](media/v2-update-provision/define-seven-hierarchy.png)](media/v2-update-provision/define-seven-hierarchy.png#lightbox)

1. Nel riquadro **Edit Hierarchy** (Modifica gerarchia) impostare i parametri seguenti:

   | Parametro | Azione |
   | --- | ---|
   | **Nome** | Immettere **Location Hierarchy** (Gerarchia località). |
   | **Livello 1** | Immettere **Country** (Paese). |
   | **Livello 2** | Immettere **City** (Città). |
   | **Livello 3** | Immettere **Building** (Edificio). |

   Selezionare **Salva**.

    [![Campi della gerarchia con pulsante di creazione](media/v2-update-provision/define-eight-add-hierarchy.png)](media/v2-update-provision/define-eight-add-hierarchy.png#lightbox)

   È possibile visualizzare la gerarchia creata:

    [![Informazioni sulla gerarchia](media/v2-update-provision/define-nine-created.png)](media/v2-update-provision/define-nine-created.png#lightbox)

1. Selezionare **Instances** (Istanze). Selezionare la prima istanza e quindi selezionare **Edit** (Modifica):

    [![Selezione del pulsante di modifica per un'istanza](media/v2-update-provision/define-ten-edit.png)](media/v2-update-provision/define-ten-edit.png#lightbox)

1. Nel riquadro **Edit instances** (Modifica istanze) impostare i parametri seguenti:

    | Parametro | Azione |
    | --- | --- |
    | **Tipo** | Selezionare **Chiller** (Refrigeratore). |
    | **Descrizione** | Immettere **Instance for Chiller-01.1** (Istanza per Refrigeratore-01.1). |
    | **Gerarchie** | Selezionare **Location Hierarchy** (Gerarchia località). |
    | **Paese** | Immettere **USA** (Stati Uniti). |
    | **Città** | Immettere **Seattle**. |
    | **Edificio** | Immettere **Space Needle**. |

    [![Campi dell'istanza con pulsante di salvataggio](media/v2-update-provision/define-eleven-chiller.png)](media/v2-update-provision/define-eleven-chiller.png#lightbox)

   Selezionare **Salva**.

1. Ripetere il passaggio precedente per gli altri sensori. Aggiornare i valori seguenti:

   * Per Chiller 01.2:

     | Parametro | Azione |
     | --- | --- |
     | **Tipo** | Selezionare **Chiller** (Refrigeratore). |
     | **Descrizione** | Immettere **Instance for Chiller-01.2** (Istanza per Refrigeratore-01.2). |
     | **Gerarchie** | Selezionare **Location Hierarchy** (Gerarchia località). |
     | **Paese** | Immettere **USA** (Stati Uniti). |
     | **Città** | Immettere **Seattle**. |
     | **Edificio** | Immettere **Pacific Science Center**. |

   * Per Chiller 01.3:

     | Parametro | Azione |
     | --- | --- |
     | **Tipo** | Selezionare **Chiller** (Refrigeratore). |
     | **Descrizione** | Immettere **Instance for Chiller-01.3** (Istanza per Refrigeratore-01.3). |
     | **Gerarchie** | Selezionare **Location Hierarchy** (Gerarchia località). |
     | **Paese** | Immettere **USA** (Stati Uniti). |
     | **Città** | Immettere **New York**. |
     | **Edificio** | Immettere **Empire State Building**. |

1. Selezionare la scheda **Analizza** e quindi aggiornare la pagina. In **Location Hierarchy** (Gerarchia località) espandere tutti i livelli della gerarchia per visualizzare le istanze della serie temporale:

   [![Scheda Analizza](media/v2-update-provision/define-twelve.png)](media/v2-update-provision/define-twelve.png#lightbox)

1. Per esplorare le istanze della serie temporale dell'ultima ora, modificare **Tempi rapidi** in **Ultima ora**:

    [![Casella Tempi rapidi con opzione Ultima ora selezionata](media/v2-update-provision/define-thirteen-explore.png)](media/v2-update-provision/define-thirteen-explore.png#lightbox)

1. In **Pacific Science Center** selezionare l'istanza della serie temporale e quindi **Show Max Humidity** (Mostra umidità massima).

    [![Istanza della serie temporale selezionata con selezione del menu Show Max Humidity (Mostra umidità massima)](media/v2-update-provision/define-fourteen-show-max.png)](media/v2-update-provision/define-fourteen-show-max.png#lightbox)

1. Si aprirà la serie temporale per **Max Humidity** (Umidità massima) con un intervallo di dimensioni pari a **1 minuto**. Per filtrare un intervallo, selezionare un'area. Per analizzare gli eventi nell'intervallo di tempo, fare quindi clic con il pulsante destro del mouse sul grafico e scegliere **Zoom**:

   [![Intervallo selezionato con comando Zoom in un menu di scelta rapida](media/v2-update-provision/define-fifteen-filter.png)](media/v2-update-provision/define-fifteen-filter.png#lightbox)

1. Per visualizzare i dettagli dell'evento, selezionare un'area e quindi fare clic con il pulsante destro del mouse sul grafico:

   [![Elenco dettagliato di eventi](media/v2-update-provision/define-eighteen.png)](media/v2-update-provision/define-eighteen.png#lightbox)


## <a name="clean-up-resources"></a>Pulire le risorse

Ora che è stata completata l'esercitazione, pulire le risorse create:

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Tutte le risorse** nel menu a sinistra e individuare il gruppo di risorse di Azure Time Series Insights.
1. Per eliminare l'intero gruppo di risorse e tutte le risorse in esso contenute, selezionare **Elimina** oppure rimuovere ogni risorsa singolarmente.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:  

> [!div class="checklist"]
> * Creare e usare un acceleratore di simulazione dispositivi.
> * Creare un ambiente di anteprima di Azure Time Series Insights con pagamento in base al consumo.
> * Connettere l'ambiente di anteprima di Azure Time Series Insights a un hub eventi.
> * Eseguire un esempio di acceleratore di soluzione per trasmettere dati nell'ambiente di anteprima di Azure Time Series Insights.
> * Eseguire un'analisi di base sui dati.
> * Definire un tipo di modello di serie temporale e una gerarchia da associare alle istanze.

Ora che si è appreso come creare un ambiente di anteprima di Azure Time Series Insights, si procederà ad apprendere altre informazioni sui concetti chiave di Azure Time Series Insights.

Leggere informazioni sulla configurazione dell'archiviazione di Azure Time Series Insights:

> [!div class="nextstepaction"]
> [Archiviazione e ingresso dei dati nella versione di anteprima di Azure Time Series Insights](./time-series-insights-update-storage-ingress.md)

Leggere altre informazioni sui modelli di serie temporali:

> [!div class="nextstepaction"]
> [Modellazione dei dati nell'anteprima di Azure Time Series Insights](./time-series-insights-update-tsm.md)
