---
title: Usare i dispositivi offline - Azure IoT Edge | Microsoft Docs
description: Comprendere come i moduli e dispositivi IoT Edge possano funzionare senza connessione Internet per periodi prolungati di tempo e come IoT Edge possa consentire anche a dispositivi IoT regolari di funzionare offline.
author: kgremban
ms.author: kgremban
ms.date: 11/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 55512491121aee28404ab5f85b4223c67a2f0e1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80236068"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices"></a>Informazioni sulle funzionalità offline estese per dispositivi IoT Edge, moduli e dispositivi figlio

Azure IoT Edge supporta le operazioni offline estese nei dispositivi IoT Edge e abilita le operazioni offline anche su dispositivi figlio non IoT Edge.Azure IoT Edge supports extended offline operations on your IoT Edge devices, and enables offline operations on non-IoT Edge child devices tootobe too too too too. Finché un dispositivo IoT Edge ha avuto l'opportunità di connettersi all'hub IoT, tale dispositivo e tutti i dispositivi figlio possono continuare a funzionare con una connessione Internet intermittente o assenza.

## <a name="how-it-works"></a>Funzionamento

Quando un dispositivo IoT Edge passa alla modalità offline, l'hub di IoT Edge svolge tre ruoli. Prima di tutto, archivia i messaggi diretti upstream e li salva fino a quando il dispositivo si riconnette. In secondo luogo, agisce per conto dell'hub IoT per l'autenticazione dei moduli e dei dispositivi figlio in modo che possano continuare a funzionare. In terzo luogo, consente le comunicazioni tra i dispositivi figlio che normalmente passerebbero attraverso l'hub IoT.

L'esempio seguente mostra il funzionamento di uno scenario IoT Edge in modalità offline:

1. **Configura i dispositivi**

   Per i dispositivi IoT Edge le funzionalità offline vengono abilitate automaticamente. Per estendere tale funzionalità ad altri dispositivi IoT, è necessario dichiarare una relazione padre-figlio tra i dispositivi nell'hub IoT. Quindi, configurare i dispositivi figlio in modo che considerino attendibile il dispositivo padre assegnato e instradano le comunicazioni da dispositivo a cloud tramite il padre come gateway.

2. **Sincronizzazione con l'hub IoT**

   Almeno una volta dopo l'installazione del runtime di IoT Edge, il dispositivo IoT Edge deve essere online per la sincronizzazione con l'hub IoT. Con questa sincronizzazione, il dispositivo IoT Edge ottiene informazioni dettagliate sugli eventuali dispositivi figlio assegnati. Il dispositivo IoT Edge aggiorna inoltre in modo sicuro la cache locale per abilitare le operazioni offline e recupera le impostazioni per l'archiviazione locale dei messaggi di telemetria.

3. **Vai offline**

   Mentre si è disconnessi dall'hub IoT, il dispositivo IoT Edge, i moduli distribuiti e tutti i dispositivi IoT figlio possono operare a tempo indeterminato. I moduli e i dispositivi figlio possono essere avviati e riavviati eseguendo l'autenticazione con l'hub di IoT Edge mentre è attiva la modalità offline. I dati di telemetria previsti per l'invio upstream all'hub IoT vengono archiviati in locale. La comunicazione tra i moduli o tra i dispositivi IoT figlio viene gestita tramite metodi diretti o messaggi.

4. **Riconnettere e risincronizzare con l'hub IoT**

   Dopo il ripristino della connessione con l'hub IoT, il dispositivo IoT Edge viene sincronizzato di nuovo. I messaggi archiviati localmente vengono recapitati immediatamente all'hub IoT, ma dipendono dalla velocità della connessione, dalla latenza dell'hub IoT e dai fattori correlati. Essi vengono consegnati nello stesso ordine in cui sono stati immagazzinati.

   Eventuali differenze tra le proprietà desiderate e quelle segnalate per i moduli e i dispositivi vengono riconciliate. Il dispositivo IoT Edge aggiorna tutte le modifiche nel set di dispositivi IoT figlio assegnati.

## <a name="restrictions-and-limits"></a>Restrizioni e limiti

Le funzionalità offline estese descritte in questo articolo sono disponibili in [IoT Edge versione 1.0.7 o successiva.](https://github.com/Azure/azure-iotedge/releases) Nelle versioni precedenti è disponibile un subset di funzionalità offline. I dispositivi IoT Edge esistenti che non hanno le funzionalità offline per periodi prolungati non possono essere aggiornati modificando la versione del runtime, ma devono essere riconfigurati con una nuova identità del dispositivo IoT Edge per ottenere queste funzionalità.

Solo i dispositivi non IoT Edge possono essere aggiunti come dispositivi figlio.

I dispositivi IoT Edge e i dispositivi figlio assegnati possono funzionare offline per un tempo indefinito dopo la sincronizzazione una tantera iniziale. Tuttavia, l'archiviazione dei messaggi dipende dall'impostazione TTL (Time to Live) e dallo spazio disponibile su disco per l'archiviazione dei messaggi.

## <a name="set-up-parent-and-child-devices"></a>Configurare i dispositivi padre e figlio

Affinché un dispositivo IoT Edge estenda le funzionalità offline estese ai dispositivi IoT figlio, devi completare due passaggi. Dichiarare innanzitutto le relazioni padre-figlio nel portale di Azure.First, declare the parent-child relationships in the Azure portal. In secondo luogo, creare una relazione di trust tra il dispositivo padre e tutti i dispositivi figlio, quindi configurare le comunicazioni da dispositivo a cloud per passare attraverso il padre come gateway.

### <a name="assign-child-devices"></a>Assegnare i dispositivi figlio

I dispositivi figlio possono essere qualsiasi dispositivo non IoT Edge registrato nello stesso hub IoT.Child devices can be any non-IoT Edge device registered to the same IoT Hub. I dispositivi padre possono avere più dispositivi figlio, ma un dispositivo figlio ha un solo elemento padre. Sono disponibili tre opzioni per impostare i dispositivi figlio su un dispositivo perimetrale: tramite il portale di Azure, usando l'interfaccia della riga di comando di Azure o l'SDK del servizio Hub IoT.There are three options to set child devices to an edge device: through the Azure portal, using the Azure CLI, or using the IoT Hub service SDK.

Le sezioni seguenti forniscono esempi di come è possibile dichiarare la relazione padre/figlio nell'hub IoT per i dispositivi IoT esistenti. Se si creano nuove identità di dispositivo per i dispositivi figlio, vedere [Autenticare un dispositivo downstream](how-to-authenticate-downstream-device.md) nell'hub IoT di Azure per altre informazioni.

#### <a name="option-1-iot-hub-portal"></a>Opzione 1: portale dell'hub IoTOption 1: IoT Hub Portal

È possibile dichiarare la relazione padre-figlio durante la creazione di un nuovo dispositivo. In alternativa, per i dispositivi esistenti, è possibile dichiarare la relazione dalla pagina dei dettagli del dispositivo di linguaggio dispositivo IoT Edge padre o del dispositivo IoT figlio.

   ![Gestire i dispositivi figlio dalla pagina dei dettagli del dispositivo IoT Edge](./media/offline-capabilities/manage-child-devices.png)

#### <a name="option-2-use-the-az-command-line-tool"></a>Opzione 2: `az` Utilizzare lo strumento da riga di comandoOption 2: Use the command-line tool

Usando [l'interfaccia della riga](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) di comando di Azure con [l'estensione IoT](https://github.com/azure/azure-iot-cli-extension) (v0.7.0 o versione precedente), è possibile gestire le relazioni padre-figlio con i sottocomandi [dell'identità](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) del dispositivo. L'esempio seguente usa una query per assegnare tutti i dispositivi non IoT Edge nell'hub come dispositivi figlio di un dispositivo IoT Edge.The example below uses a query to assign all non-IoT Edge devices in the hub to be child devices of an IoT Edge device.

```azurecli
# Set IoT Edge parent device
egde_device="edge-device1"

# Get All IoT Devices
device_list=$(az iot hub query \
        --hub-name replace-with-hub-name \
        --subscription replace-with-sub-name \
        --resource-group replace-with-rg-name \
        -q "SELECT * FROM devices WHERE capabilities.iotEdge = false" \
        --query 'join(`, `, [].deviceId)' -o tsv)

# Add all IoT devices to IoT Edge (as child)
az iot hub device-identity add-children \
  --device-id $egde_device \
  --child-list $device_list \
  --hub-name replace-with-hub-name \
  --resource-group replace-with-rg-name \
  --subscription replace-with-sub-name
```

È possibile modificare la [query](../iot-hub/iot-hub-devguide-query-language.md) per selezionare un sottoinsieme diverso di dispositivi. Il comando potrebbe richiedere alcuni secondi se si specifica un set di dispositivi di grandi dimensioni.

#### <a name="option-3-use-iot-hub-service-sdk"></a>Opzione 3: usare L'SDK del servizio hub IoTOption 3: Use IoT Hub Service SDK

Infine, è possibile gestire le relazioni padre-figlio a livello di codice utilizzando C , Java o Node.js IoT Hub Service SDK. Di seguito è riportato un [esempio di assegnazione di un dispositivo figlio](https://aka.ms/set-child-iot-device-c-sharp) usando l'SDK di C.

### <a name="set-up-the-parent-device-as-a-gateway"></a>Configurare il dispositivo padre come gateway

È possibile considerare una relazione padre/figlio come un gateway trasparente, in cui il dispositivo figlio ha la propria identità nell'hub IoT ma comunica tramite il cloud tramite il relativo elemento padre. Per una comunicazione sicura, il dispositivo figlio deve essere in grado di verificare che provenga da una fonte attendibile. In caso contrario, terze parti potrebbero configurare dispositivi dannosi per impersonare i genitori e intercettare le comunicazioni.

Un modo per creare questa relazione di trust è descritto in dettaglio negli articoli seguenti:One way to create this trust relationship is described in detail in the following articles:

* [Configurare un dispositivo IoT Edge come gateway trasparente](how-to-create-transparent-gateway.md)
* [Connettere un dispositivo downstream (figlio) a un gateway Edge di Azure IoTConnect a downstream (child) device to an Azure IoT Edge gateway](how-to-connect-downstream-device.md)

## <a name="specify-dns-servers"></a>Specificare i server DNS

Per migliorare l'affidabilità, è consigliabile specificare gli indirizzi dei server DNS utilizzati nell'ambiente. Per impostare il server DNS per IoT Edge, vedere la risoluzione per il [modulo dell'agente edge segnala continuamente "file di configurazione vuoto" e nessun modulo viene avviato sul dispositivo](troubleshoot.md#edge-agent-module-continually-reports-empty-config-file-and-no-modules-start-on-the-device) nell'articolo sulla risoluzione dei problemi.

## <a name="optional-offline-settings"></a>Impostazioni facoltative per la modalità offline

Se i dispositivi non sono in linea, il dispositivo padre IoT Edge archivia tutti i messaggi da dispositivo a cloud fino a quando non viene ristabilita la connessione. Il modulo hub IoT Edge gestisce l'archiviazione e l'inoltro dei messaggi offline. Per i dispositivi che possono passare alla modalità offline per lunghi periodi di tempo, ottimizzare le prestazioni configurando due impostazioni dell'hub IoT Edge.For devices that may go offline for extended periods of time, optimize performance by configuring two IoT Edge hub settings.

In primo luogo, aumentare l'impostazione di tempo per la durata in modo che l'hub IoT Edge manterrà i messaggi abbastanza a lungo per il dispositivo di riconnettersi. e quindi aggiungere altro spazio su disco per l'archiviazione dei messaggi.

### <a name="time-to-live"></a>Durata (TTL)

L'impostazione della durata (TTL) è la quantità di tempo (in secondi) di attesa per il recapito di un messaggio prima che scada. Il valore predefinito è 7200 secondi (due ore). Il valore massimo è limitato solo dal valore massimo di una variabile integer, pari a circa 2 miliardi.

Questa impostazione è una proprietà desiderata dell'hub di IoT Edge, che viene archiviata nel modulo gemello. È possibile configurarlo nel portale di Azure o direttamente nel manifesto di distribuzione.

```json
"$edgeHub": {
    "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {},
        "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
        }
    }
}
```

### <a name="host-storage-for-system-modules"></a>Archiviazione host per i moduli di sistema

I messaggi e le informazioni sullo stato del modulo vengono archiviati nel file system del contenitore locale dell'hub Edge IoT per impostazione predefinita. Per una maggiore affidabilità, soprattutto quando si opera offline, è anche possibile dedicare l'archiviazione sul dispositivo IoT Edge host. Per altre informazioni, vedere [Concedere ai moduli l'accesso all'archiviazione locale di un dispositivoFor](how-to-access-host-storage-from-module.md) more information, see Give modules access to a device's local storage

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come configurare un gateway trasparente per le connessioni del dispositivo padre/figlio:Learn more about how to set up a transparent gateway for your parent/child device connections:

* [Configurare un dispositivo IoT Edge come gateway trasparente](how-to-create-transparent-gateway.md)
* [Autenticare un dispositivo downstream sull'hub IoT di Azure](how-to-authenticate-downstream-device.md)
* [Connettere un dispositivo downstream a un gateway Azure IoT Edge](how-to-connect-downstream-device.md)
