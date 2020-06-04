---
title: Usare i dispositivi offline - Azure IoT Edge | Microsoft Docs
description: Comprendere come i moduli e dispositivi IoT Edge possano funzionare senza connessione Internet per periodi prolungati di tempo e come IoT Edge possa consentire anche a dispositivi IoT regolari di funzionare offline.
author: kgremban
ms.author: kgremban
ms.date: 11/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ef6ed74149f106b801049da429dfe7b79b984a70
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83725245"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices"></a>Informazioni sulle funzionalità per periodi offline prolungati per i dispositivi IoT Edge, i moduli e i dispositivi figlio

Azure IoT Edge supporta operazioni offline per periodi prolungati nei dispositivi IoT Edge e consente le operazioni offline anche nei dispositivi figlio non IoT Edge. Se un dispositivo IoT Edge ha avuto la possibilità di connettersi una volta all'hub IoT, tale dispositivo e tutti i dispositivi figlio possono continuare a funzionare con una connessione Internet intermittente o senza connessione.

## <a name="how-it-works"></a>Funzionamento

Quando un dispositivo IoT Edge passa alla modalità offline, l'hub di IoT Edge svolge tre ruoli. Prima di tutto, archivia i messaggi diretti upstream e li salva fino a quando il dispositivo si riconnette. In secondo luogo, agisce per conto dell'hub IoT per l'autenticazione dei moduli e dei dispositivi figlio in modo che possano continuare a funzionare. In terzo luogo, consente le comunicazioni tra i dispositivi figlio che normalmente passerebbero attraverso l'hub IoT.

L'esempio seguente mostra il funzionamento di uno scenario IoT Edge in modalità offline:

1. **Configurare i dispositivi**

   Per i dispositivi IoT Edge le funzionalità offline vengono abilitate automaticamente. Per estendere tale funzionalità ad altri dispositivi IoT, è necessario dichiarare una relazione padre-figlio tra i dispositivi nell'hub IoT. Configurare quindi i dispositivi figlio in modo che considerino attendibile il dispositivo padre assegnato e instradare le comunicazioni da dispositivo a cloud attraverso il padre che funge da gateway.

2. **Eseguire la sincronizzazione con l'hub IoT**

   Almeno una volta dopo l'installazione del runtime di IoT Edge, il dispositivo IoT Edge deve essere online per la sincronizzazione con l'hub IoT. Con questa sincronizzazione, il dispositivo IoT Edge ottiene informazioni dettagliate sugli eventuali dispositivi figlio assegnati. Il dispositivo IoT Edge aggiorna inoltre in modo sicuro la cache locale per abilitare le operazioni offline e recupera le impostazioni per l'archiviazione locale dei messaggi di telemetria.

3. **Disconnettersi**

   Mentre si è disconnessi dall'hub IoT, il dispositivo IoT Edge, i moduli distribuiti e tutti i dispositivi IoT figlio possono operare a tempo indeterminato. I moduli e i dispositivi figlio possono essere avviati e riavviati eseguendo l'autenticazione con l'hub di IoT Edge mentre è attiva la modalità offline. I dati di telemetria previsti per l'invio upstream all'hub IoT vengono archiviati in locale. La comunicazione tra i moduli o tra i dispositivi IoT figlio viene gestita tramite metodi diretti o messaggi.

4. **Ristabilire la connessione e ripetere la sincronizzazione con l'hub IoT**

   Dopo il ripristino della connessione con l'hub IoT, il dispositivo IoT Edge viene sincronizzato di nuovo. I messaggi archiviati in locale vengono recapitati immediatamente all'hub IoT, ma dipendono dalla velocità della connessione, dalla latenza dell'hub IoT e da fattori correlati. Vengono recapitati nello stesso ordine in cui sono stati archiviati.

   Eventuali differenze tra le proprietà desiderate e quelle segnalate per i moduli e i dispositivi vengono riconciliate. Il dispositivo IoT Edge aggiorna tutte le modifiche nel set di dispositivi IoT figlio assegnati.

## <a name="restrictions-and-limits"></a>Restrizioni e limiti

Le funzionalità offline per periodi prolungati descritte in questo articolo sono disponibili in [IoT Edge versione 1.0.7 o versioni successive](https://github.com/Azure/azure-iotedge/releases). Nelle versioni precedenti è disponibile un subset di funzionalità offline. I dispositivi IoT Edge esistenti che non hanno le funzionalità offline per periodi prolungati non possono essere aggiornati modificando la versione del runtime, ma devono essere riconfigurati con una nuova identità del dispositivo IoT Edge per ottenere queste funzionalità.

Solo i dispositivi non IoT Edge possono essere aggiunti come dispositivi figlio.

I dispositivi IoT Edge e i dispositivi figlio assegnati possono funzionare offline per un periodo illimitato dopo la sincronizzazione iniziale, una tantum. L'archiviazione dei messaggi dipende tuttavia dall'impostazione della durata (TTL) e dallo spazio disponibile su disco per archiviare i messaggi.

## <a name="set-up-parent-and-child-devices"></a>Configurare i dispositivi padre e figlio

Per estendere le funzionalità offline per periodi prolungati ai dispositivi IoT figlio di un dispositivo IoT Edge, sono necessari due passaggi. Prima di tutto, dichiarare le relazioni padre-figlio nel portale di Azure. In secondo luogo, creare una relazione di trust tra il dispositivo padre e i dispositivi figlio e quindi configurare le comunicazioni da dispositivo a cloud affinché passino attraverso il padre che funge da gateway.

### <a name="assign-child-devices"></a>Assegnare i dispositivi figlio

Un dispositivo figlio può essere qualsiasi dispositivo non IoT Edge registrato nello stesso hub IoT. I dispositivi padre possono avere più dispositivi figlio, ma un dispositivo figlio può avere un solo padre. Sono disponibili tre opzioni per impostare i dispositivi figlio per un dispositivo perimetrale: tramite il portale di Azure, usando l'interfaccia della riga di comando di Azure o usando l'SDK del servizio hub IoT.

Nelle sezioni seguenti vengono forniti esempi di come dichiarare la relazione padre/figlio nell'hub IoT per i dispositivi IoT esistenti. Se si stanno creando nuove identità del dispositivo per i dispositivi figlio, vedere [Autenticare un dispositivo downstream sull'hub IoT di Azure](how-to-authenticate-downstream-device.md) per maggiori informazioni.

#### <a name="option-1-iot-hub-portal"></a>Opzione 1: Portale dell'hub IoT

È possibile dichiarare la relazione padre-figlio quando si crea un nuovo dispositivo. Per i dispositivi esistenti è possibile dichiarare la relazione dalla pagina dei dettagli del dispositivo IoT Edge padre o del dispositivo IoT figlio.

   ![Gestire i dispositivi figlio dalla pagina dei dettagli del dispositivo IoT Edge](./media/offline-capabilities/manage-child-devices.png)

#### <a name="option-2-use-the-az-command-line-tool"></a>Opzione 2: Usare lo strumento da riga di comando `az`

Usando l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) con l'[estensione IoT](https://github.com/azure/azure-iot-cli-extension) (v0.7.0 o più recente), è possibile gestire le relazioni padre-figlio con i sottocomandi [device-identity](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity?view=azure-cli-latest). L'esempio seguente usa una query per assegnare tutti i dispositivi non IoT Edge nell'hub come dispositivi figlio di un dispositivo IoT Edge.

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

È possibile modificare la [query](../iot-hub/iot-hub-devguide-query-language.md) per selezionare un subset diverso di dispositivi. Se si specifica un set di dispositivi di grandi dimensioni, il comando può richiedere alcuni secondi.

#### <a name="option-3-use-iot-hub-service-sdk"></a>Opzione 3: Usare l'SDK del servizio hub IoT

Infine, è possibile gestire le relazioni padre-figlio a livello di codice tramite l'SDK del servizio hub IoT C#, Java o Node.js. Ecco un [esempio di assegnazione di un dispositivo figlio](https://aka.ms/set-child-iot-device-c-sharp) con l'SDK C#.

### <a name="set-up-the-parent-device-as-a-gateway"></a>Configurare il dispositivo padre come gateway

È possibile pensare a una relazione padre/figlio come a un gateway trasparente, in cui il dispositivo figlio ha una propria identità nell'hub IoT ma comunica attraverso il cloud tramite il relativo padre. Per garantire la sicurezza della comunicazione, il dispositivo figlio deve poter verificare che il dispositivo padre provenga da una fonte attendibile. In caso contrario, terze parti potrebbero configurare dispositivi dannosi per rappresentare i dispositivi padre e intercettare le comunicazioni.

Un modo per creare questa relazione di trust è descritto in dettaglio negli articoli seguenti:

* [Configurare un dispositivo IoT Edge come gateway trasparente](how-to-create-transparent-gateway.md)
* [Connettere un dispositivo downstream (figlio) a un gateway Azure IoT Edge](how-to-connect-downstream-device.md)

## <a name="specify-dns-servers"></a>Specificare i server DNS

Per migliorare l'affidabilità, è consigliabile specificare gli indirizzi dei server DNS usati nell'ambiente. Per impostare il server DNS per IoT Edge, vedere la risoluzione per [Il modulo dell'agente Edge segnala il file di configurazione vuoto e non vengono avviati moduli nel dispositivo](troubleshoot-common-errors.md#edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device) nell'articolo sulla risoluzione dei problemi.

## <a name="optional-offline-settings"></a>Impostazioni facoltative per la modalità offline

Se i dispositivi passano offline, il dispositivo padre IoT Edge archivia tutti i messaggi da dispositivo a cloud fino a quando non viene ristabilita la connessione. Il modulo dell'hub di IoT Edge gestisce l'archiviazione e l'inoltro di messaggi offline. Per i dispositivi che possono passare offline per periodi di tempo prolungati, ottimizzare le prestazioni configurando due impostazioni dell'hub di IoT Edge.

In primo luogo, aumentare l'impostazione della durata (TTL), in modo che l'hub di IoT Edge mantenga i messaggi abbastanza a lungo per consentire la riconnessione del dispositivo. Aggiungere quindi altro spazio su disco per l'archiviazione dei messaggi.

### <a name="time-to-live"></a>Durata (TTL)

L'impostazione della durata (TTL) è la quantità di tempo (in secondi) di attesa per il recapito di un messaggio prima che scada. Il valore predefinito è 7200 secondi (due ore). Il valore massimo è limitato solo dal valore massimo di una variabile di tipo Integer, che è di circa 2 miliardi.

Questa impostazione è una proprietà desiderata dell'hub di IoT Edge, che viene archiviata nel modulo gemello. È possibile eseguire la configurazione nel portale di Azure o direttamente nel manifesto della distribuzione.

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

Per impostazione predefinita, i messaggi e le informazioni sullo stato dei moduli vengono archiviati nel file system del contenitore locale dell'hub di IoT Edge. Per una maggiore affidabilità, soprattutto quando si opera offline, è anche possibile dedicare spazio di archiviazione nel dispositivo IoT Edge host. Per altre informazioni, vedere [Consentire ai moduli di accedere all'archiviazione locale di un dispositivo](how-to-access-host-storage-from-module.md)

## <a name="next-steps"></a>Passaggi successivi

Vedere altre informazioni su come configurare un gateway trasparente per le connessioni dei dispositivi padre/figlio:

* [Configurare un dispositivo IoT Edge come gateway trasparente](how-to-create-transparent-gateway.md)
* [Autenticare un dispositivo downstream sull'hub IoT di Azure](how-to-authenticate-downstream-device.md)
* [Connettere un dispositivo downstream a un gateway Azure IoT Edge](how-to-connect-downstream-device.md)
