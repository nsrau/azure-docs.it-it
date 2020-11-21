---
title: Telemetria e registrazione per i contenitori di analisi spaziale
titleSuffix: Azure Cognitive Services
description: L'analisi spaziale fornisce ogni contenitore con informazioni dettagliate, registrazione e impostazioni di sicurezza comuni del Framework di configurazione.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 09/11/2020
ms.author: aahi
ms.openlocfilehash: dd1b6d216f6225a13d86aa2435b5b1c807547ec3
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95014578"
---
# <a name="telemetry-and-troubleshooting"></a>Telemetria e risoluzione dei problemi

L'analisi spaziale include un set di funzionalità per monitorare l'integrità del sistema e diagnosticare i problemi.

## <a name="enable-visualizations"></a>Abilita visualizzazioni

Per abilitare una visualizzazione degli eventi di intelligenza artificiale in un frame video, è necessario usare la `.debug` versione di un' [operazione di analisi spaziale](spatial-analysis-operations.md) in un computer desktop. La visualizzazione non è possibile nei dispositivi Azure Stack Edge. Sono disponibili quattro operazioni di debug.

Se il dispositivo non è un dispositivo Azure Stack Edge, modificare il file manifesto di distribuzione per i [computer desktop](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/ComputerVision/spatial-analysis/DeploymentManifest_for_non_ASE_devices.json) in modo da usare il valore corretto per la `DISPLAY` variabile di ambiente. Deve corrispondere alla `$DISPLAY` variabile nel computer host. Dopo aver aggiornato il manifesto di distribuzione, ridistribuire il contenitore.

Al termine della distribuzione, potrebbe essere necessario copiare il `.Xauthority` file dal computer host al contenitore e riavviarlo. Nell'esempio seguente `peopleanalytics` è il nome del contenitore nel computer host.

```bash
sudo docker cp $XAUTHORITY peopleanalytics:/root/.Xauthority
sudo docker stop peopleanalytics
sudo docker start peopleanalytics
xhost +
```


## <a name="collect-system-health-telemetry"></a>Raccolta dati di telemetria sull'integrità del sistema

Telegraf è un'immagine open source che funziona con l'analisi spaziale ed è disponibile nel Container Registry Microsoft. Accetta gli input seguenti e li invia a monitoraggio di Azure. Il modulo Telegraf può essere compilato con gli input e gli output personalizzati desiderati. La configurazione del modulo Telegraf nell'analisi spaziale fa parte del manifesto di distribuzione (collegato in precedenza). Questo modulo è facoltativo e può essere rimosso dal manifesto, se non è necessario. 

Input: 
1. Metriche di analisi spaziale
2. Metriche del disco
3. Metriche CPU
4. Metriche di Docker
5. Metriche GPU

Output:
1. Monitoraggio di Azure

Il modulo Telegraf di analisi spaziale fornito pubblicherà tutti i dati di telemetria emessi dal contenitore di analisi spaziale in monitoraggio di Azure. Per informazioni sull'aggiunta di monitoraggio di Azure alla sottoscrizione, vedere [monitoraggio di Azure](../../azure-monitor/overview.md) .

Dopo aver configurato monitoraggio di Azure, è necessario creare le credenziali che consentono al modulo di inviare i dati di telemetria. È possibile usare il portale di Azure per creare una nuova entità servizio oppure usare il comando dell'interfaccia della riga di comando di Azure seguente per crearne uno.

> [!NOTE] 
> Per questo comando è necessario disporre dei privilegi di proprietario per la sottoscrizione. 

```bash
# Find your Azure IoT Hub resource ID by running this command. The resource ID  should start with something like 
# "/subscriptions/b60d6458-1234-4be4-9885-c7e73af9ced8/resourceGroups/...”
az iot hub list

# Create a Service Principal with `Monitoring Metrics Publisher` role in the IoTHub resource:
# Save the output from this command. The values will be used in the deployment manifest. The password won't be shown again so make sure to write it down
az ad sp create-for-rbac --role="Monitoring Metrics Publisher" --name "<principal name>" --scopes="<resource ID of IoT Hub>"
```

Nel manifesto di distribuzione per il [dispositivo Azure stack Edge](https://go.microsoft.com/fwlink/?linkid=2142179) o un altro [computer desktop](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/ComputerVision/spatial-analysis/DeploymentManifest_for_non_ASE_devices.json), cercare il modulo *Telegraf* e sostituire i valori seguenti con le informazioni sull'entità servizio del passaggio precedente e ridistribuire.

```json

"telegraf": { 
  "settings": {
  "image":   "mcr.microsoft.com/azure-cognitive-services/vision/spatial-analysis/telegraf:1.0",
  "createOptions":   "{\"HostConfig\":{\"Runtime\":\"nvidia\",\"NetworkMode\":\"azure-iot-edge\",\"Memory\":33554432,\"Binds\":[\"/var/run/docker.sock:/var/run/docker.sock\"]}}"
},
"type": "docker",
"env": {
    "AZURE_TENANT_ID": {
        "value": "<Tenant Id>"
    },
    "AZURE_CLIENT_ID": {
        "value": "Application Id"
    },
    "AZURE_CLIENT_SECRET": {
        "value": "<Password>"
    },
    "region": {
        "value": "<Region>"
    },
    "resource_id": {
        "value": "/subscriptions/{subscriptionId}/resourceGroups/{resoureGroupName}/providers/Microsoft.Devices/IotHubs/{IotHub}"
    },
...
```

Una volta distribuito il modulo Telegraf, è possibile accedere alle metriche segnalate tramite il servizio monitoraggio di Azure oppure selezionando il **monitoraggio** nell'hub Internet delle cose nel portale di Azure.

:::image type="content" source="./media/spatial-analysis/iot-hub-telemetry.png" alt-text="Report di telemetria di monitoraggio di Azure":::

### <a name="system-health-events"></a>Eventi di integrità del sistema

| Nome evento | Descrizione|
|------|---------|
|archon_exit    |Inviato quando un utente modifica lo stato del modulo di analisi spaziale da *in esecuzione* a *arrestato*.  |
|archon_error   |Inviato quando uno dei processi all'interno del contenitore si arresta in modo anomalo. Si tratta di un errore critico.  |
|InputRate  |Velocità con cui il grafico elabora l'input video. Segnalato ogni 5 minuti. | 
|OutputRate     |Velocità con cui il grafo genera informazioni dettagliate di intelligenza artificiale. Segnalato ogni 5 minuti. |
|archon_allGraphsStarted | Inviato al termine dell'avvio di tutti i grafici. |
|archon_configchange    | Inviato quando viene modificata una configurazione del grafo. |
|archon_graphCreationFailed     |Inviato quando il grafico con l'oggetto restituito `graphId` non viene avviato. |
|archon_graphCreationSuccess    |Inviato quando il grafo con l'oggetto riportato `graphId` viene avviato correttamente. |
|archon_graphCleanup    | Inviato quando il grafico con il segnalato viene `graphId` pulito e chiuso. |
|archon_graphHeartbeat  |Heartbeat inviato ogni minuto per ogni grafico di una competenza. |
|archon_apiKeyAuthFail |Inviato quando la chiave della risorsa Visione artificiale non riesce ad autenticare il contenitore per più di 24 ore, a causa dei motivi seguenti: fuori quota, non valido, offline. |
|VideoIngesterHeartbeat     |Inviato ogni ora per indicare che il video è trasmesso dall'origine video, con il numero di errori in quell'ora. Segnalato per ogni grafico. |
|VideoIngesterState | Report *interrotti* o *avviati* per lo streaming video. Segnalato per ogni grafico. |

##  <a name="troubleshooting-an-iot-edge-device"></a>Risoluzione dei problemi relativi a un dispositivo IoT Edge

È possibile usare `iotedge` lo strumento da riga di comando per controllare lo stato e i log dei moduli in esecuzione. Ad esempio:
* `iotedge list`: Riporta un elenco di moduli in esecuzione. 
  È possibile verificare ulteriormente la presenza di errori con `iotedge logs edgeAgent` . Se si `iotedge` blocca, è possibile provare a riavviarlo con `iotedge restart edgeAgent`
* `iotedge logs <module-name>`
* `iotedge restart <module-name>` per riavviare un modulo specifico 

## <a name="collect-log-files-with-the-diagnostics-container"></a>Raccogliere i file di log con il contenitore di diagnostica

L'analisi spaziale genera log di debug Docker che è possibile usare per diagnosticare i problemi di runtime o includere nei ticket di supporto. Il modulo di diagnostica di analisi spaziale è disponibile nel Container Registry Microsoft da scaricare. Nel file di distribuzione del manifesto per il [dispositivo Azure stack Edge](https://go.microsoft.com/fwlink/?linkid=2142179) o un altro [computer desktop](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/ComputerVision/spatial-analysis/DeploymentManifest_for_non_ASE_devices.json), cercare il modulo *diagnostica* .

Nella sezione "ENV" aggiungere la configurazione seguente:

```json
"diagnostics": {  
  "settings": {
  "image":   "mcr.microsoft.com/azure-cognitive-services/vision/spatial-analysis/diagnostics:1.0",
  "createOptions":   "{\"HostConfig\":{\"Mounts\":[{\"Target\":\"/usr/bin/docker\",\"Source\":\"/home/data/docker\",\"Type\":\"bind\"},{\"Target\":\"/var/run\",\"Source\":\"/run\",\"Type\":\"bind\"}],\"LogConfig\":{\"Config\":{\"max-size\":\"500m\"}}}}"
  }
```    

Per ottimizzare i log caricati in un endpoint remoto, ad esempio archiviazione BLOB di Azure, è consigliabile mantenere una dimensione di file ridotta. Vedere l'esempio seguente per la configurazione consigliata per i log di Docker.

```json
{
    "HostConfig": {
        "LogConfig": {
            "Config": {
                "max-size": "500m",
                "max-file": "1000"
            }
        }
    }
}
```

### <a name="configure-the-log-level"></a>Configurare il livello di registrazione

La configurazione a livello di log consente di controllare il livello di dettaglio dei log generati. I livelli di log supportati sono: `none` ,, `verbose` `info` , `warning` e `error` . Il livello di dettaglio del log predefinito per i nodi e la piattaforma è `info` . 

I livelli di registrazione possono essere modificati globalmente impostando la `ARCHON_LOG_LEVEL` variabile di ambiente su uno dei valori consentiti.
Può anche essere impostato tramite il documento di IoT Edge modulo gemello a livello globale, per tutte le competenze distribuite o per ogni competenza specifica impostando i valori per `platformLogLevel` e `nodeLogLevel` come illustrato di seguito.

```json
{
    "version": 1,
    "properties": {
        "desired": {
            "globalSettings": {
                "platformLogLevel": "verbose"
            },
            "graphs": {
                "samplegraph": {
                    "nodeLogLevel": "verbose",
                    "platformLogLevel": "verbose"
                }
            }
        }
    }
}
```

### <a name="collecting-logs"></a>Raccolta di log

> [!NOTE]
> Il `diagnostics` modulo non influisce sul contenuto di registrazione, ma solo per la raccolta, il filtraggio e il caricamento dei log esistenti.
> Per usare questo modulo, è necessario avere l'API Docker versione 1,40 o successiva.

Il file manifesto di distribuzione di esempio per il [dispositivo Azure stack Edge](https://go.microsoft.com/fwlink/?linkid=2142179) o un altro [computer desktop](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/ComputerVision/spatial-analysis/DeploymentManifest_for_non_ASE_devices.json)  include un modulo denominato `diagnostics` che raccoglie e carica i log. Questo modulo è disabilitato per impostazione predefinita e deve essere abilitato tramite la configurazione del modulo IoT Edge quando è necessario accedere ai log. 

La `diagnostics` raccolta è su richiesta e controllata tramite un metodo diretto IOT Edge e può inviare i log a un archivio BLOB di Azure.

### <a name="configure-diagnostics-upload-targets"></a>Configurare le destinazioni di caricamento di diagnostica

Dal portale di IoT Edge selezionare il dispositivo e quindi il modulo di **diagnostica** . Nel file manifesto di distribuzione di esempio per il [dispositivo Azure stack Edge](https://go.microsoft.com/fwlink/?linkid=2142179) o altri [computer desktop](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/ComputerVision/spatial-analysis/DeploymentManifest_for_non_ASE_devices.json), cercare la sezione **variabili di ambiente** per diagnostica, denominata `env` e aggiungere le informazioni seguenti:

**Configurare il caricamento nell'archiviazione BLOB di Azure**

1. Se non è già stato fatto, creare un account di archiviazione BLOB di Azure.
2. Ottenere la **stringa di connessione** per l'account di archiviazione dal portale di Azure. Che verrà individuato in **chiavi di accesso**.
3. I log di analisi spaziale verranno caricati automaticamente in un contenitore di archiviazione BLOB denominato *rtcvlogs* con il seguente formato di nome file: `{CONTAINER_NAME}/{START_TIME}-{END_TIME}-{QUERY_TIME}.log` .

```json
"env":{
    "IOTEDGE_WORKLOADURI":"fd://iotedge.socket",
    "AZURE_STORAGE_CONNECTION_STRING":"XXXXXX",   //from the Azure Blob Storage account
    "ARCHON_LOG_LEVEL":"info"
}
```

### <a name="uploading-spatial-analysis-logs"></a>Caricamento dei log di analisi spaziale

I log vengono caricati su richiesta con il `getRTCVLogs` metodo IOT Edge, nel `diagnostics` modulo. 


1. Andare alla pagina del portale dell'hub Internet delle cose, selezionare **dispositivi perimetrali**, quindi selezionare il dispositivo e il modulo di diagnostica. 
2. Passare alla pagina dei dettagli del modulo e fare clic sulla scheda **_metodo diretto_*.
3. Digitare `getRTCVLogs` sul nome del metodo e una stringa di formato JSON nel payload. È possibile immettere `{}` , che è un payload vuoto. 
4. Impostare i timeout di connessione e metodo, quindi fare clic su _ * richiama metodo * *.
5. Selezionare il contenitore di destinazione e compilare una stringa JSON di payload usando i parametri descritti nella sezione relativa alla **sintassi di registrazione** . Fare clic su **Richiama metodo** per eseguire la richiesta.

>[!NOTE]
> Se si richiama il `getRTCVLogs` metodo con un payload vuoto, viene restituito un elenco di tutti i contenitori distribuiti nel dispositivo. Per il nome del metodo si applica la distinzione tra maiuscole e minuscole. Si otterrà un errore 501 se viene specificato un nome di metodo non corretto.

:::image type="content" source="./media/spatial-analysis/direct-log-collection.png" alt-text="Richiamo del metodo getRTCVLogs ":::
![pagina Metodo diretto getRTCVLogs](./media/spatial-analysis/direct-log-collection.png)

 
### <a name="logging-syntax"></a>Sintassi di registrazione

La tabella seguente elenca i parametri che è possibile usare quando si eseguono query sui log.

| Parola chiave | Descrizione | Valore predefinito |
|--|--|--|
| StartTime | Ora di inizio dei log desiderati, in millisecondi UTC. | `-1`, l'inizio del runtime del contenitore. Quando `[-1.-1]` viene usato come intervallo di tempo, l'API restituisce i log dell'ultima ora.|
| EndTime | L'ora di fine dei log desiderati, in millisecondi UTC. | `-1`, l'ora corrente. Quando `[-1.-1]` si usa l'intervallo di tempo, l'API restituisce i log dell'ultima ora. |
| ContainerId | Contenitore di destinazione per il recupero dei log.| `null`, quando non è presente alcun ID contenitore. L'API restituisce tutte le informazioni sui contenitori disponibili con ID.|
| DoPost | Eseguire l'operazione di caricamento. Quando è impostato su `false` , viene eseguita l'operazione richiesta e vengono restituite le dimensioni di caricamento senza eseguire il caricamento. Se impostato su `true` , avvierà il caricamento asincrono dei log selezionati | `false`, non caricare.|
| Limitazione | Indica il numero di righe di log da caricare per batch | `1000`, Usare questo parametro per modificare la velocità post. |
| Filtri | Filtra i log da caricare | `null`, i filtri possono essere specificati come coppie chiave-valore in base alla struttura dei log di analisi spaziale: `[UTC, LocalTime, LOGLEVEL,PID, CLASS, DATA]` . ad esempio `{"TimeFilter":[-1,1573255761112]}, {"TimeFilter":[-1,1573255761112]}, {"CLASS":["myNode"]`|

Nella tabella seguente sono elencati gli attributi nella risposta alla query.

| Parola chiave | Descrizione|
|--|--|
|DoPost| *True* o *false*. Indica se i log sono stati caricati. Quando si sceglie di non caricare i log, l'API restituisce le informazioni * in modo **sincrono** _. Quando si sceglie di caricare i log, l'API restituisce 200, se la richiesta è valida e inizia a caricare i log in _*_modo asincrono_*_.|
|TimeFilter| Filtro temporale applicato ai log.|
|ValueFilters| I filtri delle parole chiave applicati ai log. |
|TimeStamp| Ora di inizio dell'esecuzione del metodo. |
|ContainerId| ID del contenitore di destinazione. |
|FetchCounter| Numero totale di righe di log. |
|FetchSizeInByte| Quantità totale di dati di log in byte. |
|MatchCounter| Numero valido di righe di log. |
|MatchSizeInByte| Quantità valida di dati di log in byte. |
|FilterCount| Numero totale di righe di log dopo l'applicazione del filtro. |
|FilterSizeInByte| Quantità totale di dati di log in byte dopo l'applicazione del filtro. |
|FetchLogsDurationInMiliSec| Durata dell'operazione di recupero. |
|PaseLogsDurationInMiliSec| Durata dell'operazione di filtro. |
|PostLogsDurationInMiliSec| Durata dell'operazione post. |

#### <a name="example-request"></a>Richiesta di esempio 

```json
{
    "StartTime": -1,
    "EndTime": -1,
    "ContainerId": "5fa17e4d8056e8d16a5a998318716a77becc01b36fde25b3de9fde98a64bf29b",
    "DoPost": false,
    "Filters": null
}
```

#### <a name="example-response"></a>Risposta di esempio 

```json
{
    "status": 200,
    "payload": {
        "DoPost": false,
        "TimeFilter": [-1, 1581310339411],
        "ValueFilters": {},
        "Metas": {
            "TimeStamp": "2020-02-10T04:52:19.4365389+00:00",
            "ContainerId": "5fa17e4d8056e8d16a5a998318716a77becc01b36fde25b3de9fde98a64bf29b",
            "FetchCounter": 61,
            "FetchSizeInByte": 20470,
            "MatchCounter": 61,
            "MatchSizeInByte": 20470,
            "FilterCount": 61,
            "FilterSizeInByte": 20470,
            "FetchLogsDurationInMiliSec": 0,
            "PaseLogsDurationInMiliSec": 0,
            "PostLogsDurationInMiliSec": 0
        }
    }
}
```

Controllare le righe, i tempi e le dimensioni del _*_log di recupero_*_ , se queste impostazioni sembrano corrette, sostituire la prima volta `true` con e che effettueranno il push dei log con gli stessi filtri alle destinazioni. 

È possibile esportare i log dall'archiviazione BLOB di Azure durante la risoluzione dei problemi. 

## <a name="common-issues"></a>Problemi comuni

Se viene visualizzato il messaggio seguente nei log del modulo, potrebbe significare che la sottoscrizione di Azure deve essere approvata: 

"Il contenitore non è in uno stato valido. La convalida della sottoscrizione non è riuscita con stato ' non corrispondente '. La chiave API non è destinata al tipo di contenitore specificato. "

Per altre informazioni, vedere [richiedere l'approvazione per eseguire il contenitore](spatial-analysis-container.md#request-approval-to-run-the-container).

## <a name="troubleshooting-the-azure-stack-edge-device"></a>Risoluzione dei problemi relativi al dispositivo Azure Stack Edge

La sezione seguente è disponibile per informazioni sul debug e la verifica dello stato del dispositivo Azure Stack Edge.

### <a name="access-the-kubernetes-api-endpoint"></a>Accedere all'endpoint API Kubernetes. 

1. Nell'interfaccia utente locale del dispositivo andare alla pagina _ *Devices** (dispositivi). 
2. In **endpoint dispositivo** copiare l'endpoint del servizio API Kubernetes. Questo endpoint è una stringa nel formato seguente: `https://compute..[device-IP-address]`.
3. Salvare la stringa dell'endpoint. Questa operazione verrà usata in un secondo momento durante la configurazione `kubectl` di per accedere al cluster Kubernetes.

### <a name="connect-to-powershell-interface"></a>Connettersi all'interfaccia di PowerShell

Connettersi in remoto da un client Windows. Dopo la creazione del cluster Kubernetes, è possibile gestire le applicazioni tramite questo cluster. Sarà necessario connettersi all'interfaccia di PowerShell del dispositivo. A seconda del sistema operativo del client, le procedure per connettersi in remoto al dispositivo potrebbero essere diverse. I passaggi seguenti sono relativi a un client Windows che esegue PowerShell.

> [!TIP]
> * Prima di iniziare, verificare che il client Windows esegua Windows PowerShell 5,0 o versione successiva.
> * PowerShell è [disponibile anche in Linux](/powershell/scripting/install/installing-powershell-core-on-linux).

1. Eseguire una sessione di Windows PowerShell come amministratore. 
    1. Verificare che il servizio Gestione remota Windows sia in esecuzione nel client. Al prompt dei comandi digitare `winrm quickconfig`.

2. Assegnare una variabile per l'indirizzo IP del dispositivo. Ad esempio: `$ip = "<device-ip-address>"`.

3. Usare il comando seguente per aggiungere l'indirizzo IP del dispositivo all'elenco di host attendibili del client. 

    ```powershell
    Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force
    ```
 
4. Avviare una sessione di Windows PowerShell nel dispositivo. 

    ```powershell
    Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell
    ```

5. Fornire la password quando richiesto. Usare la stessa password usata per accedere all'interfaccia Web locale. La password dell'interfaccia Web locale predefinita è `Password1` . 

### <a name="access-the-kubernetes-cluster"></a>Accedere al cluster Kubernetes

Dopo aver creato il cluster Kubernetes, è possibile usare lo `kubectl` strumento da riga di comando per accedere al cluster.

1. Creare un nuovo spazio dei nomi. 

    ```powershell
    New-HcsKubernetesNamespace -Namespace
    ```

2. Creare un utente e ottenere un file di configurazione. Questo comando restituirà le informazioni di configurazione per il cluster Kubernetes. Copiare queste informazioni e salvarle in un file denominato *config*. Non salvare il file in un'estensione di file.
    
    ```powershell
    New-HcsKubernetesUser -UserName
    ```

3. Aggiungere il file di *configurazione* alla cartella *. Kube* nel profilo utente nel computer locale.   

4. Associare lo spazio dei nomi all'utente creato.

    ```powershell
    Grant-HcsKubernetesNamespaceAccess -Namespace -UserName
    ```

5. Installare `kubectl` nel client Windows usando il comando seguente:
    
    ```powershell
    curl https://storage.googleapis.com/kubernetesrelease/release/v1.15.2/bin/windows/amd64/kubectl.exe -O kubectl.exe
    ```

6. Aggiungere una voce DNS al file hosts nel sistema. 
    1. Eseguire blocco note come amministratore e aprire il file *host* che si trova in `C:\windows\system32\drivers\etc\hosts` . 
    2. Creare una voce nel file degli host con l'indirizzo IP del dispositivo e il dominio DNS ottenuto dalla pagina del **dispositivo** nell'interfaccia utente locale. L'endpoint da usare avrà un aspetto simile al seguente: `https://compute.asedevice.microsoftdatabox.com/10.100.10.10` .

7. Verificare che sia possibile connettersi ai pod Kubernetes.

    ```powershell
    kubectl get pods -n "iotedge"
    ```

Per ottenere i log del contenitore, eseguire il comando seguente:

```powershell
kubectl logs <pod-name> -n <namespace> --all-containers
```

### <a name="useful-commands"></a>Comandi utili

|Comando  |Descrizione  |
|---------|---------|
|`Get-HcsKubernetesUserConfig -AseUser`     | Genera un file di configurazione Kubernetes. Quando si usa il comando, copiare le informazioni in un file denominato *config*. Non salvare il file con un'estensione di file.        |
| `Get-HcsApplianceInfo` | Restituisce informazioni sul dispositivo. |
| `Enable-HcsSupportAccess` | Genera le credenziali di accesso per avviare una sessione di supporto. |

## <a name="next-steps"></a>Passaggi successivi

* [Distribuire un'applicazione Web per il conteggio delle persone](spatial-analysis-web-app.md)
* [Configurare le operazioni di analisi spaziale](./spatial-analysis-operations.md)
* [Guida al posizionamento della fotocamera](spatial-analysis-camera-placement.md)
* [Guida al posizionamento di zone e linee](spatial-analysis-zone-line-placement.md)