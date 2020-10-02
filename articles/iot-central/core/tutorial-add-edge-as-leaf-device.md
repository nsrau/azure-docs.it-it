---
title: 'Esercitazione: Aggiungere un dispositivo Azure IoT Edge in Azure IoT Central | Microsoft Docs'
description: "Esercitazione: Informazioni per operatori su come aggiungere un dispositivo Azure IoT Edge nell'applicazione Azure IoT Central"
author: rangv
ms.author: rangv
ms.date: 05/29/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom:
- mvc
- device-developer
- iot-edge
ms.openlocfilehash: 9b4bb462c94ab5a59dbd9d8fdd4cf619e311df56
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90987010"
---
# <a name="tutorial-add-an-azure-iot-edge-device-to-your-azure-iot-central-application"></a>Esercitazione: Aggiungere un dispositivo Azure IoT Edge nell'applicazione Azure IoT Central

*Le informazioni di questo articolo sono destinate a operatori, autori di soluzioni e sviluppatori di dispositivi.*

Questa esercitazione illustra come configurare e aggiungere un dispositivo Azure IoT Edge nell'applicazione Azure IoT Central. L'esercitazione usa una macchina virtuale (VM) Linux abilitata per IoT Edge per simulare un dispositivo IoT Edge. Il dispositivo IoT Edge usa un modulo che genera dati di telemetria ambientali simulati. I dati di telemetria vengono visualizzati in un dashboard nell'applicazione IoT Central.

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

In questa sezione si creerà un modello di dispositivo IoT Central per un dispositivo IoT Edge. Si importerà prima di tutto un manifesto IoT Edge e quindi si modificherà il modello per aggiungere le definizioni e le visualizzazioni di telemetria:

### <a name="import-manifest-to-create-template"></a>Importare il manifesto per creare il modello

Per creare un modello di dispositivo da un manifesto IoT Edge:

1. Nell'applicazione IoT Central passare a **Modelli di dispositivo** e selezionare **+ Nuovo**.

1. Nella pagina **Selezionare il tipo di modello** selezionare il riquadro **Azure IoT Edge**. Selezionare quindi **Next: Personalizza**.

1. Nella pagina **Carica un manifesto della distribuzione di Azure IoT Edge** immettere *Environmental Sensor Edge Device* come nome del dispositivo. Selezionare quindi **Sfoglia** per caricare il file **EnvironmentalSensorManifest.json** scaricato in precedenza. Selezionare quindi **Next: Review** (Avanti: Rivedi).

1. Nella pagina **Rivedi** selezionare **Crea**.

1. Selezionare l'interfaccia **Gestisci** nel modulo **SimulatedTemperatureSensor** per visualizzare le due proprietà definite nel manifesto:

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/imported-manifest.png" alt-text="Modello di dispositivo creato dal manifesto IoT Edge":::

### <a name="add-telemetry-to-manifest"></a>Aggiungere i dati di telemetria al manifesto

Un manifesto IoT Edge non definisce i dati di telemetria inviati da un modulo. È necessario aggiungere le definizioni di telemetria al modello di dispositivo in IoT Central. Il modulo **SimulatedTemperatureSensor** invia messaggi di telemetria simili al codice JSON seguente:

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

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/manage-interface.png" alt-text="Modello di dispositivo creato dal manifesto IoT Edge":::

### <a name="add-views-to-template"></a>Aggiungere visualizzazioni al modello

Il modello di dispositivo non ha ancora una visualizzazione che consenta a un operatore di visualizzare i dati di telemetria dal dispositivo IoT Edge. Per aggiungere una visualizzazione al modello di dispositivo:

1. Selezionare **Visualizzazioni** nel modello **Dispositivo perimetrale sensore ambientale**.

1. Nella pagina **Selezionare per aggiungere una nuova visualizzazione** selezionare il riquadro **Visualizzazione del dispositivo**.

1. Cambiare il nome della visualizzazione in *Visualizza dati di telemetria dispositivo IoT Edge*.

1. Selezionare i tipi di dati di telemetria **ambient** e **machine**. Quindi selezionare **Aggiungi riquadro**.

1. Selezionare **Salva** per salvare la visualizzazione **Visualizza dati di telemetria dispositivo IoT Edge**.

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/template-telemetry-view.png" alt-text="Modello di dispositivo creato dal manifesto IoT Edge":::

### <a name="publish-the-template"></a>Pubblicare il modello

Prima di aggiungere un dispositivo che usa il modello **Dispositivo perimetrale sensore ambientale**, è necessario pubblicare il modello.

Passare al modello **Dispositivo perimetrale sensore ambientale** e selezionare **Pubblica**. Nel pannello **Pubblica questo modello di dispositivo nell'applicazione** selezionare **Pubblica** per pubblicare il modello:

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/publish-template.png" alt-text="Modello di dispositivo creato dal manifesto IoT Edge":::

## <a name="add-iot-edge-device"></a>Aggiungere il dispositivo IoT Edge

Una volta pubblicato il modello **Dispositivo perimetrale sensore ambientale**, è possibile aggiungere un dispositivo all'applicazione IoT Central:

1. Nell'applicazione IoT Central passare alla pagina **Dispositivi** e selezionare **Dispositivo perimetrale sensore ambientale** nell'elenco dei modelli disponibili.

1. Selezionare **+ Nuovo** per aggiungere un nuovo dispositivo dal modello. Nella pagina **Crea nuovo dispositivo** selezionare **Crea**.

Si dispone ora di un nuovo dispositivo con stato **Registrato**:

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/new-device.png" alt-text="Modello di dispositivo creato dal manifesto IoT Edge":::

### <a name="get-the-device-credentials"></a>Ottenere le credenziali del dispositivo

Per distribuire il dispositivo IoT Edge, più avanti in questa esercitazione, sono necessarie le credenziali che consentono al dispositivo di connettersi all'applicazione IoT Central. Per ottenere le credenziali del dispositivo:

1. Nella pagina **Dispositivo** selezionare il dispositivo appena creato.

1. Selezionare **Connetti**.

1. Nella pagina **Connessione del dispositivo** prendere nota dei valori di **Ambito ID**, **ID dispositivo** e **Chiave primaria**. Questi valori verranno usati più avanti.

1. Selezionare **Chiudi**.

È stata completata la configurazione dell'applicazione IoT Central per consentire la connessione di un dispositivo IoT Edge.

## <a name="deploy-an-iot-edge-device"></a>Distribuire un dispositivo IoT Edge

In questa esercitazione si userà una macchina virtuale Linux abilitata per Azure IoT Edge creata in Azure per simulare un dispositivo IoT Edge. Per creare la macchina virtuale abilitata per IoT Edge nella sottoscrizione di Azure, fare clic su:

[![Pulsante Distribuisci in Azure per iotedge-vm-deploy](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2Fmaster%2FedgeDeploy.json)

Nella pagina **Distribuzione personalizzata**:

1. Selezionare la sottoscrizione di Azure.

1. Selezionare **Crea nuovo** per creare un nuovo gruppo di risorse denominato *central-edge-rg*.

1. Selezionare un'area nelle vicinanze.

1. Aggiungere un **prefisso etichetta DNS** univoco, ad esempio *contoso-central-edge*.

1. Scegliere un nome utente dell'amministratore per la macchina virtuale.

1. Immettere *temp* come stringa di connessione. Più avanti, si configurerà il dispositivo per la connessione tramite DPS.

1. Accettare i valori predefiniti per le dimensioni della macchina virtuale, la versione di Ubuntu e il percorso.

1. Selezionare **Password** come tipo di autenticazione.

1. Immettere una password per la macchina virtuale.

1. Selezionare quindi **Rivedi e crea**.

1. Esaminare le scelte effettuate e quindi selezionare **Crea**:

    :::image type="content" source="media/tutorial-add-edge-as-leaf-device/vm-deployment.png" alt-text="Modello di dispositivo creato dal manifesto IoT Edge":::

Il completamento della distribuzione richiede alcuni minuti. Al termine della distribuzione, passare al gruppo di risorse **central-edge-rg** nel portale di Azure.

### <a name="configure-the-iot-edge-vm"></a>Configurare la macchina virtuale IoT Edge

Per configurare IoT Edge nella macchina virtuale per usare DPS per la registrazione e la connessione all'applicazione IoT Central:

1. Nel gruppo di risorse **contoso-edge-rg** selezionare l'istanza della macchina virtuale.

1. Nella sezione **Supporto e risoluzione dei problemi** selezionare **Console seriale**. Se viene richiesto di configurare la diagnostica di avvio, seguire le istruzioni visualizzate nel portale.

1. Premere **INVIO** per visualizzare il prompt `login:`. Immettere il proprio nome utente e la password per accedere.

1. Eseguire il comando seguente per controllare la versione del runtime IoT Edge. Al momento della redazione di questo documento, la versione è 1.0.9.1:

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
    #  device_connection_string: "temp"
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

    > [!TIP]
    > Assicurarsi che prima di `provisioning:` non siano presenti spazi

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

    L'output di esempio seguente mostra i moduli in esecuzione:

    ```bash
    NAME                        STATUS           DESCRIPTION      CONFIG
    SimulatedTemperatureSensor  running          Up 20 seconds    mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0
    edgeAgent                   running          Up 27 seconds    mcr.microsoft.com/azureiotedge-agent:1.0
    edgeHub                     running          Up 22 seconds    mcr.microsoft.com/azureiotedge-hub:1.0
    ```

    > [!TIP]
    > Potrebbe essere necessario attendere l'avvio dell'esecuzione di tutti i moduli.

## <a name="view-the-telemetry"></a>Visualizzare i dati di telemetria

Il dispositivo IoT Edge simulato è ora in esecuzione nella VM. Nell'applicazione IoT Central lo stato del dispositivo è ora **Provisioning eseguito** nella pagina **Dispositivi**:

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/provisioned-device.png" alt-text="Modello di dispositivo creato dal manifesto IoT Edge":::

È possibile visualizzare i dati di telemetria dal dispositivo nella pagina **Visualizza dati di telemetria dispositivo IoT Edge**:

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/device-telemetry-view.png" alt-text="Modello di dispositivo creato dal manifesto IoT Edge":::

La pagina **Moduli** mostra lo stato dei moduli IoT Edge nel dispositivo:

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/edge-module-status.png" alt-text="Modello di dispositivo creato dal manifesto IoT Edge":::

## <a name="clean-up-resources"></a>Pulire le risorse

Se si intende continuare a usare la macchina virtuale IoT Edge, è possibile mantenere e riutilizzare le risorse usate in questa esercitazione. In caso contrario, è possibile eliminare le risorse create per questa esercitazione per evitare addebiti aggiuntivi:

* Per eliminare la macchina virtuale IoT Edge e le risorse associate, eliminare il gruppo di risorse **contoso-edge-rg** nel portale di Azure.
* Per eliminare l'applicazione IoT Central, passare alla pagina **Applicazione** nella sezione **Amministrazione** dell'applicazione e selezionare **Elimina**.

A questo punto, dopo aver appreso come gestire i dispositivi IoT Edge in IoT Central, il passaggio successivo consigliato per gli operatori o gli sviluppatori di dispositivi è il seguente:

> [!div class="nextstepaction"]
> [Usare i gruppi di dispositivi per analizzare la telemetria dei dispositivi](./tutorial-use-device-groups.md)

## <a name="next-steps"></a>Passaggi successivi

A questo punto, dopo aver appreso come gestire i dispositivi IoT Edge in IoT Central, il passaggio successivo consigliato per gli sviluppatori di dispositivi è il seguente:

> [!div class="nextstepaction"]
> [Sviluppare moduli IoT Edge](../../iot-edge/tutorial-develop-for-linux.md)