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
ms.openlocfilehash: c205b4dd4871ed53e32dce72f12cc2dcfb3baf41
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73892027"
---
# <a name="tutorial-define-a-new-azure-iot-edge-device-type-in-your-azure-iot-central-application-preview-features"></a>Esercitazione: Definire un nuovo tipo di dispositivo Azure IoT Edge nell'applicazione Azure IoT Central (funzionalità in anteprima)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Questa esercitazione illustra come usare un modello di dispositivo per definire un nuovo tipo di dispositivo Azure IoT Edge nell'applicazione Azure IoT Central. 

Per una panoramica di Azure IoT Edge, [vedere questo articolo](overview-iot-central.md). 

Azure IoT Edge è costituito da tra componenti:
* I **moduli di IoT Edge** sono contenitori che eseguono servizi di Azure, servizi di terze parti o il codice dell'utente. Vengono distribuiti nei dispositivi IoT Edge ed eseguiti in locale su tali dispositivi.
* Il **runtime di IoT Edge** viene eseguito su ogni dispositivo IoT Edge e gestisce i moduli distribuiti in ogni dispositivo.
* Un'**interfaccia basata sul cloud** consente di monitorare e gestire in remoto i dispositivi IoT Edge. IoT Central sarà l'interfaccia cloud.

Un dispositivo **Azure IoT Edge** può essere un dispositivo gateway con dispositivi downstream che si connettono al dispositivo Azure IoT Edge. In questa esercitazione verranno illustrati i modelli di connettività dei dispositivi downstream.

Un **modello di dispositivo** definisce le funzionalità del dispositivo e dei moduli IoT Edge. Le funzionalità includono i dati di telemetria inviati dal modulo, le proprietà del modulo e i comandi a cui un modulo risponde.

In questa esercitazione si crea un modello di dispositivo di tipo **sensore ambientale**. Un dispositivo di tipo sensore ambientale:

* Invia dati di telemetria, ad esempio la temperatura.
* Risponde alle proprietà scrivibili quando vengono aggiornate nel cloud, ad esempio all'intervallo di invio dei dati di telemetria.
* Risponde ai comandi, ad esempio per la reimpostazione della temperatura.

In questa esercitazione viene anche creato un modello di dispositivo di tipo **gateway ambientale**. Un dispositivo gateway ambientale:

* Invia dati di telemetria, ad esempio la temperatura.
* Risponde alle proprietà scrivibili quando vengono aggiornate nel cloud, ad esempio all'intervallo di invio dei dati di telemetria.
* Risponde ai comandi, ad esempio per la reimpostazione della temperatura.
* Consente le relazioni con altri modelli di funzionalità di dispositivo


In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un nuovo modello di dispositivo Azure IoT Edge.
> * Caricare un manifesto della distribuzione.
> * Creare funzionalità, inclusi i dati di telemetria, le proprietà e i comandi per ogni modulo
> * Definire una visualizzazione per i dati di telemetria del modulo.
> * Aggiungere le relazioni ai modelli di dispositivo downstream
> * Pubblicare il modello di dispositivo.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessaria un'applicazione Azure IoT Central. Seguire i passaggi di questa guida di avvio rapido per la [creazione di un'applicazione Azure IoT Central](quick-deploy-iot-central.md).


## <a name="downstream-device-relationships-with-gateway--modules"></a>Relazioni dei dispositivi downstream con il gateway e i moduli

I dispositivi downstream possono connettersi al dispositivo gateway Azure IoT Edge tramite il modulo $edgeHub. Tale dispositivo Azure IoT Edge diventa un gateway trasparente in questo scenario

![Pagina dell'applicazione IoT Central](./media/tutorial-define-edge-device-type/gateway-transparent.png)

I dispositivi downstream possono connettersi al dispositivo gateway Azure IoT Edge tramite un modulo personalizzato. Nello scenario seguente i dispositivi downstream si connettono tramite un modulo personalizzato Modbus e possono connettersi al dispositivo gateway Azure IoT Edge tramite il modulo $edgeHub.

![Pagina dell'applicazione IoT Central](./media/tutorial-define-edge-device-type/gateway-module.png)

I dispositivi downstream possono connettersi al dispositivo gateway Azure IoT Edge tramite un modulo personalizzato. Nello scenario seguente i dispositivi downstream si connettono tramite un modulo personalizzato Modbus. 

![Pagina dell'applicazione IoT Central](./media/tutorial-define-edge-device-type/gateway-module-transparent.png)

I dispositivi downstream possono connettersi al dispositivo gateway Azure IoT Edge tramite più moduli personalizzati. Nello scenario seguente i dispositivi downstream si connettono tramite un modulo personalizzato Modbus, il modulo personalizzato Bluetooth a basso consumo e possono connettersi al dispositivo gateway Azure IoT Edge tramite il modulo $edgeHub. 

![Pagina dell'applicazione IoT Central](./media/tutorial-define-edge-device-type/gateway-module2-transparent.png)


## <a name="create-a-template"></a>Creare un modello

Gli sviluppatori possono creare e modificare modelli di dispositivo Azure IoT Edge nell'applicazione. Dopo la pubblicazione di un modello di dispositivo, è possibile connettere dispositivi reali che implementano il modello di dispositivo.

### <a name="select-device-template-type"></a>Selezionare il tipo di modello di dispositivo 

Per aggiungere un nuovo modello di dispositivo all'applicazione, passare alla pagina **Modelli di dispositivo**. A tale scopo, selezionare la scheda **Modelli di dispositivo** nel riquadro sinistro.

![Pagina dell'applicazione IoT Central](./media/tutorial-define-edge-device-type/edgedevicetemplate.png)

Fare clic su **+ Nuovo** per iniziare a creare un nuovo modello di dispositivo.

![Modelli di dispositivo - Nuovo](./media/tutorial-define-edge-device-type/edgedevicetemplatenew.png)

Verrà visualizzata la pagina di selezione del tipo di modello di dispositivo. Selezionare il riquadro **Azure IoT Edge** e fare clic sul pulsante **Avanti: Personalizza** in basso

![Selezione dei modelli di dispositivo - Azure IoT Edge](./media/tutorial-define-edge-device-type/selectiotedge.png)

### <a name="customize-device-template"></a>Personalizzare il modello di dispositivo

Azure IoT Edge consente di distribuire e gestire la logica di business sotto forma di moduli. I **moduli di Azure IoT Edge** sono l'unità di calcolo più piccola gestita da IoT Edge e possono contenere i servizi di Azure, ad esempio Analisi di flusso di Azure, o il codice specifico della soluzione. Per comprendere come vengono sviluppati, distribuiti e gestiti i moduli, vedere [Moduli di IoT Edge](../../iot-edge/iot-edge-modules.md).

A livello generale, un manifesto della distribuzione è un elenco di moduli gemelli configurati con le relative proprietà desiderate. Un manifesto della distribuzione indica a un dispositivo IoT Edge (o a un gruppo di dispositivi) quali moduli installare e come configurarli. I manifesti della distribuzione includono le proprietà desiderate per ogni modulo gemello. I dispositivi IoT Edge riportano le proprietà segnalate per ogni modulo.

Usare Visual Studio Code per creare un manifesto della distribuzione. Seguire la documentazione su come creare un [manifesto della distribuzione](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).

Di seguito è riportato un esempio di manifesto della distribuzione di base con un modulo da usare per questa esercitazione. Copiare il codice JSON seguente e salvarlo come file con estensione json. 

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

**Caricare un manifesto della distribuzione di Azure IoT Edge**

Fare clic sul pulsante **Sfoglia** 

![Modello di dispositivo - Azure IoT Edge](./media/tutorial-define-edge-device-type/edgedevicetemplateuploadmanifest.png)

Se si intende creare un modello di dispositivo gateway Azure IoT Edge, ricordarsi di selezionare la casella di controllo **Dispositivo gateway con dispositivi downstream**

![Modello di dispositivo - Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-upload.png)

Verrà visualizzata una finestra di dialogo per la selezione del file. Selezionare il file manifesto della distribuzione e fare clic sul pulsante **Apri**.

Il file manifesto della distribuzione verrà convalidato a fronte di uno schema. Se la convalida ha esito positivo, fare clic sul pulsante **Rivedi**

![Modello di dispositivo - Azure IoT Edge](./media/tutorial-define-edge-device-type/deploymentmanifestvalidate.png)

Di seguito è riportato il flusso del ciclo di vita del manifesto della distribuzione in IoT Central.

![Modello di dispositivo - Azure IoT Edge](./media/tutorial-define-edge-device-type/dmflow.png)

Viene visualizzata la pagina per la revisione con i dettagli del manifesto della distribuzione. In tale pagina verrà mostrato l'elenco dei moduli del manifesto della distribuzione. In questa esercitazione sarà elencato il modulo SimulatedTemperatureSensor. Fare clic sul pulsante **Crea**.

![Modello di dispositivo - Azure IoT Edge](./media/tutorial-define-edge-device-type/edgedevicetemplatereview.png)

Se è stato selezionato il dispositivo gateway, verrà visualizzata questa pagina di revisione

![Modello di dispositivo - Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-review.png)


Verrà visualizzata la rotellina per indicare che è in corso la creazione di un nuovo modello di dispositivo in IoT Central.

Il modello di dispositivo viene creato con modelli di funzionalità di modulo. In questa esercitazione verrà visualizzata la creazione del modello di funzionalità di modulo SimulatedTemperatureSensor. 

Modificare il titolo del modello di dispositivo specificando Environment Sensor Device Template.

![Modello di dispositivo - Azure IoT Edge](./media/tutorial-define-edge-device-type/edgedevicetemplatelanding.png)

La creazione di modelli Plug and Play del dispositivo Azure IoT Edge avviene come descritto di seguito
* Ogni modello di dispositivo Azure IoT Edge avrà un **modello di funzionalità di dispositivo**
* Per ogni modulo personalizzato elencato nel manifesto della distribuzione verrà generato un **modello di funzionalità di modulo**
* Verrà stabilita una **relazione** tra ogni modello di funzionalità di modulo e un modello di funzionalità di dispositivo
* Il modello di funzionalità di modulo implementa le **interfacce modulo**
* Ogni interfaccia modulo include
   - Telemetria
   - Properties
   - Comandi:

![Modello di dispositivo - Azure IoT Edge](./media/tutorial-define-edge-device-type/edgemodelling.png)

**Aggiungere funzionalità al modello di funzionalità di modulo**

Di seguito è riportato un output di esempio del modulo SimulatedTemperatureSensor
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

Aggiungere al modulo SimulatedTemperatureSensor funzionalità che riflettano il codice JSON precedente. 

* Fare clic su **Gestisci** per gestire un'interfaccia del modello di funzionalità di modulo SimulatedTemperatureSensor. Fare clic su **Aggiungi funzionalità**. 

    ![Modello di dispositivo - Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplateaddcapability.png)
  
* Aggiungere machine come tipo di oggetto perché si tratta di un tipo complesso
  
    ![Modello di dispositivo - Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplatemachineobject.png)

    Fare clic su **Definisci**. Nella finestra di dialogo modale popup modificare il nome dell'oggetto specificando machine e creare le proprietà per temperatura e pressione, quindi fare clic su **Applica**
  
    ![Modello di dispositivo - Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplatemachineattributes.png)
  
* Aggiungere ambient come tipo di oggetto perché si tratta di un tipo complesso

    Fare clic su **Definisci**. Nella finestra di dialogo modale popup modificare il nome dell'oggetto specificando ambient e creare le proprietà per temperatura e umidità, quindi fare clic su **Applica**
  
    ![Modello di dispositivo - Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplateambientattributes.png)

  
* Aggiungere timeCreated come tipo DateTime e fare clic su **Salva**
  
    ![Modello di dispositivo - Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplateallattributes.png)


### <a name="add-relationships"></a>Aggiungere relazioni

Se il dispositivo Azure IoT Edge è stato selezionato per fungere da dispositivo gateway, è possibile aggiungere relazioni downstream ai modelli di funzionalità di dispositivo per i dispositivi da connettere al dispositivo gateway.
  
  ![Modello di dispositivo - Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-add-relationship.png)

La relazione può essere aggiunta a livello di dispositivo o di modulo.
  
  ![Modello di dispositivo - Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-relationship-types.png)


È possibile selezionare un modello di funzionalità di dispositivo downstream o selezionare l'asterisco. 
  
  ![Modello di dispositivo - Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-downstream-rel.png)

  Per questa esercitazione verrà selezionato l'asterisco; ciò significa che sarà consentita qualsiasi relazione downstream. Fare clic su **Save** (Salva).

  ![Modello di dispositivo - Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-add-relationship-asterix.png)


### <a name="add-cloud-properties"></a>Aggiungere proprietà cloud

Un modello di dispositivo può includere proprietà cloud. Le proprietà cloud esistono solo nell'applicazione IoT Central e non vengono mai inviate a o ricevute da un dispositivo.

1. Selezionare **Proprietà cloud** e quindi **+ Aggiungi proprietà cloud**. Usare le informazioni nella tabella seguente per aggiungere proprietà cloud al modello di dispositivo.

    | Nome visualizzato      | Tipo semantico | SCHEMA |
    | ----------------- | ------------- | ------ |
    | Data ultimo utilizzo | Nessuna          | Data   |
    | Nome del cliente     | Nessuna          | string |

2. Fare clic su **Salva** per salvare le modifiche:

  
    ![Proprietà cloud - Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplatecloudproperties.png)

### <a name="add-customizations"></a>Aggiungere personalizzazioni

Usare le personalizzazioni quando è necessario modificare un'interfaccia o aggiungere funzionalità specifiche di IoT Central a una funzionalità che non richiede la creazione di una nuova versione del modello di funzionalità di dispositivo. È possibile personalizzare i campi quando il modello di funzionalità è in uno stato di bozza o pubblicato. È possibile personalizzare solo i campi che non compromettono la compatibilità dell'interfaccia. Ad esempio, è possibile:

- Personalizzare il nome visualizzato e le unità di una funzionalità.
- Aggiungere un colore predefinito da usare quando il valore viene visualizzato in un grafico.
- Specificare i valori iniziale, minimo e massimo per una proprietà.

Non è possibile personalizzare il tipo o il nome della funzionalità. Fare clic su **Save** (Salva).
  
![Personalizzazioni - Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplatecustomize.png)


### <a name="create-views"></a>Creare viste

Il creatore può personalizzare un'applicazione in modo da visualizzare le informazioni sul dispositivo sensore ambientale utili all'operatore. Le personalizzazioni consentono all'operatore di gestire i sensori ambientali connessi all'applicazione. È possibile creare due tipi di visualizzazioni che un operatore può usare per interagire con i dispositivi:

* Moduli per visualizzare e modificare le proprietà del dispositivo e cloud.
* Dashboard per visualizzare i dispositivi.

### <a name="configure-a-view-to-visualize-devices"></a>Configurare una visualizzazione per visualizzare i dispositivi

Un dashboard del dispositivo consente a un operatore di visualizzare un dispositivo usando grafici e metriche. I creatori possono definire le informazioni visualizzate sul dashboard di un dispositivo. È possibile definire più dashboard per i dispositivi. Per creare un dashboard per visualizzare i dati di telemetria del sensore ambientale, selezionare **Visualizzazioni** e quindi **Visualizzazione del dispositivo**:

  
![Visualizzazioni - Azure IoT Edge](./media/tutorial-define-edge-device-type/visualizingthedevice.png)


La telemetria ambient e la telemetria machine sono oggetti complessi. Per creare i grafici, procedere come segue

Trascinare la telemetria ambient e selezionare il grafico a linee. 
  
![Visualizzazioni - Azure IoT Edge](./media/tutorial-define-edge-device-type/sensorambientchart.png)

Fare clic sull'icona Configura e selezionare temperatura e umidità per visualizzare i dati, quindi fare clic sul pulsante **Aggiorna configurazione**. 
  
![Visualizzazioni - Azure IoT Edge](./media/tutorial-define-edge-device-type/sensorambienttelemetrychart.png)

Fare clic su **Salva** per salvare la visualizzazione:

È possibile aggiungere altri riquadri che mostrano proprietà o valori di telemetria diversi. È anche possibile aggiungere testo statico, collegamenti e immagini. Per spostare o ridimensionare un riquadro nel dashboard, spostare il puntatore del mouse sul riquadro e trascinare il riquadro in una nuova posizione o ridimensionarlo.
  
![Visualizzazioni - Azure IoT Edge](./media/tutorial-define-edge-device-type/viewsdashboard.png)

### <a name="add-a-device-form"></a>Aggiungere un modulo di dispositivo

Un modulo di dispositivo consente a un operatore di modificare le proprietà del dispositivo scrivibili e le proprietà cloud. Il creatore può definire più moduli e scegliere le proprietà del dispositivo e cloud da visualizzare in ogni modulo. È anche possibile visualizzare le proprietà del dispositivo di sola lettura in un modulo.

Per creare un modulo per la visualizzazione e la modifica delle proprietà del sensore ambientale:

Passare a **Viste** nel modello **Sensore ambientale**. Selezionare il riquadro **Modifica dei dati del dispositivo e del cloud** per aggiungere una nuova vista.
  
![Visualizzazioni - Azure IoT Edge](./media/tutorial-define-edge-device-type/editingdeviceandclouddata.png)

Immettere il nome di modulo **Proprietà sensore ambientale**.

Trascinare le proprietà cloud **Nome cliente** e **Data ultima assistenza** nella sezione esistente del modulo.
  
![Visualizzazioni - Azure IoT Edge](./media/tutorial-define-edge-device-type/views-properties.png)

Fare clic su **Salva** per salvare la visualizzazione.

### <a name="generate-default-views"></a>Generare le visualizzazioni predefinite

La funzionalità Genera visualizzazioni predefinite non è supportata per i modelli di Azure IoT Edge 

## <a name="publish-device-template"></a>Pubblicare il modello di dispositivo

Prima di poter creare un sensore ambientale simulato o di connettere un sensore ambientale reale, è necessario pubblicare il modello di dispositivo.

Per pubblicare un modello di dispositivo:

1. Passare al modello di dispositivo dalla pagina **Modelli di dispositivo**.

2. Selezionare **Pubblica**.
  
    ![Visualizzazioni - Pubblica](./media/tutorial-define-edge-device-type/edgetemplatepublish.png)

1. Nella finestra di dialogo **Publish a Device Template** (Pubblica un modello di dispositivo) scegliere **Pubblica**:
  
    ![Visualizzazioni - Pubblica](./media/tutorial-define-edge-device-type/edgepublishtemplate.png)

Dopo la pubblicazione, un modello di dispositivo viene visualizzato nella pagina **Dispositivi** ed è visibile all'operatore. In un modello di dispositivo pubblicato non è possibile modificare un modello di funzionalità di dispositivo senza creare una nuova versione. È tuttavia possibile apportare aggiornamenti a proprietà cloud, personalizzazioni e visualizzazioni in un modello di dispositivo pubblicato senza creare una nuova versione. Dopo aver apportato le modifiche, selezionare **Pubblica** per eseguire il push delle modifiche all'operatore.
  
![Visualizzazioni - Pubblica](./media/tutorial-define-edge-device-type/publishedtemplate.png)

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

* Creare un nuovo dispositivo perimetrale come modello di dispositivo foglia
* Generare i moduli da un manifesto della distribuzione caricato
* Aggiungere telemetria di tipo complesso e le proprietà
* Creare proprietà cloud.
* Creare personalizzazioni.
* Definire una visualizzazione per i dati di telemetria del dispositivo.
* Pubblicare il modello di dispositivo perimetrale.

Ora che è stato creato un modello di dispositivo nell'applicazione Azure IoT Central, il passaggio successivo consigliato è il seguente:

> [!div class="nextstepaction"]
> [Connettere il dispositivo](./tutorial-connect-pnp-device.md)
