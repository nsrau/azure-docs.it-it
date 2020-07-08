---
title: Moduli di monitoraggio gemelli-Azure IoT Edge
description: Come interpretare i dispositivi gemelli e i moduli gemelli per determinare la connettività e l'integrità.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 05/29/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c24cef2cf9e4c54d16ebc75eb1a56273d8826355
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84221138"
---
# <a name="monitor-module-twins"></a>Monitorare i moduli gemelli

I moduli gemelli nell'hub Azure Internet consentono di monitorare la connettività e l'integrità delle distribuzioni IoT Edge. I moduli gemelli archiviano informazioni utili nell'hub Internet sulle prestazioni dei moduli in esecuzione. L' [agente IOT Edge](iot-edge-runtime.md#iot-edge-agent) e i moduli di runtime dell' [Hub IOT Edge](iot-edge-runtime.md#iot-edge-hub) gestiscono ciascuno i moduli gemelli `$edgeAgent` e `$edgeHub` , rispettivamente:

* `$edgeAgent`contiene i dati sull'integrità e sulla connettività relativi ai moduli di runtime dell'agente IoT Edge e IoT Edge e ai moduli personalizzati. L'agente di IoT Edge è responsabile della distribuzione dei moduli, del monitoraggio e della segnalazione dello stato di connessione all'hub Azure.
* `$edgeHub`contiene i dati sulle comunicazioni tra l'hub IoT Edge in esecuzione su un dispositivo e l'hub Azure. Ciò include l'elaborazione dei messaggi in arrivo dai dispositivi downstream. IoT Edge Hub è responsabile dell'elaborazione delle comunicazioni tra l'hub Azure e i dispositivi e i moduli IoT Edge.

I dati sono organizzati in metadati, tag, insieme ai set di proprietà desiderati e segnalati nelle strutture JSON dei moduli gemelli. Le proprietà desiderate specificate nell'deployment.jsnel file vengono copiate nei moduli gemelli. L'agente IoT Edge e l'hub IoT Edge aggiornano ogni proprietà segnalata per i moduli.

Analogamente, le proprietà desiderate specificate per i moduli personalizzati nel deployment.jsfile vengono copiate nel relativo modulo gemello, ma la soluzione è responsabile di fornire i valori delle proprietà segnalate.

Questo articolo descrive come esaminare i moduli gemelli nell'portale di Azure, nell'interfaccia della riga di comando di Azure e in Visual Studio Code. Per informazioni sul monitoraggio del modo in cui i dispositivi ricevono le distribuzioni, vedere [monitorare IOT Edge distribuzioni](how-to-monitor-iot-edge-deployments.md). Per una panoramica sul concetto di moduli gemelli, vedere [comprendere e usare i moduli gemelli nell'hub](../iot-hub/iot-hub-devguide-module-twins.md)Internet.

> [!TIP]
> Le proprietà segnalate di un modulo di runtime potrebbero essere obsolete se un dispositivo IoT Edge viene disconnesso dall'hub Internet delle cose. È possibile eseguire il [ping](how-to-edgeagent-direct-method.md#ping) del `$edgeAgent` modulo per determinare se la connessione è stata persa.

## <a name="monitor-runtime-module-twins"></a>Monitorare i moduli di runtime gemelli

Per risolvere i problemi di connettività della distribuzione, esaminare il modulo IoT Edge Agent e IoT Edge Runtime Hub gemelli e quindi eseguire il drill-down in altri moduli.

### <a name="monitor-iot-edge-agent-module-twin"></a>Monitorare il modulo di IoT Edge agente gemello

Il codice JSON seguente illustra il `$edgeAgent` modulo gemello in Visual Studio Code con la maggior parte delle sezioni JSON compresse.

```json
{
  "deviceId": "Windows109",
  "moduleId": "$edgeAgent",
  "etag": "AAAAAAAAAAU=",
  "deviceEtag": "NzgwNjA1MDUz",
  "status": "enabled",
  "statusUpdateTime": "0001-01-01T00:00:00Z",
  "connectionState": "Disconnected",
  "lastActivityTime": "0001-01-01T00:00:00Z",
  "cloudToDeviceMessageCount": 0,
  "authenticationType": "sas",
  "x509Thumbprint": {
    "primaryThumbprint": null,
    "secondaryThumbprint": null
  },
  "version": 53,
  "properties": {
    "desired": { "···" },
    "reported": {
      "schemaVersion": "1.0",
      "version": { "···" },
      "lastDesiredStatus": { "···" },
      "runtime": { "···" },
      "systemModules": {
        "edgeAgent": { "···" },
        "edgeHub": { "···" }
      },
      "lastDesiredVersion": 5,
      "modules": {
        "SimulatedTemperatureSensor": { "···" }
      },
      "$metadata": { "···" },
      "$version": 48
    }
  }
}
```

Il codice JSON può essere descritto nelle sezioni seguenti, a partire dalla parte superiore:

* Metadati: contiene i dati di connettività. È interessante notare che lo stato della connessione per l'agente di IoT Edge è sempre in uno stato disconnesso: `"connectionState": "Disconnected"` . Il motivo per cui lo stato della connessione riguarda i messaggi da dispositivo a cloud (D2C) e l'agente di IoT Edge non invia messaggi D2C.
* Properties: contiene le `desired` `reported` sottosezioni e.
* Properties. desired-(mostrato compresso) i valori di proprietà previsti impostati dall'operatore nel deployment.jssu file.
* Properties. reported: valori delle proprietà più recenti segnalati da IoT Edge Agent.

Entrambe le `properties.desired` `properties.reported` sezioni e hanno una struttura simile e contengono metadati aggiuntivi per informazioni sullo schema, sulla versione e sul Runtime. È inclusa anche la `modules` sezione relativa a tutti i moduli personalizzati, ad esempio `SimulatedTemperatureSensor` , e la `systemModules` sezione relativa ai `$edgeAgent` moduli di runtime di `$edgeHub` .

Confrontando i valori delle proprietà segnalati con i valori desiderati, è possibile determinare le discrepanze e identificare le disconnessioni che consentono di risolvere i problemi. Per eseguire questi confronti, controllare il `$lastUpdated` valore riportato nella `metadata` sezione relativa alla proprietà che si sta esaminando.

Le seguenti proprietà sono importanti da esaminare per la risoluzione dei problemi:

* **ExitCode** -qualsiasi valore diverso da zero indica che il modulo è stato interrotto con un errore. Tuttavia, vengono usati i codici di errore 137 o 143 se un modulo è stato intenzionalmente impostato sullo stato arrestato.

* **lastStartTimeUtc** : Mostra la data e l' **ora dell'ultimo** avvio del contenitore. Questo valore è 0001-01-01T00:00:00Z se il contenitore non è stato avviato.

* **lastExitTimeUtc** : Mostra la data e l' **ora dell'ultimo** completamento del contenitore. Questo valore è 0001-01-01T00:00:00Z se il contenitore è in esecuzione e non è mai stato interrotto.

* **runtimeStatus** può essere uno dei valori seguenti:

    | valore | Descrizione |
    | --- | --- |
    | unknown | Stato predefinito fino alla creazione della distribuzione. |
    | backoff | Il modulo è pianificato per l'avvio, ma non è attualmente in esecuzione. Questo valore è utile per un modulo che sta per essere sottoposto a modifiche di stato nel riavvio. Quando un modulo in errore è in attesa del riavvio durante il periodo di raffreddamento, il modulo si troverà in uno stato backoff. |
    | in esecuzione | Indica che il modulo è attualmente in esecuzione. |
    | non integro | Indica che il controllo del probe di integrità non è riuscito o è scaduto. |
    | Arrestato | Indica che il modulo è stato terminato correttamente (con un codice di uscita zero). |
    | non riuscita | Indica che il modulo è stato terminato con un codice di uscita di errore (diverso da zero). Il modulo può eseguire la transizione a backoff da questo stato a seconda del criterio di riavvio attivo. Questo stato può indicare che si è verificato un errore irreversibile del modulo. Si verifica un errore quando il Microsoft Monitoring Agent (MMA) non è più in grado di rianimare il modulo, richiedendo una nuova distribuzione. |

Per informazioni dettagliate, vedere [proprietà segnalate da EdgeAgent](module-edgeagent-edgehub.md#edgeagent-reported-properties) .

### <a name="monitor-iot-edge-hub-module-twin"></a>Monitorare il modulo gemello di IoT Edge Hub

Il codice JSON seguente illustra il `$edgeHub` modulo gemello in Visual Studio Code con la maggior parte delle sezioni JSON compresse.

```json
{
  "deviceId": "Windows109",
  "moduleId": "$edgeHub",
  "etag": "AAAAAAAAAAU=",
  "deviceEtag": "NzgwNjA1MDU2",
  "status": "enabled",
  "statusUpdateTime": "0001-01-01T00:00:00Z",
  "connectionState": "Connected",
  "lastActivityTime": "0001-01-01T00:00:00Z",
  "cloudToDeviceMessageCount": 0,
  "authenticationType": "sas",
  "x509Thumbprint": {
    "primaryThumbprint": null,
    "secondaryThumbprint": null
  },
  "version": 102,
    "properties": {
      "desired": { "···" },
      "reported": {
        "schemaVersion": "1.0",
        "version": { "···" },
      "lastDesiredVersion": 5,
      "lastDesiredStatus": { "···" },
      "clients": {
        "Windows109/SimulatedTemperatureSensor": {
          "status": "Disconnected",
          "lastConnectedTimeUtc": "2020-04-08T21:42:42.1743956Z",
          "lastDisconnectedTimeUtc": "2020-04-09T07:02:42.1398325Z"
        }
      },
      "$metadata": { "···" },
      "$version": 97
    }
  }
}

```

Il codice JSON può essere descritto nelle sezioni seguenti, a partire dalla parte superiore:

* Metadati: contiene i dati di connettività.

* Properties: contiene le `desired` `reported` sottosezioni e.
* Properties. desired-(mostrato compresso) i valori di proprietà previsti impostati dall'operatore nel deployment.jssu file.
* Properties. reported: valori delle proprietà più recenti segnalati dall'hub IoT Edge.

Se si verificano problemi con i dispositivi downstream, l'analisi di questi dati è un punto di partenza valido.

## <a name="monitor-custom-module-twins"></a>Monitorare i gemelli del modulo personalizzato

Le informazioni sulla connettività dei moduli personalizzati vengono gestite nel modulo gemello di IoT Edge Agent. Il modulo gemello per il modulo personalizzato viene usato principalmente per la manutenzione dei dati per la soluzione. Le proprietà desiderate definite nella deployment.jssu file si riflettono nel modulo gemello e il modulo può aggiornare i valori delle proprietà segnalati in base alle esigenze.

È possibile usare il linguaggio di programmazione preferito con gli [SDK per dispositivi dell'hub Azure](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks#azure-iot-hub-device-sdks) per aggiornare i valori delle proprietà segnalate nel modulo gemello, in base al codice dell'applicazione del modulo. La procedura seguente usa Azure SDK per .NET a tale scopo, usando il codice del modulo [SimulatedTemperatureSensor](https://github.com/Azure/iotedge/blob/dd5be125df165783e4e1800f393be18e6a8275a3/edge-modules/SimulatedTemperatureSensor/src/Program.cs) :

1. Creare un'istanza di [ModuleClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient) con il metodo [CreateFromEnvironmentAysnc](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.createfromenvironmentasync) .

1. Ottenere una raccolta delle proprietà del modulo gemello con il metodo [GetTwinAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.gettwinasync?view=azure-dotnet) .

1. Creare un listener (passando un callback) per rilevare le modifiche apportate alle proprietà desiderate con il metodo [SetDesiredPropertyUpdateCallbackAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.setdesiredpropertyupdatecallbackasync?view=azure-dotnet) .

1. Nel metodo di callback aggiornare le proprietà segnalate nel modulo gemello con il metodo [UpdateReportedPropertiesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient) , passando un oggetto [gemello](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.shared.twincollection) dei valori di proprietà che si desidera impostare.

## <a name="access-the-module-twins"></a>Accedere ai moduli gemelli

È possibile esaminare il file JSON per i moduli gemelli nell'hub Azure, in Visual Studio Code e con l'interfaccia della riga di comando di Azure.

### <a name="monitor-in-azure-iot-hub"></a>Monitorare nell'hub di Azure

Per visualizzare il codice JSON per il modulo gemello:

1. Accedere al [portale di Azure](https://portal.azure.com) e passare all'hub IoT.
1. Selezionare **IoT Edge** nel riquadro a sinistra.
1. Nella scheda **dispositivi IOT Edge** selezionare l' **ID** del dispositivo con i moduli che si desidera monitorare.
1. Selezionare il nome del modulo dalla scheda **moduli** e quindi selezionare **modulo Identity gemello** dalla barra dei menu superiore.

  ![Selezionare un modulo gemello da visualizzare nella portale di Azure](./media/how-to-monitor-module-twins/select-module-twin.png)

Se viene visualizzato il messaggio "un'identità del modulo non esiste per questo modulo", questo errore indica che la soluzione back-end non è più disponibile che ha originariamente creato l'identità.

### <a name="monitor-module-twins-in-visual-studio-code"></a>Monitorare i gemelli del modulo in Visual Studio Code

Per esaminare e modificare un modulo gemello:

1. Se non è già installato, installare l' [estensione strumenti di Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) per la Visual Studio Code.
1. In **Esplora risorse**espandere l' **Hub Azure**Internet e quindi espandere il dispositivo con il modulo che si desidera monitorare.
1. Fare clic con il pulsante destro del mouse sul modulo e scegliere **modifica modulo gemello**. Un file temporaneo del modulo gemello viene scaricato nel computer e visualizzato in Visual Studio Code.

  ![Ottenere un modulo gemello da modificare in Visual Studio Code](./media/how-to-monitor-module-twins/edit-module-twin-vscode.png)

Se si apportano modifiche, selezionare **Aggiorna modulo gemello** sopra il codice nell'editor per salvare le modifiche apportate all'hub.

  ![Aggiornare un modulo gemello in Visual Studio Code](./media/how-to-monitor-module-twins/update-module-twin-vscode.png)

### <a name="monitor-module-twins-in-azure-cli"></a>Monitorare moduli gemelli nell'interfaccia della riga di comando di Azure

Per verificare se IoT Edge è in esecuzione, usare il comando [AZ all hub Invoke-Module-Method](how-to-edgeagent-direct-method.md#ping) per eseguire il ping dell'agente IOT Edge.

La struttura [AZ l'hub Module-Twin](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/module-twin) fornisce i comandi seguenti:

* **AZ all hub Module-Twin Show** -Mostra la definizione di un modulo gemello.
* **AZ all hub Module-gemelle Update** -aggiorna la definizione di un modulo gemello.
* **AZ all hub Module-Twin Replace** -sostituisce una definizione di modulo gemello con un file JSON di destinazione.

## <a name="next-steps"></a>Passaggi successivi

Leggere le informazioni su come [comunicare con EdgeAgent usando i metodi diretti predefiniti](how-to-edgeagent-direct-method.md).
