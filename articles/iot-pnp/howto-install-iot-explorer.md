---
title: Installare e usare Esplora risorse di Azure | Microsoft Docs
description: Installare lo strumento Azure Internet Explorer e usarlo per interagire con i dispositivi Plug and Play anteprima connessi all'hub Internet.
author: miagdp
ms.author: miag
ms.date: 07/02/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: f59e449589c7f3027dc8a9daf9d8d12f04831dd7
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/04/2019
ms.locfileid: "71960581"
---
# <a name="install-and-use-azure-iot-explorer"></a>Installare e usare Azure Internet Explorer

Azure Internet Explorer è uno strumento grafico per l'interazione e il testing dei dispositivi Plug and Play anteprima. Dopo aver installato lo strumento nel computer locale, è possibile usarlo per connettersi a un dispositivo. È possibile usare lo strumento per visualizzare i dati di telemetria che il dispositivo invia, utilizzare le proprietà del dispositivo e chiamare i comandi.

Questo articolo illustra come:

- Installare e configurare lo strumento Azure Internet Explorer.
- Usare lo strumento per interagire e testare i dispositivi.

## <a name="prerequisites"></a>Prerequisiti

Per usare lo strumento Azure Internet Explorer, è necessario:

- Un hub IoT di Azure. Sono disponibili diversi modi per aggiungere un hub Internet alla sottoscrizione di Azure, ad esempio [la creazione di un hub Internet delle cose usando l'interfaccia della riga di comando di Azure](../iot-hub/iot-hub-create-using-cli.md). Per eseguire lo strumento Azure Internet Explorer è necessaria la stringa di connessione dell'hub Internet. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
- Un dispositivo registrato nell'hub Internet. È possibile usare il comando dell'interfaccia della riga di comando di Azure seguente per registrare un dispositivo. Assicurarsi di sostituire i segnaposto `{YourIoTHubName}` e `{YourDeviceID}` con i valori seguenti:

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

## <a name="install-azure-iot-explorer"></a>Installare Azure Internet Explorer Explorer

Passare ad [Azure Internet Explorer releases](https://github.com/Azure/azure-iot-explorer/releases) ed espandere l'elenco degli asset per la versione più recente. Scaricare e installare la versione più recente dell'applicazione.

## <a name="use-azure-iot-explorer"></a>Usare Esplora risorse di Azure

Per un dispositivo, è possibile connettere il proprio dispositivo o usare uno dei dispositivi simulati di esempio. Seguire [queste istruzioni](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/iothub_client/samples) per eseguire l'esempio di dispositivo simulato.

### <a name="connect-to-your-hub"></a>Connettersi all'hub

La prima volta che si esegue Azure Internet Explorer, viene richiesta la stringa di connessione dell'hub. Dopo aver aggiunto la stringa di connessione, selezionare **Connetti**. È possibile usare le impostazioni dello strumento per passare a un altro hub delle cose aggiornando la stringa di connessione.

La definizione del modello per un dispositivo Plug and Play viene archiviata sia nel repository pubblico, in un repository aziendale che nel dispositivo connesso. Per impostazione predefinita, lo strumento Cerca la definizione del modello nel repository del modello pubblico e nel dispositivo connesso. È possibile aggiungere e rimuovere origini oppure configurare la priorità delle origini nelle **Impostazioni**:

Per aggiungere un'origine:

1. Passare a **Impostazioni**.
1. Selezionare **nuovo** e scegliere l'origine.
1. Se si sta aggiungendo il repository del modello aziendale, fornire la stringa di connessione.

Per rimuovere un'origine:

1. Passare a **Impostazioni**.
1. Individuare l'origine che si desidera rimuovere.
1. Selezionare **X** per rimuoverlo. Non è possibile rimuovere il repository del modello pubblico perché le definizioni dell'interfaccia comune provengono da questo repository.

Modificare le priorità di origine:

È possibile trascinare e rilasciare una delle origini della definizione del modello in un rango diverso nell'elenco. Se si verifica un conflitto, le origini delle definizioni con rango più elevati eseguono l'override delle origini con rango inferiore.

### <a name="view-devices"></a>Visualizzare i dispositivi

Una volta che lo strumento si connette all'hub Internet, Visualizza la pagina di elenco dei **dispositivi** in cui sono elencate le identità del dispositivo registrate con l'hub Internet. È possibile espandere qualsiasi voce dell'elenco per visualizzare altre informazioni.

Nella pagina dell'elenco dei **dispositivi** è possibile:

- Selezionare **Aggiungi** per registrare un nuovo dispositivo con l'hub. Immettere quindi un ID dispositivo. Usare le impostazioni predefinite per generare automaticamente le chiavi di autenticazione e abilitare la connessione all'hub.
- Selezionare un dispositivo e quindi fare clic su **Elimina** per eliminare un'identità del dispositivo. Esaminare i dettagli del dispositivo prima di completare questa azione per assicurarsi di eliminare l'identità del dispositivo corretta.
- Eseguire una query per `capabilityID` e `interfaceID`. Aggiungere il `capabilityID` o `interfaceID` come parametro per eseguire query sui dispositivi.

## <a name="interact-with-a-device"></a>Interagire con un dispositivo

Nella pagina elenco **dispositivi** selezionare un valore nella colonna **ID dispositivo** per visualizzare la pagina dei dettagli del dispositivo registrato. Per il dispositivo sono presenti due sezioni: **Dispositivi e dispositivi** **gemelli digitali**.

### <a name="device"></a>Dispositivo

Questa sezione include le schede **identità dispositivo**, **dispositivo gemello**e **telemetria** .

- È possibile visualizzare e aggiornare le informazioni sull' [identità del dispositivo](../iot-hub/iot-hub-devguide-identity-registry.md) nella scheda **identità dispositivo** .
- È possibile accedere alle informazioni sul [dispositivo gemello](../iot-hub/iot-hub-devguide-device-twins.md) nella scheda del **dispositivo gemello** .
- Se un dispositivo è connesso e invia attivamente dati, è possibile visualizzare i dati di [telemetria](../iot-hub/iot-hub-devguide-messages-read-builtin.md) nella scheda **telemetria** .

### <a name="digital-twin"></a>Gemello digitale

È possibile usare lo strumento per visualizzare un'istanza del dispositivo gemello digitale del dispositivo. Per un dispositivo Plug and Play, tutte le interfacce associate al modello di funzionalità del dispositivo vengono visualizzate in questo articolo. Consente di selezionare un'interfaccia per espandere le relative [plug and Play primitive](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL).

#### <a name="properties"></a>Proprietà

È possibile visualizzare le proprietà di sola lettura definite in un'interfaccia nella pagina delle **Proprietà** . È possibile aggiornare le proprietà scrivibili definite in un'interfaccia nella pagina **proprietà scrivibili** .

1. Passare alla pagina delle **proprietà scrivibili** .
1. Fare clic sulla proprietà che si desidera aggiornare.
1. Immettere il nuovo valore per la proprietà.
1. Visualizzare in anteprima il payload da inviare al dispositivo.
1. Inviare la modifica.

Dopo aver inviato una modifica, è possibile tenere traccia dello stato di aggiornamento: **sincronizzazione**, **esito positivo**o **errore**. Al termine della sincronizzazione, viene visualizzato il nuovo valore della proprietà nella colonna delle **proprietà restituita** . Se si passa ad altre pagine prima del completamento della sincronizzazione, lo strumento continua a notificare il completamento dell'aggiornamento. È anche possibile usare il centro notifiche dello strumento per visualizzare la cronologia delle notifiche.

#### <a name="commands"></a>Comandi:

Per inviare un comando a un dispositivo, passare alla pagina **comandi** :

1. Nell'elenco dei comandi espandere il comando che si desidera attivare.
1. Immettere i valori necessari per il comando.
1. Visualizzare in anteprima il payload da inviare al dispositivo.
1. Inviare il comando.

#### <a name="telemetry"></a>Telemetria

Per visualizzare i dati di telemetria per l'interfaccia selezionata, passare alla relativa pagina di **telemetria** .

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sulle procedure si è appreso come installare e usare Azure Internet Explorer Explorer per interagire con i dispositivi Plug and Play. Un passaggio successivo suggerito consiste nell'apprendere come [installare e usare l'estensione dell'interfaccia](./howto-install-pnp-cli.md)della riga di comando di Azure.
