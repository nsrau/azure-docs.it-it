---
title: 'Esercitazione: Configurare un ambiente di anteprima di Azure Time Series Insights | Microsoft Docs'
description: Informazioni su come configurare l'ambiente nella versione di anteprima di Azure Time Series Insights.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 04/25/2019
ms.custom: seodec18
ms.openlocfilehash: f83063a88207f51f9d481447923fd8a8498692a2
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64713906"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>Esercitazione: Configurare un ambiente di anteprima di Azure Time Series Insights

Questa esercitazione illustra il processo di creazione di un ambiente di anteprima con pagamento in base al consumo di Azure Time Series Insights. In questa esercitazione si apprenderà come:

* Creare un ambiente di anteprima di Azure Time Series Insights.
* Connettere l'ambiente di anteprima di Azure Time Series Insights a un hub eventi in Hub eventi di Azure.
* Eseguire un esempio di acceleratore di soluzione per trasmettere dati nell'ambiente di anteprima di Azure Time Series Insights.
* Eseguire un'analisi di base sui dati.
* Definire un tipo di modello di serie temporale e una gerarchia e associarli alle istanze.

# <a name="create-a-device-simulation"></a>Creare una simulazione di dispositivi

In questa sezione vengono creati tre dispositivi simulati che invieranno dati a un hub IoT.

1. Accedere alla [pagina degli acceleratori di soluzione di Azure IoT](https://www.azureiotsolutions.com/Accelerators). La pagina mostra diversi esempi predefiniti. Accedere tramite l'account di Azure personale. Quindi, selezionare **Simulazione dispositivi**.

   [![Pagina degli acceleratori di soluzione Azure IoT](media/v2-update-provision/device-one-accelerator.png)](media/v2-update-provision/device-one-accelerator.png#lightbox)

   Selezionare **Prova adesso**.

1. Immettere i parametri richiesti nella pagina **Crea soluzione Simulazione dispositivi**:

   | Parametro | DESCRIZIONE |
   | --- | --- |
   | **Nome soluzione** |    Immettere un valore univoco per la creazione di un nuovo gruppo di risorse. Le risorse di Azure elencate vengono create e assegnate al gruppo di risorse. |
   | **Sottoscrizione** | Specificare la stessa sottoscrizione usata per la creazione dell'ambiente Time Series Insights. |
   | **Area** |   Specificare la stessa area usata per la creazione dell'ambiente Time Series Insights. |
   | **Distribuisci risorse di Azure facoltative**    | Lasciare selezionato Hub IoT, perché verrà usato dai dispositivi simulati per la connessione e la trasmissione dei dati. |

   Selezionare quindi **Crea soluzione**. Attendere 10-15 minuti che la soluzione venga distribuita.

   [![Pagina Crea soluzione Simulazione dispositivi](media/v2-update-provision/device-two-create.png)](media/v2-update-provision/device-two-create.png#lightbox)

1. Nel dashboard dell'acceleratore di soluzione selezionare il pulsante **Avvia**:

   [![Avviare la soluzione Simulazione dispositivi](media/v2-update-provision/device-three-launch.png)](media/v2-update-provision/device-three-launch.png#lightbox)

1. Si verrà reindirizzati alla pagina **Microsoft Azure IoT Device Simulation** (Simulazione dispositivi di Microsoft Azure IoT). Selezionare **+ New simulation** (+ Nuova simulazione) in alto a destra nella pagina.

   [![Pagina di simulazione di Azure IoT](media/v2-update-provision/device-four-iot-sim-page.png)](media/v2-update-provision/device-four-iot-sim-page.png#lightbox)

1. Immettere i parametri obbligatori nel modo seguente:

    [![Parametri da compilare](media/v2-update-provision/device-five-params.png)](media/v2-update-provision/device-five-params.png#lightbox)

    |||
    | --- | --- |
    | **Nome** | Immettere un nome univoco per un simulatore. |
    | **Descrizione** | Immettere una definizione. |
    | **Simulation duration** (Durata simulazione) | Impostare su **Run indefinitely** (Esecuzione a tempo indefinito). |
    | **Device model** (Modello del dispositivo) | **Nome**: Immettere `Chiller`. </br>**Amount** (Quantità): Immettere `3`. |
    | **Target IoT Hub** (Hub IoT di destinazione) | Impostare su **Use pre-provisioned IoT Hub** (Usa l'hub IoT di cui è già stato eseguito il provisioning). |

    Selezionare quindi **Start simulation** (Avvia simulazione).

1. Nel dashboard di simulazione dei dispositivi esaminare **Active devices** (Dispositivi attivi) e **Messages per second** (Messaggi al secondo).

    [![Dashboard di simulazione di Azure IoT](media/v2-update-provision/device-seven-dashboard.png)](media/v2-update-provision/device-seven-dashboard.png#lightbox)

## <a name="list-device-simulation-properties"></a>Elencare le proprietà della simulazione del dispositivo

Prima di creare un ambiente di Azure Time Series Insights, sono necessari i nomi dell'hub IoT, della sottoscrizione e del gruppo di risorse.

1. Accedere al dashboard dell'acceleratore di soluzioni con lo stesso account di sottoscrizione di Azure. Trovare la simulazione del dispositivo creata nei passaggi precedenti.

1. Selezionare il simulatore di dispositivi e quindi **Launch** (Avvia). Selezionare il collegamento del **portale di gestione di Azure** visualizzato sul lato destro.

    [![Elenchi di simulatori](media/v2-update-provision/device-six-listings.png)](media/v2-update-provision/device-six-listings.png#lightbox)

1. Prendere nota dei nomi dell'hub IoT, della sottoscrizione e del gruppo di risorse.

    [![Portale di Azure](media/v2-update-provision/device-eight-portal.png)](media/v2-update-provision/device-eight-portal.png#lightbox)

## <a name="create-a-time-series-insights-preview-payg-environment"></a>Creare un ambiente di anteprima di Time Series Insights con pagamento in base al consumo

Questa sezione illustra come creare un ambiente di anteprima di Azure Time Series Insights tramite il [portale di Azure](https://portal.azure.com/).

1. Accedere al portale di Azure usando l'account della sottoscrizione.

1. Selezionare **Crea una risorsa**.

1. Selezionare la categoria **Internet delle cose** e quindi **Time Series Insights**.

   [![Selezionare Internet delle cose e quindi Time Series Insights](media/v2-update-provision/payg-one-azure.png)](media/v2-update-provision/payg-one-azure.png#lightbox)

1. Compilare i campi nella pagina come segue:

   | | |
   | --- | ---|
   | **Nome ambiente** | Scegliere un nome univoco per l'ambiente di anteprima di Azure Time Series Insights. |
   | **Sottoscrizione** | Immettere la sottoscrizione in cui si desidera creare l'ambiente di anteprima di Azure Time Series Insights. È consigliabile usare la stessa sottoscrizione delle altre risorse di IoT create dal simulatore di dispositivi. |
   | **Gruppo di risorse** | Un gruppo di risorse è un contenitore per le risorse di Azure. Scegliere un gruppo di risorse esistente per la risorsa dell'ambiente di anteprima di Azure Time Series Insights oppure crearne uno nuovo. È consigliabile usare lo stesso gruppo di risorse delle altre risorse di IoT create dal simulatore di dispositivi. |
   | **Posizione** | Scegliere un'area del data center per l'ambiente di anteprima di Azure Time Series Insights. Per evitare un aumento della latenza e dei costi della larghezza di banda, è consigliabile mantenere l'ambiente di anteprima di Azure Time Series Insights nella stessa area delle altre risorse IoT. |
   | **Livello** |  Selezionare **PAYG**, ovvero l'acronimo del pagamento in base al consumo. Si tratta dello SKU per il prodotto di anteprima di Azure Time Series Insights. |
   | **ID proprietà** | Immettere un nome che identifichi in modo univoco la serie temporale. Si noti che questo campo è invariabile e non può essere modificato in un secondo momento. Per questa esercitazione, usare `iothub-connection-device-id`. Per altre informazioni sull'ID serie temporale, leggere [Come scegliere un ID serie temporale](./time-series-insights-update-how-to-id.md). |
   | **Nome account di archiviazione** | Immettere un nome univoco globale per un nuovo account di archiviazione da creare. |

   Selezionare quindi **Avanti: Origine evento**.

   [![Pagina per la creazione di un ambiente Time Series Insights](media/v2-update-provision/payg-two-create.png)](media/v2-update-provision/payg-two-create.png#lightbox)

1. Nella pagina relativa all'origine evento compilare i campi come indicato di seguito:

   | | |
   | --- | --- |
   | **Create an event source?** (Creare un origine evento?) | Immettere `Yes`.|
   | **Nome** | Immettere un valore univoco che viene usato per assegnare un nome all'origine evento.|
   | **Tipo di origine** | Selezionare **Hub IoT**. |
   | **Select a hub?** (Selezionare un hub?) | Scegliere **Seleziona esistente**. |
   | **Sottoscrizione** | Selezionare la sottoscrizione usata per il simulatore di dispositivo. |
   | **Nome hub IoT** | Selezionare il nome dell'hub IoT creato per il simulatore di dispositivo. |
   | **Criteri di accesso dell'hub IoT** | selezionare **iothubowner**. |
   | **Gruppo di consumer dell'hub IoT** | È necessario un gruppo di consumer univoco per l'anteprima di Azure Time Series Insights. Selezionare **Nuovo**, immettere un nome univoco e quindi selezionare **Aggiungi**. |
   | **Timestamp property** (Proprietà timestamp) | Questo campo viene usato per identificare la proprietà timestamp nei dati di telemetria in ingresso. Per questa esercitazione, non compilare il campo. Questo simulatore usa il timestamp in ingresso dall'hub IoT predefinito di Time Series Insights.|

   Selezionare quindi **Rivedi e crea**.

   [![Configurare un'origine evento](media/v2-update-provision/payg-five-event-source.png)](media/v2-update-provision/payg-five-event-source.png#lightbox)

1. Esaminare tutti i campi nella pagina di revisione e selezionare **Crea**.

   [![Pagina Rivedi e crea, con pulsante Crea](media/v2-update-provision/payg-six-review.png)](media/v2-update-provision/payg-six-review.png#lightbox)

1. È possibile visualizzare lo stato della distribuzione.

   [![Notifica del completamento della distribuzione](media/v2-update-provision/payg-seven-deploy.png)](media/v2-update-provision/payg-seven-deploy.png#lightbox)

1. Se si è proprietari del tenant, si dovrebbe ricevere l'accesso all'ambiente di anteprima di Azure Time Series Insights. Assicurarsi di avere accesso:

   a. Cercare il gruppo di risorse e selezionare l'ambiente di anteprima di Azure Time Series Insights:

      [![Ambiente selezionato](media/v2-update-provision/payg-eight-environment.png)](media/v2-update-provision/payg-eight-environment.png#lightbox)

   b. Nella pagina di anteprima di Azure Time Series Insights passare a **Criteri di accesso ai dati**.

     [![Criteri di accesso ai dati](media/v2-update-provision/payg-nine-data-access.png)](media/v2-update-provision/payg-nine-data-access.png#lightbox)

   c. Verificare che le credenziali siano elencate.

     [![Credenziali elencate](media/v2-update-provision/payg-ten-verify.png)](media/v2-update-provision/payg-ten-verify.png#lightbox)

   Se le credenziali non sono elencate, è necessario assegnarsi manualmente l'autorizzazione per accedere all'ambiente. Per altre informazioni sull'impostazione delle autorizzazioni, leggere [Concedere l'accesso ai dati](./time-series-insights-data-access.md).

## <a name="analyze-data-in-your-environment"></a>Analizzare i dati nell'ambiente

In questa sezione verranno eseguite attività di analisi di base sui dati delle serie temporali usando lo [strumento di esplorazione aggiornato dell'anteprima di Azure Time Series Insights](./time-series-insights-update-explorer.md).

1. Passare allo strumento di esplorazione dell'anteprima di Azure Time Series Insights selezionando l'URL nella pagina delle risorse nel [portale di Azure](https://portal.azure.com/).

   [![URL dello strumento di esplorazione dell'anteprima di Time Series Insights](media/v2-update-provision/analyze-one-portal.png)](media/v2-update-provision/analyze-one-portal.png#lightbox)

1. Nello strumento di esplorazione selezionare il nodo **Time Series Instances** (Istanze di Time Series) per visualizzare tutte le istanze della versione di anteprima di Azure Time Series Insights nell'ambiente.

   [![Elenco di istanze senza padre](media/v2-update-provision/analyze-two-unparented.png)](media/v2-update-provision/analyze-two-unparented.png#lightbox)

1. Nella serie temporale visualizzata selezionare la prima istanza. Selezionare quindi **Show Avg pressure** (Mostra pressione media).

   [![Istanza selezionata con il comando di menu per visualizzare la pressione media](media/v2-update-provision/analyze-three-show-pressure.png)](media/v2-update-provision/analyze-three-show-pressure.png#lightbox)

   Viene visualizzato un grafico della serie temporale a destra. Impostare**** l'intervallo su `15s`.

   [![Grafico serie temporale](media/v2-update-provision/analyze-four-chart.png)](media/v2-update-provision/analyze-four-chart.png#lightbox)

1. Ripetere il **passaggio 3** con le altre due serie temporali. È quindi possibile visualizzare tutte le serie temporali come illustrato in questo grafico:

   [![Grafico relativo a tutte le serie temporali](media/v2-update-provision/analyze-five-chart.png)](media/v2-update-provision/analyze-five-chart.png#lightbox)

1. Modificare l'intervallo di tempo per visualizzare le tendenze nelle serie temporali dell'ultima ora.

   a. Selezionare la casella di opzione **Intervallo di tempo**:

      [![Impostare l'intervallo di tempo su un'ora](media/v2-update-provision/analyze-six-time.png)](media/v2-update-provision/analyze-six-time.png#lightbox)

## <a name="define-and-apply-a-model"></a>Definire e applicare un modello

In questa sezione si applica un modello per strutturare i dati. Per completare il modello, vengono definiti i tipi, le gerarchie e le istanze. Per altre informazioni sulla modellazione dei dati, passare a [Modello Time Series](./time-series-insights-update-tsm.md).

1. Nella finestra di esplorazione, selezionare la scheda **Modello**:

   [![Scheda Modello nello strumento di esplorazione](media/v2-update-provision/define-one-model.png)](media/v2-update-provision/define-one-model.png#lightbox)

1. Selezionare **+ Add** (+ Aggiungi) per aggiungere un tipo. A destra verrà aperto un editor di tipi.

   [![Pulsante di aggiunta per i tipi](media/v2-update-provision/define-two-add.png)](media/v2-update-provision/define-two-add.png#lightbox)

1. Definire tre variabili per il tipo, ovvero pressure (pressione), temperature (temperatura) e humidity (umidità). Immettere le seguenti informazioni:

   | | |
   | --- | ---|
   | **Nome** | Immettere `Chiller`. |
   | **Descrizione** | Immettere `This is a type definition of Chiller`. |

   * Definire la pressione con tre variabili:

      | | |
      | --- | ---|
      | **Nome** | Immettere `Avg Pressure`. |
      | **Valore** | Selezionare **pressione (doppia)** . Tenere presente che il popolamento di questo campo potrebbe richiedere alcuni minuti dopo l'inizio della ricezione di eventi in Azure Time Series Insights. |
      | **Operazione di aggregazione** | Selezionare **AVG** (MEDIA). |

      [![Selezioni per la definizione della pressione](media/v2-update-provision/define-three-variable.png)](media/v2-update-provision/define-three-variable.png#lightbox)

      Selezionare **+ Add Variable** (+ Aggiungi variabile) per aggiungere la variabile successiva.

   * Definire la temperatura:

      | | |
      | --- | ---|
      | **Nome** | Immettere `Avg Temperature`. |
      | **Valore** | Selezionare **temperatura (doppia)** . Tenere presente che il popolamento di questo campo potrebbe richiedere alcuni minuti dopo l'inizio della ricezione di eventi in Azure Time Series Insights. |
      | **Operazione di aggregazione** | Selezionare **AVG** (MEDIA).|

      [![Selezioni per la definizione della temperatura](media/v2-update-provision/define-four-avg.png)](media/v2-update-provision/define-four-avg.png#lightbox)

   * Definire l'umidità:

      | | |
      | --- | ---|
      | **Nome** | Immettere `Max Humidity` |
      | **Valore** | Selezionare **umidità (doppia)** . Tenere presente che il popolamento di questo campo potrebbe richiedere alcuni minuti dopo l'inizio della ricezione di eventi in Azure Time Series Insights. |
      | **Operazione di aggregazione** | Selezionare **MAX** (MASSIMA).|

      [![Selezioni per la definizione della temperatura](media/v2-update-provision/define-five-humidity.png)](media/v2-update-provision/define-five-humidity.png#lightbox)

   Scegliere quindi **Create** (Crea).

1. È possibile visualizzare il tipo aggiunto:

   [![Informazioni sul tipo aggiunto](media/v2-update-provision/define-six-type.png)](media/v2-update-provision/define-six-type.png#lightbox)

1. Il passaggio successivo consiste nell'aggiungere una gerarchia. Nella sezione **Hierarchies** (Gerarchie) selezionare **+ Add** (+ Aggiungi):

   [![Scheda delle gerarchie con pulsante di aggiunta](media/v2-update-provision/define-seven-hierarchy.png)](media/v2-update-provision/define-seven-hierarchy.png#lightbox)

1. Definire la gerarchia. Compilare i campi come indicato di seguito:

   | | |
   | --- | ---|
   | **Nome** | Immettere `Location Hierarchy`. |
   | **Livello 1** | Immettere `Country`. |
   | **Livello 2** | Immettere `City`. |
   | **Livello 3** | Immettere `Building`. |

   Scegliere quindi **Create** (Crea).

   [![Campi della gerarchia con pulsante di creazione](media/v2-update-provision/define-eight-add-hierarchy.png)](media/v2-update-provision/define-eight-add-hierarchy.png#lightbox)

1. È possibile visualizzare la gerarchia creata:

   [![Informazioni sulla gerarchia](media/v2-update-provision/define-nine-created.png)](media/v2-update-provision/define-nine-created.png#lightbox)

1. Selezionare **Instances** (Istanze) sulla sinistra. Quando le istanze vengono visualizzate, selezionare la prima e quindi fare clic su **Edit** (Modifica):

   [![Selezione del pulsante di modifica per un'istanza](media/v2-update-provision/define-ten-edit.png)](media/v2-update-provision/define-ten-edit.png#lightbox)

1. A destra verrà aperto un editor di testo. Aggiungere le informazioni seguenti:

   | | |
   | --- | --- |
   | **Tipo** | Selezionare **Chiller** (Refrigeratore). |
   | **Descrizione** | Immettere `Instance for Chiller-01.1`. |
   | **Gerarchie** | Selezionare **Location Hierarchy** (Gerarchia località). |
   | **Paese** | Immettere `USA`. |
   | **Città** | Immettere `Seattle`. |
   | **Edificio** | Immettere `Space Needle`. |

    Selezionare quindi **Salva**.

   [![Campi dell'istanza con pulsante di salvataggio](media/v2-update-provision/define-eleven-chiller.png)](media/v2-update-provision/define-eleven-chiller.png#lightbox)

1. Ripetere il passaggio precedente per gli altri sensori. Usare i campi seguenti:

   * Per Chiller 01.2:

     | | |
     | --- | --- |
     | **Tipo** | Selezionare **Chiller** (Refrigeratore). |
     | **Descrizione** | Immettere `Instance for Chiller-01.2`. |
     | **Gerarchie** | Selezionare **Location Hierarchy** (Gerarchia località). |
     | **Paese** | Immettere `USA`. |
     | **Città** | Immettere `Seattle`. |
     | **Edificio** | Immettere `Pacific Science Center`. |

   * Per Chiller 01.3:

     | | |
     | --- | --- |
     | **Tipo** | Selezionare **Chiller** (Refrigeratore). |
     | **Descrizione** | Immettere `Instance for Chiller-01.3`. |
     | **Gerarchie** | Selezionare **Location Hierarchy** (Gerarchia località). |
     | **Paese** | Immettere `USA`. |
     | **Città** | Immettere `New York`. |
     | **Edificio** | Immettere `Empire State Building`. |

1. Aprire la scheda **Analizza** e aggiornare la pagina. Espandere tutti i livelli della gerarchia per trovare la serie temporale.

   [![Scheda Analizza](media/v2-update-provision/define-twelve.png)](media/v2-update-provision/define-twelve.png#lightbox)

1. Per esplorare le serie temporali dell'ultima ora, modificare **Tempi rapidi** in **Ultima ora**:

    [![Casella Tempi rapidi con opzione Ultima ora selezionata](media/v2-update-provision/define-thirteen-explore.png)](media/v2-update-provision/define-thirteen-explore.png#lightbox)

1. Selezionare la serie temporale in **Pacific Science Center** e quindi **Show Max Humidity** (Mostra umidità massima).

    [![Serie temporale selezionata con selezione del menu Show Max Humidity (Mostra umidità massima)](media/v2-update-provision/define-fourteen-show-max.png)](media/v2-update-provision/define-fourteen-show-max.png#lightbox)

1. Si aprirà la serie temporale per **Max Humidity** (Umidità massima) con un intervallo di dimensioni pari a **1 minuto**. Selezionare un'area per filtrare un intervallo. Fare quindi clic con il pulsante destro del mouse e selezionare **Zoom** per analizzare gli eventi nell'intervallo di tempo:

   [![Intervallo selezionato con comando Zoom in un menu di scelta rapida](media/v2-update-provision/define-fifteen-filter.png)](media/v2-update-provision/define-fifteen-filter.png#lightbox)

1. È anche possibile selezionare un'area e quindi fare clic con il pulsante destro del mouse per visualizzare i dettagli dell'evento:

   [![Elenco dettagliato di eventi](media/v2-update-provision/define-eighteen.png)](media/v2-update-provision/define-eighteen.png#lightbox)

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