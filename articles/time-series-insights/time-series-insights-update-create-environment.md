---
title: 'Esercitazione: Configurare un ambiente di anteprima di Azure Time Series Insights | Microsoft Docs'
description: Informazioni su come configurare l'ambiente nella versione di anteprima di Azure Time Series Insights.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 11/06/2019
ms.custom: seodec18
ms.openlocfilehash: 25571dbd87e4d01645a3a7a991588a3a943b3e4d
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73719455"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>Esercitazione: Configurare un ambiente di anteprima di Azure Time Series Insights

Questa esercitazione illustra il processo di creazione di un ambiente di anteprima con pagamento in base al consumo di Azure Time Series Insights.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
>
> * Creare un ambiente di anteprima di Azure Time Series Insights.
> * Connettere l'ambiente di anteprima di Azure Time Series Insights a un hub IoT.
> * Eseguire un esempio di acceleratore di soluzione per trasmettere dati nell'ambiente di anteprima di Azure Time Series Insights.
> * Eseguire un'analisi di base sui dati.
> * Definire un tipo di modello di serie temporale e una gerarchia e associarli alle istanze.
> * Usare il connettore Power BI e visualizzare i dati in Power BI.

>[!TIP]
> Gli [acceleratori di soluzioni IoT](https://www.azureiotsolutions.com/Accelerators) offrono soluzioni preconfigurate di livello aziendale utilizzabili per accelerare lo sviluppo di soluzioni IoT personalizzate.

Registrarsi per creare una [sottoscrizione di Azure gratuita](https://azure.microsoft.com/free/), se non è già stato fatto.

## <a name="prerequisites"></a>Prerequisiti

* È necessario avere almeno il ruolo **Collaboratore** per la sottoscrizione di Azure. Per altre informazioni, vedere [Gestire l'accesso mediante il controllo degli accessi in base al ruolo e il portale di Azure](../role-based-access-control/role-assignments-portal.md).

## <a name="create-a-device-simulation"></a>Creare una simulazione di dispositivi

In questa sezione verranno creati tre dispositivi simulati che inviano dati a un'istanza dell'hub IoT di Azure.

1. Accedere alla [pagina degli acceleratori di soluzione di Azure IoT](https://www.azureiotsolutions.com/Accelerators). La pagina mostra diversi esempi predefiniti. Accedere tramite l'account di Azure personale. Quindi, selezionare **Simulazione dispositivi**.

   [![Pagina degli acceleratori di soluzione Azure IoT](media/v2-update-provision/device-one-accelerator.png)](media/v2-update-provision/device-one-accelerator.png#lightbox)

1. Nella pagina successiva selezionare **Prova adesso**.

   [![Pagina Simulazione dispositivi](media/v2-update-provision/device-two-try.png)](media/v2-update-provision/device-two-try.png#lightbox)

1. Nella pagina della soluzione **Crea simulazione dispositivo** impostare i parametri seguenti:

    | Parametro | Azione |
    | --- | --- |
    | **Nome della distribuzione** | Immettere un valore univoco per un nuovo gruppo di risorse. Le risorse di Azure elencate vengono create e assegnate al gruppo di risorse. |
    | **Sottoscrizione di Azure** | Selezionare la sottoscrizione in cui verrà creato l'ambiente Time Series Insights. |
    | **Località di Azure** | Selezionare l'area in cui si intende archiviare l'ambiente Time Series Insights. Tenere presente che il simulatore di dispositivi è disponibile solo in un numero limitato di aree. Se l'area desiderata non è presente si può pertanto scegliere di selezionare una località da usare esclusivamente per l'esercitazione e creare quindi un nuovo ambiente Time Series Insights quando si è pronti a passare alla fase successiva del processo di onboarding.  |
    | **Opzioni di distribuzione** | Selezionare **Effettuare il provisioning di un nuovo hub IoT**. |

    1. Selezionare **Create** (Crea).
    [![Pagina Simulazione dispositivi](media/v2-update-provision/device-two-create.png)](media/v2-update-provision/device-two-create.png#lightbox)

1. Dopo circa 20 minuti, l'acceleratore di soluzione sarà pronto.

    [![Pagina Simulazione dispositivi](media/v2-update-provision/device-two-ready.png)](media/v2-update-provision/device-two-ready.png#lightbox)

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
    | **Posizione** | Selezionare un'area del data center per l'ambiente di anteprima di Azure Time Series Insights. Per evitare un aumento della latenza, è consigliabile creare l'ambiente di anteprima di Azure Time Series Insights nella stessa area dell'hub IoT creato dal simulatore di dispositivi. |
    | **Livello** |  Selezionare **PAYG** (*pagamento a consumo*). Si tratta dello SKU per il prodotto di anteprima di Azure Time Series Insights. |
    | **ID proprietà** | Immettere un valore che identifichi in modo univoco l'istanza della serie temporale. Il valore immesso nella casella **ID proprietà** non può essere modificato successivamente. Per questa esercitazione immettere **iothub-connection-device-id**. Per altre informazioni sull'ID serie temporale, vedere [Procedure consigliate per la scelta di un ID serie temporale](./time-series-insights-update-how-to-id.md). |
    | **Nome account di archiviazione** | Immettere un nome univoco globale per un nuovo account di archiviazione.|
    |**Abilita archivio ad accesso frequente**|Selezionare **Sì** per abilitare l'archivio ad accesso frequente.|
    |**Conservazione dei dati (in giorni)**|Scegliere l'opzione predefinita di 7 giorni. |

    Selezionare **Avanti: Origine evento**.

   [![Riquadro per la creazione di un ambiente Time Series Insights](media/v2-update-provision/payg-two-1-create.png)](media/v2-update-provision/payg-two-1-create.png#lightbox)
   [![Riquadro per la creazione di un ambiente Time Series Insights](media/v2-update-provision/payg-two-2-create.png)](media/v2-update-provision/payg-two-2-create.png#lightbox)

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

1. Selezionare **Create** (Crea).

    [![Pagina Rivedi e crea, con pulsante Crea](media/v2-update-provision/payg-six-review.png)](media/v2-update-provision/payg-six-review.png#lightbox)

    È possibile visualizzare lo stato della distribuzione:

    [![Notifica del completamento della distribuzione](media/v2-update-provision/payg-seven-deploy.png)](media/v2-update-provision/payg-seven-deploy.png#lightbox)

1. Se si è proprietari della sottoscrizione di Azure, si avrà accesso all'ambiente di anteprima di Azure Time Series Insights per impostazione predefinita. Verificare di avere accesso:

   1. Cercare il gruppo di risorse e quindi selezionare l'ambiente di anteprima di Azure Time Series Insights appena creato. 
      [![Ambiente selezionato](media/v2-update-provision/payg-eight-environment.png)](media/v2-update-provision/payg-eight-environment.png#lightbox)

   1. Nella pagina di anteprima di Azure Time Series Insights selezionare **Criteri di accesso ai dati**: [![Criteri di accesso ai dati](media/v2-update-provision/payg-nine-data-access.png)](media/v2-update-provision/payg-nine-data-access.png#lightbox)

   1. Verificare che le credenziali siano elencate:

      [![Credenziali elencate](media/v2-update-provision/payg-ten-verify.png)](media/v2-update-provision/payg-ten-verify.png#lightbox)

   Se le credenziali non sono elencate, è necessario concedere a se stessi l'autorizzazione per accedere all'ambiente selezionando Aggiungi e cercando le proprie credenziali. Per altre informazioni sull'impostazione delle autorizzazioni, leggere [Concedere l'accesso ai dati](./time-series-insights-data-access.md).

## <a name="stream-data"></a>Trasmettere i dati

Ora che l'ambiente Time Series Insights è stato distribuito, iniziare a trasmettere i dati per l'analisi.

1. Tornare alla [pagina degli acceleratori di soluzione di Azure IoT](https://www.azureiotsolutions.com/Accelerators). Individuare la soluzione nella dashboard dell'acceleratore di soluzione e quindi selezionare **Avvia**:

    [![Avviare la soluzione Simulazione dispositivi](media/v2-update-provision/device-three-launch.png)](media/v2-update-provision/device-three-launch.png#lightbox)

1. Selezionare **Vai a Acceleratore di soluzione**.

    [![Avviare la soluzione Simulazione dispositivi](media/v2-update-provision/device-accelerator.png)](media/v2-update-provision/device-accelerator.png#lightbox)

1. Si verrà reindirizzati alla pagina **Microsoft Azure IoT Device Simulation** (Simulazione dispositivi di Microsoft Azure IoT). Nell'angolo in alto a destra della pagina selezionare **New simulation** (Nuova simulazione).

    [![Pagina di simulazione di Azure IoT](media/v2-update-provision/device-four-iot-sim-page.png)](media/v2-update-provision/device-four-iot-sim-page.png#lightbox)

1. Nel riquadro **Simulation setup** (Configurazione simulazione) impostare i parametri seguenti:

    | Parametro | Azione |
    | --- | --- |
    | **Nome** | Immettere un nome univoco per un simulatore. |
    | **Descrizione** | Immettere una definizione. |
    | **Simulation duration** (Durata simulazione) | Impostare su **Run indefinitely** (Esecuzione a tempo indefinito). |
    | **Device model** (Modello del dispositivo) | Fare clic su + **Add a device type** (Aggiungi un tipo di dispositivo) <br />**Nome**: immettere **Elevator** (Ascensore). <br />**Amount** (Quantità): immettere **3**. <br /> Lasciare i valori predefiniti restanti |
    | **Target IoT Hub** (Hub IoT di destinazione) | Impostare su **Use pre-provisioned IoT Hub** (Usa l'hub IoT di cui è già stato eseguito il provisioning). |

    [![Parametri da impostare](media/v2-update-provision/device-five-params.png)](media/v2-update-provision/device-five-params.png#lightbox)

    Selezionare **Start simulation** (Avvia simulazione).

    Nella dashboard della simulazione dispositivi verranno visualizzati **Active devices** (Dispositivi attivi) e **Total messages** (Totale messaggi).

    [![Dashboard di simulazione di Azure IoT](media/v2-update-provision/device-seven-dashboard.png)](media/v2-update-provision/device-seven-dashboard.png#lightbox)

## <a name="analyze-data"></a>Analizzare i dati

In questa sezione verranno eseguite attività di analisi di base sui dati delle serie temporali usando lo [strumento di esplorazione aggiornato dell'anteprima di Azure Time Series Insights](./time-series-insights-update-explorer.md).

1. Passare allo strumento di esplorazione dell'anteprima di Azure Time Series Insights selezionando l'URL nella pagina delle risorse nel [portale di Azure](https://portal.azure.com/).

    [![URL dello strumento di esplorazione dell'anteprima di Time Series Insights](media/v2-update-provision/analyze-one-portal.png)](media/v2-update-provision/analyze-one-portal.png#lightbox)

1. Nello strumento di esplorazione di Time Series Insights è presente una barra che si estende nella parte superiore della schermata. Questa è la selezione disponibilità. Assicurarsi di avere almeno due valori 2m selezionati e, se necessario, espandere l'intervallo di tempo selezionando e trascinando i quadratini di selezione verso sinistra e verso destra.

1. Le **istanze della serie temporale** saranno visibili a sinistra.


    [![Elenco di istanze senza padre](media/v2-update-provision/analyze-two-unparented.png)](media/v2-update-provision/analyze-two-unparented.png#lightbox)

1. Selezionare la prima istanza della serie temporale. Selezionare quindi **Show pressure** (Mostra pressione).

    [![Istanza della serie temporale selezionata con il comando di menu per visualizzare la pressione media](media/v2-update-provision/analyze-three-show-pressure.png)](media/v2-update-provision/analyze-three-show-pressure.png#lightbox)

    Viene visualizzato un grafico della serie temporale. Modificare l'**intervallo** impostandolo su **30s**.

    [![Grafico serie temporale](media/v2-update-provision/analyze-four-chart.png)](media/v2-update-provision/analyze-four-chart.png#lightbox)

1. Ripetere il passaggio 3 per le altre due istanze della serie temporale in modo da visualizzarle tutte e tre, come illustrato in questo grafico:

    [![Grafico relativo a tutte le serie temporali](media/v2-update-provision/analyze-five-chart.png)](media/v2-update-provision/analyze-five-chart.png#lightbox)

1. Selezionare lo strumento di selezione dell'intervallo di tempo nell'angolo superiore destro. Qui è possibile selezionare orari di inizio e di fine specifici fino al millisecondo oppure scegliere tra le opzioni preconfigurate, ad esempio l'ultima ora. È anche possibile modificare il fuso orario predefinito.

    [![Impostare l'intervallo di tempo su un'ora](media/v2-update-provision/analyze-six-time.png)](media/v2-update-provision/analyze-six-time.png#lightbox)

    Di seguito è riportata un'acquisizione di schermata del riquadro dei grafici dopo che la simulazione è stata eseguita per un'ora:

    [![Riquadro dei grafici](media/v2-update-provision/analyze-seven-time.png)](media/v2-update-provision/analyze-seven-time.png#lightbox)

## <a name="define-and-apply-a-model"></a>Definire e applicare un modello

In questa sezione si applica un modello per strutturare i dati. Per completare il modello, vengono definiti i tipi, le gerarchie e le istanze. Per altre informazioni sulla modellazione dei dati, vedere [Modello Time Series](./time-series-insights-update-tsm.md).

1. Nella finestra di esplorazione, selezionare la scheda **Modello**:

   [![Scheda Modello nello strumento di esplorazione](media/v2-update-provision/define-one-model.png)](media/v2-update-provision/define-one-model.png#lightbox)

1. Nella scheda **Tipi** selezionare **Aggiungi**.

   [![Pulsante di aggiunta per i tipi](media/v2-update-provision/define-two-add.png)](media/v2-update-provision/define-two-add.png#lightbox)

1. Immettere i parametri seguenti:

    | Parametro | Azione |
    | --- | ---|
    | **Nome** | Immettere **Elevator** (Ascensore) |
    | **Descrizione** | Immettere **This is a type definition for Elevator** (Definizione del tipo per Ascensore) |

    [![Pulsante di aggiunta per i tipi](media/v2-update-provision/define-three-properties.png)](media/v2-update-provision/define-three-properties.png#lightbox)

1. Selezionare quindi la scheda **Variabili**. [![Selezionare la scheda Variabili](media/v2-update-provision/define-four-variables.png)](media/v2-update-provision/define-four-variables.png#lightbox)

1. Selezionare **+ Aggiungi variabile** e inserire i valori seguenti per la prima variabile del tipo Elevator. Verranno create in totale tre variabili.

    | Parametro | Azione |
    | --- | --- |
    | **Nome** | Immettere **Avg Temperature** (Temperatura media). |
    | **Tipologia** | Selezionare **Numerico** |
    | **Valore** | Seleziona da set di impostazioni: Selezionare **temperatura (doppia)** . <br /> Note: Il popolamento automatico del campo **Value** (Valore) potrebbe richiedere alcuni minuti dopo l'inizio della ricezione di eventi nell'anteprima di Azure Time Series Insights.|
    | **Operazione di aggregazione** | Espandere **Opzioni avanzate**. <br /> Selezionare **AVG** (MEDIA). |

    [![Selezioni per la definizione della temperatura](media/v2-update-provision/define-five-variable.png)](media/v2-update-provision/define-five-variable.png#lightbox)

    Selezionare **Applica**.

    Selezionare di nuovo **+ Aggiungi variabile** e impostare i valori seguenti:

    | Parametro | Azione |
    | --- | --- |
    | **Nome** | Immettere **Avg Vibration** (Vibrazione media). |
    | **Tipologia** | Selezionare **Numerico** |
    | **Valore** | Seleziona da set di impostazioni: selezionare **vibration (Double)** . <br /> Note: Il popolamento automatico del campo **Value** (Valore) potrebbe richiedere alcuni minuti dopo l'inizio della ricezione di eventi nell'anteprima di Azure Time Series Insights.|
    | **Operazione di aggregazione** | Espandere **Opzioni avanzate**. <br /> Selezionare **AVG** (MEDIA). |

    [![Selezioni per la definizione della vibrazione](media/v2-update-provision/define-five-vibration.png)](media/v2-update-provision/define-five-vibration.png#lightbox)

    Selezionare **Applica**.

    Selezionare di nuovo **+ Aggiungi variabile** e impostare i valori seguenti per la terza e ultima variabile:

    | Parametro | Azione |
    | --- | --- |
    | **Nome** | Immettere **Floor** (Piano). |
    | **Tipologia** | Selezionare **Categorie** |
    | **Valore** | Seleziona da set di impostazioni: selezionare **Floor (Double)** . <br /> Note: Il popolamento automatico del campo **Value** (Valore) potrebbe richiedere alcuni minuti dopo l'inizio della ricezione di eventi nell'anteprima di Azure Time Series Insights.|
    | **Categories** (Categorie) | <span style="text-decoration: underline">Etichetta </span>  - <span style="text-decoration: underline">Valori</span> <br /> Lower (Inferiori): 1,2,3,4 <br /> Middle (Medi): 5,6,7,8,9 <br /> Upper (Superiori): 10,11,12,13,14,15 |
    | **Categoria predefinita** | Immettere **Unknown** (Sconosciuta) |

    [![Selezioni per la definizione della vibrazione](media/v2-update-provision/define-five-floor.png)](media/v2-update-provision/define-five-floor.png#lightbox)

    Selezionare **Applica**. Sono state create tre variabili:

    [![Selezioni per la definizione della vibrazione](media/v2-update-provision/define-five-floor2.png)](media/v2-update-provision/define-five-floor2.png#lightbox)

   Selezionare **Salva**. Verrà visualizzato il **Tipo** creato:

    [![Selezioni per la definizione della vibrazione](media/v2-update-provision/define-five-floor3.png)](media/v2-update-provision/define-five-floor3.png#lightbox)

1. Selezionare la scheda **Gerarchie**. Selezionare **+Aggiungi**.

    [![Scheda delle gerarchie con pulsante di aggiunta](media/v2-update-provision/define-seven-hierarchy.png)](media/v2-update-provision/define-seven-hierarchy.png#lightbox)

1. Nel riquadro **Edit Hierarchy** (Modifica gerarchia) impostare i parametri seguenti:

   | Parametro | Azione |
   | --- | ---|
   | **Nome** | Immettere **Location Hierarchy** (Gerarchia località). |
   |**Livelli**| Immettere **Country** (Paese) come nome del primo livello <br> Selezionare **+ Aggiungi livello** <br> Immettere **City** (Città) per il secondo livello e quindi selezionare **+ Aggiungi livello** <br> Immettere **Building** (Edificio) come nome del terzo e ultimo livello |

   Selezionare **Salva**.

    [![Campi della gerarchia con pulsante di creazione](media/v2-update-provision/define-eight-add-hierarchy.png)](media/v2-update-provision/define-eight-add-hierarchy.png#lightbox)

   È possibile visualizzare la gerarchia creata:

    [![Informazioni sulla gerarchia](media/v2-update-provision/define-nine-created.png)](media/v2-update-provision/define-nine-created.png#lightbox)

1. Passare a **Istanze**. In **Azioni** nella parte destra selezionare l'icona della matita per modificare la prima istanza con i valori seguenti:

    | Parametro | Azione |
    | --- | --- |
    | **Tipo** | Selezionare **Elevator** (Ascensore). |
    | **Nome** | Immettere **Elevator 1** (Ascensore 1)|
    | **Descrizione** | Immettere **Instance for Elevator 1** (Istanza per Ascensore 1) |

    [![Selezione del pulsante di modifica per un'istanza](media/v2-update-provision/define-ten-edit.png)](media/v2-update-provision/define-ten-edit.png#lightbox)

    Passare a **Campi di istanza** e immettere quanto segue:

    | Parametro | Azione |
    | --- | --- |
    | **Gerarchie** | Selezionare **Location Hierarchy** (Gerarchia località) |
    | **Paese** | Immettere **USA** (Stati Uniti) |
    | **Città** | Immettere **Seattle** |
    | **Edificio** | Immettere **Space Needle** |

    [![Selezione del pulsante di modifica per un'istanza](media/v2-update-provision/define-ten2-edit.png)](media/v2-update-provision/define-ten2-edit.png#lightbox)

    Selezionare **Salva**.

1. Ripetere il passaggio 8 per le altre due istanze usando i valori seguenti:

    <span style="text-decoration: underline;">Per Elevator 2</span> (Ascensore 2):

    | Parametro | Azione |
    | --- | --- |
    | **Tipo** | Selezionare **Elevator** (Ascensore). |
    | **Nome** | Immettere **Elevator 2** (Ascensore 2)|
    | **Descrizione** | Immettere **Instance for Elevator 2** (Istanza per Ascensore 2) |
    | **Gerarchie** | Selezionare **Location Hierarchy** (Gerarchia località) |
    | **Paese** | Immettere **USA** (Stati Uniti) |
    | **Città** | Immettere **Seattle** |
    | **Edificio** | Immettere **Pacific Science Center** |

    <span style="text-decoration: underline;">Per Elevator 3</span> (Ascensore 3):

    | Parametro | Azione |
    | --- | --- |
    | **Tipo** | Selezionare **Elevator** (Ascensore). |
    | **Nome** | Immettere **Elevator 3** (Ascensore 3)|
    | **Descrizione** | Immettere **Instance for Elevator 3** (Istanza per Ascensore 3) |
    | **Gerarchie** | Selezionare **Location Hierarchy** (Gerarchia località) |
    | **Paese** | Immettere **USA** (Stati Uniti) |
    | **Città** | Immettere **New York** |
    | **Edificio** | Immettere **Empire State Building** |

1. Tornare alla scheda **Analizza** per visualizzare il riquadro dei grafici. In **Location Hierarchy** (Gerarchia località) espandere tutti i livelli della gerarchia per visualizzare le istanze della serie temporale:

   [![Scheda Analizza](media/v2-update-provision/define-twelve.png)](media/v2-update-provision/define-twelve.png#lightbox)

1. In **Pacific Science Center** selezionare l'istanza della serie temporale **Elevator 2** (Ascensore 2) e quindi **Show Average Temperature** (Mostra temperatura media).

    [![Istanza della serie temporale - Mostra temperatura media](media/v2-update-provision/define-eleven-temperature.png)](media/v2-update-provision/define-eleven-temperature.png#lightbox)

1. Per la stessa istanza **Elevator 2** (Ascensore 2) selezionare **Show Floor** (Mostra piano).

    [![Istanza della serie temporale - Mostra piano](media/v2-update-provision/define-twelve-floor.png)](media/v2-update-provision/define-twelve-floor.png#lightbox)

    Con la variabile categoriale è possibile determinare il tempo trascorso dall'ascensore nei piani superiori, inferiori e medi.

## <a name="clean-up-resources"></a>Pulire le risorse

Ora che è stata completata l'esercitazione, pulire le risorse create:

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Tutte le risorse** nel menu a sinistra e individuare il gruppo di risorse di Azure Time Series Insights.
1. Per eliminare l'intero gruppo di risorse e tutte le risorse in esso contenute, selezionare **Elimina** oppure rimuovere ogni risorsa singolarmente.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:  

* Creare e usare un acceleratore di simulazione dispositivi.
* Creare un ambiente di anteprima di Azure Time Series Insights con pagamento in base al consumo.
* Connettere l'ambiente di anteprima di Azure Time Series Insights a un hub IoT.
* Eseguire un esempio di acceleratore di soluzione per trasmettere dati nell'ambiente di anteprima di Azure Time Series Insights.
* Eseguire un'analisi di base sui dati.
* Definire un tipo di modello di serie temporale e una gerarchia da associare alle istanze.

Ora che si è appreso come creare un ambiente di anteprima di Azure Time Series Insights, si procederà ad apprendere altre informazioni sui concetti chiave di Azure Time Series Insights.

Leggere informazioni sulla configurazione dell'archiviazione di Azure Time Series Insights:

> [!div class="nextstepaction"]
> [Archiviazione e ingresso dei dati nella versione di anteprima di Azure Time Series Insights](./time-series-insights-update-storage-ingress.md)

Leggere altre informazioni sui modelli di serie temporali:

> [!div class="nextstepaction"]
> [Modellazione dei dati nell'anteprima di Azure Time Series Insights](./time-series-insights-update-tsm.md)
