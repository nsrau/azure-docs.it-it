---
title: Aggiungere un dispositivo Azure IoT Edge in Azure IoT Central | Microsoft Docs
description: Un operatore può aggiungere un dispositivo Azure IoT Edge nell'applicazione Azure IoT Central
author: rangv
ms.author: rangv
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: c60cf4b90b089d271c0ccd91031420efe9017b1e
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758169"
---
# <a name="tutorial-add-an-azure-iot-edge-device-to-your-azure-iot-central-application"></a>Esercitazione: Aggiungere un dispositivo Azure IoT Edge nell'applicazione Azure IoT Central

*Le informazioni di questo articolo sono destinate a generatori di soluzioni e sviluppatori di dispositivi.*

Questa esercitazione illustra come configurare e aggiungere un dispositivo Azure IoT Edge nell'applicazione Azure IoT Central. L'esercitazione usa una macchina virtuale (VM) Linux abilitata per IoT Edge di Azure Marketplace per simulare un dispositivo IoT Edge. Il dispositivo IoT Edge usa un modulo che genera dati di telemetria ambientali simulati. I dati di telemetria vengono visualizzati in un dashboard nell'applicazione IoT Central.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare un modello di dispositivo per un dispositivo IoT Edge
> * Creare un dispositivo IoT Edge in IoT Central
> * Distribuire un dispositivo IoT Edge simulato in una VM Linux

## <a name="prerequisites"></a>Prerequisiti

Completare la guida di avvio rapido [Creare un'applicazione Azure IoT Central](./quick-deploy-iot-central.md) per creare un'applicazione IoT Central usando il modello **App personalizzata > Applicazione personalizzata**.

Per completare i passaggi dell'esercitazione, è necessaria una sottoscrizione di Azure attiva.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

Scaricare il file manifesto IoT Edge da GitHub. Fare clic con il pulsante destro del mouse sul collegamento seguente e scegliere **Salva link con nome**: [EnvironmentalSensorManifest.json](https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/iotedge/EnvironmentalSensorManifest.json)

## <a name="create-device-template"></a>Creare un modello di dispositivo

In questa sezione si creerà un modello di dispositivo per un dispositivo IoT Edge che si connette all'applicazione IoT Central. Si importerà prima di tutto un manifesto IoT Edge e quindi si modificherà il modello per aggiungere le definizioni e le visualizzazioni di telemetria:

### <a name="import-manifest-to-create-template"></a>Importare il manifesto per creare il modello

Per creare un modello di dispositivo da un manifesto IoT Edge:

1. Nell'applicazione IoT Central passare a **Modelli di dispositivo** e selezionare **+ Nuovo**.

1. Nella pagina **Selezionare il tipo di modello** selezionare il riquadro **Azure IoT Edge**. Selezionare quindi **Next: Personalizza**.

1. Nella pagina **Carica un manifesto della distribuzione di Azure IoT Edge** selezionare **Sfoglia** per caricare il file **EnvironmentalSensorManifest.json** precedentemente scaricato. Selezionare quindi **Next: Review** (Avanti: Rivedi).

1. Nella pagina **Rivedi** selezionare **Crea**.

1. Una volta creato il modello, cambiarne il nome in *Dispositivo perimetrale sensore ambientale*.

1. Selezionare l'interfaccia **Gestisci** nel modulo **SimulatedTemperatureSensor** per visualizzare le due proprietà definite nel manifesto:

![Modello di dispositivo creato dal manifesto IoT Edge](./media/tutorial-add-edge-as-leaf-device/imported-manifest.png)

### <a name="add-telemetry-to-manifest"></a>Aggiungere i dati di telemetria al manifesto

Un manifesto IoT Edge non definisce i dati di telemetria inviati da un modulo. È necessario aggiungere le definizioni di telemetria al modello di dispositivo. Il modulo **SimulatedTemperatureSensor** invia messaggi di telemetria simili al codice JSON seguente:

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

Per aggiungere le definizioni di telemetria al modello di dispositivo:

1. Selezionare l'interfaccia **Gestisci** nel modello **Dispositivo perimetrale sensore ambientale**.

1. Selezionare **+ Aggiungi funzionalità**. Immettere *machine* in **Nome visualizzato** e verificare che in **Tipo di funzionalità** sia selezionato **Telemetria**.

1. Selezionare **Oggetto** come tipo di schema, quindi selezionare **Definisci**. Nella pagina di definizione dell'oggetto aggiungere *temperature* e *pressure* come attributi di tipo **Double** e quindi selezionare **Applica**.

1. Selezionare **+ Aggiungi funzionalità**. Immettere *ambient* in **Nome visualizzato** e verificare che in **Tipo di funzionalità** sia selezionato **Telemetria**.

1. Selezionare **Oggetto** come tipo di schema, quindi selezionare **Definisci**. Nella pagina di definizione dell'oggetto aggiungere *temperature* e *humidity* come attributi di tipo **Double** e quindi selezionare **Applica**.

1. Selezionare **+ Aggiungi funzionalità**. Immettere *timeCreated* in **Nome visualizzato** e verificare che in **Tipo di funzionalità** sia selezionato **Telemetria**.

1. Selezionare **DateTime** come tipo di schema.

1. Selezionare **Salva** per aggiornare il modello.

L'interfaccia **Gestisci** ora include i tipi di telemetria **machine**, **ambient** e **timeCreated**:

![Interfaccia con i tipi di dati di telemetria machine e ambient](./media/tutorial-add-edge-as-leaf-device/manage-interface.png)

### <a name="add-views-to-template"></a>Aggiungere visualizzazioni al modello

Il modello di dispositivo non ha ancora una visualizzazione che consenta a un operatore di visualizzare i dati di telemetria dal dispositivo IoT Edge. Per aggiungere una visualizzazione al modello di dispositivo:

1. Selezionare **Visualizzazioni** nel modello **Dispositivo perimetrale sensore ambientale**.

1. Nella pagina **Selezionare per aggiungere una nuova visualizzazione** selezionare il riquadro **Visualizzazione del dispositivo**.

1. Cambiare il nome della visualizzazione in *Visualizza dati di telemetria dispositivo IoT Edge*.

1. Selezionare i tipi di dati di telemetria **ambient** e **machine**. Quindi selezionare **Aggiungi riquadro**.

1. Selezionare **Salva** per salvare la visualizzazione **Visualizza dati di telemetria dispositivo IoT Edge**.

![Modello di dispositivo con visualizzazione dei dati di telemetria](./media/tutorial-add-edge-as-leaf-device/template-telemetry-view.png)

### <a name="publish-the-template"></a>Pubblicare il modello

Prima di aggiungere un dispositivo che usa il modello **Dispositivo perimetrale sensore ambientale**, è necessario pubblicare il modello.

Passare al modello **Dispositivo perimetrale sensore ambientale** e selezionare **Pubblica**. Quindi selezionare **Pubblica** per pubblicare il modello:

![Pubblicare il modello di dispositivo](./media/tutorial-add-edge-as-leaf-device/publish-template.png)

## <a name="add-iot-edge-device"></a>Aggiungere il dispositivo IoT Edge

Una volta pubblicato il modello **Dispositivo perimetrale sensore ambientale**, è possibile aggiungere un dispositivo all'applicazione IoT Central:

1. Nell'applicazione IoT Central passare alla pagina **Dispositivi** e selezionare **Dispositivo perimetrale sensore ambientale** nell'elenco dei modelli disponibili.

1. Selezionare **+** per aggiungere un nuovo dispositivo dal modello. Nella pagina **Crea nuovo dispositivo** selezionare **Crea**.

Si dispone ora di un nuovo dispositivo con stato **Registrato**:

![Pubblicare il modello di dispositivo](./media/tutorial-add-edge-as-leaf-device/new-device.png)

### <a name="get-the-device-credentials"></a>Ottenere le credenziali del dispositivo

Per distribuire il dispositivo IoT Edge, più avanti in questa esercitazione, sono necessarie le credenziali che consentono al dispositivo di connettersi all'applicazione IoT Central. Per ottenere le credenziali del dispositivo:

1. Nella pagina **Dispositivo** selezionare il dispositivo appena creato.

1. Selezionare **Connetti**.

1. Nella pagina **Connessione del dispositivo** prendere nota dei valori di **Ambito ID**, **ID dispositivo** e **Chiave primaria**. Questi valori verranno usati più avanti.

1. Selezionare **Chiudi**.

È stata completata la configurazione dell'applicazione IoT Central per consentire la connessione di un dispositivo IoT Edge.

## <a name="deploy-an-iot-edge-device"></a>Distribuire un dispositivo IoT Edge

In questa esercitazione si userà una macchina virtuale Linux abilitata per Azure IoT Edge creata in Azure per simulare un dispositivo IoT Edge. Per creare la VM abilitata per IoT Edge:

1. Passare ad [Azure IoT Edge on Ubuntu](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) in Azure Marketplace. Selezionare quindi **Scarica adesso**.

1. Nella pagina **Crea questa app in Azure** selezionare **Continua**. Questo collegamento indirizza al portale di Azure, dove potrebbe essere necessario accedere alla propria sottoscrizione di Azure.

1. Nella pagina **Azure IoT Edge on Ubuntu** nel portale di Azure selezionare **Crea**.

1. Nella pagina **Crea macchina virtuale > Informazioni di base**:

    - Selezionare la sottoscrizione di Azure.
    - Creare un nuovo gruppo di risorse denominato **iot-edge-devices**.
    - Usare il nome di macchina virtuale **iotedgevm**.
    - Scegliere l'area più vicina.
    - Impostare il tipo di autenticazione su **Password**.
    - Scegliere un nome utente e una password.
    - È possibile lasciare invariati i valori predefiniti delle altre opzioni.
    - Selezionare **Rivedi e crea**.

1. Dopo il completamento della convalida, selezionare **Crea**.

Dopo alcuni minuti, una volta completata la distribuzione, selezionare **Vai alla risorsa**.

### <a name="provision-vm-as-an-iot-edge-device"></a>Effettuare il provisioning della macchina virtuale come dispositivo IoT Edge 

Per effettuare il provisioning della VM come dispositivo IoT Edge:

1. Nella sezione **Supporto e risoluzione dei problemi** selezionare **Console seriale**.

1. Premere **INVIO** per visualizzare il prompt `login:`. Immettere il proprio nome utente e la password per accedere.

1. Eseguire il comando seguente per controllare la versione del runtime IoT Edge. Al momento della redazione di questo documento, la versione è 1.0.8:

    ```bash
    sudo iotedge --version
    ```

1. Usare l'editor `nano` per aprire il file config.yaml di IoT Edge:

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. Scorrere verso il basso fino a visualizzare `# Manual provisioning configuration`. Impostare come commento le tre righe successive, come illustrato nel frammento di codice seguente:

    ```yaml
    # Manual provisioning configuration
    #provisioning:
    #  source: "manual"
    #  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
    ```

1. Scorrere verso il basso fino a visualizzare `# DPS symmetric key provisioning configuration`. Rimuovere il commento dalle tre righe successive, come illustrato nel frammento di codice seguente:

    ```yaml
    # DPS symmetric key provisioning configuration
    provisioning:
      source: "dps"
      global_endpoint: "https://global.azure-devices-provisioning.net"
      scope_id: "{scope_id}"
      attestation:
        method: "symmetric_key"
        registration_id: "{registration_id}"
        symmetric_key: "{symmetric_key}"
    ```

1. Sostituire `{scope_id}` con il valore di **Ambito ID** annotato in precedenza.

1. Sostituire `{registration_id}` con il valore di **ID dispositivo** annotato in precedenza.

1. Sostituire `{symmetric_key}` con il valore di **Chiave primaria** annotato in precedenza.

1. Salvare le modifiche (**CTRL+O**) e uscire dall'editor `nano` (**CTRL+X**).

1. Eseguire il comando seguente per riavviare il daemon di IoT Edge:

    ```bash
    sudo systemctl restart iotedge
    ```

1. Per verificare lo stato dei moduli di IoT Edge, eseguire il comando seguente:

    ```bash
    iotedge list
    ```

    L'output è simile al seguente:

    ```bash
    NAME                        STATUS           DESCRIPTION      CONFIG
    SimulatedTemperatureSensor  running          Up 20 seconds    mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0
    edgeAgent                   running          Up 27 seconds    mcr.microsoft.com/azureiotedge-agent:1.0
    edgeHub                     running          Up 22 seconds    mcr.microsoft.com/azureiotedge-hub:1.0
    ```

## <a name="view-the-telemetry"></a>Visualizzare i dati di telemetria

Il dispositivo IoT Edge simulato è ora in esecuzione nella VM. Nell'applicazione IoT Central lo stato del dispositivo è ora **Provisioning eseguito** nella pagina **Dispositivi**:

![Dispositivo con stato Provisioning eseguito](./media/tutorial-add-edge-as-leaf-device/provisioned-device.png)

È possibile visualizzare i dati di telemetria nella pagina **Visualizza dati di telemetria dispositivo IoT Edge**:

![Telemetria dei dispositivi](./media/tutorial-add-edge-as-leaf-device/device-telemetry-view.png)

La pagina **Moduli** mostra lo stato dei moduli IoT Edge:

![Telemetria dei dispositivi](./media/tutorial-add-edge-as-leaf-device/edge-module-status.png)

## <a name="next-steps"></a>Passaggi successivi

A questo punto, dopo aver appreso come gestire i dispositivi IoT Edge in IoT Central, il passaggio successivo consigliato per gli sviluppatori di dispositivi è il seguente:

<!-- Next how-tos in the sequence -->

> [!div class="nextstepaction"]
> [Connettersi ad Azure IoT Central](./concepts-get-connected.md)
