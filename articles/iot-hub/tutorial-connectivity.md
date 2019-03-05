---
title: Verificare la connettività dei dispositivi all'hub IoT di Azure
description: Usare gli strumenti dell'hub IoT per risolvere, in fase di sviluppo, i problemi di connettività dei dispositivi all'hub IoT.
services: iot-hub
author: dominicbetts
manager: timlt
ms.author: dobett
ms.custom: mvc
ms.date: 02/22/2019
ms.topic: tutorial
ms.service: iot-hub
ms.openlocfilehash: ebd206f6de031ea73d621568e091632e2e8123b9
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56674506"
---
# <a name="tutorial-use-a-simulated-device-to-test-connectivity-with-your-iot-hub"></a>Esercitazione: Usare un dispositivo simulato per testare la connettività con l'hub IoT

In questa esercitazione vengono usati gli strumenti del portale dell'hub IoT di Azure e i comandi dell'interfaccia della riga di comando di Azure per testare la connettività dei dispositivi. Questa esercitazione usa inoltre un semplice simulatore di dispositivi eseguibile nel computer desktop.

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> * Verificare l'autenticazione del dispositivo
> * Verificare la connettività da dispositivo a cloud
> * Verificare la connettività da cloud a dispositivo
> * Verificare la sincronizzazione dei dispositivi gemelli

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Prerequisiti

Gli script dell'interfaccia della riga di comando eseguiti in questa esercitazione usano l'[estensione IoT di Microsoft Azure per l'interfaccia della riga di comando di Azure](https://github.com/Azure/azure-iot-cli-extension/blob/master/README.md). Per installare questa estensione, eseguire il comando dell'interfaccia della riga di comando seguente:

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

L'applicazione del simulatore di dispositivi in esecuzione in questa esercitazione è scritta con Node.js. È necessario avere Node.js v4.x.x o versione successiva nel computer di sviluppo.

È possibile scaricare Node.js per più piattaforme da [nodejs.org](https://nodejs.org).

È possibile verificare la versione corrente di Node.js installata nel computer di sviluppo tramite il comando seguente:

```cmd/sh
node --version
```

Scaricare il progetto di esempio del simulatore di dispositivi di Node.js da https://github.com/Azure-Samples/azure-iot-samples-node/archive/master.zip ed estrarre l'archivio ZIP.

## <a name="create-an-iot-hub"></a>Creare un hub IoT

Se in una guida introduttiva o in un'esercitazione precedente è stato creato un hub IoT di livello gratuito o standard, è possibile ignorare questo passaggio.

[!INCLUDE [iot-hub-tutorials-create-free-hub](../../includes/iot-hub-tutorials-create-free-hub.md)]

## <a name="check-device-authentication"></a>Verificare l'autenticazione del dispositivo

Un dispositivo deve eseguire l'autenticazione con l'hub prima di qualsiasi scambio di dati con l'hub stesso. È possibile usare lo strumento **Dispositivi IoT** nella sezione **Gestione dei dispositivi** del portale per gestire i dispositivi e verificare le chiavi di autenticazione in uso. In questa sezione dell'esercitazione viene aggiunto un nuovo dispositivo di test, viene recuperata la relativa chiave e si verifica che il dispositivo di test sia in grado di connettersi all'hub. Successivamente viene reimpostata la chiave di autenticazione per osservare cosa accade quando un dispositivo prova a usare una chiave non aggiornata. In questa sezione dell'esercitazione viene usato il portale di Azure per creare, gestire e monitorare un dispositivo, oltre al simulatore di dispositivi di esempio di Node.js.

Accedere al portale e passare all'hub IoT. Passare quindi allo strumento **Dispositivi IoT**:

![Strumento Dispositivi IoT](media/tutorial-connectivity/iot-devices-tool.png)

Per registrare un nuovo dispositivo, fare clic su **+ Aggiungi**, impostare **ID dispositivo** su **MyTestDevice** e fare clic su **Salva**:

![Aggiungere un nuovo dispositivo](media/tutorial-connectivity/add-device.png)

Per recuperare la stringa di connessione per **MyTestDevice**, fare clic su di essa nell'elenco dei dispositivi e quindi copiare il valore di **Stringa di connessione - chiave primaria**. La stringa di connessione include la *chiave di accesso condiviso* per il dispositivo.

![Recuperare la stringa di connessione del dispositivo](media/tutorial-connectivity/copy-connection-string.png)

Per simulare l'invio dei dati di telemetria da **MyTestDevice** all'hub IoT, eseguire l'applicazione del dispositivo simulato di Node.js scaricata in precedenza.

In una finestra del terminale nel computer di sviluppo passare alla cartella radice del progetto di esempio di Node.js scaricato. Passare quindi alla cartella **iot-hub\Tutorials\ConnectivityTests**.

Nella finestra del terminale eseguire i comandi seguenti per installare le librerie necessarie ed eseguire l'applicazione del dispositivo simulato. Usare la stringa di connessione del dispositivo annotata quando il dispositivo è stato aggiunto nel portale.

```cmd/sh
npm install
node SimulatedDevice-1.js "{your device connection string}"
```

La finestra del terminale visualizza alcune informazioni mentre viene eseguito un tentativo di connessione all'hub:

![Connessione del dispositivo simulato](media/tutorial-connectivity/sim-1-connected.png)

L'autenticazione da un dispositivo tramite una chiave del dispositivo generata dall'hub IoT è stata completata correttamente.

### <a name="reset-keys"></a>Reimpostare le chiavi

In questa sezione viene reimpostata la chiave del dispositivo per osservare l'errore generato quando il dispositivo simulato tenta di connettersi.

Per reimpostare la chiave del dispositivo primaria per **MyTestDevice**, eseguire i comandi seguenti:

```azurecli-interactive
# Generate a new Base64 encoded key using the current date
read key < <(date +%s | sha256sum | base64 | head -c 32)

# Requires the IoT Extension for Azure CLI
# az extension add --name azure-cli-iot-ext

# Reset the primary device key for MyTestDevice
az iot hub device-identity update --device-id MyTestDevice --set authentication.symmetricKey.primaryKey=$key --hub-name {YourIoTHubName}
```

Nella finestra del terminale nel computer di sviluppo eseguire nuovamente l'applicazione del dispositivo simulato:

```cmd/sh
npm install
node SimulatedDevice-1.js "{your device connection string}"
```

Questa volta viene visualizzato un errore di autenticazione quando l'applicazione tenta di connettersi:

![Errore di connessione](media/tutorial-connectivity/sim-1-fail.png)

### <a name="generate-shared-access-signature-sas-token"></a>Generare un token di firma di accesso condiviso

Se il dispositivo usa uno degli SDK per dispositivi hub IoT, il codice della libreria SDK genera il token di firma di accesso condiviso usato per l'autenticazione con l'hub. Un token di firma di accesso condiviso viene generato dal nome dell'hub, dal nome del dispositivo e dalla chiave del dispositivo.

In alcuni scenari, ad esempio in un gateway di protocollo cloud o nell'ambito di uno schema di autenticazione personalizzato, potrebbe essere necessario generare manualmente il token di firma di accesso condiviso. Per risolvere i problemi relativi al codice di generazione della firma di accesso condiviso, è utile generare un token di firma di accesso condiviso valido noto da usare durante il test.

> [!NOTE]
> Il file di esempio SimulatedDevice-2.js include esempi che illustrano come generare un token di firma di accesso condiviso con o senza l'SDK.

Per generare un token di firma di accesso condiviso corretto noto tramite l'interfaccia della riga di comando, eseguire il comando seguente:

```azurecli-interactive
az iot hub generate-sas-token --device-id MyTestDevice --hub-name {YourIoTHubName}
```

Prendere nota del testo completo del token di firma di accesso condiviso generato. Un token di firma di accesso condiviso ha un aspetto simile al seguente: `SharedAccessSignature sr=tutorials-iot-hub.azure-devices.net%2Fdevices%2FMyTestDevice&sig=....&se=1524155307`

In una finestra del terminale nel computer di sviluppo passare alla cartella radice del progetto di esempio di Node.js scaricato. Passare quindi alla cartella **iot-hub\Tutorials\ConnectivityTests\simulated-device**.

Nella finestra del terminale eseguire i comandi seguenti per installare le librerie necessarie ed eseguire l'applicazione del dispositivo simulato:

```cmd/sh
npm install
node SimulatedDevice-2.js "{Your SAS token}"
```

La finestra del terminale visualizza alcune informazioni mentre viene eseguito un tentativo di connessione all'hub tramite il token di firma di accesso condiviso:

![Connessione del dispositivo simulato tramite il token](media/tutorial-connectivity/sim-2-connected.png)

L'autenticazione da un dispositivo tramite un token di firma di accesso condiviso di test generato da un comando dell'interfaccia della riga di comando è stata completata correttamente. Il file **SimulatedDevice-2.js** include codice di esempio che illustra come generare un token di firma di accesso condiviso nel codice.

### <a name="protocols"></a>Protocolli

Un dispositivo può usare uno dei protocolli seguenti per la connessione all'hub IoT:

| Protocollo | Porta in uscita |
| --- | --- |
| MQTT |8883 |
| MQTT su WebSocket |443 |
| AMQP |5671 |
| AMQP su WebSockets |443 |
| HTTPS |443 |

Se la porta in uscita è bloccata da un firewall, il dispositivo non può connettersi:

![Porta bloccata](media/tutorial-connectivity/port-blocked.png)

## <a name="check-device-to-cloud-connectivity"></a>Verificare la connettività da dispositivo a cloud

Dopo che un dispositivo ha eseguito la connessione, in genere tenta di inviare dati di telemetria all'hub IoT. Questa sezione mostra in che modo verificare che i dati di telemetria inviati dal dispositivo raggiungano l'hub.

Per prima cosa, recuperare la stringa di connessione corrente per il dispositivo simulato usando il comando seguente:

```azurecli-interactive
az iot hub device-identity show-connection-string --device-id MyTestDevice --output table --hub-name {YourIoTHubName}
```

Per eseguire un dispositivo simulato che invia messaggi, passare alla cartella **iot-hub\Tutorials\ConnectivityTests\simulated-device** nel codice scaricato.

Nella finestra del terminale eseguire i comandi seguenti per installare le librerie necessarie ed eseguire l'applicazione del dispositivo simulato:

```cmd/sh
npm install
node SimulatedDevice-3.js "{your device connection string}"
```

La finestra del terminale visualizza alcune informazioni mentre vengono inviati i dati di telemetria all'hub:

![Invio dei messaggi dal dispositivo simulato](media/tutorial-connectivity/sim-3-sending.png)

È possibile usare **Metriche** nel portale per verificare che i messaggi di telemetria raggiungano l'hub IoT. Selezionare l'hub IoT nell'elenco a discesa **Risorsa**, selezionare **i Messaggi di telemetria inviati** come metrica e impostare l'intervallo di tempo su **Ultima ora**. Il grafico mostra il conteggio aggregato dei messaggi inviati dal dispositivo simulato:

![Visualizzare le metriche dell'hub IoT](media/tutorial-connectivity/metrics-portal.png)

Sono necessari alcuni minuti prima che le metriche diventino disponibili dopo l'avvio del dispositivo simulato.

## <a name="check-cloud-to-device-connectivity"></a>Verificare la connettività da cloud a dispositivo

Questa sezione illustra come eseguire una chiamata al metodo diretto di test per un dispositivo per verificare la connettività da cloud a dispositivo. Viene eseguito un dispositivo simulato nel computer di sviluppo per ascoltare le chiamate al metodo diretto dall'hub.

Nella finestra del terminale usare il comando seguente per eseguire l'applicazione del dispositivo simulato:

```cmd/sh
node SimulatedDevice-3.js "{your device connection string}"
```

Usare un comando dell'interfaccia della riga di comando per chiamare un metodo diretto nel dispositivo:

```azurecli-interactive
az iot hub invoke-device-method --device-id MyTestDevice --method-name TestMethod --timeout 10 --method-payload '{"key":"value"}' --hub-name {YourIoTHubName}
```

Il dispositivo simulato stampa un messaggio nella console quando riceve una chiamata al metodo diretto:

![Il dispositivo simulato riceve una chiamata al metodo diretto](media/tutorial-connectivity/receive-method-call.png)

Quando il dispositivo simulato riceve correttamente la chiamata al metodo diretto, invia nuovamente un acknowledgement all'hub:

![Ricezione dell'acknowledgement del metodo diretto](media/tutorial-connectivity/method-acknowledgement.png)

## <a name="check-twin-synchronization"></a>Verificare la sincronizzazione dei dispositivi gemelli

I dispositivi usano dispositivi gemelli per sincronizzare lo stato tra il dispositivo e l'hub. In questa sezione vengono usati i comandi dell'interfaccia della riga di comando per inviare le _proprietà desiderate_ a un dispositivo e leggere le _proprietà segnalate_ inviate dal dispositivo.

Il dispositivo simulato usato in questa sezione invia le proprietà segnalate all'hub ogni volta che viene avviato e stampa le proprietà desiderate nella console ogni volta che le riceve.

Nella finestra del terminale usare il comando seguente per eseguire l'applicazione del dispositivo simulato:

```cmd/sh
node SimulatedDevice-3.js "{your device connection string}"
```

Per verificare che l'hub abbia ricevuto le proprietà segnalate dal dispositivo, usare il comando dell'interfaccia della riga di comando seguente:

```azurecli-interactive
az iot hub device-twin show --device-id MyTestDevice --hub-name {YourIoTHubName}
```

Nell'output del comando è possibile visualizzare la proprietà **devicelaststarted** nella sezione delle proprietà segnalate. Questa proprietà indica la data e l'ora dell'ultimo avvio del dispositivo simulato.

![Visualizzare le proprietà segnalate](media/tutorial-connectivity/reported-properties.png)

Per verificare che l'hub sia in grado di inviare i valori delle proprietà desiderate al dispositivo, usare il comando dell'interfaccia della riga di comando seguente:

```azurecli-interactive
az iot hub device-twin update --set properties.desired='{"mydesiredproperty":"propertyvalue"}' --device-id MyTestDevice --hub-name {YourIoTHubName}
```

Il dispositivo simulato stampa un messaggio quando riceve un aggiornamento delle proprietà desiderate dall'hub:

![Ricevere le proprietà desiderate](media/tutorial-connectivity/desired-properties.png)

Oltre a ricevere le modifiche delle proprietà desiderate man mano che vengono apportate, il dispositivo simulato controlla automaticamente le proprietà desiderate al momento dell'avvio.

## <a name="clean-up-resources"></a>Pulire le risorse

Se l'hub IoT non è più necessario, eliminarlo insieme al gruppo di risorse nel portale, Per farlo, selezionare il gruppo di risorse **tutorials-iot-hub-rg** che contiene l'hub IoT e fare clic su **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato spiegato come controllare le chiavi del dispositivo, verificare la connettività da dispositivo a cloud, verificare la connettività da cloud a dispositivo e verificare la sincronizzazione dei dispositivi gemelli. Per altre informazioni su come monitorare l'hub IoT, vedere l'articolo sulle procedure per il monitoraggio dell'hub IoT.

> [!div class="nextstepaction"]
> [Monitorare con la diagnostica](iot-hub-monitor-resource-health.md)
