---
title: Definire un nuovo tipo di dispositivo Azure IoT Edge in Azure IoT Central | Microsoft Docs
description: Questa esercitazione illustra come creare un nuovo dispositivo Azure IoT Edge nell'applicazione Azure IoT Central. Il creatore definisce i dati di telemetria, lo stato, le proprietà e i comandi per il tipo.
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 0a293d74c9e37a6771c5bb246b74bda38db3b7c3
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406474"
---
# <a name="tutorial-define-a-new-azure-iot-edge-device-type-in-your-azure-iot-central-application-preview-features"></a>Esercitazione: Definire un nuovo tipo di dispositivo Azure IoT Edge nell'applicazione Azure IoT Central (funzionalità in anteprima)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Questa esercitazione illustra come usare un modello di dispositivo per definire un nuovo tipo di dispositivo Azure IoT Edge nell'applicazione Azure IoT Central. 

Per una panoramica, vedere [Informazioni su Azure IoT Central (funzionalità di anteprima)](overview-iot-central.md). 

IoT Edge è costituito da tra componenti:
* I **moduli di IoT Edge** sono contenitori che eseguono servizi di Azure, servizi partner o il codice dell'utente. Vengono distribuiti nei dispositivi IoT Edge ed eseguiti in locale in tali dispositivi.
* Il **runtime IoT Edge** viene eseguito in ogni dispositivo IoT Edge e gestisce i moduli distribuiti in ogni dispositivo.
* Un'**interfaccia basata sul cloud** consente di monitorare e gestire in remoto i dispositivi IoT Edge. IoT Central è l'interfaccia cloud.

Un dispositivo **Azure IoT Edge** può essere un dispositivo gateway con dispositivi downstream che si connettono al dispositivo IoT Edge. Questa esercitazione fornisce informazioni più dettagliate sui modelli di connettività dei dispositivi downstream.

Un **modello di dispositivo** definisce le funzionalità del dispositivo e dei moduli IoT Edge. Le funzionalità includono i dati di telemetria inviati dal modulo, le proprietà del modulo e i comandi a cui un modulo risponde.

In questa esercitazione si crea un modello di dispositivo di tipo sensore ambientale. Un dispositivo di tipo sensore ambientale:

* Invia dati di telemetria, ad esempio la temperatura.
* Risponde alle proprietà scrivibili quando vengono aggiornate nel cloud, ad esempio all'intervallo di invio dei dati di telemetria.
* Risponde ai comandi, ad esempio per la reimpostazione della temperatura.

In questa esercitazione viene anche creato un modello di dispositivo di tipo gateway ambientale. Un dispositivo gateway ambientale:

* Invia dati di telemetria, ad esempio la temperatura.
* Risponde alle proprietà scrivibili quando vengono aggiornate nel cloud, ad esempio all'intervallo di invio dei dati di telemetria.
* Risponde ai comandi, ad esempio per la reimpostazione della temperatura.
* Consente le relazioni con altri modelli di funzionalità di dispositivo.


In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un nuovo modello di dispositivo Azure IoT Edge.
> * Caricare un manifesto della distribuzione.
> * Creare funzionalità, inclusi i dati di telemetria, le proprietà e i comandi per ogni modulo.
> * Definire una visualizzazione per i dati di telemetria del modulo.
> * Aggiungere le relazioni ai modelli di dispositivo downstream.
> * Pubblicare il modello di dispositivo.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario [creare un'applicazione Azure IoT Central](quick-deploy-iot-central.md).


## <a name="downstream-device-relationships-with-a-gateway-and-modules"></a>Relazioni dei dispositivi downstream con il gateway e i moduli

I dispositivi downstream possono connettersi al dispositivo gateway IoT Edge tramite il modulo `$edgeHub`. Questo dispositivo IoT Edge diventa un gateway trasparente in questo scenario.

![Diagramma del gateway trasparente](./media/tutorial-define-edge-device-type/gateway-transparent.png)

I dispositivi downstream possono anche connettersi al dispositivo gateway IoT Edge tramite un modulo personalizzato. Nello scenario seguente i dispositivi downstream si connettono tramite un modulo personalizzato Modbus.

![Diagramma della connessione del modulo personalizzato](./media/tutorial-define-edge-device-type/gateway-module.png)

Il diagramma seguente mostra la connessione a un dispositivo gateway IoT Edge tramite entrambi i tipi di moduli (personalizzato e `$edgeHub`).  

![Diagramma della connessione tramite entrambi i moduli di connessione](./media/tutorial-define-edge-device-type/gateway-module-transparent.png)

Infine, i dispositivi downstream possono connettersi al dispositivo gateway IoT Edge tramite più moduli personalizzati. Il diagramma seguente mostra i dispositivi downstream che si connettono tramite un modulo personalizzato Modbus, un modulo personalizzato BLE (Bluetooth a basso consumo) e il modulo `$edgeHub`. 

![Diagramma della connessione tramite più moduli personalizzati](./media/tutorial-define-edge-device-type/gateway-module2-transparent.png)


## <a name="create-a-template"></a>Creare un modello

Gli sviluppatori possono creare e modificare modelli di dispositivo IoT Edge nell'applicazione. Dopo la pubblicazione di un modello di dispositivo, è possibile connettere dispositivi reali che implementano il modello di dispositivo.

### <a name="select-device-template-type"></a>Selezionare il tipo di modello di dispositivo 

Per aggiungere un nuovo modello di dispositivo all'applicazione, passare alla pagina **Modelli di dispositivo** nel riquadro sinistro.

![Screenshot dell'applicazione in anteprima con i modelli di dispositivo evidenziati](./media/tutorial-define-edge-device-type/edgedevicetemplate.png)

Fare clic su **+ Nuovo** per iniziare a creare un nuovo modello di dispositivo.

![Screenshot della pagina Modelli di dispositivo, con la voce Nuovo evidenziata](./media/tutorial-define-edge-device-type/edgedevicetemplatenew.png)

Nella pagina **Selezionare il tipo di modello** selezionare **Azure IoT Edge** e quindi fare clic su **Avanti: Personalizza**.

![Screenshot della pagina Selezionare il tipo di modello](./media/tutorial-define-edge-device-type/selectiotedge.png)

### <a name="customize-device-template"></a>Personalizzare il modello di dispositivo

IoT Edge consente di distribuire e gestire la logica di business sotto forma di moduli. I moduli di IoT Edge sono l'unità di calcolo più piccola gestita da IoT Edge e possono contenere i servizi di Azure, ad esempio Analisi di flusso di Azure, o il codice specifico della soluzione. Per comprendere come vengono sviluppati, distribuiti e gestiti i moduli, vedere [Moduli di IoT Edge](../../iot-edge/iot-edge-modules.md).

A livello generale, un manifesto della distribuzione è un elenco di moduli gemelli configurati con le relative proprietà desiderate. Un manifesto della distribuzione indica a un dispositivo IoT Edge (o a un gruppo di dispositivi) quali moduli installare e come configurarli. I manifesti della distribuzione includono le proprietà desiderate per ogni modulo gemello. I dispositivi IoT Edge riportano le proprietà segnalate per ogni modulo.

Usare Visual Studio Code per creare un manifesto della distribuzione. Per altre informazioni, vedere [Azure IoT Edge per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).

Di seguito è riportato un esempio di manifesto della distribuzione di base con un modulo di esempio da usare per questa esercitazione. Copiare il codice JSON seguente e salvarlo come file con estensione json. 

   ```JSON
   {
     "modulesContent": {
       "$edgeAgent": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "runtime": {
             "type": "docker",
             "settings": {
               "minDockerVersion": "v1.25",
               "loggingOptions": "",
               "registryCredentials": {}
             }
           },
           "systemModules": {
             "edgeAgent": {
               "type": "docker",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                 "createOptions": "{}"
               }
             },
             "edgeHub": {
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                 "createOptions": "{}"
               }
             }
           },
           "modules": {
             "SimulatedTemperatureSensor": {
               "version": "1.0",
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
                 "createOptions": "{}"
               }
             }
           }
         }
       },
       "$edgeHub": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "routes": {
               "route": "FROM /* INTO $upstream"
           },
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       },
       "SimulatedTemperatureSensor": {
         "properties.desired": {
              "SendData": true,
              "SendInterval": 10
         }
       }
     }
   }
   ```

#### <a name="upload-an-iot-edge-deployment-manifest"></a>Caricare un manifesto della distribuzione di IoT Edge

Nella pagina **Personalizza dispositivo** selezionare **Sfoglia** in **Carica un manifesto della distribuzione di Azure IoT Edge**. 

![Screenshot della pagina Personalizza dispositivo con la voce Sfoglia evidenziata](./media/tutorial-define-edge-device-type/edgedevicetemplateuploadmanifest.png)

Se si intende creare un modello di dispositivo gateway IoT Edge, ricordarsi di selezionare **Dispositivo gateway con dispositivi downstream**.

![Screenshot della pagina Personalizza dispositivo con la voce Dispositivo gateway con dispositivi downstream evidenziata](./media/tutorial-define-edge-device-type/gateway-upload.png)

Nella finestra di dialogo di selezione file selezionare il file manifesto della distribuzione e fare clic su **Apri**.

IoT Edge convalida il file manifesto della distribuzione a fronte di uno schema. Se la convalida ha esito positivo, selezionare **Rivedi**.

![Screenshot della pagina Personalizza dispositivo con le voci Manifesto della distribuzione e Rivedi evidenziate](./media/tutorial-define-edge-device-type/deploymentmanifestvalidate.png)

Il diagramma di flusso seguente mostra il ciclo di vita del manifesto della distribuzione in IoT Central.

![Diagramma di flusso del ciclo di vita del manifesto della distribuzione](./media/tutorial-define-edge-device-type/dmflow.png)

Verrà quindi visualizzata la pagina per la revisione con i dettagli del manifesto della distribuzione. Questa pagina mostra un elenco di moduli del manifesto della distribuzione. In questa esercitazione osservare che è elencato il modulo `SimulatedTemperatureSensor`. Selezionare **Create** (Crea).

![Screenshot della pagina Rivedi con le voci Modulo e Crea evidenziate](./media/tutorial-define-edge-device-type/edgedevicetemplatereview.png)

Se è stato selezionato un dispositivo gateway, verrà visualizzata la pagina di revisione seguente.

![Screenshot della pagina Rivedi con la voce Gateway Azure IoT Edge evidenziata](./media/tutorial-define-edge-device-type/gateway-review.png)


Il modello di dispositivo viene creato con modelli di funzionalità del modulo. In questa esercitazione verrà creato un modello di dispositivo con il modello di funzionalità del modulo `SimulatedTemperatureSensor`. 

Sostituire il titolo del modello di dispositivo con **Environment Sensor Device Template**.

![Screenshot del modello di dispositivo con il titolo aggiornato evidenziato](./media/tutorial-define-edge-device-type/edgedevicetemplatelanding.png)

Nel dispositivo IoT Edge creare il modello Plug and Play IoT come segue:
* Ogni modello di dispositivo IoT Edge ha un modello di funzionalità di dispositivo.
* Per ogni modulo personalizzato elencato nel manifesto della distribuzione viene generato un modello di funzionalità del modulo.
* Viene stabilita una relazione tra ogni modello di funzionalità del modulo e un modello di funzionalità di dispositivo.
* Il modello di funzionalità del modulo implementa le interfacce del modulo.
* Ogni interfaccia del modulo contiene i dati di telemetria, le proprietà e i comandi.

![Diagramma della creazione del modello IoT Edge](./media/tutorial-define-edge-device-type/edgemodelling.png)

#### <a name="add-capabilities-to-a-module-capability-model"></a>Aggiungere funzionalità al modello di funzionalità del modulo

Di seguito è riportato un output di esempio dal modulo `SimulatedTemperatureSensor`:
```json
{

    "machine": {

        "temperature": 75.0,
        "pressure": 40.2
    },
    "ambient": {
        "temperature": 23.0,
        "humidity": 30.0
    },
    "timeCreated": ""
}
```

È possibile aggiungere funzionalità al modulo `SimulatedTemperatureSensor`, che rifletteranno l'output precedente. 

1. Per gestire un'interfaccia del modello di funzionalità del modulo `SimulatedTemperatureSensor`, selezionare **Gestisci** > **Aggiungi funzionalità**. 

    ![Screenshot del modello di sensore ambientale con la voce Aggiungi funzionalità evidenziata](./media/tutorial-define-edge-device-type/edgetemplateaddcapability.png)
  
1. Aggiungere un dispositivo machine come tipo di oggetto.
  
    ![Screenshot della pagina delle funzionalità del modello di sensore ambientale con la voce Schema evidenziata](./media/tutorial-define-edge-device-type/edgetemplatemachineobject.png)

1. Selezionare **Definisci**. Nella finestra di dialogo visualizzata sostituire il nome dell'oggetto con **machine**. Creare le proprietà di temperatura e pressione e selezionare **Applica**.
  
    ![Screenshot della finestra di dialogo Attributi con varie opzioni evidenziate](./media/tutorial-define-edge-device-type/edgetemplatemachineattributes.png)
  
1. Aggiungere **ambient** come tipo di oggetto.

1. Selezionare **Definisci**. Nella finestra di dialogo visualizzata sostituire il nome dell'oggetto con **ambient**. Creare le proprietà di temperatura e umidità e selezionare **Applica**.
  
    ![Screenshot della finestra di dialogo Attributi con varie opzioni evidenziate](./media/tutorial-define-edge-device-type/edgetemplateambientattributes.png)

  
1. Aggiungere `timeCreated` come tipo di `DateTime` e selezionare **Salva**.
  
    ![Screenshot del modello di sensore ambientale con l'opzione Salva evidenziata](./media/tutorial-define-edge-device-type/edgetemplateallattributes.png)


### <a name="add-relationships"></a>Aggiungere relazioni

Se il dispositivo IoT Edge è stato selezionato per fungere da dispositivo gateway, è possibile aggiungere relazioni downstream ai modelli di funzionalità di dispositivo per i dispositivi da connettere al dispositivo gateway.
  
  ![Screenshot del modello di gateway ambientale, con la voce Aggiungi relazione evidenziata](./media/tutorial-define-edge-device-type/gateway-add-relationship.png)

La relazione può essere aggiunta a livello di dispositivo o di modulo.
  
  ![Screenshot del modello di gateway ambientale, con le relazioni a livello di dispositivo e di modulo evidenziate](./media/tutorial-define-edge-device-type/gateway-relationship-types.png)


È possibile selezionare un modello di funzionalità di dispositivo downstream o selezionare l'asterisco. 
  
  ![Screenshot del modello di gateway ambientale, con la voce Destinazione evidenziata](./media/tutorial-define-edge-device-type/gateway-downstream-rel.png)

  Per questa esercitazione, selezionare l'asterisco. Questa opzione consente qualsiasi relazione downstream. Selezionare quindi **Salva**.

  ![Screenshot del modello di gateway ambientale, con la voce Destinazione evidenziata](./media/tutorial-define-edge-device-type/gateway-add-relationship-asterix.png)


### <a name="add-cloud-properties"></a>Aggiungere proprietà cloud

Un modello di dispositivo può includere proprietà cloud. Le proprietà cloud esistono solo nell'applicazione IoT Central e non vengono mai inviate a o ricevute da un dispositivo.

1. Selezionare **Proprietà cloud** >  **+ Aggiungi proprietà cloud**. Usare le informazioni nella tabella seguente per aggiungere proprietà cloud al modello di dispositivo.

    | Nome visualizzato      | Tipo semantico | SCHEMA |
    | ----------------- | ------------- | ------ |
    | Data ultimo utilizzo | Nessuna          | Data   |
    | Nome del cliente     | Nessuna          | string |

2. Selezionare **Salva**.

  
    ![Screenshot del modello di sensore ambientale con l'opzione Salva evidenziata](./media/tutorial-define-edge-device-type/edgetemplatecloudproperties.png)

### <a name="add-customizations"></a>Aggiungere personalizzazioni

Usare le personalizzazioni per modificare un'interfaccia o aggiungere funzionalità specifiche di IoT Central a una funzionalità che non richiede la creazione di una nuova versione del modello di funzionalità di dispositivo. È possibile personalizzare i campi quando il modello di funzionalità è in uno stato di bozza o pubblicato. È possibile personalizzare solo i campi che non compromettono la compatibilità dell'interfaccia. Ad esempio, è possibile:

- Personalizzare il nome visualizzato e le unità di una funzionalità.
- Aggiungere un colore predefinito da usare quando il valore viene visualizzato in un grafico.
- Specificare i valori iniziale, minimo e massimo per una proprietà.

Non è possibile personalizzare il tipo o il nome della funzionalità.

Al termine della personalizzazione, fare clic su **Salva**.
  
![Screenshot della pagina di personalizzazione del modello di sensore ambientale](./media/tutorial-define-edge-device-type/edgetemplatecustomize.png)


### <a name="create-views"></a>Creare viste

Il creatore può personalizzare un'applicazione in modo da visualizzare le informazioni sul dispositivo sensore ambientale utili all'operatore. Le personalizzazioni consentono all'operatore di gestire i sensori ambientali connessi all'applicazione. È possibile creare due tipi di visualizzazioni che un operatore può usare per interagire con i dispositivi:

* Moduli per visualizzare e modificare le proprietà del dispositivo e cloud.
* Dashboard per visualizzare i dispositivi.

### <a name="configure-a-view-to-visualize-devices"></a>Configurare una visualizzazione per visualizzare i dispositivi

Un dashboard del dispositivo consente a un operatore di visualizzare un dispositivo usando grafici e metriche. Gli sviluppatori possono definire le informazioni visualizzate nel dashboard di un dispositivo. È possibile definire più dashboard per i dispositivi. Per creare un dashboard per visualizzare i dati di telemetria del sensore ambientale, selezionare **Visualizzazioni** > **Visualizzazione del dispositivo**:

  
![Screenshot della pagina delle visualizzazioni dei modelli del sensore ambientale, con la voce Visualizzazione del dispositivo evidenziata](./media/tutorial-define-edge-device-type/visualizingthedevice.png)


La telemetria ambient e la telemetria machine sono oggetti complessi. Per creare i grafici:

1. Trascinare **Ambient Telemetry** (Telemetria Ambient) e selezionare il **Grafico a linee**. 
  
   ![Screenshot del modello di sensore ambientale, con le voci Ambient Telemetry e Grafico a linee evidenziate](./media/tutorial-define-edge-device-type/sensorambientchart.png)

1. Selezionare l'icona Configura. Fare clic sull'icona Configura e selezionare **temperatura** e **umidità** per visualizzare i dati, quindi fare clic su **Aggiorna configurazione**. 
  
   ![Screenshot del modello di sensore ambientale con varie opzioni evidenziate](./media/tutorial-define-edge-device-type/sensorambienttelemetrychart.png)

1. Selezionare **Salva**.

È possibile aggiungere altri riquadri che mostrano proprietà o valori di telemetria diversi. È anche possibile aggiungere testo statico, collegamenti e immagini. Per spostare o ridimensionare un riquadro nel dashboard, spostare il puntatore del mouse sul riquadro e trascinare il riquadro in una nuova posizione o ridimensionarlo.
  
![Screenshot della visualizzazione del dashboard del modello di sensore ambientale](./media/tutorial-define-edge-device-type/viewsdashboard.png)

### <a name="add-a-device-form"></a>Aggiungere un modulo di dispositivo

Un modulo di dispositivo consente a un operatore di modificare le proprietà del dispositivo scrivibili e le proprietà cloud. Il creatore può definire più moduli e scegliere le proprietà del dispositivo e cloud da visualizzare in ogni modulo. È anche possibile visualizzare le proprietà del dispositivo di sola lettura in un modulo.

Per creare un modulo per la visualizzazione e la modifica delle proprietà del sensore ambientale:

1. Passare a **Visualizzazioni** in **Environmental Sensor Template**. Selezionare il riquadro **Modifica dei dati del dispositivo e del cloud** per aggiungere una nuova vista.
  
   ![Screenshot della pagina delle visualizzazioni dei modelli di sensori ambientali con la voce Modifica dei dati del dispositivo e del cloud evidenziata](./media/tutorial-define-edge-device-type/editingdeviceandclouddata.png)

1. Immettere il nome di modulo **Proprietà sensore ambientale**.

1. Trascinare le proprietà cloud **Nome cliente** e **Data ultima assistenza** nella sezione esistente del modulo.
  
   ![Screenshot della pagina delle visualizzazioni del modello di sensore ambientale con varie opzioni evidenziate](./media/tutorial-define-edge-device-type/views-properties.png)

1. Selezionare **Salva**.

## <a name="publish-a-device-template"></a>Pubblicare un modello di dispositivo

Prima di poter creare un sensore ambientale simulato o di connettere un sensore ambientale reale, è necessario pubblicare il modello di dispositivo.

Per pubblicare un modello di dispositivo:

1. Passare al modello di dispositivo dalla pagina **Modelli di dispositivo**.

2. Selezionare **Pubblica**.
  
    ![Screenshot del modello di sensore ambientale con l'opzione Pubblica evidenziata](./media/tutorial-define-edge-device-type/edgetemplatepublish.png)

1. Nella finestra di dialogo **Publish a Device Template** (Pubblica un modello di dispositivo) scegliere **Pubblica**.
  
    ![Screenshot della finestra di dialogo Pubblica modello di dispositivo con l'opzione Pubblica evidenziata](./media/tutorial-define-edge-device-type/edgepublishtemplate.png)

Dopo la pubblicazione, un modello di dispositivo viene visualizzato nella pagina **Dispositivi** ed è visibile all'operatore. In un modello di dispositivo pubblicato non è possibile modificare un modello di funzionalità di dispositivo senza creare una nuova versione. È tuttavia possibile apportare aggiornamenti a proprietà cloud, personalizzazioni e visualizzazioni in un modello di dispositivo pubblicato. Questi aggiornamenti non comportano la creazione di una nuova versione. Dopo aver apportato le modifiche, selezionare **Pubblica** per eseguire il push delle modifiche all'operatore.
  
![Screenshot dell'elenco dei modelli di dispositivo pubblicati](./media/tutorial-define-edge-device-type/publishedtemplate.png)

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

* Creare un nuovo dispositivo perimetrale come modello di dispositivo foglia.
* Generare i moduli da un manifesto della distribuzione caricato.
* Aggiungere dati di telemetria di tipo complesso e le proprietà.
* Creare proprietà cloud.
* Creare personalizzazioni.
* Definire una visualizzazione per i dati di telemetria del dispositivo.
* Pubblicare il modello di dispositivo perimetrale.

Dopo aver creato un modello di dispositivo nell'applicazione Azure IoT Central, è possibile:

> [!div class="nextstepaction"]
> [Connettere il dispositivo](./tutorial-connect-pnp-device.md)
