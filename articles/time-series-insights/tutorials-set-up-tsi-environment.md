---
title: 'Esercitazione: Configurare un ambiente Gen2 - Azure Time Series Insights Gen2 | Microsoft Docs'
description: 'Esercitazione: Informazioni su come configurare un ambiente in Azure Time Series Insights Gen2.'
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 09/30/2020
ms.custom: seodec18
ms.openlocfilehash: 79a4fc048b8301d67206bf28b571f88f9e5ad024
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2020
ms.locfileid: "91597676"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-gen2-environment"></a>Esercitazione: Configurare un ambiente Azure Time Series Insights Gen2

Questa esercitazione illustra il processo di creazione di un ambiente Azure Time Series Insights Gen2 *con pagamento in base al consumo*.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
>
> * Creare un ambiente Azure Time Series Insights Gen2.
> * Connettere l'ambiente Azure Time Series Insights Gen2 a un hub IoT.
> * Eseguire un esempio di acceleratore di soluzione per trasmettere dati nell'ambiente Azure Time Series Insights Gen2.
> * Eseguire un'analisi di base sui dati.
> * Definire un tipo di modello di serie temporale e una gerarchia e associarli alle istanze.

>[!TIP]
> Gli [acceleratori di soluzioni IoT](https://www.azureiotsolutions.com/Accelerators) offrono soluzioni preconfigurate di livello aziendale utilizzabili per accelerare lo sviluppo di soluzioni IoT personalizzate.

Registrarsi per creare una [sottoscrizione di Azure gratuita](https://azure.microsoft.com/free/), se non è già stato fatto.

## <a name="prerequisites"></a>Prerequisiti

* È necessario avere almeno il ruolo **Collaboratore** per la sottoscrizione di Azure. Per altre informazioni, vedere [Gestire l'accesso mediante il controllo degli accessi in base al ruolo e il portale di Azure](../role-based-access-control/role-assignments-portal.md).

## <a name="create-a-device-simulation"></a>Creare una simulazione di dispositivi

In questa sezione verranno creati tre dispositivi simulati che inviano dati a un'istanza dell'hub IoT di Azure.

1. Accedere alla [pagina degli acceleratori di soluzione di Azure IoT](https://www.azureiotsolutions.com/Accelerators). La pagina mostra diversi esempi predefiniti. Accedere tramite l'account di Azure personale. Quindi, selezionare **Simulazione dispositivi**.

   [![Pagina degli acceleratori della soluzione Azure IoT.](media/v2-update-provision/iot-solution-accelerators-landing-page.png)](media/v2-update-provision/iot-solution-accelerators-landing-page.png#lightbox)

1. Immettere la sottoscrizione in cui si vuole creare la simulazione del dispositivo.

   Parametro|Descrizione
   ---|---
   **Nome distribuzione** | Questo valore univoco viene usato per creare un nuovo gruppo di risorse. Le risorse di Azure elencate vengono create e assegnate al gruppo di risorse.
   **Sottoscrizione di Azure** | Specificare la stessa sottoscrizione usata per creare l'ambiente Azure Time Series Insights Gen2 nella sezione precedente.
   **Opzioni di distribuzione** | Selezionare **Provision new IoT Hub** (Effettuare il provisioning di un nuovo hub IoT) per creare un nuovo hub IoT specifico per questa esercitazione.
   **Località di Azure** | Specificare la stessa area usata per creare l'ambiente Azure Time Series Insights Gen2 nella sezione precedente.

   Al termine, selezionare **Crea** per effettuare il provisioning delle risorse di Azure della soluzione. Il completamento del processo può richiedere fino a 20 minuti.

   [![Eseguire il provisioning della soluzione di simulazione dispositivi.](media/v2-update-provision/iot-solution-accelerators-configuration.png)](media/v2-update-provision/iot-solution-accelerators-configuration.png#lightbox)

1. Al termine del provisioning, verranno visualizzate due notifiche che mostrano che lo stato della distribuzione è passato da **Provisioning** a **Pronto**.

   >[!IMPORTANT]
   > Non immettere ancora l'acceleratore di soluzione, ma mantenere aperta questa pagina Web per tornarvi in un secondo momento.

   [![Completamento del provisioning della soluzione di simulazione dispositivi.](media/v2-update-provision/iot-solution-accelerator-ready.png)](media/v2-update-provision/iot-solution-accelerator-ready.png#lightbox)

1. Esaminare ora le risorse appena create nel portale di Azure. Nella pagina **Gruppi di risorse** si noti che è stato creato un nuovo gruppo di risorse con il **Nome soluzione** specificato nel passaggio precedente. Prendere nota delle risorse create per la simulazione di dispositivi.

   [![Risorse per la simulazione di dispositivi.](media/v2-update-provision/tsi-device-sim-solution-resources.png)](media/v2-update-provision/tsi-device-sim-solution-resources.png#lightbox)

## <a name="create-an-azure-time-series-insights-gen2-environment"></a>Creare un ambiente Azure Time Series Insights Gen2

Questa sezione descrive come creare un ambiente Azure Time Series Insights Gen2 e connetterlo all'hub IoT creato dall'acceleratore di soluzione IoT usando il [portale di Azure](https://portal.azure.com/).

1. Accedere al [portale di Azure](https://portal.azure.com) usando l'account della sottoscrizione di Azure.
1. Selezionare **+ Crea una risorsa** in alto a sinistra.
1. Selezionare la categoria **Internet delle cose** e quindi **Time Series Insights**.

   [![Selezionare la risorsa dell'ambiente Time Series Insights.](media/v2-update-provision/tsi-create-new-environment.png)](media/v2-update-provision/tsi-create-new-environment.png#lightbox)

1. Nel riquadro **Crea ambiente Time Series Insights** della scheda **Generale** impostare i parametri seguenti:

    | Parametro | Azione |
    | --- | ---|
    | **Nome ambiente** | Immettere un nome univoco per l'ambiente Azure Time Series Insights Gen2. |
    | **Sottoscrizione** | Immettere la sottoscrizione in cui si vuole creare l'ambiente Azure Time Series Insights Gen2. È consigliabile usare la stessa sottoscrizione delle altre risorse IoT create dal simulatore di dispositivi. |
    | **Gruppo di risorse** | Selezionare un gruppo di risorse esistente per la risorsa dell'ambiente Azure Time Series Insights Gen2 oppure creare un gruppo di risorse nuovo. Un gruppo di risorse è un contenitore per le risorse di Azure. È consigliabile usare lo stesso gruppo di risorse delle altre risorse IoT create dal simulatore di dispositivi. |
    | **Posizione** | Selezionare un'area del data center per l'ambiente Azure Time Series Insights Gen2. Per evitare un aumento della latenza, è consigliabile creare l'ambiente Azure Time Series Insights Gen2 nella stessa area dell'hub IoT creato dal simulatore di dispositivo. |
    | **Livello** |  Selezionare **Gen2(L1)** . Si tratta dello SKU del prodotto Azure Time Series Insights Gen2. |
    | **Nome proprietà ID serie temporale** | Immettere il nome di una proprietà contenente valori che identificano in modo univoco le istanze della serie temporale. Il valore immesso nella casella **Nome proprietà** come ID della serie temporale non può essere modificato successivamente. Per questa esercitazione immettere ***iothub-connection-device-id***. Per altre informazioni sull'ID serie temporale, incluso l'ID serie temporale composito, vedere [Procedure consigliate per la scelta di un ID serie temporale](./time-series-insights-update-how-to-id.md). |
    | **Nome account di archiviazione** | Immettere un nome univoco globale per un nuovo account di archiviazione.|
    | **Tipo di account di archiviazione** | Selezionare il tipo di archiviazione per un nuovo account di archiviazione. Il tipo consigliato è Archiviazione V2|
    | **Replica dell'account di archiviazione** | Selezionare il tipo di archiviazione per un nuovo account di archiviazione. In base alla selezione della località, è possibile scegliere tra Archiviazione con ridondanza locale, Archiviazione con ridondanza geografica e Archiviazione con ridondanza della zona. Per questa esercitazione è possibile selezionare Archiviazione con ridondanza locale.|
    | **Spazio dei nomi gerarchico** |Questa opzione può essere selezionata dopo aver selezionato Archiviazione V2 come tipo di archiviazione. Per impostazione predefinita, è disabilitata. Per questa esercitazione è possibile mantenere lo stato predefinito *disabilitato*|
    |**Abilita archivio ad accesso frequente**|Selezionare **Sì** per abilitare l'archivio ad accesso frequente. Questa impostazione può essere disabilitata e riabilitata anche dopo che è stato creato l'ambiente. |
    |**Conservazione dei dati (in giorni)**|Scegliere l'opzione predefinita di 7 giorni. |

    [![Configurazione del nuovo ambiente Time Series Insights.](media/v2-update-provision/tsi-environment-configuration.png)](media/v2-update-provision/tsi-environment-configuration.png#lightbox)

1. Selezionare **Avanti: Origine evento**.

   [![Configure l'ID serie temporale per l'ambiente.](media/v2-update-provision/tsi-time-series-id-selection.png)](media/v2-update-provision/tsi-time-series-id-selection.png#lightbox)

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
   | **Gruppo di consumer dell'hub IoT** | Selezionare **Nuovo**, immettere un nome univoco e quindi selezionare **+ Aggiungi**. Il gruppo di consumer deve essere un valore univoco in Azure Time Series Insights Gen2. |
   | **Timestamp property** (Proprietà timestamp) | Questo valore viene usato per identificare la proprietà **Timestamp** nei dati di telemetria in ingresso. Per questa esercitazione, lasciare vuota questa casella. Questo simulatore usa il timestamp in ingresso dall'hub IoT predefinito di Azure Time Series Insights Gen2. |

1. Selezionare **Rivedi e crea**.

   [![Configurare l'hub IoT creato come origine evento.](media/v2-update-provision/tsi-configure-event-source.png)](media/v2-update-provision/tsi-configure-event-source.png#lightbox)

1. Selezionare **Crea**.

    [![Pagina Rivedi e crea, con il pulsante Crea.](media/v2-update-provision/tsi-environment-confirmation.png)](media/v2-update-provision/tsi-environment-confirmation.png#lightbox)

    È possibile esaminare lo stato della distribuzione:

    [![Notifica del completamento della distribuzione.](media/v2-update-provision/tsi-deployment-notification.png)](media/v2-update-provision/tsi-deployment-notification.png#lightbox)

1. Se si è proprietari della sottoscrizione di Azure, si avrà accesso all'ambiente Azure Time Series Insights Gen2 per impostazione predefinita. Verificare di avere accesso:

   1. Cercare il gruppo di risorse e quindi selezionare l'ambiente Azure Time Series Insights Gen2 appena creato.

      [![Selezionare e visualizzare l'ambiente.](media/v2-update-provision/verify-tsi-resource-in-group.png)](media/v2-update-provision/verify-tsi-resource-in-group.png#lightbox)

   1. Nella pagina Azure Time Series Insights Gen2 selezionare **Criteri di accesso ai dati**:

      [![Verificare i criteri di accesso ai dati.](media/v2-update-provision/tsi-data-access-panel.png)](media/v2-update-provision/tsi-data-access-panel.png#lightbox)

   1. Verificare che le credenziali siano elencate:

      Se le credenziali non sono elencate, è necessario concedere a se stessi l'autorizzazione per accedere all'ambiente selezionando Aggiungi e cercando le proprie credenziali. Per altre informazioni sull'impostazione delle autorizzazioni, leggere [Concedere l'accesso ai dati](./time-series-insights-data-access.md).

## <a name="stream-data"></a>Trasmettere i dati

Ora che l'ambiente Time Series Insights Gen2 è stato distribuito, si può iniziare a trasmettere i dati per l'analisi.

1. Tornare al [dashboard Acceleratori di soluzione](https://www.azureiotsolutions.com/Accelerators#dashboard). Se necessario, eseguire nuovamente l'accesso usando lo stesso account di Azure già usato in questa esercitazione. Selezionare la "soluzione per dispositivi" e quindi **passare all'acceleratore di soluzione** per avviare la soluzione distribuita.

   [![Dashboard di acceleratori della soluzione.](media/v2-update-provision/iot-solution-accelerator-ready.png)](media/v2-update-provision/iot-solution-accelerator-ready.png#lightbox)

1. All'inizio, l'app Web di simulazione dei dispositivi richiede il consenso dell'utente per la concessione all'applicazione Web dell'autorizzazione **Accedi e visualizza il profilo personale**. Questa autorizzazione consente di recuperare le informazioni sul profilo utente necessarie per supportare il funzionamento dell'applicazione:

   [![Consenso per l'applicazione Web di simulazione dispositivi.](media/v2-update-provision/sawa-signin-consent.png)](media/v2-update-provision/sawa-signin-consent.png#lightbox)

1. Selezionare **+ Nuova simulazione**.

    1. Al termine del caricamento della pagina **Simulation setup** (Configurazione simulazione), immettere i parametri richiesti.

        | Parametro | Azione |
        | --- | --- |
        | **Nome** | Immettere un nome univoco per un simulatore. |
        | **Descrizione** | Immettere una definizione. |
        | **Simulation duration** (Durata simulazione) | Impostare su **Run indefinitely** (Esecuzione a tempo indefinito). |
        | **Device model** (Modello del dispositivo) | Fare clic su + **Add a device type** (Aggiungi un tipo di dispositivo) <br />**Name**: immettere **Elevator** (Ascensore). <br />**Amount** (Quantità): immettere **3**. <br /> Lasciare i valori predefiniti restanti |
        | **Target IoT Hub** (Hub IoT di destinazione) | Impostare su **Use pre-provisioned IoT Hub** (Usa l'hub IoT di cui è già stato eseguito il provisioning). |

        [![Configurare i parametri e avviare.](media/v2-update-provision/tsi-launch-solution-accelerator.png)](media/v2-update-provision/tsi-launch-solution-accelerator.png#lightbox)

    1. Selezionare **Start simulation** (Avvia simulazione). Nel dashboard di simulazione dei dispositivi verranno visualizzati **Active devices** (Dispositivi attivi) e **Total messages** (Totale messaggi).

        [![Dashboard di simulazione di Azure IoT.](media/v2-update-provision/tsi-see-active-devices-and-messages.png)](media/v2-update-provision/tsi-see-active-devices-and-messages.png#lightbox)

## <a name="analyze-data"></a>Analizzare i dati

In questa sezione verranno eseguite attività di analisi di base sui dati delle serie temporali usando [Azure Time Series Insights Gen2 Explorer](./time-series-insights-update-explorer.md).

1. Passare ad Azure Time Series Insights Gen2 Explorer selezionando l'URL nella pagina delle risorse nel [portale di Azure](https://portal.azure.com/).

    [![URL di Azure Time Series Insights Gen2 Explorer.](media/v2-update-provision/tsi-select-explorer-url.png)](media/v2-update-provision/tsi-select-explorer-url.png#lightbox)

1. In Azure Time Series Insights Gen2 Explorer verrà visualizzata una barra che si estende nella parte superiore della schermata. Questa è la selezione disponibilità. Assicurarsi di avere almeno due valori 2 m selezionati e, se necessario, espandere l'intervallo di tempo selezionando e trascinando i quadratini di selezione verso sinistra e verso destra.

1. **Le istanze della serie temporale** verranno visualizzate sul lato sinistro.

    [![Elenco di istanze senza padre.](media/v2-update-provision/tsi-explorer-unparented-instances.png)](media/v2-update-provision/tsi-explorer-unparented-instances.png#lightbox)

1. Selezionare la prima istanza della serie temporale. Quindi selezionare **Show temperature** (Mostra temperatura).

    [![Istanza della serie temporale selezionata con il comando di menu per visualizzare la temperatura media.](media/v2-update-provision/select-instance-and-temperature.png)](media/v2-update-provision/select-instance-and-temperature.png#lightbox)

    Viene visualizzato un grafico della serie temporale. Modificare l'**intervallo** impostandolo su **30s**.

1. Ripetere il passaggio precedente per le altre due istanze della serie temporale in modo da visualizzarle tutte e tre, come illustrato in questo grafico:

    [![Grafico relativo a tutte le serie temporali.](media/v2-update-provision/tsi-explorer-add-three-instances.png)](media/v2-update-provision/tsi-explorer-add-three-instances.png#lightbox)

1. Selezionare lo strumento di selezione dell'intervallo di tempo nell'angolo superiore destro. Qui è possibile selezionare orari di inizio e di fine specifici fino al millisecondo oppure scegliere tra le opzioni preconfigurate, ad esempio **Last 30 minutes** (Ultimi 30 minuti). È anche possibile modificare il fuso orario predefinito.

    [![Impostare l'intervallo di tempo sugli ultimi 30 minuti.](media/v2-update-provision/tsi-explorer-thirty-minute-time-range.png)](media/v2-update-provision/tsi-explorer-thirty-minute-time-range.png#lightbox)

    In Azure Time Series Insights Gen2 Explorer viene ora visualizzato lo stato di avanzamento dell'acceleratore di soluzione negli **ultimi 30 minuti**.

## <a name="define-and-apply-a-model"></a>Definire e applicare un modello

In questa sezione si applica un modello per strutturare i dati. Per completare il modello, vengono definiti i tipi, le gerarchie e le istanze. Per altre informazioni sulla modellazione dei dati, vedere [Modello di serie temporale](./concepts-model-overview.md).

1. In Explorer selezionare la scheda **Modello**:

   [![Visualizzare la scheda Modello in Explorer.](media/v2-update-provision/tsi-select-model-view.png)](media/v2-update-provision/tsi-select-model-view.png#lightbox)

   Nella scheda **Tipi** selezionare **+ Aggiungi**.

1. Immettere i parametri seguenti:

    | Parametro | Azione |
    | --- | ---|
    | **Nome** | Immettere **Elevator** (Ascensore) |
    | **Descrizione** | Immettere **This is a type definition for Elevator** (Definizione del tipo per Ascensore) |

1. Selezionare quindi la scheda **Variabili**.

    1. Selezionare **+ Aggiungi variabile** e inserire i valori seguenti per la prima variabile del tipo Elevator. Verranno create in totale tre variabili.

        | Parametro | Azione |
        | --- | --- |
        | **Nome** | Immettere **Avg Temperature** (Temperatura media). |
        | **Tipologia** | Selezionare **Numerico** |
        | **Valore** | Seleziona da set di impostazioni: Selezionare **temperatura (doppia)** . <br /> Nota: Il popolamento automatico del campo **Valore** potrebbe richiedere alcuni minuti dopo l'inizio della ricezione di eventi in Azure Time Series Insights Gen2.|
        | **Operazione di aggregazione** | Espandere **Opzioni avanzate**. <br /> Selezionare **AVG** (MEDIA). |

    1. Selezionare **Applica**. Selezionare di nuovo **+ Aggiungi variabile** e impostare i valori seguenti:

        | Parametro | Azione |
        | --- | --- |
        | **Nome** | Immettere **Avg Vibration** (Vibrazione media). |
        | **Tipologia** | Selezionare **Numerico** |
        | **Valore** | Seleziona da set di impostazioni: selezionare **vibration (Double)** . <br /> Nota: Il popolamento automatico del campo **Valore** potrebbe richiedere alcuni minuti dopo l'inizio della ricezione di eventi in Azure Time Series Insights Gen2.|
        | **Operazione di aggregazione** | Espandere **Opzioni avanzate**. <br /> Selezionare **AVG** (MEDIA). |

    1. Selezionare **Applica**. Selezionare di nuovo **+ Aggiungi variabile** e impostare i valori seguenti per la terza e ultima variabile:

        | Parametro | Azione |
        | --- | --- |
        | **Nome** | Immettere **Floor** (Piano). |
        | **Tipologia** | Selezionare **Categorie** |
        | **Valore** | Seleziona da set di impostazioni: selezionare **Floor (Double)** . <br /> Nota: Il popolamento automatico del campo **Valore** potrebbe richiedere alcuni minuti dopo l'inizio della ricezione di eventi in Azure Time Series Insights Gen2.|
        | **Categories** (Categorie) | <span style="text-decoration: underline">Etichetta </span>  - <span style="text-decoration: underline">Valori</span> <br /> Lower (Inferiori): 1,2,3,4 <br /> Middle (Medi): 5,6,7,8,9 <br /> Upper (Superiori): 10,11,12,13,14,15 |
        | **Categoria predefinita** | Immettere **Unknown** (Sconosciuta) |

        [![Aggiungere variabili di tipo.](media/v2-update-provision/tsi-add-type-variables.png)](media/v2-update-provision/tsi-add-type-variables.png#lightbox)

    1. Selezionare **Applica**. 
    1. Selezionare **Salva**. Vengono create e visualizzate tre variabili.

        [![Dopo aver aggiunto il tipo, esaminarlo nella visualizzazione Modello.](media/v2-update-provision/tsi-add-type-and-view.png)](media/v2-update-provision/tsi-add-type-and-view.png#lightbox)

1. Selezionare la scheda **Gerarchie**. Quindi selezionare **+ Aggiungi**.

   1. Nel riquadro **Edit Hierarchy** (Modifica gerarchia) impostare i parametri seguenti:

        | Parametro | Azione |
        | --- | ---|
        | **Nome** | Immettere **Location Hierarchy** (Gerarchia località). |
        |**Livelli**| Immettere **Country** (Paese) come nome del primo livello <br /> Selezionare **+ Aggiungi livello** <br /> Immettere **City** (Città) per il secondo livello e quindi selezionare **+ Aggiungi livello** <br /> Immettere **Building** (Edificio) come nome del terzo e ultimo livello |

   1. Selezionare **Salva**.

        [![Visualizzare la nuova gerarchia nella visualizzazione modello.](media/v2-update-provision/tsi-add-hierarchy-and-view.png)](media/v2-update-provision/tsi-add-hierarchy-and-view.png#lightbox)

1. Passare a **Istanze**.

    1. In **Azioni** nella parte destra selezionare l'icona della matita per modificare la prima istanza con i valori seguenti:

        | Parametro | Azione |
        | --- | --- |
        | **Tipo** | Selezionare **Elevator** (Ascensore). |
        | **Nome** | Immettere **Elevator 1** (Ascensore 1)|
        | **Descrizione** | Immettere **Instance for Elevator 1** (Istanza per Ascensore 1) |

    1. Passare a **Campi di istanza** e immettere i valori seguenti:

        | Parametro | Azione |
        | --- | --- |
        | **Gerarchie** | Selezionare **Location Hierarchy** (Gerarchia località) |
        | **Paese** | Immettere **USA** (Stati Uniti) |
        | **Città** | Immettere **Seattle** |
        | **Edificio** | Immettere **Space Needle** |

    1. Selezionare **Salva**.

1. Ripetere il passaggio precedente con le altre due istanze usando i valori seguenti:

    **Per Elevator 2** (Ascensore 2):

    | Parametro | Azione |
    | --- | --- |
    | **Tipo** | Selezionare **Elevator** (Ascensore). |
    | **Nome** | Immettere **Elevator 2** (Ascensore 2)|
    | **Descrizione** | Immettere **Instance for Elevator 2** (Istanza per Ascensore 2) |
    | **Gerarchie** | Selezionare **Location Hierarchy** (Gerarchia località) |
    | **Paese** | Immettere **USA** (Stati Uniti) |
    | **Città** | Immettere **Seattle** |
    | **Edificio** | Immettere **Pacific Science Center** |

    **Per Elevator 3** (Ascensore 3):

    | Parametro | Azione |
    | --- | --- |
    | **Tipo** | Selezionare **Elevator** (Ascensore). |
    | **Nome** | Immettere **Elevator 3** (Ascensore 3)|
    | **Descrizione** | Immettere **Instance for Elevator 3** (Istanza per Ascensore 3) |
    | **Gerarchie** | Selezionare **Location Hierarchy** (Gerarchia località) |
    | **Paese** | Immettere **USA** (Stati Uniti) |
    | **Città** | Immettere **New York** |
    | **Edificio** | Immettere **Empire State Building** |

    [![Visualizzare le istanze aggiornate.](media/v2-update-provision/iot-solution-accelerator-instances.png)](media/v2-update-provision/iot-solution-accelerator-instances.png#lightbox)

1. Tornare alla scheda **Analizza** per visualizzare il riquadro dei grafici. In **Location Hierarchy** (Gerarchia località) espandere tutti i livelli della gerarchia per visualizzare le istanze della serie temporale:

    [![Visualizzare tutte le gerarchie nella visualizzazione grafico.](media/v2-update-provision/iot-solution-accelerator-view-hierarchies.png)](media/v2-update-provision/iot-solution-accelerator-view-hierarchies.png#lightbox)

1. In **Pacific Science Center** selezionare l'istanza della serie temporale **Elevator 2** (Ascensore 2) e quindi **Show Average Temperature** (Mostra temperatura media).

1. Per la stessa istanza **Elevator 2** (Ascensore 2) selezionare **Show Floor** (Mostra piano).

    Con la variabile categoriale è possibile determinare il tempo trascorso dall'ascensore nei piani superiori, inferiori e medi.

    [![Visualizzare l'ascensore 2 con la gerarchia e i dati.](media/v2-update-provision/iot-solution-accelerator-elevator-two.png)](media/v2-update-provision/iot-solution-accelerator-elevator-two.png#lightbox)

## <a name="clean-up-resources"></a>Pulire le risorse

Ora che è stata completata l'esercitazione, pulire le risorse create:

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Tutte le risorse** nel menu a sinistra e individuare il gruppo di risorse di Azure Time Series Insights Gen2.
1. Per eliminare l'intero gruppo di risorse e tutte le risorse in esso contenute, selezionare **Elimina** oppure rimuovere ogni risorsa singolarmente.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:  

* Creare e usare un acceleratore di simulazione dispositivi.
* Creare un ambiente Azure Time Series Insights Gen2 con pagamento in base al consumo.
* Connettere l'ambiente Azure Time Series Insights Gen2 a un hub IoT.
* Eseguire un esempio di acceleratore di soluzione per trasmettere dati nell'ambiente Azure Time Series Insights Gen2.
* Eseguire un'analisi di base sui dati.
* Definire un tipo di modello di serie temporale e una gerarchia da associare alle istanze.

Ora che si è appreso come creare un ambiente Azure Time Series Insights Gen2, si procederà ad apprendere altre informazioni sui concetti chiave di Azure Time Series Insights Gen2.

Per informazioni sull'inserimento dati in Azure Time Series Insights Gen2, vedere:

> [!div class="nextstepaction"]
> [Panoramica dell'inserimento dati in Azure Time Series Insights Gen2](./concepts-ingestion-overview.md)

Per informazioni sull'archiviazione in Azure Time Series Insights Gen2, vedere:

> [!div class="nextstepaction"]
> [Archiviazione dei dati di Azure Time Series Insights Gen2](./concepts-storage.md)

Leggere altre informazioni sui modelli di serie temporali:

> [!div class="nextstepaction"]
> [Modellazione dei dati in Azure Time Series Insights Gen2](./concepts-model-overview.md)

Per altre informazioni su come connettere l'ambiente a Power BI, vedere:

> [!div class="nextstepaction"]
> [Visualizzare i dati da Azure Time Series Insights Gen2 in Power BI](./how-to-connect-power-bi.md)
