---
title: Analisi di flusso di Azure in IoT Edge (anteprima)
description: Creare processi Edge in Analisi di flusso di Azure e distribuirli in dispositivi che eseguono Azure IoT Edge.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/16/2017
ms.openlocfilehash: a9d3b92b9cb3334c8c52a9127a2fab92d187e3d9
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2018
ms.locfileid: "51687436"
---
# <a name="azure-stream-analytics-on-iot-edge-preview"></a>Analisi di flusso di Azure in IoT Edge (anteprima)

> [!IMPORTANT]
> Questa funzionalità è disponibile in anteprima e non è consigliabile usarla nell'ambiente di produzione.
 
Analisi di flusso di Azure in IoT Edge permette agli sviluppatori di distribuire funzionalità di intelligence di analisi quasi in tempo reale più vicino ai dispositivi IoT, in modo da ottenere il massimo valore dai dati generati dai dispositivi. Analisi di flusso di Azure è progettato per offrire bassa latenza, resilienza, uso efficiente della larghezza di banda e conformità. Le organizzazioni possono ora distribuire una logica di controllo prossima alle operazioni industriali e integrare l'analisi di Big Data eseguita nel cloud.  

L'Analisi di flusso di Azure su IoT Edge viene eseguita all'interno del framework di [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/). Dopo la creazione del processo in ASA, è possibile distribuire e gestire i processi di ASA tramite hub IoT. Questa funzionalità è in anteprima. In caso di domande o suggerimenti, è possibile usare [questo sondaggio](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u) per contattare il team del prodotto. 

## <a name="scenarios"></a>Scenari
![Diagramma di alto livello](media/stream-analytics-edge/ASAedge_highlevel.png)

* **Comando e controllo a bassa latenza**: ad esempio, i sistemi di sicurezza di produzione devono rispondere ai dati operativi con una latenza estremamente bassa. Con Active Server Application in IoT Edge, è possibile analizzare i dati dei sensori quasi in tempo reale ed eseguire comandi in caso di rilevamento di anomalie per arrestare una macchina o attivare avvisi.
*   **Connettività al cloud limitata**: per i sistemi mission-critical, come le attrezzature per le attività minerarie in remoto, le imbarcazioni connesse o le trivellazioni offshore, è fondamentale analizzare e reagire ai dati, anche quando la connettività cloud è intermittente. Con Analisi di flusso di Azure, la logica di streaming viene eseguita indipendentemente dalla connettività di rete ed è possibile scegliere cosa inviare al cloud per un'ulteriore elaborazione o archiviazione.
* **Larghezza di banda limitata**: il volume dei dati generati dai motori a reazione o dalle automobili connesse può essere talmente elevato che i dati devono essere filtrati o pre-elaborati prima di essere inviati al cloud. Usando Analisi di flusso di Azure, è possibile filtrare o aggregare i dati da inviare al cloud.
* **Conformità**: la conformità alle normative potrebbe richiedere che alcuni dati siano resi anonimi o aggregati in locale prima di essere inviati al cloud.

## <a name="edge-jobs-in-azure-stream-analytics"></a>Processi Edge in Analisi di flusso di Azure
### <a name="what-is-an-edge-job"></a>Che cos'è un processo Edge?

I processi Edge di Analisi di flusso di Azure vengono eseguiti come moduli all'interno del [runtime di Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works). Sono costituiti da due parti:
1.  Una parte nel cloud responsabile della definizione del processo: gli utenti definiscono input, output, query e altre impostazioni (eventi non in ordine e così via) nel cloud.
2.  Il modulo di Analisi di flusso di Azure in IoT Edge che viene eseguito localmente. Contiene il motore di elaborazione di eventi complessi di Analisi di flusso di Azure e riceve la definizione del processo dal cloud. 

Analisi di flusso di Azure usa un hub IoT per distribuire i processi Edge ai dispositivi. Altre informazioni sulla [distribuzione di IoT Edge sono disponibili qui](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).

![Processo Edge](media/stream-analytics-edge/ASAedge_job.png)


### <a name="installation-instructions"></a>Istruzioni per l'installazione
Le procedure generali sono descritte nella tabella seguente. Altri dettagli sono disponibili nelle sezioni successive.
|      |Passaggio   | Posizione     | Note   |
| ---   | ---   | ---       |  ---      |
| 1   | **Creare un contenitore di archiviazione**   | Portale di Azure       | I contenitori di archiviazione vengono usati per salvare la definizione del processo in cui sono accessibili dai dispositivi IoT. <br>  È possibile riusare qualsiasi contenitore di archiviazione esistente.     |
| 2   | **Creare un processo Edge di Analisi di flusso di Azure**   | Portale di Azure      |  Creare un nuovo processo, selezionare **Edge** come **Ambiente host**. <br> Questi processi vengono creati e gestiti dal cloud ed eseguiti nei dispositivi IoT Edge.     |
| 3   | **Configurare l'ambiente IoT Edge nei dispositivi**   | Dispositivo/i      | Istruzioni per [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) o [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).          |
| 4   | **Distribuire Analisi di flusso di Azure nei dispositivi IoT Edge**   | Portale di Azure      |  La definizione del processo di Analisi di flusso di Azure viene esportata nel contenitore di archiviazione creato in precedenza.       |
È possibile seguire [questa esercitazione dettagliata](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics) per distribuire il primo processo di Analisi di flusso di Azure in IoT Edge. Il video seguente fornisce informazioni sull'esecuzione di un processo di Analisi di flusso in un dispositivo IoT Edge:  


> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T157/player]

#### <a name="create-a-storage-container"></a>Creare un contenitore di archiviazione
Per esportare la query compilata e la configurazione del processo di Analisi di flusso di Azure, è necessario un contenitore di archiviazione, che viene usato per configurare l'immagine Docker di Analisi di flusso di Azure con la query specifica. 
1. Seguire [queste istruzioni](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) per creare un account di archiviazione dal portale di Azure. È possibile mantenere tutte le opzioni predefinite per usare questo account con Analisi di flusso di Azure.
2. Nell'account di archiviazione appena creato creare un contenitore di archiviazione BLOB:
    1. Fare clic su **BLOB** e quindi su **+ Contenitore**. 
    2. Immettere un nome e mantenere il contenitore **Privato**.

#### <a name="create-an-asa-edge-job"></a>Creare un processo Edge di Analisi di flusso di Azure
> [!Note]
> Questa esercitazione è incentrata sulla creazione di processi ASA tramite il portale di Azure. È anche possibile [usare il plug-in Visual Studio per creare un processo Edge ASA](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-edge-jobs)

1. Dal portale di Azure creare un nuovo "processo di Analisi di flusso". [Collegamento diretto per la creazione di un nuovo processo di Analisi di flusso di Azure](https://ms.portal.azure.com/#create/Microsoft.StreamAnalyticsJob).

2. Nella schermata di creazione selezionare **Edge** come **Ambiente host** (vedere la figura seguente).

   ![Creazione del processo](media/stream-analytics-edge/ASAEdge_create.png)
3. Definizione del processo
    1. **Define Input Stream(s)** (Definisci flussi di input). Definire uno o più flussi di input per il processo.
    2. Define Reference data (Definisci dati di riferimento) (facoltativo).
    3. **Define Output Stream(s)** (Definisci flussi di output). Definire uno o più flussi di ouput per il processo. 
    4. **Definisci query**. Definire la query di Analisi di flusso di Azure nel cloud tramite l'editor inline. Il compilatore verifica automaticamente la sintassi abilitata per i dispositivi Edge di Analisi di flusso di Azure. È anche possibile testare la query caricando dati di esempio. 

4. Impostare le informazioni sul contenitore di archiviazione nel menu **Impostazioni di IoT Edge**.

5. Configurare le impostazioni facoltative
    1. **Ordinamento eventi**. È possibile configurare criteri non ordinati nel portale. La documentazione è disponibile [qui](https://msdn.microsoft.com/library/azure/mt674682.aspx?f=255&MSPPError=-2147217396).
    2. **Impostazioni locali**. Impostare il formato di internalizzazione.



> [!Note]
> Quando viene creata una distribuzione, Analisi di flusso di Azure esporta la definizione del processo in un contenitore di archiviazione. Questa definizione del processo rimane invariata per tutta la durata di una distribuzione. Di conseguenza, se si vuole aggiornare un processo in esecuzione in un dispositivo Edge, è necessario modificare il processo in Analisi di flusso di Azure e quindi creare una nuova distribuzione nell'hub IoT.


#### <a name="set-up-your-iot-edge-environment-on-your-devices"></a>Configurare l'ambiente IoT Edge nei dispositivi
I processi Edge possono essere distribuiti nei dispositivi che eseguono Azure IoT Edge.
A tale scopo, è necessario seguire questa procedura:
- Creare un hub IoT.
- Installare Docker e il runtime IoT Edge nei dispositivi Edge.
- Configurare i dispositivi come **Dispositivi IoT Edge** nell'hub IoT.

Questi passaggi sono descritti nella documentazione di IoT Edge per [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) o [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).  


####  <a name="deployment-asa-on-your-iot-edge-devices"></a>Distribuzione di Analisi di flusso di Azure nei dispositivi IoT Edge
##### <a name="add-asa-to-your-deployment"></a>Aggiungere Analisi di flusso di Azure alla distribuzione
- Nel portale di Azure aprire l'hub IoT, passare a **IoT Edge** e fare clic sul dispositivo da usare come destinazione per questa distribuzione.
- Selezionare **Imposta moduli**, quindi selezionare **+ Aggiungi** e scegliere il **modulo di Analisi di flusso di Azure**.
- Selezionare la sottoscrizione e il processo Edge di Analisi di flusso di Azure creato. Fare clic su Save.
![Aggiungere un modulo di Analisi di flusso di Azure nella distribuzione](media/stream-analytics-edge/set_module.png)


> [!Note]
> Durante questo passaggio Analisi di flusso di Azure crea una cartella denominata "EdgeJobs" nel contenitore di archiviazione (se non esiste già). Per ogni distribuzione, viene creata una nuova sottocartella nella cartella "EdgeJobs".
> Per distribuire il processo ai dispositivi Edge, Analisi di flusso di Azure crea una firma di accesso condiviso per il file di definizione del processo. La chiave della firma di accesso condiviso viene trasmessa in modo sicuro ai dispositivi IoT Edge tramite dispositivo gemello. Questa chiave ha una scadenza di tre anni dalla data di creazione.


Per altre informazioni sulle distribuzioni IoT Edge, vedere [questa pagina](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).


##### <a name="configure-routes"></a>Configurare le route
IoT Edge consente di instradare in modo dichiarativo i messaggi tra i moduli e tra i moduli e l'hub IoT. La sintassi completa è descritta [qui](https://docs.microsoft.com/azure/iot-edge/module-composition).
I nomi di input e output creati nel processo di Analisi di flusso di Azure possono essere usati come endpoint per il routing.  

###### <a name="example"></a>Esempio
```
{
"routes": {                                              
    "sensorToAsa":   "FROM /messages/modules/tempSensor/* INTO BrokeredEndpoint(\"/modules/ASA/inputs/temperature\")",
    "alertsToCloud": "FROM /messages/modules/ASA/* INTO $upstream", 
    "alertsToReset": "FROM /messages/modules/ASA/* INTO BrokeredEndpoint(\"/modules/tempSensor/inputs/control\")" 
}
}   

```
Questo esempio mostra le route per lo scenario descritto nella figura seguente. Contiene un processo Edge chiamato "**ASA**", con un input denominato "**temperature**"e un output denominato"**alert**".
![Esempio di routing](media/stream-analytics-edge/RoutingExample.png)

Questo esempio definisce le route seguenti:
- Tutti i messaggi da **tempSensor** vengono inviati al modulo denominato **ASA** all'input denominato **temperature**.
- Tutti gli output del modulo **ASA** vengono inviati all'hub IoT collegato a questo dispositivo ($upstream).
- Tutti gli output del modulo **ASA** vengono inviati all'endpoint **control** di **tempSensor**.


## <a name="technical-information"></a>Informazioni tecniche
### <a name="current-limitations-for-edge-jobs-compared-to-cloud-jobs"></a>Limitazioni correnti per i processi Edge rispetto ai processi cloud
L'obiettivo è di ottenere una parità tra i processi Edge e i processi cloud. La maggior parte delle funzionalità del linguaggio di query SQL è già supportata.
Tuttavia, le funzionalità seguenti non sono ancora supportate per i processi Edge:
* Funzioni definite dall'utente (UDF) e aggregazioni definite dall'utente (UDA).
* Funzioni di Azure Machine Learning.
* Uso di più di 14 aggregati in un unico passaggio.
* Formato AVRO per l'input/output. In questo momento sono supportati solo CSV e JSON.
* Operatori SQL seguenti:
    * AnomalyDetection
    * Operatori geospaziali:
        * CreatePoint
        * CreatePolygon
        * CreateLineString
        * ST_DISTANCE
        * ST_WITHIN
        * ST_OVERLAPS
        * ST_INTERSECTS
    * PARTITION BY
    * GetMetadataPropertyValue


### <a name="runtime-and-hardware-requirements"></a>Requisiti hardware e di runtime
Per eseguire Analisi di flusso di Azure in IoT Edge, è necessario disporre di dispositivi in grado di eseguire [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/). 

Analisi di flusso di Azure e Azure IoT Edge usano contenitori **Docker** per fornire una soluzione portatile che viene eseguita in più sistemi operativi host (Windows, Linux).

Analisi di flusso di Azure in IoT Edge è reso disponibile come immagini Windows e Linux, in esecuzione in architetture di Azure Resource Manager o x86-64. 


### <a name="input-and-output"></a>Input e output
#### <a name="input-and-output-streams"></a>Flussi di input e output
I processi Edge di Analisi di flusso di Azure possono ottenere input e output da altri moduli in esecuzione in dispositivi IoT Edge. Per la connessione da e a moduli specifici, è possibile impostare la configurazione di routing in fase di distribuzione. Altre informazioni sono disponibili nella [documentazione relativa alla composizione di un modulo IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-composition).

Per input e output sono supportati i formati CSV e JSON.

Per ogni flusso di input e di output creato nel processo di Analisi di flusso di Azure, viene creato un endpoint corrispondente nel modulo distribuito. Questi endpoint possono essere usati nelle route della distribuzione.

Al momento, gli unici tipi supportati di input e output del flusso sono rappresentati dall'hub Edge. L'input di riferimento supporta il tipo di file di riferimento. Altri output possono essere ottenuti tramite un downstream del processo cloud. Ad esempio, un processo di Analisi di flusso ospitato in Edge invia l'output all'hub Edge, che può quindi inviare l'output all'hub IoT. È possibile usare un secondo processo di Analisi di flusso di Azure ospitato sul cloud con l'input dall'hub IoT e l'output in Power BI o un altro tipo di output.



##### <a name="reference-data"></a>Dati di riferimento
I dati di riferimento (noti anche come tabella di ricerca) sono un set di dati limitato di natura statica o che cambia molto lentamente, usato per eseguire una ricerca o per la correlazione con il flusso di dati. Per usare i dati di riferimento in un processo di Analisi di flusso di Azure, si usa in genere un [JOIN dei dati di riferimento](https://docs.microsoft.com/stream-analytics-query/reference-data-join-azure-stream-analytics) nella query. Per altre informazioni, vedere [Uso dei dati di riferimento per le ricerche in Analisi di flusso](stream-analytics-use-reference-data.md).

Sono supportati solo dati di riferimento locali. Quando un processo viene distribuito in un dispositivo IoT Edge, carica i dati di riferimento dal percorso file definito dall'utente.

Per creare un processo con i dati di riferimento in Edge:

1. Creare un nuovo input per il processo.

2. Scegliere **Dati di riferimento** come **Tipo di origine**.

3. Tenere un file di dati di riferimento pronto nel dispositivo. Per un contenitore Windows, inserire il file di dati di riferimento nell'unità locale e condividere l'unità locale con il contenitore Docker. Per un contenitore Linux, creare un volume Docker e popolare il file di dati nel volume.

4. Impostare il percorso del file. Per un dispositivo Windows, usare il percorso assoluto. Per un dispositivo Linux, usare il percorso nel volume.

![Nuovo input dei dati di riferimento per il processo Analisi di flusso di Azure in IoT Edge](./media/stream-analytics-edge/ReferenceDataNewInput.png)

L'aggiornamento dei dati di riferimento in IoT Edge viene attivato da una distribuzione. Una volta attivato, il modulo ASA seleziona i dati aggiornati senza interrompere il processo in esecuzione.

È possibile aggiornare i dati di riferimento in due modi:
* Aggiornare il percorso dei dati di riferimento nel processo ASA dal portale di Azure.
* Aggiornare la distribuzione IoT Edge.


## <a name="license-and-third-party-notices"></a>Licenza e comunicazioni di terze parti
* [Licenza per Analisi di flusso di Azure in IoT Edge (anteprima)](https://go.microsoft.com/fwlink/?linkid=862827). 
* [Comunicazioni di terze parti per Analisi di flusso di Azure in IoT Edge (anteprima)](https://go.microsoft.com/fwlink/?linkid=862828).

## <a name="get-help"></a>Ottenere aiuto
Per assistenza, provare il [Forum di Analisi di flusso di Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).


## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni su Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works)
* [Esercitazione su Analisi di flusso di Azure in IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics)
* [Invio di commenti e suggerimenti al team tramite questo sondaggio](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u) 
* [Sviluppare processi Edge di Analisi di flusso usando gli strumenti di Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-edge-jobs)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
