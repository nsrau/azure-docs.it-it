---
title: Installare e usare Azure IoT Explorer . Documenti Microsoft
description: Installare lo strumento Azure IoT Explorer e usarlo per interagire con i dispositivi IoT Plug and Play Preview connessi all'hub IoT.Install the Azure IoT explorer tool and use it to interact with the IoT Plug and Play Preview devices connected to my IoT hub.
author: miagdp
ms.author: miag
ms.date: 12/27/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 963421fa1ef06599448c9a4197f0d7a6ad2e142d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159184"
---
# <a name="install-and-use-azure-iot-explorer"></a>Installare e usare Azure IoT ExplorerInstall and use Azure IoT explorer

Azure IoT Explorer è uno strumento grafico per interagire e testare i dispositivi IoT Plug and Play Preview.The Azure IoT explorer is a graphical tool for interacting with and testing your IoT Plug and Play Preview devices. Dopo aver installato lo strumento nel computer locale, è possibile utilizzarlo per connettersi a un dispositivo. È possibile usare lo strumento per visualizzare i dati di telemetria che il dispositivo invia, usare le proprietà del dispositivo e chiamare i comandi.

Questo articolo illustra come:

- Installare e configurare lo strumento Azure IoT Explorer.Install and configure the Azure IoT explorer tool.
- Usa lo strumento per interagire con i tuoi dispositivi e testarli.

## <a name="prerequisites"></a>Prerequisiti

Per usare lo strumento Esplora IoT di Azure, è necessario:To use the Azure IoT explorer tool, you need:

- Un hub IoT di Azure. Esistono molti modi per aggiungere un hub IoT alla sottoscrizione di Azure, ad esempio [Creazione di un hub IoT tramite l'interfaccia della riga](../iot-hub/iot-hub-create-using-cli.md)di comando di Azure. È necessaria la stringa di connessione dell'hub IoT per eseguire lo strumento Esplora IoT di Azure.You need the IoT hub connection string to run the Azure IoT explorer tool. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
- Un dispositivo registrato nell'hub IoT. È possibile usare il comando dell'interfaccia della riga di comando di Azure seguente per registrare un dispositivo. Assicurarsi di `{YourIoTHubName}` sostituire `{YourDeviceID}` i segnaposto e con i valori specificati:

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

## <a name="install-azure-iot-explorer"></a>Installare Azure IoT ExplorerInstall Azure IoT explorer

Passare alle versioni di [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer/releases) ed espandere l'elenco delle risorse per la versione più recente. Scaricare e installare la versione più recente dell'applicazione.

## <a name="use-azure-iot-explorer"></a>Usare Esplora IoT di AzureUse Azure IoT explorer

Per un dispositivo, è possibile collegare il proprio dispositivo o utilizzare uno dei nostri dispositivi simulati di esempio. Seguire [queste istruzioni](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/iothub_client/samples) per eseguire l'esempio di dispositivo simulato.

### <a name="connect-to-your-hub"></a>Connettersi all'hub

La prima volta che si esegue Esplora IoT di Azure, viene richiesta la stringa di connessione dell'hub IoT.The first time you run Azure IoT explorer, you're prompted for your IoT hub's connection string. Dopo aver aggiunto la stringa di connessione, selezionare **Connetti**. È possibile usare le impostazioni dello strumento per passare a un altro hub IoT aggiornando la stringa di connessione.

La definizione del modello per un dispositivo IoT Plug and Play viene archiviata nel repository pubblico, in un repository aziendale o nel dispositivo connesso. Per impostazione predefinita, lo strumento cerca la definizione del modello nel repository dei modelli pubblici e nel dispositivo connesso. È possibile aggiungere e rimuovere origini o configurare la priorità delle origini in **Impostazioni**:

Per aggiungere una fonte:

1. Passare a **Settings**.
1. Selezionare **Nuovo** e scegliere l'origine.
1. Se si aggiunge il repository del modello aziendale, specificare la stringa di connessione.

Per rimuovere una sorgente:

1. Passare a **Settings**.
1. Individuare l'origine che si desidera rimuovere.
1. Selezionare **X** per rimuoverlo. Non è possibile rimuovere il repository dei modelli pubblici perché le definizioni di interfaccia comuni provengono da questo repository.

Modificare le priorità di origine:

È possibile trascinare una delle origini di definizione del modello in una classificazione diversa nell'elenco. In caso di conflitto, le origini delle definizioni con classificazioni più alte sostituiscono le origini con classificazioni più basse.

### <a name="view-devices"></a>Visualizzare i dispositivi

Dopo che lo strumento si connette all'hub IoT, visualizza la pagina elenco **Dispositivi** in cui sono elencate le identità dei dispositivi registrate nell'hub IoT. È possibile espandere qualsiasi voce nell'elenco per visualizzare ulteriori informazioni.

Nella pagina elenco **Dispositivi** è possibile:

- Selezionare **Aggiungi** per registrare un nuovo dispositivo con l'hub. Quindi immettere un ID dispositivo. Usare le impostazioni predefinite per generare automaticamente le chiavi di autenticazione e abilitare la connessione all'hub.
- Selezionare un dispositivo e quindi **Elimina** per eliminare un'identità del dispositivo. Esaminare i dettagli del dispositivo prima di completare questa azione per assicurarsi di eliminare l'identità del dispositivo corretta.
- Interrogare `capabilityID` `interfaceID`in base a e . Aggiungi il `capabilityID` `interfaceID` tuo o come parametro per interrogare i tuoi dispositivi.

## <a name="interact-with-a-device"></a>Interagire con un dispositivo

Nella pagina elenco **Dispositivi** selezionare un valore nella colonna **ID dispositivo** per visualizzare la pagina dei dettagli per il dispositivo registrato. Per ogni dispositivo ci sono due sezioni: **Dispositivo** e **Digital Twin**.

### <a name="device"></a>Dispositivo

Questa sezione include le schede **Identità dispositivo**, **Dispositivo Gemello**, **Telemetria**, **Metodo diretto** e **Messaggi cloud-to-device** .

- È possibile visualizzare e aggiornare le informazioni [sull'identità](../iot-hub/iot-hub-devguide-identity-registry.md) del dispositivo nella scheda **Identità dispositivo.**
- È possibile accedere alle informazioni [del dispositivo gemello](../iot-hub/iot-hub-devguide-device-twins.md) nella scheda **Dispositivo gemello.**
- Se un dispositivo è connesso e invia attivamente i dati, è possibile visualizzare i [dati di telemetria](../iot-hub/iot-hub-devguide-messages-read-builtin.md) nella scheda **Telemetria.**
- È possibile chiamare un [metodo diretto](../iot-hub/iot-hub-devguide-direct-methods.md) sul dispositivo nella scheda **Metodo diretto.**
- È possibile inviare un [messaggio da cloud a dispositivo](../iot-hub/iot-hub-devguide-messages-c2d.md) nella scheda Messaggi da cloud a **dispositivo.**

### <a name="digital-twin"></a>Gemello digitale

È possibile utilizzare lo strumento per visualizzare l'istanza digitale doppia del dispositivo. Per un dispositivo IoT Plug and Play, tutte le interfacce associate al modello di funzionalità del dispositivo vengono visualizzate in questa sezione dello strumento. Selezionare un'interfaccia per espandere le [primitive IoT Plug and Play corrispondenti.](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL)

### <a name="interface"></a>Interfaccia

Nella pagina **Interfaccia** è possibile visualizzare la definizione JSON dell'interfaccia.

#### <a name="properties"></a>Proprietà

È possibile visualizzare le proprietà di sola lettura definite in un'interfaccia nella pagina **delle proprietà non scrivibili.** È possibile aggiornare le proprietà scrivibili definite in un'interfaccia nella pagina **proprietà scrivibile:You** can update the writeable properties defined in an interface on the Writeable properties page:

1. Passare alla pagina **delle proprietà Scrivibile.**
1. Fai clic sulla proprietà che desideri aggiornare.
1. Immettere il nuovo valore per la proprietà.
1. Visualizzare in anteprima il payload da inviare al dispositivo.
1. Inviare la modifica.

Dopo aver inviato una modifica, è possibile tenere traccia dello stato di aggiornamento: **synching**, **success**o **error**. Al termine della sincronizzazione, il nuovo valore della proprietà viene visualizzato nella colonna **Proprietà segnalata.** Se si passa ad altre pagine prima del completamento della sincronizzazione, lo strumento continua a notificare quando l'aggiornamento è completo. È inoltre possibile utilizzare il centro notifiche dello strumento per visualizzare la cronologia delle notifiche.

#### <a name="commands"></a>Comandi:

Per inviare un comando a un dispositivo, vai alla pagina **Comandi:**

1. Nell'elenco dei comandi espandere il comando che si desidera attivare.
1. Immettere i valori necessari per il comando.
1. Visualizzare in anteprima il payload da inviare al dispositivo.
1. Inviare il comando.

#### <a name="telemetry"></a>Telemetria

Per visualizzare i dati di telemetria per l'interfaccia selezionata, passare alla relativa pagina **Telemetria.**

## <a name="next-steps"></a>Passaggi successivi

In questo articolo delle procedure è stato illustrato come installare e usare Esplora IoT di Azure per interagire con i dispositivi Plug and Play IoT.In this how-to article, you learned how to install and use Azure IoT explorer to interact with your IoT Plug and Play devices. Un passaggio successivo consigliato consiste nell'apprendere come [installare e usare l'estensione dell'interfaccia della riga](./howto-install-pnp-cli.md)di comando di Azure.A suggested next step is to learn how to Install and use Azure CLI extension .
