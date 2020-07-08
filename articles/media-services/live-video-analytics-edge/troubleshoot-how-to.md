---
title: Risolvere i problemi di analisi video in tempo reale su IoT Edge-Azure
description: Questo articolo illustra i passaggi per la risoluzione dei problemi di analisi video in tempo reale su IoT Edge.
author: IngridAtMicrosoft
ms.topic: how-to
ms.author: inhenkel
ms.date: 05/24/2020
ms.openlocfilehash: dd55050521a1791a11f220cd5617d9df2fa2d160
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86045578"
---
# <a name="troubleshoot-live-video-analytics-on-iot-edge"></a>Risolvere i problemi di analisi video in tempo reale su IoT Edge

Questo articolo illustra i passaggi per la risoluzione dei problemi di analisi video in tempo reale su IoT Edge.

## <a name="troubleshoot-deployment-issues"></a>Risolvere i problemi relativi alla distribuzione

### <a name="diagnostics"></a>Diagnostica

Come parte della distribuzione di analisi video in tempo reale, si configureranno le risorse di Azure, ad esempio l'hub Internet e il dispositivo IoT Edge. Come primo passaggio per la diagnosi dei problemi, garantisce sempre che il bordo sia configurato correttamente seguendo queste istruzioni:

1. [Eseguire il comando ' check '](https://docs.microsoft.com/azure/iot-edge/troubleshoot#run-the-check-command)
1. [Controllare la versione di IoT Edge](https://docs.microsoft.com/azure/iot-edge/troubleshoot#check-your-iot-edge-version)
1. [Verificare lo stato di IoT Edge Security Manager e dei relativi log](https://docs.microsoft.com/azure/iot-edge/troubleshoot#check-the-status-of-the-iot-edge-security-manager-and-its-logs)
1. [Visualizzare i messaggi che passano attraverso l'hub IoT Edge](https://docs.microsoft.com/azure/iot-edge/troubleshoot#view-the-messages-going-through-the-iot-edge-hub)
1. [Riavviare i contenitori](https://docs.microsoft.com/azure/iot-edge/troubleshoot#restart-containers)
1. [Controllare le regole di configurazione del firewall e della porta](https://docs.microsoft.com/azure/iot-edge/troubleshoot#check-your-firewall-and-port-configuration-rules)

### <a name="pre-deployment-issues"></a>Problemi di pre-distribuzione

Se l'infrastruttura perimetrale è corretta, è possibile cercare problemi con il file manifesto di distribuzione. Per distribuire analisi video in tempo reale sul modulo IoT Edge sul dispositivo perimetrale insieme a tutti gli altri moduli Internet, si userà un manifesto di distribuzione che contiene l'hub perimetrale, l'agente perimetrale e altri moduli con le rispettive proprietà. Se il formato JSON non è corretto, è possibile ottenere un errore come riportato di seguito: 

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content <path-to-deployment_manifest.json>
```

Non è stato possibile analizzare JSON dal file:' <deployment manifest.json> ' per l'argomento ' content ' con eccezione: "dati aggiuntivi: riga 101 colonna 1 (char 5325)"

Se si verifica questo errore, è consigliabile controllare il file JSON per le parentesi quadre mancanti o altri problemi con la struttura del file. È possibile usare un client come il [blocco note + + con il plug](https://riptutorial.com/notepadplusplus/example/18201/json-viewer) -in del Visualizzatore JSON o uno strumento online come https://jsonformatter.curiousconcept.com/ per convalidare la struttura di file.

### <a name="deployment--diagnose-with-media-graph-direct-methods"></a>Distribuzione: diagnostica con i metodi diretti del grafo multimediale 

Dopo aver distribuito correttamente il modulo di analisi video in tempo reale sul dispositivo perimetrale in IoT Edge, è possibile creare ed eseguire il grafico multimediale richiamando i [metodi diretti](direct-methods.md). È possibile usare il portale per eseguire la diagnosi del grafico multimediale tramite metodi diretti:

1. Tramite il portale passare all'hub Internet delle cose connesso al dispositivo perimetrale.
    1. Una volta nel pannello hub tutto, cercare l'IoT Edge >gestione automatica dei dispositivi.
    1. Fare clic su IoT Edge dovrebbe visualizzare un elenco di dispositivi perimetrali. Selezionare il dispositivo che si desidera diagnosticare.
         
        ![Dispositivi perimetrali](./media/troubleshoot-how-to/lva-sample-device.png)
    1. Controllare se il codice di risposta è 200-OK. Sono disponibili diversi altri codici di risposta per il [runtime di IOT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime) , ad esempio:
        1. 400 - La configurazione della distribuzione è in formato non corretto o non valida.
        1. 417: il dispositivo non ha un set di configurazione della distribuzione.
        1. 412 - La versione dello schema nella configurazione della distribuzione non è valida.
        1. 406 - Il dispositivo è offline o non invia segnalazioni sullo stato.
        1. 500 - Si è verificato un errore nel runtime di IoT Edge.
    1. Facendo clic sul dispositivo è inoltre possibile visualizzare l'elenco dei moduli di IoT Edge previsti che sono stati distribuiti e il relativo stato
    1. Se le colonne "specificato in distribuzione" e "segnalato dal dispositivo" indicano "Sì", è possibile richiamare i metodi diretti sul modulo di analisi video live in IoT Edge. Fare clic sul modulo per passare a una schermata in cui è possibile controllare le proprietà desiderate e segnalate e può richiamare metodi diretti. 
        1. Il controllo delle proprietà segnalate e desiderate consente di comprendere se le proprietà del modulo sono state sincronizzate con la distribuzione. In caso contrario, è possibile riavviare il dispositivo perimetrale 
        1. Usare la guida ai [metodi diretti](direct-methods.md) per chiamare alcuni metodi, soprattutto quelli semplici come GraphTopologyList. La guida specifica inoltre i payload di richiesta e risposta previsti e i codici di errore. Una volta che i semplici metodi diretti hanno esito positivo, è possibile verificare che il modulo Live Video Analytics Edge funzioni correttamente.
        
        ![Metodo diretto](./media/troubleshoot-how-to/direct-method.png) 
1. Se si ottiene un codice di stato 501, verificare che il nome del metodo diretto sia accurato. Se il nome del metodo e il payload della richiesta sono accurati, i risultati dovrebbero essere restituiti insieme al codice di esito positivo = 200. Se il payload della richiesta non è accurato, si otterrà uno stato = 400 e un payload della risposta che indica il codice di errore e il messaggio che dovrebbero aiutare a diagnosticare il problema con la chiamata al metodo diretto. 

### <a name="post-deployment--diagnose-logs-for-issues-during-run"></a>Post-distribuzione: diagnostica i registri per i problemi durante l'esecuzione 

I log dei contenitori per il modulo perimetrale dovrebbero avere la diagnostica<!--<todo:add link to diagnostics doc>--> informazioni che consentono di eseguire il debug dei problemi durante il runtime del modulo. È possibile [controllare i log dei contenitori per](https://docs.microsoft.com/azure/iot-edge/troubleshoot#check-container-logs-for-issues) individuare i problemi e diagnosticare autonomamente, se tutti i controlli precedenti sono stati eseguiti e si stanno ancora verificando problemi, quindi raccogliere i log dal dispositivo IOT Edge [con il comando ' bundle di supporto '](https://docs.microsoft.com/azure/iot-edge/troubleshoot#gather-debug-information-with-support-bundle-command) che può essere analizzato ulteriormente dal team di Azure. È possibile [contattare](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) Microsoft per supportare e inviare i log raccolti.

## <a name="common-error-resolutions"></a>Risoluzioni di errori comuni

L'analisi video in tempo reale viene distribuita come modulo IoT Edge sul dispositivo perimetrale e funziona in modo collaborativo con i moduli IoT Edge Agent e Hub. Alcuni degli errori comuni che si affronteranno con la distribuzione di analisi video in tempo reale saranno dovuti ai problemi con l'infrastruttura di Internet delle cose sottostante. Di seguito sono riportati alcuni errori comuni che possono avere IoT Edge agente e l'hub:

1. [IOT Edge agente si interrompe dopo circa un minuto](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-agent-stops-after-about-a-minute).
1. [IOT Edge Agent non è in grado di accedere all'immagine di un modulo (403)](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-agent-cant-access-a-modules-image-403).
1. [Il modulo dell'agente Edge segnala il file di configurazione vuoto e non vengono avviati moduli nel dispositivo](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device).
1. [Non è possibile avviare l'hub IOT Edge](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-hub-fails-to-start).
1. [IOT Edge daemon di sicurezza ha esito negativo con un nome host non valido](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-security-daemon-fails-with-an-invalid-hostname).
1. [L'analisi di video in tempo reale o qualsiasi altro modulo di IOT Edge personalizzato non riesce a inviare un messaggio all'Hub Edge con errore 404](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-module-fails-to-send-a-message-to-edgehub-with-404-error).
1. Il [modulo IOT Edge distribuisce correttamente, quindi scompare dal dispositivo](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-module-deploys-successfully-then-disappears-from-device).

### <a name="edge-set-up-script-issues"></a>Problemi relativi allo script di configurazione Edge

Nell'ambito della nostra documentazione è stato fornito uno script di [configurazione](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) per la distribuzione di risorse perimetrali e cloud per iniziare a usare l'analisi dei video in tempo reale. In questa sezione sono stati acquisiti gli errori che si potrebbero incontrare con lo script e come eseguirne il debug.

Lo script esegue parzialmente la creazione di poche risorse, ma ha esito negativo con il messaggio seguente:

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
1. Verificare che siano installate le estensioni seguenti. Al momento della stesura di questa guida, la versione delle estensioni è la seguente:

    | Estensione | Versione |
    |---|---|
    |azure-cli   |      2.5.1|
    |moduli di comando-nspkg         |   2.0.3|
    |core  |    2.5.1|
    |nspkg    | 3.0.4|
    |telemetry| 1.0.4|
    |Extensions:    ||
    |storage-preview          |     0.2.10|
    |azure-cli-iot-ext          |    0.8.9|
    |eventgrid| 0.4.9|
    |azure-iot                       | 0.9.2|
1. Se una delle estensioni è precedente ai numeri di versione precedenti, aggiornare l'estensione alla versione più recente usando il comando:

    ```
    az extension update --name <Extension name>
    ```

    Ad esempio: `az extension update --name azure-iot`

### <a name="sample-app-issues"></a>Problemi relativi all'app di esempio

Nell'ambito della nostra versione è stato fornito un codice di esempio .NET per avviare il bootstrap della community degli sviluppatori. In questa sezione sono stati acquisiti errori che potrebbero verificarsi durante l'esecuzione del codice di esempio e come eseguire il debug di tali errori.

1. Program.cs ha esito negativo con l'errore seguente sulla chiamata al metodo diretto:

    ```
    Unhandled exception. Microsoft.Azure.Devices.Common.Exceptions.UnauthorizedException: {"Message":"{\"errorCode\":401002,\"trackingId\":\"b1da85801b2e4faf951a2291a2c467c3-G:32-TimeStamp:04/06/2020 17:15:11\",\"message\":\"Unauthorized\",\"timestampUtc\":\"2020-04-06T17:15:11.6990676Z\"}","ExceptionMessage":""}
    
        at Microsoft.Azure.Devices.HttpClientHelper.ExecuteAsync(HttpClient httpClient, HttpMethod httpMethod, Uri requestUri, Func`3 modifyRequestMessageAsync, Func`2 isMappedToException, Func`3 processResponseMessageAsync, IDictionary`2 errorMappingOverrides, CancellationToken cancellationToken)
    
        at Microsoft.Azure.Devices.HttpClientHelper.ExecuteAsync(HttpMethod httpMethod, Uri requestUri, Func`3 modifyRequestMessageAsync, Func`3 processResponseMessageAsync, IDictionary`2 errorMappingOverrides, CancellationToken cancellationToken)
        
        at Microsoft.Azure.Devices.HttpClientHelper.PostAsync[T,T2](Uri requestUri, T entity, TimeSpan operationTimeout, IDictionary`2 errorMappingOverrides, IDictionary`2 customHeaders, CancellationToken cancellationToken)…
    ```

    1. Assicurarsi di aver installato gli [strumenti di Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) Internet per l'uso nell'ambiente di Visual Studio Code e la connessione alla configurazione dell'hub Internet. (CTRL + MAIUSC + P, quindi selezionare il metodo dell'hub Internet per connettersi alla sottoscrizione e all'hub delle cose)
1. Controllare se è possibile richiamare un metodo diretto sul modulo perimetrale tramite VS Code (ad esempio, chiamare GraphToplogyList con il payload seguente {" @apiVersion ": "1,0"}) ed è necessario recuperare la risposta seguente. 

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

    ![Visual Studio Code](./media/troubleshoot-how-to/visual-studio-code1.png)
1. Se l'operazione non riesce, provare a eseguire le operazioni seguenti:
    1. Passare al prompt dei comandi sul dispositivo perimetrale e digitare.
    
    ```
    sudo systemctl restart iotedge
    ```

    Il dispositivo perimetrale e tutti i moduli verrà riavviato. Attendere alcuni minuti ed eseguire il comando seguente per verificare che i moduli siano in esecuzione, prima di provare a usare nuovamente DirectMethod.

    ```
    sudo iotedge list
    ```
    1. Se sopra si verifica un errore, provare a riavviare la macchina virtuale o il computer.
    1. Se l'operazione non riesce, eseguire il comando seguente per ottenere un file ZIP con tutti i [log rilevanti](https://docs.microsoft.com/azure/iot-edge/troubleshoot#gather-debug-information-with-support-bundle-command) da allegare a tale [ticket di supporto](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

    ```
    sudo iotedge support-bundle --since 2h
    ```
1. Se viene ricevuto un codice di errore 400, verificare che il payload della chiamata al metodo sia ben formato in base alla guida al [metodo diretto](direct-methods.md) .
1. Se si ottiene il codice di stato 200, significa che l'hub funziona correttamente e che la distribuzione del modulo è corretta e reattiva. Il passaggio successivo consiste nel verificare se le configurazioni dell'app sono accurate. La configurazione dell'app è costituita dai campi seguenti nel appsettings.jssu file. Verificare che deviceId e moduleId siano accurati. Un modo semplice per verificarlo è la sezione relativa all'estensione dell'hub Azure Internet in VSCode. I valori in appsettings.jsnel file e nella sezione hub Internet devono corrispondere.
    
    ```
    {
        "IoThubConnectionString" : 
        "deviceId" : 
        "moduleId" : 
    }
    ```

    ![HUB INTERNET DELLE COSE](./media/troubleshoot-how-to/iot-hub.png)

1. Assicurarsi infine che all'interno appsettings.jssia stata fornita la stringa di connessione dell'hub Internet e non la stringa di connessione del dispositivo dell'hub Internet, perché i [formati](https://devblogs.microsoft.com/iotdev/understand-different-connection-strings-in-azure-iot-hub/) sono diversi.

### <a name="live-video-analytics-working-with-external-modules"></a>Analisi video in tempo reale utilizzo di moduli esterni

L'analisi video in tempo reale tramite il processore di estensione HTTP può estendere il grafico multimediale per inviare e ricevere dati da altri moduli IoT Edge tramite HTTP tramite REST.  Come [esempio specifico](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/httpExtension) , il grafico multimediale può inviare fotogrammi video come immagini a un modulo di inferenza esterno come Yolo V3 e ricevere risultati analitici basati su JSON. In una topologia di questo tipo, la destinazione finale per gli eventi è principalmente l'hub Internet. In situazioni in cui non vengono visualizzati gli eventi di inferenza nell'hub, verificare quanto segue:

1. Controllare se l'hub in cui viene pubblicato il grafo multimediale è uguale a quello che si sta esaminando. In alcuni casi, quando si creano più distribuzioni, si otterranno più hub e si potrà controllare erroneamente l'hub errato per gli eventi.
1. Controllare tramite VSCode se il modulo esterno è distribuito e in esecuzione. Nell'immagine di esempio, rtspsim e CV sono IoT Edge moduli in esecuzione esterna al modulo lvaEdge.

    ![HUB INTERNET DELLE COSE](./media/troubleshoot-how-to/iot-hub.png)
1. Controllare se si stanno inviando eventi all'endpoint URL corretto. Il contenitore esterno per intelligenza artificiale espone un URL e una porta su cui riceve e restituisce i dati dalle richieste POST. Questo URL viene specificato come proprietà endpoint: URL per il processore di estensione http. Come osservato nell' [URL della topologia](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/topology.json) è impostato sul parametro URL di inferenza. Verificare che il valore predefinito per il parametro http://yolov3/score) o il valore passato sia accurato ed è possibile verificare se funziona usando curl.  
    1. Ad esempio, il contenitore Yolo V3 in esecuzione nel computer locale e l'indirizzo IP per il contenitore sono 172.17.0.3 (usare Docker ispeziona per trovare l'indirizzo IP).

    ```
    curl -X POST http://172.17.0.3/score -H "Content-Type: image/jpeg" --data-binary @<fullpath to jpg>
    ```

    Risultato restituito:

    ```
    {"inferences": [{"type": "entity", "entity": {"tag": {"value": "car", "confidence": 0.8668569922447205}, "box": {"l": 0.3853073438008626, "t": 0.6063712999658677, "w": 0.04174524943033854, "h": 0.02989496027381675}}}]}
    ```

1. Se si eseguono una o più istanze di un grafico sfruttando il processore di estensione http, è necessario disporre di un filtro frequenza dei fotogrammi prima che ogni processore di estensione http gestisca i frame al secondo (fps) del feed video. In alcune situazioni in cui la CPU e la memoria del computer perimetrale sono molto utilizzate, è possibile perdere determinati eventi di inferenza. Per risolvere questo problema, impostare un valore basso per la proprietà maximumFps sul filtro della frequenza dei fotogrammi. È possibile impostarlo su 0,5 ("maximumFps": 0,5) in ogni istanza del grafo e rieseguire il controllo per verificare la presenza di eventi di inferenza nell'hub.
    1. In alternativa, è anche possibile ottenere un computer Edge più potente con CPU e memoria più elevate.
    
### <a name="multiple-direct-methods-in-parallel--timeout-failure"></a>Più metodi diretti in parallelo: errore di timeout 

Analisi video in tempo reale su IoT Edge fornisce un modello di programmazione basato su metodo diretto che consente di configurare più topologie e più istanze di Graph. Nell'ambito della configurazione della topologia e del grafo, verranno richiamate più chiamate al metodo diretto sul modulo perimetrale. Se si richiamano queste chiamate a più metodi, in particolare quelle che avviano e arrestano i grafici, in parallelo, è possibile che si verifichino alcuni errori di timeout, ad esempio quelli riportati di seguito. 

Il metodo di inizializzazione dell'assembly Microsoft.Media.LiveVideoAnalytics.Test.Feature.Edge.AssemblyInitializer.InitializeAssemblyAsync ha generato un'eccezione. Microsoft. Azure. Devices. Common. Exceptions. IotHubException: Microsoft. Azure. Devices. Common. Exceptions. IotHubException:<br/> `{"Message":"{\"errorCode\":504101,\"trackingId\":\"55b1d7845498428593c2738d94442607-G:32-TimeStamp:05/15/2020 20:43:10-G:10-TimeStamp:05/15/2020 20:43:10\",\"message\":\"Timed out waiting for the response from device.\",\"info\":{},\"timestampUtc\":\"2020-05-15T20:43:10.3899553Z\"}","ExceptionMessage":""}. Aborting test execution. `

Si consiglia di non chiamare metodi diretti in modo parallelo, ma in modo sequenziale, ad esempio  una chiamata al metodo diretto solo dopo il completamento di quella precedente.

### <a name="collecting-logs-for-submitting-a-support-ticket"></a>Raccolta dei log per l'invio di un ticket di supporto

Quando i passaggi di risoluzione dei problemi autoguidati non consentono di risolvere i problemi, è necessario passare alla portale di Azure e [aprire un ticket di supporto](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).

Eseguire i passaggi seguenti per raccogliere i log rilevanti che devono essere aggiunti al ticket. Sarà possibile caricare i file di log nella scheda **Dettagli** della richiesta di supporto.

### <a name="support-bundle"></a>Supporto-bundle

Quando è necessario raccogliere i log da un dispositivo IoT Edge, il modo più semplice consiste nell'usare il `support-bundle` comando. Questo comando raccoglie:

- Log dei moduli
- IoT Edge Security Manager e i log del motore del contenitore
- Iotedge controllare l'output JSON
- Informazioni di debug utili

#### <a name="use-the-iot-edge-security-manager"></a>Usare Gestione sicurezza IoT Edge
 
Il IoT Edge Security Manager è responsabile di operazioni quali l'inizializzazione del sistema IoT Edge all'avvio e il provisioning dei dispositivi. Se IoT Edge non viene avviato, è possibile che i log del gestore sicurezza forniscano informazioni utili. Per visualizzare log più dettagliati di IoT Edge Security Manager:

1. Modificare le impostazioni del daemon di IoT Edge sul dispositivo Edge:

    ```
    sudo systemctl edit iotedge.service
    ```

1. Aggiornare le righe seguenti:

    ```
    [Service]
    Environment=IOTEDGE_LOG=edgelet=debug
    ```

1. Riavviare il daemon di sicurezza di IoT Edge eseguendo i comandi seguenti:

    ```
    sudo systemctl cat iotedge.service
    sudo systemctl daemon-reload
    sudo systemctl restart iotedge
    ```

1. Eseguire il `support-bundle` comando con il flag--since per specificare per quanto tempo dal passato si desidera ottenere i log. Ad esempio, 2h otterrà i log dopo le ultime due ore. È possibile modificare il valore di questo flag per includere i log per un periodo diverso.

    ```
    sudo iotedge support-bundle --since 2h
    ```

### <a name="lva-debug-logs"></a>Log di debug LVA

Seguire questa procedura per configurare LVA in IoT Edge modulo per generare i log di debug:

1. Accedere al [portale di Azure](https://portal.azure.com) e passare all'hub IoT.
1. Selezionare **IoT Edge** dal menu.
1. Fare clic sull'ID del dispositivo di destinazione nell'elenco dei dispositivi.
1. Fare clic sul collegamento **imposta moduli** nel menu in alto.

  ![impostare i moduli portale di Azure](media/troubleshoot-how-to/set-modules.png)

5. Nella sezione moduli IoT Edge trovare e fare clic su **lvaEdge**.
1. Fare clic su **contenitore crea opzioni**.
1. Nella sezione binds aggiungere il comando seguente:

    `/var/local/mediaservices/logs:/var/lib/azuremediaservices/logs`

    Questa operazione associa le cartelle dei log tra il dispositivo perimetrale e il contenitore.

1. Fare clic sul pulsante **Aggiorna**
1. Fare clic sul pulsante **Verifica e crea** nella parte inferiore della pagina. Viene eseguita una convalida semplice e viene pubblicato un messaggio di convalida con esito positivo in un banner verde.
1. Fare clic sul pulsante **Crea**.
1. Aggiornare quindi il **modulo Identity gemelle** in modo che punti al parametro DebugLogsDirectory in modo che punti alla directory in cui verranno raccolti i log:
    1. Selezionare **lvaEdge** nella tabella **moduli** .
    1. Fare clic sul collegamento **Module Identity gemelle** . Che verrà trovato nella parte superiore della pagina. Verrà aperto un riquadro modificabile.
    1. Aggiungere la coppia chiave-valore seguente nella **chiave desiderata**:

        `"DebugLogsDirectory": "/var/lib/azuremediaservices/logs"`

    1. Fare clic su **Save**.

1. Riprodurre il problema.
1. Connettersi alla macchina virtuale dalla pagina hub Internet delle cose nel portale.
1. Passare alla cartella e comprimere `/var/local/mediaservices/logs` il contenuto del cestino della cartella e condividerlo con noi. Questi file di log non sono destinati alla diagnosi automatica. Sono progettati per la progettazione di Azure per analizzare i problemi.

1. È possibile arrestare la raccolta di log impostando nuovamente il valore nel **modulo Identity gemelle** su *null* . Tornare alla pagina **Module Identity gemelle** e aggiornare il parametro seguente come:

    `"DebugLogsDirectory": ""`

## <a name="next-steps"></a>Passaggi successivi

[Esercitazione: Registrazione video basata su eventi nel cloud e riproduzione dal cloud](event-based-video-recording-tutorial.md)
