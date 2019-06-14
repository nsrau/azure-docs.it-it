---
title: Usare i dispositivi offline - Azure IoT Edge | Microsoft Docs
description: Comprendere come i moduli e dispositivi IoT Edge possano funzionare senza connessione Internet per periodi prolungati di tempo e come IoT Edge possa consentire anche a dispositivi IoT regolari di funzionare offline.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/04/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 4f3e5c1566271573b43e24a1749b42daa7530555
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67051959"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices"></a>Comprendere le funzionalità estese non in linea per i dispositivi IoT Edge, moduli e dispositivi figlio

Azure IoT Edge supporta le operazioni non in linea estese nei dispositivi IoT Edge e consente anche operazioni offline nei dispositivi figlio non IoT Edge. Se un dispositivo IoT Edge ha avuto la possibilità di connettersi una volta all'hub IoT, tale dispositivo e tutti i dispositivi figlio possono continuare a funzionare con una connessione Internet intermittente o senza connessione. 


## <a name="how-it-works"></a>Funzionamento

Quando un dispositivo IoT Edge passa alla modalità offline, l'hub di IoT Edge svolge tre ruoli. Prima di tutto, archivia i messaggi diretti upstream e li salva fino a quando il dispositivo si riconnette. In secondo luogo, agisce per conto dell'hub IoT per l'autenticazione dei moduli e dei dispositivi figlio in modo che possano continuare a funzionare. In terzo luogo, consente le comunicazioni tra i dispositivi figlio che normalmente passerebbero attraverso l'hub IoT. 

L'esempio seguente mostra il funzionamento di uno scenario IoT Edge in modalità offline:

1. **Configurare i dispositivi**

   Per i dispositivi IoT Edge le funzionalità offline vengono abilitate automaticamente. Per estendere tale funzionalità ad altri dispositivi IoT, è necessario dichiarare una relazione padre-figlio tra i dispositivi nell'hub IoT. È quindi possibile configurare i dispositivi figlio per considerare attendibile il dispositivo assegnato padre e indirizzare le comunicazioni da dispositivo a cloud tramite l'elemento padre come gateway. 

2. **Sincronizzazione con l'Hub IoT**

   Almeno una volta dopo l'installazione del runtime di IoT Edge, il dispositivo IoT Edge deve essere online per la sincronizzazione con l'hub IoT. Con questa sincronizzazione, il dispositivo IoT Edge ottiene informazioni dettagliate sugli eventuali dispositivi figlio assegnati. Il dispositivo IoT Edge aggiorna inoltre in modo sicuro la cache locale per abilitare le operazioni offline e recupera le impostazioni per l'archiviazione locale dei messaggi di telemetria. 

3. **Passare alla modalità offline**

   Mentre si è disconnessi dall'hub IoT, il dispositivo IoT Edge, i moduli distribuiti e tutti i dispositivi IoT figlio possono operare a tempo indeterminato. I moduli e i dispositivi figlio possono essere avviati e riavviati eseguendo l'autenticazione con l'hub di IoT Edge mentre è attiva la modalità offline. I dati di telemetria previsti per l'invio upstream all'hub IoT vengono archiviati in locale. La comunicazione tra i moduli o tra i dispositivi IoT figlio viene gestita tramite metodi diretti o messaggi. 

4. **Ristabilire la connessione e risincronizza con l'IoT Hub**

   Dopo il ripristino della connessione con l'hub IoT, il dispositivo IoT Edge viene sincronizzato di nuovo. I messaggi archiviati in locale vengono recapitati nello stesso ordine in cui sono stati archiviati. Eventuali differenze tra le proprietà desiderate e quelle segnalate per i moduli e i dispositivi vengono riconciliate. Il dispositivo IoT Edge aggiorna tutte le modifiche nel set di dispositivi IoT figlio assegnati.

## <a name="restrictions-and-limits"></a>Restrizioni e limiti

Le funzionalità offline per periodi prolungati descritte in questo articolo sono disponibili in [IoT Edge versione 1.0.4 o versioni successive](https://github.com/Azure/azure-iotedge/releases). Nelle versioni precedenti è disponibile un subset di funzionalità offline. I dispositivi IoT Edge esistenti che non hanno le funzionalità offline per periodi prolungati non possono essere aggiornati modificando la versione del runtime, ma devono essere riconfigurati con una nuova identità del dispositivo IoT Edge per ottenere queste funzionalità. 

Il supporto delle funzionalità offline per periodi prolungati è disponibile in tutte le aree in cui è disponibile l'hub IoT, **ad eccezione** di Stati Uniti orientali.

Solo i dispositivi non IoT Edge possono essere aggiunto come dispositivi figlio. 

I dispositivi IoT Edge e i dispositivi figlio assegnati possono funzionare offline per un periodo illimitato dopo la sincronizzazione iniziale, una tantum. L'archiviazione dei messaggi dipende tuttavia dall'impostazione della durata (TTL) e dallo spazio disponibile su disco per archiviare i messaggi. 

## <a name="set-up-parent-and-child-devices"></a>Configurare dispositivi padre e figlio

Per un dispositivo IoT Edge per estenderne le funzionalità offline estese ai dispositivi IoT figlio, è necessario completare due passaggi. Innanzitutto, dichiarare padre-figlio le relazioni nel portale di Azure. In secondo luogo, creare una relazione di trust tra il dispositivo principale e tutti i dispositivi figlio, quindi configurare le comunicazioni da dispositivo a cloud per il passaggio attraverso il padre di un gateway. 

### <a name="assign-child-devices"></a>Assegnare i dispositivi figlio

Dispositivi figlio possono essere qualsiasi dispositivo - IoT Edge registrato nell'IoT Hub stesso. Dispositivi padre possono avere più dispositivi figlio, ma un dispositivo figlio ha solo un elemento padre. Sono disponibili tre opzioni per impostare i dispositivi figlio in un dispositivo perimetrale: tramite il portale di Azure tramite la CLI di Azure o tramite il SDK del servizio IoT Hub. 

Le sezioni seguenti forniscono esempi del modo in cui è possibile dichiarare la relazione padre/figlio nell'IoT Hub per dispositivi IoT esistente. Se si sta creando nuove identità dispositivo per il bambino dispositivi, vedere [autenticare un dispositivo all'IoT Hub di Azure a valle](how-to-authenticate-downstream-device.md) per altre informazioni.

#### <a name="option-1-iot-hub-portal"></a>Opzione 1: Portale dell'Hub IoT

È possibile dichiarare la relazione padre-figlio quando si crea un nuovo dispositivo. O per i dispositivi esistenti, è possibile dichiarare la relazione dalla pagina dei dettagli del dispositivo del padre dispositivo IoT Edge o il dispositivo IoT figlio. 

   ![Gestire i dispositivi figlio dalla pagina dei dettagli del dispositivo IoT Edge](./media/offline-capabilities/manage-child-devices.png)


#### <a name="option-2-use-the-az-command-line-tool"></a>Opzione 2: Usare il `az` lo strumento da riga di comando

Usando il [interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) con [estensione IoT](https://github.com/azure/azure-iot-cli-extension) (v0.7.0 o versione successiva), è possibile gestire relazioni padre-figlio con il [device-identity](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) sottocomandi. L'esempio seguente usa una query per assegnare tutti i dispositivi non IoT Edge nell'hub siano dispositivi figlio di un dispositivo IoT Edge. 

```shell
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

È possibile modificare il [query](../iot-hub/iot-hub-devguide-query-language.md) per selezionare un sottoinsieme diverso di dispositivi. Se si specifica un ampio set di dispositivi, il comando potrebbe richiedere qualche secondo.

#### <a name="option-3-use-iot-hub-service-sdk"></a>Opzione 3: Usare IoT Hub SDK per servizi 

Infine, è possibile gestire relazioni padre-figlio in modo programmatico utilizzando C#, Java o Node. js IoT Hub Service SDK. Di seguito è riportato un [esempio di assegnazione di un dispositivo figlio](https://aka.ms/set-child-iot-device-c-sharp) usando il C# SDK.

### <a name="set-up-the-parent-device-as-a-gateway"></a>Configurare il dispositivo principale come un gateway

È possibile considerare una relazione padre/figlio come gateway trasparente, in cui la periferica figlio ha la propria identità nell'IoT Hub, ma comunica attraverso il cloud tramite il relativo elemento padre. Per proteggere le comunicazioni, il dispositivo figlio deve essere in grado di verificare che il dispositivo padre proviene da una fonte attendibile. In caso contrario, terze parti è stato possibile configurare dispositivi malintenzionati di rappresentare gli elementi padre e intercettare le comunicazioni. 

Un modo per creare la relazione di trust è descritta in dettaglio negli articoli seguenti: 
* [Configurare un dispositivo IoT Edge come gateway trasparente](how-to-create-transparent-gateway.md)
* [Connettere un dispositivo downstream (figlio) a un gateway Azure IoT Edge](how-to-connect-downstream-device.md)

## <a name="specify-dns-servers"></a>Specificare i server DNS 

Migliorare l'affidabilità, è consigliabile che specificare gli indirizzi di server DNS usati nell'ambiente. Visualizzate due opzioni per [impostare il server DNS nell'articolo sulla risoluzione dei problemi](troubleshoot.md#resolution-7).

## <a name="optional-offline-settings"></a>Impostazioni facoltative per la modalità offline

Se i dispositivi disconnessi, il dispositivo di IoT Edge padre archivia tutti i messaggi da dispositivo a cloud fino a quando non viene ristabilita la connessione. Il modulo dell'hub di IoT Edge gestisce l'archiviazione e inoltro di messaggi non in linea. Per i dispositivi che potrebbero passare alla modalità offline per periodi prolungati di tempo, ottimizzare le prestazioni tramite la configurazione di due impostazioni dell'hub di IoT Edge. 

In primo luogo, aumentare il tempo necessario per l'impostazione in tempo reale in modo che l'hub di IoT Edge manterrà i messaggi di tempo sufficiente per il dispositivo ristabilire la connessione. e quindi aggiungere altro spazio su disco per l'archiviazione dei messaggi. 

### <a name="time-to-live"></a>Durata (TTL)

L'impostazione della durata (TTL) è la quantità di tempo (in secondi) di attesa per il recapito di un messaggio prima che scada. Il valore predefinito è 7200 secondi (due ore). Il valore massimo è limitato solo dal valore massimo di una variabile integer, ovvero circa 2 miliardi. 

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

### <a name="additional-offline-storage"></a>Spazio di archiviazione aggiuntivo per la modalità offline

Per impostazione predefinita, i messaggi vengono archiviati nel file system del contenitore dell'hub di IoT Edge. Se tale quantità di spazio di archiviazione non è sufficiente per le esigenze per la modalità offline, è possibile dedicare spazio di archiviazione locale nel dispositivo IoT Edge. Per l'hub di IoT Edge creare una variabile di ambiente che punti a una cartella di archiviazione nel contenitore. Usare quindi le opzioni di creazione per associare tale cartella di archiviazione a una cartella nel computer host. 

È possibile configurare le variabili di ambiente e le opzioni di creazione per il modulo dell'hub di IoT Edge nel portale di Azure, nella sezione **Configura impostazioni avanzate per il runtime di IoT Edge**, oppure direttamente nel manifesto della distribuzione. 

```json
"edgeHub": {
    "type": "docker",
    "settings": {
        "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
        "createOptions": {
            "HostConfig": {
                "Binds": ["<HostStoragePath>:<ModuleStoragePath>"],
                "PortBindings": {
                    "8883/tcp": [{"HostPort":"8883"}],
                    "443/tcp": [{"HostPort":"443"}],
                    "5671/tcp": [{"HostPort":"5671"}]
                }
            }
        }
    },
    "env": {
        "storageFolder": {
            "value": "<ModuleStoragePath>"
        }
    },
    "status": "running",
    "restartPolicy": "always"
}
```

Sostituire `<HostStoragePath>` e `<ModuleStoragePath>` con i percorsi di archiviazione dell'host e del modulo. Entrambi devono essere percorsi assoluti. Nelle opzioni di creazione associare tra loro i percorsi di archiviazione dell'host e del modulo e quindi creare una variabile di ambiente che punti al percorso di archiviazione del modulo.  

Ad esempio, `"Binds":["/etc/iotedge/storage/:/iotedge/storage/"]` indica che la directory **/etc/iotedge/storage** nel sistema host è mappata alla directory **/iotedge/storage/** nel contenitore. Un altro esempio per i sistemi Windows è `"Binds":["C:\\temp:C:\\contemp"]` e indica che la directory **C:\\temp** nel sistema host è mappata alla directory **C:\\contemp** nel contenitore. 

Per informazioni più dettagliate sulle opzioni di creazione, vedere i [documenti su Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate).

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come configurare un gateway trasparente per le connessioni del dispositivo padre/figlio: 

* [Configurare un dispositivo IoT Edge come gateway trasparente](how-to-create-transparent-gateway.md)
* [Autenticare un dispositivo downstream all'IoT Hub di Azure](how-to-authenticate-downstream-device.md)
* [Connettere un dispositivo downstream a un gateway Azure IoT Edge](how-to-connect-downstream-device.md)
