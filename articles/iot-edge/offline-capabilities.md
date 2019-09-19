---
title: Usare i dispositivi offline - Azure IoT Edge | Microsoft Docs
description: Comprendere come i moduli e dispositivi IoT Edge possano funzionare senza connessione Internet per periodi prolungati di tempo e come IoT Edge possa consentire anche a dispositivi IoT regolari di funzionare offline.
author: kgremban
ms.author: kgremban
ms.date: 08/04/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 80a38767121f5c54afe51a7d4d788716fe9547e2
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091353"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices"></a>Informazioni sulle funzionalità estese offline per IoT Edge dispositivi, moduli e dispositivi figlio

Azure IoT Edge supporta le operazioni offline estese nei dispositivi IoT Edge e Abilita le operazioni offline nei dispositivi figlio non IoT Edge. Se un dispositivo IoT Edge ha avuto la possibilità di connettersi una volta all'hub IoT, tale dispositivo e tutti i dispositivi figlio possono continuare a funzionare con una connessione Internet intermittente o senza connessione. 


## <a name="how-it-works"></a>Funzionamento

Quando un dispositivo IoT Edge passa alla modalità offline, l'hub di IoT Edge svolge tre ruoli. Prima di tutto, archivia i messaggi diretti upstream e li salva fino a quando il dispositivo si riconnette. In secondo luogo, agisce per conto dell'hub IoT per l'autenticazione dei moduli e dei dispositivi figlio in modo che possano continuare a funzionare. In terzo luogo, consente le comunicazioni tra i dispositivi figlio che normalmente passerebbero attraverso l'hub IoT. 

L'esempio seguente mostra il funzionamento di uno scenario IoT Edge in modalità offline:

1. **Configurare i dispositivi**

   Per i dispositivi IoT Edge le funzionalità offline vengono abilitate automaticamente. Per estendere tale funzionalità ad altri dispositivi IoT, è necessario dichiarare una relazione padre-figlio tra i dispositivi nell'hub IoT. Configurare quindi i dispositivi figlio in modo che considerino attendibile il dispositivo padre assegnato e instradare le comunicazioni da dispositivo a cloud tramite il padre come gateway. 

2. **Sincronizzare con l'hub Internet**

   Almeno una volta dopo l'installazione del runtime di IoT Edge, il dispositivo IoT Edge deve essere online per la sincronizzazione con l'hub IoT. Con questa sincronizzazione, il dispositivo IoT Edge ottiene informazioni dettagliate sugli eventuali dispositivi figlio assegnati. Il dispositivo IoT Edge aggiorna inoltre in modo sicuro la cache locale per abilitare le operazioni offline e recupera le impostazioni per l'archiviazione locale dei messaggi di telemetria. 

3. **Passa alla modalità offline**

   Mentre si è disconnessi dall'hub IoT, il dispositivo IoT Edge, i moduli distribuiti e tutti i dispositivi IoT figlio possono operare a tempo indeterminato. I moduli e i dispositivi figlio possono essere avviati e riavviati eseguendo l'autenticazione con l'hub di IoT Edge mentre è attiva la modalità offline. I dati di telemetria previsti per l'invio upstream all'hub IoT vengono archiviati in locale. La comunicazione tra i moduli o tra i dispositivi IoT figlio viene gestita tramite metodi diretti o messaggi. 

4. **Riconnettersi e risincronizzare con l'hub Internet delle cose**

   Dopo il ripristino della connessione con l'hub IoT, il dispositivo IoT Edge viene sincronizzato di nuovo. I messaggi archiviati in locale vengono recapitati nello stesso ordine in cui sono stati archiviati. Eventuali differenze tra le proprietà desiderate e quelle segnalate per i moduli e i dispositivi vengono riconciliate. Il dispositivo IoT Edge aggiorna tutte le modifiche nel set di dispositivi IoT figlio assegnati.

## <a name="restrictions-and-limits"></a>Restrizioni e limiti

Le funzionalità estese offline descritte in questo articolo sono disponibili in [IOT Edge versione 1.0.7 o successiva](https://github.com/Azure/azure-iotedge/releases). Nelle versioni precedenti è disponibile un subset di funzionalità offline. I dispositivi IoT Edge esistenti che non hanno le funzionalità offline per periodi prolungati non possono essere aggiornati modificando la versione del runtime, ma devono essere riconfigurati con una nuova identità del dispositivo IoT Edge per ottenere queste funzionalità. 

Il supporto delle funzionalità offline per periodi prolungati è disponibile in tutte le aree in cui è disponibile l'hub IoT, **ad eccezione** di Stati Uniti orientali.

Solo i dispositivi non IoT Edge possono essere aggiunti come dispositivi figlio. 

I dispositivi IoT Edge e i dispositivi figlio assegnati possono funzionare offline per un periodo illimitato dopo la sincronizzazione iniziale, una tantum. L'archiviazione dei messaggi dipende tuttavia dall'impostazione della durata (TTL) e dallo spazio disponibile su disco per archiviare i messaggi. 

## <a name="set-up-parent-and-child-devices"></a>Configurare dispositivi padre e figlio

Affinché un dispositivo IoT Edge estenda le funzionalità estese offline ai dispositivi figlio, è necessario completare due passaggi. Dichiarare innanzitutto le relazioni padre-figlio nell'portale di Azure. In secondo luogo, creare una relazione di trust tra il dispositivo padre e tutti i dispositivi figlio, quindi configurare le comunicazioni da dispositivo a cloud per passare attraverso l'elemento padre come gateway. 

### <a name="assign-child-devices"></a>Assegnare i dispositivi figlio

I dispositivi figlio possono essere qualsiasi dispositivo non IoT Edge registrato nello stesso hub Internet. I dispositivi padre possono avere più dispositivi figlio, ma un dispositivo figlio ha un solo elemento padre. Sono disponibili tre opzioni per impostare i dispositivi figlio su un dispositivo perimetrale: tramite la portale di Azure, usando l'interfaccia della riga di comando di Azure o l'SDK del servizio hub Internet. 

Nelle sezioni seguenti vengono forniti esempi di come è possibile dichiarare la relazione padre/figlio nell'hub Internet per i dispositivi esistenti. Se si stanno creando nuove identità del dispositivo per i dispositivi figlio, vedere [autenticare un dispositivo downstream nell'hub di Azure](how-to-authenticate-downstream-device.md) . per altre informazioni.

#### <a name="option-1-iot-hub-portal"></a>Opzione 1: Portale hub Internet

È possibile dichiarare la relazione padre-figlio quando si crea un nuovo dispositivo. In alternativa, per i dispositivi esistenti è possibile dichiarare la relazione dalla pagina Dettagli dispositivo del dispositivo IoT Edge padre o del dispositivo figlio. 

   ![Gestire i dispositivi figlio dalla pagina dei dettagli del dispositivo IoT Edge](./media/offline-capabilities/manage-child-devices.png)


#### <a name="option-2-use-the-az-command-line-tool"></a>Opzione 2: Usare lo `az` strumento da riga di comando

Usando l' [interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) con l' [estensione](https://github.com/azure/azure-iot-cli-extension) Internet (v 0.7.0 o versione successiva), è possibile gestire le relazioni padre-figlio con i sottocomandi [Device-Identity](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) . Nell'esempio seguente viene usata una query per assegnare tutti i dispositivi non IoT Edge nell'hub come dispositivi figlio di un dispositivo IoT Edge. 

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

È possibile modificare la [query](../iot-hub/iot-hub-devguide-query-language.md) per selezionare un subset diverso di dispositivi. Se si specifica un ampio set di dispositivi, il comando può richiedere alcuni secondi.

#### <a name="option-3-use-iot-hub-service-sdk"></a>Opzione 3: Usare l'SDK del servizio dell'hub Internet 

Infine, è possibile gestire le relazioni padre-figlio a livello C#di codice usando, Java o node. js Internet SDK per servizi. Di seguito è [riportato un esempio di assegnazione di un dispositivo figlio](https://aka.ms/set-child-iot-device-c-sharp) tramite l' C# SDK.

### <a name="set-up-the-parent-device-as-a-gateway"></a>Configurare il dispositivo padre come gateway

È possibile pensare a una relazione padre/figlio come gateway trasparente, in cui il dispositivo figlio ha una propria identità nell'hub degli elementi del tutto ma comunica attraverso il cloud tramite il relativo padre. Per la comunicazione protetta, il dispositivo figlio deve essere in grado di verificare che il dispositivo padre provenga da una fonte attendibile. In caso contrario, terze parti potrebbero configurare dispositivi dannosi per rappresentare gli elementi padre e intercettare le comunicazioni. 

Un modo per creare questa relazione di trust è descritto in dettaglio negli articoli seguenti: 
* [Configurare un dispositivo IoT Edge come gateway trasparente](how-to-create-transparent-gateway.md)
* [Connettere un dispositivo downstream (figlio) a un gateway Azure IoT Edge](how-to-connect-downstream-device.md)

## <a name="specify-dns-servers"></a>Specificare i server DNS 

Per migliorare l'affidabilità, è consigliabile specificare gli indirizzi del server DNS utilizzati nell'ambiente in uso. Per impostare il server DNS per IoT Edge, vedere la risoluzione del [modulo dell'agente perimetrale segnala continuamente ' file di configurazione vuoto ' e nessun modulo viene avviato nel dispositivo](troubleshoot.md#edge-agent-module-continually-reports-empty-config-file-and-no-modules-start-on-the-device) nell'articolo sulla risoluzione dei problemi.

## <a name="optional-offline-settings"></a>Impostazioni facoltative per la modalità offline

Se i dispositivi passano offline, il IoT Edge dispositivo padre archivia tutti i messaggi da dispositivo a cloud fino a quando non viene ristabilita la connessione. Il modulo Hub IoT Edge gestisce l'archiviazione e l'invio di messaggi offline. Per i dispositivi che possono andare offline per periodi prolungati di tempo, ottimizzare le prestazioni configurando due IoT Edge impostazioni dell'hub. 

In primo luogo, aumentare l'impostazione durata (TTL), in modo che l'hub IoT Edge mantenga i messaggi abbastanza a lungo per la riconnessione del dispositivo. e quindi aggiungere altro spazio su disco per l'archiviazione dei messaggi. 

### <a name="time-to-live"></a>Durata (TTL)

L'impostazione della durata (TTL) è la quantità di tempo (in secondi) di attesa per il recapito di un messaggio prima che scada. Il valore predefinito è 7200 secondi (due ore). Il valore massimo è limitato solo dal valore massimo di una variabile Integer, che è circa 2 miliardi. 

Questa impostazione è una proprietà desiderata dell'hub di IoT Edge, che viene archiviata nel modulo gemello. È possibile configurarlo nel portale di Azure o direttamente nel manifesto della distribuzione. 

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

I messaggi e le informazioni sullo stato del modulo vengono archiviati nel file System del contenitore locale dell'hub IoT Edge per impostazione predefinita. Per una maggiore affidabilità, soprattutto quando si opera offline, è anche possibile dedicare spazio di archiviazione nell'host IoT Edge dispositivo.

Per configurare l'archiviazione nel sistema host, creare le variabili di ambiente per l'hub IoT Edge e IoT Edge Agent che puntano a una cartella di archiviazione nel contenitore. Usare quindi le opzioni di creazione per associare tale cartella di archiviazione a una cartella nel computer host. 

È possibile configurare le variabili di ambiente e le opzioni di creazione per il modulo dell'hub di IoT Edge nel portale di Azure, nella sezione **Configura impostazioni avanzate per il runtime di IoT Edge**, 

1. Per entrambi IoT Edge Hub e IoT Edge Agent, aggiungere una variabile di ambiente denominata **StorageFolder** che punta a una directory del modulo.
1. Per IoT Edge Hub e IoT Edge Agent, aggiungere binding per connettere una directory locale nel computer host a una directory del modulo. Esempio: 

   ![Aggiungere le opzioni di creazione e le variabili di ambiente per l'archiviazione locale](./media/offline-capabilities/offline-storage.png)

In alternativa, è possibile configurare l'archiviazione locale direttamente nel manifesto della distribuzione. Esempio: 

```json
"systemModules": {
    "edgeAgent": {
        "settings": {
            "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
            "createOptions": {
                "HostConfig": {
                    "Binds":["<HostStoragePath>:<ModuleStoragePath>"]
                }
            }
        },
        "type": "docker",
        "env": {
            "storageFolder": {
                "value": "<ModuleStoragePath>"
            }
        }
    },
    "edgeHub": {
        "settings": {
            "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
            "createOptions": {
                "HostConfig": {
                    "Binds":["<HostStoragePath>:<ModuleStoragePath>"],
                    "PortBindings":{"5671/tcp":[{"HostPort":"5671"}],"8883/tcp":[{"HostPort":"8883"}],"443/tcp":[{"HostPort":"443"}]}}}
        },
        "type": "docker",
        "env": {
            "storageFolder": {
                "value": "<ModuleStoragePath>"
            }
        },
        "status": "running",
        "restartPolicy": "always"
    }
}
```

Sostituire `<HostStoragePath>` e`<ModuleStoragePath>` con il percorso di archiviazione dell'host e del modulo. entrambi i valori devono essere un percorso assoluto. 

Ad esempio, `"Binds":["/etc/iotedge/storage/:/iotedge/storage/"]` indica che la directory **/etc/iotedge/storage** nel sistema host è mappata alla directory **/iotedge/storage/** nel contenitore. Un altro esempio per i sistemi Windows è `"Binds":["C:\\temp:C:\\contemp"]` e indica che la directory **C:\\temp** nel sistema host è mappata alla directory **C:\\contemp** nel contenitore. 

Nei dispositivi Linux, verificare che il profilo utente dell'hub IoT Edge, UID 1000, disponga delle autorizzazioni di lettura, scrittura ed esecuzione per la directory del sistema host. Queste autorizzazioni sono necessarie in modo che l'hub IoT Edge possa archiviare i messaggi nella directory e recuperarli in un secondo momento. L'agente IoT Edge funziona come root, quindi non necessita di autorizzazioni aggiuntive. Sono disponibili diversi modi per gestire le autorizzazioni di directory nei sistemi Linux, `chown` incluso l'utilizzo di per modificare il `chmod` proprietario della directory e quindi modificare le autorizzazioni. Esempio:

```bash
sudo chown 1000 <HostStoragePath>
sudo chmod 700 <HostStoragePath>
```

Per altre informazioni su come creare opzioni, vedere la [documentazione di Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate).

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come configurare un gateway trasparente per le connessioni del dispositivo padre/figlio: 

* [Configurare un dispositivo IoT Edge come gateway trasparente](how-to-create-transparent-gateway.md)
* [Autenticare un dispositivo downstream sull'hub IoT di Azure](how-to-authenticate-downstream-device.md)
* [Connettere un dispositivo downstream a un gateway Azure IoT Edge](how-to-connect-downstream-device.md)
