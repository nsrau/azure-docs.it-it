---
title: Creare un dashboard di valutazione dei dati sanitari con Azure IoT Central | Microsoft Docs
description: Informazioni su come creare un dashboard di valutazione dei dati sanitari usando i modelli di applicazione di Azure IoT Central.
author: philmea
ms.author: philmea
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 88b4df909e2c56f1a60e0f3c737b05ae4c27ca4a
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123252"
---
# <a name="tutorial-build-a-power-bi-provider-dashboard"></a>Esercitazione: Creare un dashboard del provider Power BI

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Quando si crea la soluzione di monitoraggio pazienti continuo, può essere utile creare anche un dashboard che consenta a un team per l'assistenza ospedaliera di visualizzare i dati dei pazienti. Questa esercitazione illustra i passaggi da eseguire per poter creare un dashboard di streaming in tempo reale Power BI dal modello di applicazione di monitoraggio pazienti continuo di IoT Central.

>[!div class="mx-imgBorder"]
>![GIF del dashboard](media/dashboard-gif-3.gif)

L'architettura di base seguirà questa struttura:

>[!div class="mx-imgBorder"] 
>![Dashboard di valutazione del provider](media/dashboard-architecture.png)

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Esportare dati da Azure IoT Central a Hub eventi di Azure
> * Configurare un set di dati di streaming Power BI
> * Connettere l'app per la logica a Hub eventi di Azure
> * Trasmettere dati a Power BI dall'app per la logica
> * Creare un dashboard in tempo reale per i segni vitali dei pazienti

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* Un modello di applicazione di monitoraggio pazienti continuo di Azure IoT Central. Se ancora non si dispone di un tale modello, è possibile seguire la procedura descritta in [Distribuire un modello di applicazione](overview-iot-central-healthcare.md).

* Uno [spazio dei nomi di Hub eventi di Azure e un hub eventi](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

* L'app per la logica che deve accedere all'hub eventi. Per avviare l'app per la logica con un trigger di Hub eventi di Azure, è necessaria un'[app per la logica vuota](https://docs.microsoft.com/azure/logic-apps/quickstart-create-first-logic-app-workflow).

* Un account del servizio Power BI. Se ancora non si dispone di un tale account, è possibile [creare un account in versione di valutazione gratuita per il servizio Power BI](https://app.powerbi.com/). Se non si è usato Power BI in precedenza, può essere utile leggere le informazioni contenute in [Introduzione a Power BI](https://docs.microsoft.com/power-bi/service-get-started).

## <a name="set-up-a-continuous-data-export-to-azure-event-hubs"></a>Configurare un'esportazione continua dei dati a Hub eventi di Azure
Sarà prima di tutto necessario configurare un'esportazione continua dei dati dal modello di app di Azure IoT Central all'hub eventi di Azure nella propria sottoscrizione. A tale scopo, è possibile seguire la procedura descritta in questa esercitazione di Azure IoT Central per l'[esportazione a Hub eventi](https://docs.microsoft.com/azure/iot-central/core/howto-export-data-pnp). Ai fini di tale esercitazione, sarà necessario eseguire l'esportazione solo per la telemetria.

## <a name="create-a-power-bi-streaming-dataset"></a>Creare un set di dati di streaming Power BI

1. Accedere al proprio account Power BI.

2. Nell'area di lavoro preferita creare un nuovo set di dati di streaming selezionando il pulsante **+ Crea** nell'angolo superiore destro della barra degli strumenti. Sarà necessario creare un set di dati separato per ogni paziente da includere nel dashboard.

    >[!div class="mx-imgBorder"] 
    >![Creare un set di dati di streaming](media/create-streaming-dataset.png)

3. Scegliere **API** come origine del set di dati.

4. Immettere un **nome** per il set di dati, ad esempio il nome di un paziente, e quindi completare i valori del flusso. Di seguito è riportato un esempio basato sui valori provenienti dai dispositivi simulati nel modello di applicazione di monitoraggio pazienti continuo. L'esempio riguarda due pazienti:

    * Teddy Silvers, con dati provenienti dal tutore intelligente per il ginocchio
    * Yesenia Sanford, con dati provenienti dalla patch intelligente per i segni vitali

    >[!div class="mx-imgBorder"] 
    >![Immettere i valori del set di dati](media/enter-dataset-values.png)

Per altre informazioni sui set di dati di streaming in Power BI, è possibile leggere questo documento sullo [streaming in tempo reale in Power BI](https://docs.microsoft.com/power-bi/service-real-time-streaming).

## <a name="connect-your-logic-app-to-azure-event-hubs"></a>Connettere l'app per la logica a Hub eventi di Azure
Per connettere l'app per la logica a Hub eventi di Azure, è possibile seguire le istruzioni riportate in questo documento sull'[invio di eventi con Hub eventi di Azure e App per la logica di Azure](https://docs.microsoft.com/azure/connectors/connectors-create-api-azure-event-hubs#add-event-hubs-action). Ecco alcuni parametri consigliati:

|Parametro|Valore|
|---|---|
|Tipo di contenuto|application/json|
|Interval|3|
|Frequenza|Second|

Alla fine di questo passaggio, la finestra di progettazione dell'app per la logica dovrebbe essere simile alla seguente:

>[!div class="mx-imgBorder"] 
>![App per la logica in fase di connessione a Hub eventi](media/eh-logic-app.png)

## <a name="stream-data-to-power-bi-from-your-logic-app"></a>Trasmettere dati a Power BI dall'app per la logica
Il passaggio successivo consiste nell'analizzare i dati provenienti dall'hub eventi per trasmetterli nei set di dati Power BI creati in precedenza.

1. Prima di poter eseguire questa operazione, sarà necessario conoscere il payload JSON che deve essere inviato dal dispositivo all'hub eventi. A tale scopo, è possibile osservare questo [schema di esempio](https://docs.microsoft.com/azure/iot-central/preview/howto-export-data#telemetry) e modificarlo in modo che corrisponda al proprio schema oppure usare [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer) per esaminare i messaggi. Se si usano le applicazioni di monitoraggio pazienti continuo, i messaggi saranno simili ai seguenti:

**Telemetria della patch intelligente per i segni vitali**

```json
{
  "HeartRate": 80,
  "RespiratoryRate": 12,
  "HeartRateVariability": 64,
  "BodyTemperature": 99.08839032397609,
  "BloodPressure": {
    "Systolic": 23,
    "Diastolic": 34
  },
  "Activity": "walking"
}
```

**Telemetria del tutore intelligente per il ginocchio**

```json
{
  "Acceleration": {
    "x": 72.73510947763711,
    "y": 72.73510947763711,
    "z": 72.73510947763711
  },
  "RangeOfMotion": 123,
  "KneeBend": 3
}
```

**Proprietà**

```json
{
  "iothub-connection-device-id": "1qsi9p8t5l2",
  "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",  \"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
  "iothub-connection-auth-generation-id": "637063718586331040",
  "iothub-enqueuedtime": 1571681440990,
  "iothub-message-source": "Telemetry",
  "iothub-interface-name": "Patient_health_data_3bk",
  "x-opt-sequence-number": 7,
  "x-opt-offset": "3672",
  "x-opt-enqueued-time": 1571681441317
}
```

2. Ora che i payload JSON sono stati esaminati, tornare alla finestra di progettazione dell'app per la logica e selezionare **+ Nuovo passaggio**. Cercare e aggiungere **Inizializza variabile** come passaggio successivo e immettere i parametri seguenti:

    |Parametro|Valore|
    |---|---|
    |NOME|Interface Name (Nome interfaccia)|
    |Type|string|

    Premere **Salva**. 

3. Aggiungere un'altra variabile denominata **Body** (Corpo) con il campo Tipo impostato su **String** (Stringa). All'app per la logica verranno aggiunte queste azioni:

    >[!div class="mx-imgBorder"]
    >![Inizializzare le variabili](media/initialize-string-variables.png)
    
4. Selezionare **+ Nuovo passaggio** e aggiungere un'azione **Parse JSON** (Analizza JSON). Rinominarla come **Parse Properties** (Analizza proprietà). Come contenuto, scegliere le **Proprietà** provenienti dall'hub eventi. Selezionare **Usare il payload di esempio per generare lo schema** nella parte inferiore e incollare il payload di esempio dalla sezione Proprietà precedente.

5. Scegliere quindi l'azione **Set variable** (Imposta variabile) e aggiornare la variabile **Interface Name** (Nome interfaccia) con il valore **iothub-interface-name** proveniente dalle proprietà JSON analizzate.

6. Aggiungere un controllo **Split** (Dividi) come azione successiva e scegliere la variabile **Interface Name** (Nome interfaccia) come parametro On (Su). Questo consentirà di incanalare i dati verso il set di dati corretto.

7. Nell'applicazione Azure IoT Central trovare il nome interfaccia per i dati sanitari relativi alla patch intelligente per i segni vitali e quelli relativi al tutore intelligente per il ginocchio dalla vista **Modelli di dispositivo**. Creare due casi diversi per il controllo **Switch** (Passa a) per ogni nome interfaccia e rinominare il controllo in modo appropriato. È possibile impostare il caso predefinito per l'uso del controllo **Termina** e scegliere lo stato da mostrare.

    >[!div class="mx-imgBorder"] 
    >![Controllo Split (Dividi)](media/split-by-interface.png)

8. Nel caso della **patch intelligente per i segni vitali**, aggiungere un'azione **Parse JSON** (Analizza JSON). Come contenuto, scegliere il **Contenuto** proveniente dall'hub eventi. Copiare e incollare i payload di esempio per la patch intelligente per i segni vitali precedente per generare lo schema.

9. Aggiungere un'azione **Set variable** (Imposta variabile) e aggiornare la variabile **Body** (Corpo) con il **Corpo** proveniente dal codice JSON analizzato nel passaggio 7.

10. Aggiungere un controllo **Condition** (Condizione) come azione successiva e impostare la condizione su **Body**, **contains**, **HeartRate**. In questo modo, si ha la garanzia di disporre del set di dati corretto, proveniente dalla patch intelligente per i segni vitali, prima di popolare il set di dati Power BI. I passaggi da 7 a 9 saranno simili ai seguenti:

    >[!div class="mx-imgBorder"] 
    >![Aggiunta di una condizione per la patch intelligente per i segni vitali](media/smart-vitals-pbi.png)

11. Per il caso **True** della condizione, aggiungere un'azione che chiami la funzionalità Power BI **Add rows to a dataset** (Aggiungi righe a un set di dati). A tale scopo, sarà necessario accedere a Power BI. Il caso **False** può di nuovo usare il controllo **Termina**.

12. Scegliere i valori appropriati per **Workspace** (Area di lavoro), **Dataset** (Set di dati) e **Table** (Tabella). Eseguire il mapping dei parametri specificati durante la creazione del set di dati di streaming in Power BI ai valori JSON analizzati provenienti dall'hub eventi. Le azioni completate dovrebbero essere simili alle seguenti:

    >[!div class="mx-imgBorder"] 
    >![Aggiungere righe a Power BI](media/add-rows-yesenia.png)

13. Per il caso Switch (Passa a) del **tutore intelligente per il ginocchio**, aggiungere un'azione **Parse JSON** (Analizza JSON) per analizzare il contenuto, in modo analogo al passaggio 7. Usare quindi **Add rows to a dataset** (Aggiungi righe a un set di dati) per aggiornare il set di dati di Teddy Silvers in Power BI.

    >[!div class="mx-imgBorder"] 
    >![Aggiunta di una condizione per la patch intelligente per i segni vitali](media/knee-brace-pbi.png)

14. Premere **Salva** e quindi eseguire l'app per la logica.

## <a name="build-a-real-time-dashboard-for-patient-vitals"></a>Creare un dashboard in tempo reale per i segni vitali dei pazienti
Ora tornare a Power BI e selezionare **+ Crea** per creare un nuovo **Dashboard**. Assegnare un nome al dashboard e premere **Crea**.

Selezionare i tre puntini nella barra di spostamento superiore e quindi selezionare **+ Aggiungi riquadro**.

>[!div class="mx-imgBorder"] 
>![Aggiungere un riquadro al dashboard](media/add-tile.png)

Scegliere il tipo di riquadro che si intende aggiungere e personalizzare l'app come desiderato.

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende continuare a usare questa applicazione, eliminare le risorse seguendo questa procedura:

1. Dal portale di Azure è possibile eliminare le risorse hub eventi e app per la logica create.

2. Per l'applicazione IoT Central, passare alla scheda Amministrazione e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

* Leggere le [indicazioni relative all'architettura del monitoraggio pazienti continuo](concept-continuous-patient-monitoring-architecture.md).
