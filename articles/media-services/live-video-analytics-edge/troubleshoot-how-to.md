---
title: Risolvere i problemi di analisi video in tempo reale su IoT Edge-Azure
description: Questo articolo illustra i passaggi per la risoluzione dei problemi di analisi video in tempo reale su IoT Edge.
author: IngridAtMicrosoft
ms.topic: how-to
ms.author: inhenkel
ms.date: 05/24/2020
ms.openlocfilehash: bbd3cb88b017209adff58a646e274caf31ab425f
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87486443"
---
# <a name="troubleshoot-live-video-analytics-on-iot-edge"></a>Risolvere i problemi di analisi video in tempo reale su IoT Edge

Questo articolo illustra i passaggi per la risoluzione dei problemi di analisi video in tempo reale (LVA) in Azure IoT Edge.

## <a name="troubleshoot-deployment-issues"></a>Risolvere i problemi relativi alla distribuzione

### <a name="diagnostics"></a>Diagnostica

Come parte della distribuzione di analisi video in tempo reale, è possibile configurare le risorse di Azure, ad esempio i dispositivi IoT Edge e l'hub. Come primo passaggio per diagnosticare i problemi, assicurarsi sempre che il dispositivo perimetrale sia configurato correttamente seguendo queste istruzioni:

1. [Eseguire il `check` comando](../../iot-edge/troubleshoot.md#run-the-check-command).
1. [Controllare la versione del IOT Edge](../../iot-edge/troubleshoot.md#check-your-iot-edge-version).
1. [Verificare lo stato di IOT Edge Security Manager e dei relativi log](../../iot-edge/troubleshoot.md#check-the-status-of-the-iot-edge-security-manager-and-its-logs).
1. [Visualizzare i messaggi che passano attraverso l'hub IOT Edge](../../iot-edge/troubleshoot.md#view-the-messages-going-through-the-iot-edge-hub).
1. [Riavviare i contenitori](../../iot-edge/troubleshoot.md#restart-containers).
1. [Controllare le regole di configurazione del firewall e delle porte](../../iot-edge/troubleshoot.md#check-your-firewall-and-port-configuration-rules).

### <a name="pre-deployment-issues"></a>Problemi di pre-distribuzione

Se l'infrastruttura perimetrale è corretta, è possibile cercare problemi con il file manifesto di distribuzione. Per distribuire l'analisi video in tempo reale sul modulo IoT Edge sul dispositivo IoT Edge insieme a tutti gli altri moduli Internet, si usa un manifesto di distribuzione che contiene l'hub IoT Edge, l'agente IoT Edge e altri moduli e le relative proprietà. Se il formato del codice JSON non è corretto, è possibile che venga visualizzato l'errore seguente: 

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content <path-to-deployment_manifest.json>
```

Non è stato possibile analizzare JSON dal file:' <deployment manifest.json> ' per l'argomento ' content ' con eccezione: "dati aggiuntivi: riga 101 colonna 1 (char 5325)"

Se si verifica questo errore, è consigliabile controllare il codice JSON per le parentesi mancanti o altri problemi con la struttura del file. Per convalidare la struttura dei file, è possibile usare un client come il [plug-in Notepad + + con il Visualizzatore JSON](https://riptutorial.com/notepadplusplus/example/18201/json-viewer) o uno strumento online come il [formattatore JSON & validator](https://jsonformatter.curiousconcept.com/).

### <a name="during-deployment-diagnose-with-media-graph-direct-methods"></a>Durante la distribuzione: diagnostica con i metodi diretti del grafico multimediale 

Dopo che l'analisi video in tempo reale su IoT Edge modulo è stata distribuita correttamente nel dispositivo IoT Edge, è possibile creare ed eseguire il grafico multimediale richiamando i [metodi diretti](direct-methods.md). È possibile usare la portale di Azure per eseguire una diagnosi del grafico multimediale tramite metodi diretti:

1. Nella portale di Azure passare all'hub Internet delle cose connesso al dispositivo IoT Edge.

1. Cercare **gestione automatica dispositivi**, quindi selezionare **IOT Edge**.  

1. Nell'elenco dei dispositivi perimetrali selezionare il dispositivo che si desidera diagnosticare.  
         
    ![Screenshot della portale di Azure visualizzazione di un elenco di dispositivi perimetrali](./media/troubleshoot-how-to/lva-sample-device.png)

1. Verificare se il codice di risposta è *200-OK*. Altri codici di risposta per il [runtime di IOT Edge](../../iot-edge/iot-edge-runtime.md) includono:
    * 400 - La configurazione della distribuzione è in formato non corretto o non valida.
    * 417: il dispositivo non ha un set di configurazione della distribuzione.
    * 412 - La versione dello schema nella configurazione della distribuzione non è valida.
    * 406 - Il dispositivo è offline o non invia segnalazioni sullo stato.
    * 500 - Si è verificato un errore nel runtime di IoT Edge.

1. Se si ottiene un codice di stato 501, verificare che il nome del metodo diretto sia accurato. Se il nome del metodo e il payload della richiesta sono accurati, è necessario ottenere risultati con codice di esito positivo = 200. Se il payload della richiesta non è accurato, si otterrà uno stato = 400 e un payload della risposta che indica il codice di errore e il messaggio che dovrebbero aiutare a diagnosticare il problema con la chiamata al metodo diretto.
    * Il controllo delle proprietà segnalate e desiderate consente di comprendere se le proprietà del modulo sono state sincronizzate con la distribuzione. In caso contrario, è possibile riavviare il dispositivo IoT Edge. 
    * Usare la guida ai [metodi diretti](direct-methods.md) per chiamare alcuni metodi, soprattutto quelli semplici, ad esempio GraphTopologyList. La guida specifica inoltre i payload di richiesta e risposta previsti e i codici di errore. Una volta completati i semplici metodi diretti, è possibile assicurarsi che il modulo di analisi di video in tempo reale IoT Edge sia accettabile dal punto di vista funzionale.
        
       ![Screenshot del riquadro "metodo diretto" per il modulo IoT Edge.](./media/troubleshoot-how-to/direct-method.png) 

1. Se l' **oggetto specificato nella distribuzione** e **segnalato dalle** colonne del dispositivo indica *Sì*, è possibile richiamare i metodi diretti sul modulo di analisi video live in IOT Edge. Selezionare il modulo per passare a una pagina in cui è possibile controllare le proprietà desiderate e segnalate e richiamare i metodi diretti. Tenere presente quanto segue: 

### <a name="post-deployment-diagnose-logs-for-issues-during-the-run"></a>Post-distribuzione: diagnostica i registri per i problemi durante l'esecuzione 

I log dei contenitori per il modulo di IoT Edge devono contenere informazioni di diagnostica per facilitare il debug dei problemi durante il runtime del modulo. È possibile [controllare i log dei contenitori per individuare eventuali problemi](../../iot-edge/troubleshoot.md#check-container-logs-for-issues) e diagnosticare autonomamente il problema. 

Se sono stati eseguiti tutti i controlli precedenti e continuano a verificarsi problemi, raccogliere i log dal dispositivo IoT Edge [con il `support bundle` comando](../../iot-edge/troubleshoot.md#gather-debug-information-with-support-bundle-command) per un'ulteriore analisi da parte del team di Azure. È possibile [contattarci](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) per assistenza e per inviare i log raccolti.

## <a name="common-error-resolutions"></a>Risoluzioni di errori comuni

L'analisi video in tempo reale viene distribuita come modulo IoT Edge sul dispositivo IoT Edge e collabora con i moduli di IoT Edge Agent e Hub. Alcuni degli errori comuni che si verificano con la distribuzione di analisi video in tempo reale sono causati da problemi con l'infrastruttura di Internet delle cose sottostante. Gli errori includono:

* [L'agente di IOT Edge si interrompe dopo circa un minuto](../../iot-edge/troubleshoot-common-errors.md#iot-edge-agent-stops-after-about-a-minute).
* [L'agente di IOT Edge non è in grado di accedere all'immagine di un modulo (403)](../../iot-edge/troubleshoot-common-errors.md#iot-edge-agent-cant-access-a-modules-image-403).
* [Il modulo dell'agente di IOT Edge segnala "file di configurazione vuoto" e non vengono avviati moduli nel dispositivo](../../iot-edge/troubleshoot-common-errors.md#edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device).
* [Non è possibile avviare l'hub IOT Edge](../../iot-edge/troubleshoot-common-errors.md#iot-edge-hub-fails-to-start).
* [Il daemon di sicurezza IoT Edge ha esito negativo con un nome host non valido](../../iot-edge/troubleshoot-common-errors.md#iot-edge-security-daemon-fails-with-an-invalid-hostname).
* [L'analisi video in tempo reale o qualsiasi altro modulo di IOT Edge personalizzato non riesce a inviare un messaggio all'Hub Edge con errore 404](../../iot-edge/troubleshoot-common-errors.md#iot-edge-module-fails-to-send-a-message-to-edgehub-with-404-error).
* [Il modulo IOT Edge viene distribuito correttamente, quindi scompare dal dispositivo](../../iot-edge/troubleshoot-common-errors.md#iot-edge-module-deploys-successfully-then-disappears-from-device).

### <a name="edge-setup-script-issues"></a>Problemi degli script di configurazione Edge

Come parte della nostra documentazione, abbiamo fornito uno [script di configurazione](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) per distribuire risorse perimetrali e cloud e iniziare a usare l'analisi dei video in tempo reale. Questa sezione presenta alcuni errori di script che possono verificarsi, insieme alle soluzioni per eseguirne il debug.

Problema: lo script viene eseguito, creando parzialmente alcune risorse, ma ha esito negativo con il messaggio seguente:

```
registering device...

Unable to load extension 'eventgrid: unrecognized kwargs: ['min_profile']'. Use --debug for more information.
The command failed with an unexpected error. Here is the traceback:

No module named 'azure.mgmt.iothub.iot_hub_client'
Traceback (most recent call last):
File "/opt/az/lib/python3.6/site-packages/knack/cli.py", line 215, in invoke
  cmd_result = self.invocation.execute(args)
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 631, in execute
  raise ex
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 695, in _run_jobs_serially
  results.append(self._run_job(expanded_arg, cmd_copy))
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 688, in _run_job
  six.reraise(*sys.exc_info())
File "/opt/az/lib/python3.6/site-packages/six.py", line 693, in reraise
  raise value
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 665, in _run_job
  result = cmd_copy(params)
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 324, in __call__
  return self.handler(*args, **kwargs)
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/__init__.py", line 574, in default_command_handler
  return op(**command_args)
File "/home/.azure/cliextensions/azure-cli-iot-ext/azext_iot/operations/hub.py", line 75, in iot_device_list
  result = iot_query(cmd, query, hub_name, top, resource_group_name, login=login)
File "/home/.azure/cliextensions/azure-cli-iot-ext/azext_iot/operations/hub.py", line 45, in iot_query
  target = get_iot_hub_connection_string(cmd, hub_name, resource_group_name, login=login)
File "/home/.azure/cliextensions/azure-cli-iot-ext/azext_iot/common/_azure.py", line 112, in get_iot_hub_connection_string
  client = iot_hub_service_factory(cmd.cli_ctx)
File "/home/.azure/cliextensions/azure-cli-iot-ext/azext_iot/_factory.py", line 28, in iot_hub_service_factory
  from azure.mgmt.iothub.iot_hub_client import IotHubClient
ModuleNotFoundError: No module named 'azure.mgmt.iothub.iot_hub_client'
```
    
Per risolvere il problema:

1. Eseguire il comando seguente:

    ```
    az --version
    ```
1. Verificare che siano installate le estensioni seguenti. Al momento della pubblicazione di questo articolo, le estensioni e le relative versioni sono:

    | Estensione | Versione |
    |---|---|
    |azure-cli   |      2.5.1|
    |moduli di comando-nspkg         |   2.0.3|
    |core  |    2.5.1|
    |nspkg    | 3.0.4|
    |Telemetria| 1.0.4|
    |storage-preview          |     0.2.10|
    |azure-cli-iot-ext          |    0.8.9|
    |eventgrid| 0.4.9|
    |azure-iot                       | 0.9.2|
1. Se si dispone di un'estensione installata la cui versione è precedente al numero di versione elencato qui, aggiornare l'estensione usando il comando seguente:

    ```
    az extension update --name <Extension name>
    ```

    Ad esempio, è possibile eseguire `az extension update --name azure-iot` .

### <a name="sample-app-issues"></a>Problemi relativi all'app di esempio

Nell'ambito della nostra versione è stato fornito un codice di esempio .NET per consentire il bootstrap della community degli sviluppatori. In questa sezione vengono illustrati alcuni errori che possono verificarsi durante l'esecuzione del codice di esempio, insieme alle soluzioni per eseguirne il debug.

Problema: Program.cs ha esito negativo con l'errore seguente sulla chiamata al metodo diretto:

```
Unhandled exception. Microsoft.Azure.Devices.Common.Exceptions.UnauthorizedException: {"Message":"{\"errorCode\":401002,\"trackingId\":\"b1da85801b2e4faf951a2291a2c467c3-G:32-TimeStamp:04/06/2020 17:15:11\",\"message\":\"Unauthorized\",\"timestampUtc\":\"2020-04-06T17:15:11.6990676Z\"}","ExceptionMessage":""}
    
        at Microsoft.Azure.Devices.HttpClientHelper.ExecuteAsync(HttpClient httpClient, HttpMethod httpMethod, Uri requestUri, Func`3 modifyRequestMessageAsync, Func`2 isMappedToException, Func`3 processResponseMessageAsync, IDictionary`2 errorMappingOverrides, CancellationToken cancellationToken)
    
        at Microsoft.Azure.Devices.HttpClientHelper.ExecuteAsync(HttpMethod httpMethod, Uri requestUri, Func`3 modifyRequestMessageAsync, Func`3 processResponseMessageAsync, IDictionary`2 errorMappingOverrides, CancellationToken cancellationToken)
        
        at Microsoft.Azure.Devices.HttpClientHelper.PostAsync[T,T2](Uri requestUri, T entity, TimeSpan operationTimeout, IDictionary`2 errorMappingOverrides, IDictionary`2 customHeaders, CancellationToken cancellationToken)…
```

1. Assicurarsi di aver installato gli [strumenti di Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) per l'uso nell'ambiente di Visual Studio Code e di avere configurato la connessione all'hub. A tale scopo, premere CTRL + MAIUSC + P, quindi scegliere il **Metodo Hub**Internet.

1. Verificare se è possibile richiamare un metodo diretto sul modulo IoT Edge tramite Visual Studio Code. Ad esempio, chiamare GraphTopologyList con il payload { &nbsp; " @apiVersion ": "1,0"} seguente. Si dovrebbe ricevere la risposta seguente: 

    ```
    {
      "status": 200,
      "payload": {
        "values": [
          {…
    …}
          ]
        }
    }
    ```

    ![Screenshot della risposta in Visual Studio Code.](./media/troubleshoot-how-to/visual-studio-code1.png)
1. Se la soluzione precedente ha esito negativo, provare a eseguire le operazioni seguenti:

    a. Passare al prompt dei comandi nel dispositivo IoT Edge ed eseguire il comando seguente:
    
      ```
      sudo systemctl restart iotedge
      ```

      Questo comando Riavvia il dispositivo IoT Edge e tutti i moduli. Attendere alcuni minuti e quindi, prima di provare a usare di nuovo il metodo diretto, verificare che i moduli siano in esecuzione eseguendo il comando seguente:

      ```
      sudo iotedge list
      ```

    b. Se anche l'approccio precedente ha esito negativo, provare a riavviare la macchina virtuale o il computer.

    c. Se tutti gli approcci hanno esito negativo, eseguire il comando seguente per ottenere un file compresso con tutti i [log rilevanti](../../iot-edge/troubleshoot.md#gather-debug-information-with-support-bundle-command)e associarlo a un [ticket di supporto](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

    ```
    sudo iotedge support-bundle --since 2h
    ```

1. Se viene ricevuto un codice di errore *400* , verificare che il payload della chiamata al metodo sia ben formato, in base alla guida ai [metodi diretti](direct-methods.md) .
1. Se si ottiene un codice di stato *200* , significa che l'hub funziona correttamente e che la distribuzione del modulo è corretta e reattiva. 

1. Verificare se la configurazione dell'app è precisa. La configurazione dell'app è costituita dai campi seguenti nel *appsettings.jssu* file. Verificare che deviceId e moduleId siano accurati. Un modo semplice per verificarlo è passare alla sezione relativa all'estensione dell'hub Azure Internet in Visual Studio Code. I valori nella sezione *appsettings.jssu* file e hub Internet devono corrispondere.
    
    ```
    {
        "IoThubConnectionString" : 
        "deviceId" : 
        "moduleId" : 
    }
    ```

1. Nella *appsettings.jssu* file, assicurarsi di avere specificato la stringa di connessione dell'hub Internet e *non* la stringa di connessione del dispositivo dell'hub Internet, perché i [formati delle stringhe di connessione sono diversi](https://devblogs.microsoft.com/iotdev/understand-different-connection-strings-in-azure-iot-hub/).

### <a name="live-video-analytics-working-with-external-modules"></a>Analisi video in tempo reale utilizzo di moduli esterni

L'analisi video in tempo reale tramite il processore di estensione HTTP può estendere il grafico multimediale per inviare e ricevere dati da altri moduli IoT Edge tramite HTTP usando REST. Come [esempio specifico](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/httpExtension), il grafico multimediale può inviare fotogrammi video come immagini a un modulo di inferenza esterno, ad esempio Yolo V3 e ricevere risultati analitici basati su JSON. In una topologia di questo tipo, la destinazione per gli eventi è principalmente l'hub Internet. In situazioni in cui non vengono visualizzati gli eventi di inferenza nell'hub, verificare quanto segue:

* Verificare che l'hub in cui è in corso la pubblicazione del grafo multimediale e che l'hub che si sta esaminando sia lo stesso. Quando si creano più distribuzioni, è possibile che si verifichino più hub e che si verifichi erroneamente l'hub errato per gli eventi.
* In Visual Studio Code verificare se il modulo esterno è distribuito e in esecuzione. Nell'immagine di esempio, rtspsim e CV sono IoT Edge moduli in esecuzione esterna al modulo lvaEdge.

    ![Screenshot che mostra lo stato di esecuzione dei moduli nell'hub Azure.](./media/troubleshoot-how-to/iot-hub.png)

* Verificare se si stanno inviando eventi all'endpoint URL corretto. Il contenitore esterno per intelligenza artificiale espone un URL e una porta attraverso cui riceve e restituisce i dati dalle richieste POST. Questo URL viene specificato come `endpoint: url` proprietà per il processore di estensione http. Come illustrato nell' [URL della topologia](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/topology.json), l'endpoint viene impostato sul parametro URL di inferenza. Verificare che il valore predefinito per il parametro o il valore passato sia accurato. È possibile testare per verificare se funziona usando l'URL client (cURL).  

    Ad esempio, di seguito è riportato un contenitore Yolo V3 in esecuzione nel computer locale con un indirizzo IP di 172.17.0.3. Usare Docker ispeziona per trovare l'indirizzo IP.

    ```
    curl -X POST http://172.17.0.3/score -H "Content-Type: image/jpeg" --data-binary @<fullpath to jpg>
    ```

    Risultato restituito:

    ```
    {"inferences": [{"type": "entity", "entity": {"tag": {"value": "car", "confidence": 0.8668569922447205}, "box": {"l": 0.3853073438008626, "t": 0.6063712999658677, "w": 0.04174524943033854, "h": 0.02989496027381675}}}]}
    ```

* Se si eseguono una o più istanze di un grafico che utilizza il processore di estensione HTTP, è necessario disporre di un filtro di frequenza dei fotogrammi prima che ogni processore di estensione HTTP gestisca la frequenza di frame al secondo (fps) del feed video. 

   In alcune situazioni, in cui la CPU o la memoria del computer perimetrale è altamente utilizzata, è possibile perdere determinati eventi di inferenza. Per risolvere questo problema, impostare un valore basso per la proprietà maximumFps sul filtro della frequenza dei fotogrammi. È possibile impostarlo su 0,5 ("maximumFps": 0,5) in ogni istanza del grafo, quindi eseguire di nuovo l'istanza per verificare la presenza di eventi di inferenza nell'hub.

   In alternativa, è possibile ottenere un computer Edge più potente con CPU e memoria più elevate.
    
### <a name="multiple-direct-methods-in-parallel--timeout-failure"></a>Più metodi diretti in parallelo: errore di timeout 

Analisi video in tempo reale su IoT Edge fornisce un modello di programmazione diretto basato su metodo che consente di configurare più topologie e più istanze di Graph. Nell'ambito della configurazione della topologia e del grafo, si richiamano più chiamate a metodi diretti sul modulo IoT Edge. Se si richiamano queste chiamate a più metodi in parallelo, in particolare quelle che avviano e arrestano i grafici, è possibile che si verifichi un errore di timeout simile al seguente: 

Il metodo di inizializzazione dell'assembly Microsoft.Media.LiveVideoAnalytics.Test.Feature.Edge.AssemblyInitializer.InitializeAssemblyAsync ha generato un'eccezione. Microsoft. Azure. Devices. Common. Exceptions. IotHubException: Microsoft. Azure. Devices. Common. Exceptions. IotHubException:<br/> `{"Message":"{\"errorCode\":504101,\"trackingId\":\"55b1d7845498428593c2738d94442607-G:32-TimeStamp:05/15/2020 20:43:10-G:10-TimeStamp:05/15/2020 20:43:10\",\"message\":\"Timed out waiting for the response from device.\",\"info\":{},\"timestampUtc\":\"2020-05-15T20:43:10.3899553Z\"}","ExceptionMessage":""}. Aborting test execution. `

Si consiglia di *non* chiamare metodi diretti in parallelo. Chiamarli in modo sequenziale, ovvero effettuare una chiamata diretta al metodo solo dopo il completamento di quello precedente.

### <a name="collect-logs-for-submitting-a-support-ticket"></a>Raccolta dei log per l'invio di un ticket di supporto

Quando i passaggi per la risoluzione dei problemi autoguidati non risolvono il problema, passare alla portale di Azure e [aprire un ticket di supporto](../../azure-portal/supportability/how-to-create-azure-support-request.md).

> [!WARNING]
> I log possono contenere informazioni personali, ad esempio l'indirizzo IP. Tutte le copie locali dei log verranno eliminate non appena viene completata l'analisi e la chiusura del ticket di supporto.  

Per raccogliere i log rilevanti che devono essere aggiunti al ticket, seguire le istruzioni riportate nelle sezioni successive. È possibile caricare i file di log nel riquadro dei **Dettagli** della richiesta di supporto.

### <a name="use-the-support-bundle-command"></a>Usare il comando support-bundle

Quando è necessario raccogliere i log da un dispositivo IoT Edge, il modo più semplice consiste nell'usare il `support-bundle` comando. Questo comando raccoglie:

- Log dei moduli
- IoT Edge Security Manager e i log del motore del contenitore
- Iotedge controllare l'output JSON
- Informazioni di debug utili

1. Eseguire il `support-bundle` comando con il flag *--since* per specificare la quantità di tempo in cui si desidera che i log vengano coperti. Ad esempio, 2h otterrà i log per le ultime due ore. È possibile modificare il valore di questo flag per includere i registri per periodi diversi.

    ```
    sudo iotedge support-bundle --since 2h
    ```

   Questo comando crea un file denominato *support_bundle.zip* nella directory in cui è stato eseguito il comando. 
   
1. Alleghi il file di *support_bundle.zip* al ticket di supporto.

### <a name="live-video-analytics-debug-logs"></a>Log di debug di analisi video live

Per configurare l'analisi video in tempo reale sul modulo IoT Edge per generare i log di debug, effettuare le operazioni seguenti:

1. Accedere al [portale di Azure](https://portal.azure.com)e passare all'hub Internet.
1. Nel riquadro sinistro selezionare **IOT Edge**.
1. Nell'elenco dei dispositivi selezionare l'ID del dispositivo di destinazione.
1. Nella parte superiore del riquadro selezionare **imposta moduli**.

   ![Screenshot del pulsante "imposta moduli" nel portale di Azure.](media/troubleshoot-how-to/set-modules.png)

1. Nella sezione **moduli IOT Edge** cercare e selezionare **lvaEdge**.
1. Selezionare le **Opzioni di creazione del contenitore**.
1. Nella sezione **binds** aggiungere il comando seguente:

    `/var/local/mediaservices/logs:/var/lib/azuremediaservices/logs`

    > [!NOTE] 
    > Questo comando associa le cartelle dei log tra il dispositivo perimetrale e il contenitore. Se si vogliono raccogliere i log in un percorso diverso, usare il comando seguente, sostituendo **$LOG _LOCATION_ON_EDGE_DEVICE** con il percorso che si vuole usare:`/var/$LOG_LOCATION_ON_EDGE_DEVICE:/var/lib/azuremediaservices/logs`

1. Selezionare **Aggiorna**.
1. Selezionare **Rivedi e crea**. Un messaggio di convalida con esito positivo viene pubblicato sotto un banner verde.
1. Selezionare **Crea**.
1. Aggiornare l' **identità del modulo gemello** per puntare al parametro DebugLogsDirectory, che fa riferimento alla directory in cui vengono raccolti i log:

    a. Nella tabella **modules** selezionare **lvaEdge**.  
    b. Nella parte superiore del riquadro selezionare **Module Identity gemelle**. Verrà visualizzato un riquadro modificabile.  
    c. In **chiave desiderata**aggiungere la coppia chiave/valore seguente:  
    `"DebugLogsDirectory": "/var/lib/azuremediaservices/logs"`

    > [!NOTE] 
    > Questo comando associa le cartelle dei log tra il dispositivo perimetrale e il contenitore. Se si vogliono raccogliere i log in un percorso diverso, usare il comando seguente, sostituendo **$DEBUG _LOG_LOCATION_ON_EDGE_DEVICE** con il percorso che si vuole usare:  
    > `"DebugLogsDirectory": "/var/$DEBUG_LOG_LOCATION_ON_EDGE_DEVICE"`  

    d. Selezionare **Salva**.

1. Riprodurre il problema.
1. Connettersi alla macchina virtuale dalla pagina **Hub** Internet delle cose nel portale.
1. Zippare tutti i file nella cartella *debugLogs* .

   > [!NOTE]
   > Questi file di log non sono destinati alla diagnosi automatica. Sono destinati al team di progettazione di Azure per analizzare i problemi.

   a. Nel comando seguente, assicurarsi di sostituire **$DEBUG _LOG_LOCATION_ON_EDGE_DEVICE** con il percorso dei log di debug sul dispositivo perimetrale configurato in precedenza.  

   ```
   sudo apt install zip unzip  
   zip -r debugLogs.zip $DEBUG_LOG_LOCATION_ON_EDGE_DEVICE 
   ```

   b. Alleghi il file di *debugLogs.zip* al ticket di supporto.

1. È possibile arrestare la raccolta dei log impostando il valore nel **modulo Identity gemelle** su *null*. Tornare alla pagina **Module Identity gemelle** e aggiornare il parametro seguente come:

    `"DebugLogsDirectory": ""`

## <a name="next-steps"></a>Passaggi successivi

[Esercitazione: Registrazione video basata su eventi nel cloud e riproduzione dal cloud](event-based-video-recording-tutorial.md)
