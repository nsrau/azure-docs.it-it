---
title: 'Esercitazione: Esportare i dati e visualizzare le informazioni dettagliate in Azure IoT Central'
description: Questa esercitazione illustra come esportare i dati da IoT Central e visualizzare le informazioni dettagliate in un dashboard Power BI.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.custom:
- iot-storeAnalytics-checkout
- iot-p0-scenario
ms.author: dobett
author: dominicbetts
ms.date: 11/12/2019
ms.openlocfilehash: b85dd8d899a7e5d7d9f9d41ad7e2872249ee29c5
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74702008"
---
# <a name="tutorial-export-data-from-azure-iot-central-and-visualize-insights-in-power-bi"></a>Esercitazione: Esportare i dati da Azure IoT Central e visualizzare le informazioni dettagliate in Power BI

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Nelle due esercitazioni precedenti è stata creata e personalizzata un'applicazione IoT Central usando il modello di applicazione **Analisi punti vendita - Pagamento**. In questa esercitazione si configurerà l'applicazione IoT Central per esportare i dati di telemetria raccolti dai dispositivi. Quindi con Power BI si creerà un dashboard personalizzato per la gestione del negozio per visualizzare le informazioni dettagliate derivate dai dati di telemetria.

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> * Configurare un'applicazione IoT Central per esportare i dati di telemetria in un hub eventi.
> * Usare le app per la logica per inviare dati da un hub eventi a un set di dati di streaming Power BI.
> * Creare un dashboard Power BI per visualizzare i dati nel set di dati di streaming.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, sono necessari:

* Per completare le due esercitazioni precedenti, [Creare un'applicazione per la vendita al dettaglio in Azure IoT Central](./tutorial-in-store-analytics-create-app-pnp.md) e [Personalizzare il dashboard dell'operatore e gestire i dispositivi in Azure IoT Central](./tutorial-in-store-analytics-customize-dashboard-pnp.md).
* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
* Un account di Power BI. Se non si dispone di un account di Power BI, registrarsi per ottenere una [versione di valutazione gratuita Power BI Pro](https://app.powerbi.com/signupredirect?pbi_source=web) prima di iniziare.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Prima di creare l'hub eventi e l'app per la logica, è necessario creare un gruppo di risorse per gestirli. Il gruppo di risorse deve trovarsi nella stessa posizione dell'applicazione **Analisi punti vendita - Pagamento** di IoT Central. Per creare un gruppo di risorse:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Nel riquadro di spostamento a sinistra selezionare **Gruppi di risorse**. Quindi selezionare **Aggiungi**.
1. Per **Sottoscrizione** selezionare il nome della sottoscrizione di Azure usata per creare l'applicazione IoT Central.
1. Per il nome **Gruppo di risorse**  immettere _retail-store-analysis_*.
1. Per **Regione** selezionare la stessa regione scelta per l'applicazione IoT Central.
1. Selezionare **Rivedi e crea**.
1. Nel riquadro **Rivedi e crea** selezionare **Crea**.

A questo punto è disponibile un gruppo di risorse denominato **retail-store-analysis** nella sottoscrizione.

## <a name="create-an-event-hub"></a>Creare un hub eventi

Prima di poter configurare l'applicazione per il monitoraggio delle vendite al dettaglio per esportare i dati di telemetria, è necessario creare un hub eventi per ricevere i dati esportati. I passaggi seguenti mostrano come creare un hub eventi:

1. Nel portale di Azure selezionare **Crea una risorsa** nella parte superiore sinistra della schermata.
1. In **Cerca nel marketplace**, immettere _Hub eventi_, quindi premere **Invio**.
1. Nella pagina **Hub eventi** selezionare **Crea**.
1. Nella pagina **Crea uno spazio dei nomi** seguire questa procedura:
    * Immettere un nome univoco per lo spazio dei nomi, ad esempio _yourname-retail-store-analysis_. Verrà eseguito un controllo sulla disponibilità del nome.
    * Scegliere il piano tariffario **Basic**.
    * Selezionare la stessa **Sottoscrizione** usata per creare l'applicazione IoT Central.
    * Selezionare il gruppo di risorse **retail-store-analysis**.
    * Selezionare la stessa posizione usata per l'applicazione IoT Central.
    * Selezionare **Create** (Crea). Per il provisioning delle risorse da parte del sistema, potrebbero essere necessari alcuni minuti.
1. Nel portale passare al gruppo di risorse **retail-store-analysis**. Attendere il completamento della distribuzione. Potrebbe essere necessario selezionare **Aggiorna** per aggiornare lo stato della distribuzione. È anche possibile controllare lo stato della creazione dello spazio dei nomi dell'hub eventi alla voce **Notifiche**.
1. Nel gruppo di risorse **retail-store-analysis** selezionare **Spazio dei nomi Hub eventi**. Nel portale verrà visualizzata la home page per lo **Spazio dei nomi Hub eventi**.

A questo punto si dispone di uno **Spazio dei nomi Hub eventi** ed è possibile creare un **Hub eventi** da usare con l'applicazione IoT Central:

1. Sulla home page dello **Spazio dei nomi Hub eventi** nel portale, selezionare **+ Hub eventi**.
1. Nella pagina **Crea hub eventi**, immettere _store-telemetry_ come nome e quindi selezionare **Crea**.

È ora disponibile un hub eventi che è possibile usare quando si configura l'esportazione dei dati dall'applicazione IoT Central:

![Hub eventi](./media/tutorial-in-store-analytics-visualize-insights-pnp/event-hub.png)

## <a name="configure-data-export"></a>Configurare l'esportazione dati

A questo punto si dispone di un hub eventi ed è possibile configurare l'applicazione **Analisi punti vendita - Pagamento** per esportare i dati di telemetria dai dispositivi connessi. I passaggi seguenti spiegano come configurare l'esportazione:

1. Accedere all'applicazione **Analisi punti vendita - Pagamento** di IoT Central.
1. Selezionare **Data export** (Esportazione dati) nel riquadro a sinistra.
1. Selezionare **Nuovo > Hub eventi di Azure**.
1. Immettere _Telemetry export_ (Esporta dati di telemetria) come **Nome visualizzato**.
1. Selezionare **Spazio dei nomi degli hub eventi**.
1. Selezionare l'hub eventi **store-telemetry**.
1. Disattivare **Dispositivi** e **Modelli di dispositivo** nella sezione **Dati da esportare**.
1. Selezionare **Salva**.

L'esportazione dei dati potrebbe richiedere alcuni minuti prima di iniziare a inviare dati di telemetria all'hub eventi. È possibile visualizzare lo stato dell'esportazione alla pagina **Data exports** (Esportazioni dati):

![Configurazione dell'esportazione continua dei dati](./media/tutorial-in-store-analytics-visualize-insights-pnp/export-configuration.png)

## <a name="create-the-power-bi-datasets"></a>Creare i set di dati Power BI

Nel dashboard Power BI vengono visualizzati i dati dell'applicazione di monitoraggio delle vendite al dettaglio. In questa soluzione si usano i set di dati di streaming Power BI come origine dati per il dashboard di Power BI. In questa sezione si definisce lo schema dei set di dati di streaming in modo che l'app per la logica possa inviare i dati dall'hub eventi. I passaggi seguenti illustrano come creare due set di dati di streaming per i sensori ambientali e un set di dati di streaming per il sensore di occupazione:

1. Accedere all'account **Power BI**.
1. Selezionare **Aree di lavoro**e quindi **Crea un'area di lavoro**.
1. Nella pagina **Crea un'area di lavoro**, immettere _Analisi punti vendita - Pagamento_ come **Nome area di lavoro**.
1. Scorrere fino alla fine della pagina **Benvenuti nell'area di lavoro Analisi punti vendita - Pagamento** e selezionare **Ignora**.
1. Nella pagina dell'area di lavoro selezionare **Crea > Set di dati di streaming**.
1. Nella pagina **Nuovo set di dati di streaming** scegliere **API** e quindi selezionare **Avanti**.
1. Immettere _Sensore Zona 1_ come **Nome set di dati**.
1. Immettere i tre **Valori dal flusso** nella tabella seguente:

    | Nome del valore  | Tipo di valore |
    | ----------- | ---------- |
    | Timestamp   | Datetime   |
    | Umidità    | Number     |
    | Temperatura | Number     |

1. Attivare **Analisi dati cronologici**.
1. Selezionare **Crea** e quindi **Chiudi**.
1. Creare un altro set di dati di streaming denominato **Sensore Zona 2** con lo stesso schema e le stesse impostazioni del set di dati di streaming **Sensore Zona 1**.

Si dispone ora di due set di dati di streaming. L'app per la logica instraderà i dati di telemetria dai due sensori ambientali connessi all'applicazione **Analisi punti vendita - Pagamento** a questi due set di dati:

![Set di dati di zona](./media/tutorial-in-store-analytics-visualize-insights-pnp/dataset-1.png)

Questa soluzione usa un set di dati di streaming per ogni sensore dato che non è possibile applicare filtri ai dati di streaming in Power BI.

È anche necessario un set di dati di streaming per i dati di telemetria dell'occupazione:

1. Nella pagina dell'area di lavoro selezionare **Crea > Set di dati di streaming**.
1. Nella pagina **Nuovo set di dati di streaming** scegliere **API** e quindi selezionare **Avanti**.
1. Immettere _Sensore di occupazione_ come **Nome set di dati**.
1. Immettere i cinque **Valori dal flusso** nella tabella seguente:

    | Nome del valore     | Tipo di valore |
    | -------------- | ---------- |
    | Timestamp      | Datetime   |
    | Lunghezza coda 1 | Number     |
    | Lunghezza coda 2 | Number     |
    | Tempo di attesa 1   | Number     |
    | Tempo di attesa 2   | Number     |

1. Attivare **Analisi dati cronologici**.
1. Selezionare **Crea** e quindi **Chiudi**.

È ora disponibile un terzo set di dati di streaming che archivia i valori dal sensore di occupazione simulato. Questo sensore segnala la lunghezza della coda alle due transazioni nel negozio e il tempo di attesa dei clienti in coda:

![Set di dati di occupazione](./media/tutorial-in-store-analytics-visualize-insights-pnp/dataset-2.png)

## <a name="create-a-logic-app"></a>Creare un'app per la logica

In questa soluzione l'app per la logica legge i dati di telemetria dall'hub eventi, analizza i dati e li invia ai set di dati di streaming Power BI creati.

Per poter creare l'app per la logica sono necessari gli ID dispositivo dei due sensori RuuviTag connessi all'applicazione IoT Central nell'esercitazione [Creare un'applicazione di analisi punto vendita in Azure IoT Central](./tutorial-in-store-analytics-create-app-pnp.md):

1. Accedere all'applicazione **Analisi punti vendita - Pagamento** di IoT Central.
1. Selezionare **Dispositivi** nel riquadro a sinistra. Selezionare quindi **RuuviTag**.
1. Prendere nota dei valori **ID dei dispositivi**. Nella schermata seguente gli ID sono **f5dcf4ac32e8** ed **e29ffc8d5326**:

    ![ID dei dispositivi](./media/tutorial-in-store-analytics-visualize-insights-pnp/device-ids.png)

La procedura seguente illustra come creare l'app per la logica nel portale di Azure:

1. Accedere al [portale di Azure](https://portal.azure.com) e selezionare **Crea una risorsa** nella parte superiore sinistra della schermata.
1. In **Cerca nel marketplace**, immettere _App per la logica_ e quindi premere **Invio**.
1. Nella pagina **App per la logica** selezionare **Crea**.
1. Nella pagina per creare l'**App per la logica**:
    * Immettere un nome univoco per l'app per la logica, ad esempio _yourname-retail-store-analysis_.
    * Selezionare la stessa **Sottoscrizione** usata per creare l'applicazione IoT Central.
    * Selezionare il gruppo di risorse **retail-store-analysis**.
    * Selezionare la stessa posizione usata per l'applicazione IoT Central.
    * Selezionare **Create** (Crea). Per il provisioning delle risorse da parte del sistema, potrebbero essere necessari alcuni minuti.
1. Nel portale di Azure passare alla nuova app per la logica.
1. Nella pagina **Progettazione app per la logica** scorrere verso il basso e selezionare **App per la logica vuota**.
1. In **Cerca connettori e trigger**, immettere _Hub eventi_.
1. In **Trigger** selezionare **Quando sono disponibili eventi nell'hub eventi**.
1. Immettere _Memorizza dati di telemetria_ come **Nome connessione** e selezionare **Spazio dei nomi Hub eventi**.
1. Selezionare il criterio **RootManageSharedAccess** e quindi **Crea**.
1. Nell'azione **Quando sono disponibili eventi nell'hub eventi**:
    * In **Nome Hub eventi** selezionare **store-telemetry**.
    * In **Tipo contenuto**selezionare **application/json**.
    * Impostare l'**intervallo** su tre e la **frequenza** in secondi
1. Selezionare **Salva** per salvare l'app per la logica.

Per aggiungere la logica alla progettazione dell'app per la logica, selezionare **Visualizzazione codice**:

1. Sostituire `"actions": {},` con il JSON seguente. Sostituire i due segnaposto `[YOUR RUUVITAG DEVICE ID 1]` e `[YOUR RUUVITAG DEVICE ID 2]` con gli ID annotati nei due dispositivi RuuviTag:

    ```json
    "actions": {
        "Initialize_Device_ID_variable": {
            "inputs": {
                "variables": [
                    {
                        "name": "DeviceID",
                        "type": "String"
                    }
                ]
            },
            "runAfter": {},
            "type": "InitializeVariable"
        },
        "Initialize_Interface_ID_variable": {
            "inputs": {
                "variables": [
                    {
                        "name": "InterfaceID",
                        "type": "String",
                        "value": "Other"
                    }
                ]
            },
            "runAfter": {
                "Initialize_Device_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "InitializeVariable"
        },
        "Parse_Properties": {
            "inputs": {
                "content": "@triggerBody()?['Properties']",
                "schema": {
                    "properties": {
                        "iothub-connection-auth-generation-id": {
                            "type": "string"
                        },
                        "iothub-connection-auth-method": {
                            "type": "string"
                        },
                        "iothub-connection-device-id": {
                            "type": "string"
                        },
                        "iothub-enqueuedtime": {
                            "type": "string"
                        },
                        "iothub-interface-name": {
                            "type": "string"
                        },
                        "iothub-message-source": {
                            "type": "string"
                        },
                        "x-opt-enqueued-time": {
                            "type": "string"
                        },
                        "x-opt-offset": {
                            "type": "string"
                        },
                        "x-opt-sequence-number": {
                            "type": "integer"
                        }
                    },
                    "type": "object"
                }
            },
            "runAfter": {
                "Initialize_Interface_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "ParseJson"
        },
        "Parse_Telemetry": {
            "inputs": {
                "content": "@triggerBody()?['ContentData']",
                "schema": {
                    "properties": {
                        "DwellTime1": {
                            "type": "number"
                        },
                        "DwellTime2": {
                            "type": "number"
                        },
                        "count1": {
                            "type": "number"
                        },
                        "count2": {
                            "type": "number"
                        },
                        "humidity": {
                            "type": "number"
                        },
                        "temperature": {
                            "type": "number"
                        }
                    },
                    "type": "object"
                }
            },
            "runAfter": {
                "Initialize_Interface_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "ParseJson"
        },
        "Set_Device_ID_variable": {
            "inputs": {
                "name": "DeviceID",
                "value": "@body('Parse_Properties')?['iothub-connection-device-id']"
            },
            "runAfter": {
                "Parse_Properties": [
                    "Succeeded"
                ]
            },
            "type": "SetVariable"
        },
        "Set_Interface_ID_variable": {
            "inputs": {
                "name": "InterfaceID",
                "value": "@body('Parse_Properties')?['iothub-interface-name']"
            },
            "runAfter": {
                "Set_Device_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "SetVariable"
        },
        "Switch_by_DeviceID": {
            "cases": {
                "Occupancy": {
                    "actions": {
                        "Switch_by_InterfaceID": {
                            "cases": {
                                "Dwell_Time_interface": {
                                    "actions": {},
                                    "case": "RS40_Occupancy_Sensor_v2_1l0"
                                },
                                "People_Count_interface": {
                                    "actions": {},
                                    "case": "RS40_Occupancy_Sensor_iv"
                                }
                            },
                            "default": {
                                "actions": {}
                            },
                            "expression": "@variables('InterfaceID')",
                            "runAfter": {},
                            "type": "Switch"
                        }
                    },
                    "case": "Occupancy"
                },
                "Zone 2 environment": {
                    "actions": {},
                    "case": "[YOUR RUUVITAG DEVICE ID 2]"
                },
                "Zone_1_environment": {
                    "actions": {},
                    "case": "[YOUR RUUVITAG DEVICE ID 1]"
                }
            },
            "default": {
                "actions": {}
            },
            "expression": "@variables('DeviceID')",
            "runAfter": {
                "Parse_Telemetry": [
                    "Succeeded"
                ],
                "Set_Interface_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "Switch"
        }
    },
    ```

1. Selezionare **Salva** e quindi **Finestra di progettazione** per mostrare la versione visiva della logica aggiunta:

    ![Progettazione dell'app per la logica](./media/tutorial-in-store-analytics-visualize-insights-pnp/logic-app.png)

1. Selezionare **Passa a ID dispositivo** per espandere l'azione. Selezionare quindi **Ambiente Zona 1** e **Aggiungi un'azione**.
1. In **Cerca connettori e azioni**, immettere **Power BI** e quindi premere **Invio**.
1. Selezionare l'azione **Aggiungi righe a un set di dati (anteprima)** .
1. Selezionare **Accedi** e seguire le istruzioni per accedere all'account Power BI.
1. Al termine del processo di accesso, nell'azione **Aggiungi righe a un set di dati**:
    * Selezionare **Analisi punti vendita - Pagamento** come area di lavoro.
    * Selezionare **Sensore Zona 1** come set di dati.
    * Selezionare **RealTimeData** come tabella.
    * Selezionare **Aggiungi nuovo parametro** e quindi i campi **Timestamp**, **Umidità** e **Temperatura**.
    * Selezionare il campo **Timestamp** e quindi **x-opt-enqueuedtime** dall'elenco **Contenuto dinamico**.
    * Selezionare il campo **Umidità** e quindi **Mostra dettagli** accanto ad **Analizza dati di telemetria**. Quindi selezionare **Umidità**.
    * Selezionare il campo **Temperatura** e quindi **Mostra dettagli** accanto ad **Analizza dati di telemetria**. Quindi selezionare **Temperatura**.
    * Fare clic su **Salva** per salvare le modifiche. L'azione **Ambiente Zona 1** è simile alla schermata seguente: ![Ambiente Zona 1](./media/tutorial-in-store-analytics-visualize-insights-pnp/zone-1-action.png)
1. Selezionare quindi l'azione **Ambiente Zona 2** e **Aggiungi un'azione**.
1. In **Cerca connettori e azioni**, immettere **Power BI** e quindi premere **Invio**.
1. Selezionare l'azione **Aggiungi righe a un set di dati (anteprima)** .
1. Nell'azione **Aggiungi righe a un set di dati 2**:
    * Selezionare **Analisi punti vendita - Pagamento** come area di lavoro.
    * Selezionare **Sensore Zona 2** come set di dati.
    * Selezionare **RealTimeData** come tabella.
    * Selezionare **Aggiungi nuovo parametro** e quindi i campi **Timestamp**, **Umidità** e **Temperatura**.
    * Selezionare il campo **Timestamp** e quindi **x-opt-enqueuedtime** dall'elenco **Contenuto dinamico**.
    * Selezionare il campo **Umidità** e quindi **Mostra dettagli** accanto ad **Analizza dati di telemetria**. Quindi selezionare **Umidità**.
    * Selezionare il campo **Temperatura** e quindi **Mostra dettagli** accanto ad **Analizza dati di telemetria**. Quindi selezionare **Temperatura**.
    Fare clic su **Salva** per salvare le modifiche.  L'azione **Ambiente Zona 2** è simile alla schermata seguente: ![Ambiente Zona 2](./media/tutorial-in-store-analytics-visualize-insights-pnp/zone-2-action.png)
1. Selezionare l'azione **Occupazione** e quindi **Passa a ID interfaccia**.
1. Selezionare l'**interfaccia tempo di attesa** e selezionare **Aggiungi un'azione**.
1. In **Cerca connettori e azioni**, immettere **Power BI** e quindi premere **Invio**.
1. Selezionare l'azione **Aggiungi righe a un set di dati (anteprima)** .
1. Nell'azione **Aggiungi righe a un set di dati**:
    * Selezionare **Analisi punti vendita - Pagamento** come area di lavoro.
    * Selezionare **Sensore di occupazione** come set di dati.
    * Selezionare **RealTimeData** come tabella.
    * Selezionare **Aggiungi nuovo parametro** e quindi i campi **Timestamp**, **Tempo di attesa 1** e **Tempo di attesa 2**.
    * Selezionare il campo **Timestamp** e quindi **x-opt-enqueuedtime** dall'elenco **Contenuto dinamico**.
    * Selezionare il campo **Tempo di attesa 1** e quindi **Mostra dettagli** accanto ad **Analizza dati di telemetria**. Quindi selezionare **Tempo di attesa 1**.
    * Selezionare il campo **Tempo di attesa 2** e quindi **Mostra dettagli** accanto ad **Analizza dati di telemetria**. Quindi selezionare **Tempo di attesa 2**.
    * Fare clic su **Salva** per salvare le modifiche. L'azione **Interfaccia tempo di attesa** è simile alla schermata seguente: ![Azione di occupazione](./media/tutorial-in-store-analytics-visualize-insights-pnp/occupancy-action-1.png)
1. Selezionare l'**interfaccia numero di persone** e selezionare **Aggiungi un'azione**.
1. In **Cerca connettori e azioni**, immettere **Power BI** e quindi premere **Invio**.
1. Selezionare l'azione **Aggiungi righe a un set di dati (anteprima)** .
1. Nell'azione **Aggiungi righe a un set di dati**:
    * Selezionare **Analisi punti vendita - Pagamento** come area di lavoro.
    * Selezionare **Sensore di occupazione** come set di dati.
    * Selezionare **RealTimeData** come tabella.
    * Selezionare **Aggiungi nuovo parametro** e quindi i campi **Timestamp**, **Lunghezza coda 1**e **Lunghezza coda 2**.
    * Selezionare il campo **Timestamp** e quindi **x-opt-enqueuedtime** dall'elenco **Contenuto dinamico**.
    * Selezionare il campo **Lunghezza coda 1** e quindi **Mostra dettagli** accanto ad **Analizza dati di telemetria**. Selezionare quindi **count1**.
    * Selezionare il campo **Lunghezza coda 2** e quindi **Mostra dettagli** accanto ad **Analizza dati di telemetria**. Selezionare quindi **count2**.
    * Fare clic su **Salva** per salvare le modifiche. L'azione **Interfaccia numero di persone** è simile alla schermata seguente: ![Azione di occupazione](./media/tutorial-in-store-analytics-visualize-insights-pnp/occupancy-action-2.png)

L'app per la logica viene eseguita automaticamente. Per visualizzare lo stato di ogni esecuzione, passare alla pagina **Panoramica** per l'app per la logica nel portale di Azure:

## <a name="create-a-power-bi-dashboard"></a>Creare un nuovo dashboard di Power BI

A questo punto i dati di telemetria passano dall'applicazione IoT Central tramite l'hub eventi. L'app per la logica analizza quindi i messaggi dell'hub eventi e li aggiunge a un set di dati di streaming Power BI. A questo punto, è possibile creare un dashboard Power BI per visualizzare i dati di telemetria:

1. Accedere all'account **Power BI**.
1. Selezionare **Aree di lavoro > Analisi punti vendita - Pagamento**.
1. Selezionare **Crea > Dashboard**.
1. Immettere **Analisi punti vendita** come nome del dashboard e selezionare **Crea**.

### <a name="add-line-charts"></a>Aggiungere grafici a linee

Aggiungere quattro riquadri nel grafico a linee per mostrare la temperatura e l'umidità dei due sensori ambientali. Usare le informazioni nella tabella seguente per creare i riquadri. Per aggiungere ogni riquadro, iniziare selezionando **... (Altre opzioni) > Aggiungi riquadro**. Selezionare quindi **Dati in streaming personalizzati** e **Avanti**:

| Impostazione | Grafico #1 | Grafico #2 | Grafico #3 | Grafico #4 |
| ------- | -------- | -------- | -------- | -------- |
| Set di dati | Sensore Zona 1 | Sensore Zona 1 | Sensore Zona 2 | Sensore Zona 2 |
| Tipo di visualizzazione | Grafico a linee | Grafico a linee | Grafico a linee | Grafico a linee |
| Asse | Timestamp | Timestamp | Timestamp | Timestamp |
| Valori | Temperatura | Umidità | Temperatura | Umidità |
| Intervallo di tempo | 60 minuti | 60 minuti | 60 minuti | 60 minuti |
| Title | Temperatura (1 ora) | Umidità (1 ora) | Temperatura (1 ora) | Umidità (1 ora) |
| Sottotitolo | Zona 1 | Zona 1 | Zona 2 | Zona 2 |

La schermata seguente mostra le impostazioni del primo grafico:

![Impostazioni Grafico a linee](./media/tutorial-in-store-analytics-visualize-insights-pnp/line-chart.png)

### <a name="add-cards-to-show-environmental-data"></a>Aggiungere le schede per visualizzare i dati ambientali

Aggiungere quattro riquadri alla scheda per mostrare i valori di temperatura e umidità più recenti dai due sensori ambientali. Usare le informazioni nella tabella seguente per creare i riquadri. Per aggiungere ogni riquadro, iniziare selezionando **... (Altre opzioni) > Aggiungi riquadro**. Selezionare quindi **Dati in streaming personalizzati** e **Avanti**:

| Impostazione | Scheda #1 | Scheda #2 | Scheda #3 | Scheda #4 |
| ------- | ------- | ------- | ------- | ------- |
| Set di dati | Sensore Zona 1 | Sensore Zona 1 | Sensore Zona 2 | Sensore Zona 2 |
| Tipo di visualizzazione | Card | Card | Card | Card |
| Campi | Temperatura | Umidità | Temperatura | Umidità |
| Title | Temperatura (F) | Umidità (%) | Temperatura (F) | Umidità (%) |
| Sottotitolo | Zona 1 | Zona 1 | Zona 2 | Zona 2 |

La schermata seguente mostra le impostazioni della prima scheda:

![Impostazioni della scheda](./media/tutorial-in-store-analytics-visualize-insights-pnp/card-settings.png)

### <a name="add-tiles-to-show-checkout-occupancy-data"></a>Aggiungere i riquadri per visualizzare i dati di occupazione di estrazione

Aggiungere quattro riquadri alla scheda per mostrare la lunghezza della coda e il tempo di attesa per le due estrazioni presenti nell'archivio. Usare le informazioni nella tabella seguente per creare i riquadri. Per aggiungere ogni riquadro, iniziare selezionando **... (Altre opzioni) > Aggiungi riquadro**. Selezionare quindi **Dati in streaming personalizzati** e **Avanti**:

| Impostazione | Scheda #1 | Scheda #2 | Scheda #3 | Scheda #4 |
| ------- | ------- | ------- | ------- | ------- |
| Set di dati | Sensore di occupazione | Sensore di occupazione | Sensore di occupazione | Sensore di occupazione |
| Tipo di visualizzazione | Istogramma a colonne raggruppate | Istogramma a colonne raggruppate | Misuratore | Misuratore |
| Asse    | Timestamp | Timestamp | N/D | N/D |
| Valore | Tempo di attesa 1 | Tempo di attesa 2 | Lunghezza coda 1 | Lunghezza coda 2 |
| Intervallo di tempo | 60 minuti | 60 minuti |  N/D | N/D |
| Title | Tempo di attesa | Tempo di attesa | Lunghezza coda | Lunghezza coda |
| Sottotitolo | Completamento della transazione 1 | Completamento della transazione 2 | Completamento della transazione 1 | Completamento della transazione 2 |

Ridimensionare e ridisporre i riquadri nel dashboard per ottenere un aspetto simile alla schermata seguente:

![Dashboard di Power BI](./media/tutorial-in-store-analytics-visualize-insights-pnp/pbi-dashboard.png)

È possibile aggiungere altre risorse grafiche per personalizzare ulteriormente il dashboard:

![Dashboard di Power BI](./media/tutorial-in-store-analytics-visualize-insights-pnp/pbi-dashboard-graphics.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Se si è terminato di usare l'applicazione IoT Central, è possibile eliminarla eseguendo l'accesso all'applicazione e passando alla pagina **impostazioni applicazione** nella sezione **Amministrazione**.

Se si vuole mantenere l'applicazione riducendo i costi associati, disabilitare l'esportazione dei dati che invia i dati di telemetria all'hub eventi.

È possibile eliminare l'hub eventi e l'app per la logica nel portale di Azure eliminando il gruppo di risorse denominato **retail-store-analysis**.

È possibile eliminare il dashboard e i set di dati Power BI eliminando l'area di lavoro dalla pagina Impostazioni di Microsoft Power BI per l'area di lavoro.

## <a name="next-steps"></a>Passaggi successivi

Queste tre esercitazioni hanno illustrato una soluzione end-to-end che usa il modello dell'applicazione IoT Central **Analisi punti vendita - Pagamento**. Sono stati connessi dispositivi all'applicazione, è stato usato IoT Central per monitorare i dispositivi e Power BI per creare un dashboard per visualizzare le informazioni dettagliate dai dati di telemetria del dispositivo. Un passaggio successivo consigliato consiste nell'esplorare uno degli altri modelli dell'applicazione IoT Central:

> [!div class="nextstepaction"]
> * [Creare soluzioni per il settore energetico con IoT Central](../energy/overview-iot-central-energy.md)
> * [Creare soluzioni per enti pubblici con IoT Central](../government/overview-iot-central-government.md)
> * [Creare soluzioni per il settore sanitario con IoT Central](../healthcare/overview-iot-central-healthcare.md)
