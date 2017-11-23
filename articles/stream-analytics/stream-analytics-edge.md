---
title: Analisi di flusso di Azure in IoT Edge (anteprima)
description: Creare processi Edge in Analisi di flusso di Azure e distribuirli in dispositivi che eseguono Azure IoT Edge.
keywords: flusso dei dati, iot, edge
services: stream-analytics
documentationcenter: 
author: jseb225
manager: jhubbard
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 11/15/2017
ms.author: jeanb
ms.openlocfilehash: 6e94758581bd510e58a709a53e30c11a5c1f1b62
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2017
---
# <a name="azure-stream-analytics-on-iot-edge-preview"></a>Analisi di flusso di Azure in IoT Edge (anteprima)

> [!IMPORTANT]
> Questa funzionalità è in anteprima. Non è consigliabile usarla nell'ambiente di produzione.
 
Analisi di flusso di Azure in IoT Edge permette agli sviluppatori di distribuire funzionalità di intelligence di analisi quasi in tempo reale più vicino ai dispositivi IoT, in modo da ottenere il massimo valore dai dati generati dai dispositivi. Progettato per i clienti che richiedono bassa latenza, resilienza, uso efficiente della larghezza di banda e conformità, consente alle organizzazioni di distribuire una logica di controllo prossima alle operazioni industriali e di integrare l'analisi di Big Data eseguita nel cloud.  
Analisi di flusso di Azure in IoT Edge viene eseguito all'interno del framework [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/) e la distribuzione e la gestione dei processi di Analisi di flusso di Azure possono essere eseguite tramite un hub IoT dopo che il processo è stato creato in Analisi di flusso di Azure.
Questa funzionalità è in anteprima. In caso di domande o suggerimenti, è possibile usare [questo sondaggio](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u) per contattare il team del prodotto. 

## <a name="scenarios"></a>Scenari
![Diagramma generale](media/stream-analytics-edge/ASAedge_highlevel.png) Ecco alcuni scenari tipici per i quali l'esecuzione di Analisi di flusso di Azure in dispositivi Edge si rivela particolarmente interessante:
* **Comando e controllo a bassa latenza**: ad esempio, per i sistemi di sicurezza di produzione è indispensabile rispondere ai dati operativi con una latenza estremamente bassa. Con Analisi di flusso di Azure in IoT Edge, è possibile analizzare i dati dei sensori quasi in tempo reale ed eseguire comandi in caso di rilevamento di anomalie per arrestare una macchina o attivare avvisi.
*   **Connettività al cloud limitata**: per i sistemi mission-critical, come le attrezzature per le attività minerarie in remoto, le imbarcazioni connesse o le trivellazioni offshore, è fondamentale analizzare e reagire ai dati, anche quando la connettività cloud è intermittente. Con Analisi di flusso di Azure, la logica di streaming viene eseguita indipendentemente dalla connettività di rete ed è possibile scegliere cosa inviare al cloud per un'ulteriore elaborazione o archiviazione.
* **Larghezza di banda limitata**: il volume dei dati generati dai motori a reazione o dalle automobili connesse può essere talmente elevato che i dati devono essere filtrati o pre-elaborati prima di essere inviati al cloud. Usando Analisi di flusso di Azure, è possibile filtrare o aggregare i dati da inviare al cloud.
* **Conformità**: la conformità alle normative potrebbe richiedere che alcuni dati siano resi anonimi o aggregati in locale prima di essere inviati al cloud. Con Analisi di flusso di Azure, 

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
| 1   | **Creare un processo Edge di Analisi di flusso di Azure**   | Portale di Azure      |  Creare un nuovo processo, selezionare **Edge** come **Ambiente host**. <br> Questi processi vengono creati e gestiti dal cloud ed eseguiti nei dispositivi IoT Edge.     |
| 2   | **Creare un contenitore di archiviazione**   | Portale di Azure       | I contenitori di archiviazione vengono usati per salvare la definizione del processo in cui sono accessibili dai dispositivi IoT. <br>  È possibile riusare qualsiasi contenitore di archiviazione esistente.     |
| 3   | **Configurare l'ambiente IoT Edge nei dispositivi**   | Dispositivo/i      | Istruzioni per [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) o [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).          |
| 4   | **Distribuire Analisi di flusso di Azure nei dispositivi IoT Edge**   | Portale di Azure      |  La definizione del processo di Analisi di flusso di Azure viene esportata nel contenitore di archiviazione creato in precedenza.       |
È possibile seguire [questa esercitazione dettagliata](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics) per distribuire il primo processo di Analisi di flusso di Azure in IoT Edge.

#### <a name="create-an-asa-edge-job"></a>Creare un processo Edge di Analisi di flusso di Azure
1. Dal portale di Azure creare un nuovo "processo di Analisi di flusso". [Collegamento diretto per la creazione di un nuovo processo di Analisi di flusso di Azure](https://ms.portal.azure.com/#create/Microsoft.StreamAnalyticsJob).
2. Nella schermata di creazione selezionare **Edge** come **Ambiente host** (vedere la figura seguente). ![Creazione del processo](media/stream-analytics-edge/ASAEdge_create.png)
3. Definizione del processo
    1. **Define Input Stream(s)** (Definisci flussi di input). Definire uno o più flussi di input per il processo.
    2. Define Reference data (Definisci dati di riferimento) (facoltativo).
    3. **Define Output Stream(s)** (Definisci flussi di output). Definire uno o più flussi di ouput per il processo. 
    4. **Definisci query**. Definire la query di Analisi di flusso di Azure nel cloud tramite l'editor inline. Il compilatore verifica automaticamente la sintassi abilitata per i dispositivi Edge di Analisi di flusso di Azure. È anche possibile testare la query caricando dati di esempio. 
4. Configurare le impostazioni facoltative
    1. **Ordinamento eventi**. È possibile configurare criteri non ordinati nel portale. La documentazione è disponibile [qui](https://msdn.microsoft.com/library/azure/mt674682.aspx?f=255&MSPPError=-2147217396).
    2. **Impostazioni locali**. Impostare il formato di internalizzazione.


#### <a name="create-a-storage-container"></a>Creare un contenitore di archiviazione
Per esportare la query compilata e la configurazione del processo di Analisi di flusso di Azure, è necessario un contenitore di archiviazione, che viene usato per configurare l'immagine Docker di Analisi di flusso di Azure con la query specifica. 
1. Seguire [queste istruzioni](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) per creare un account di archiviazione dal portale di Azure. È possibile mantenere tutte le opzioni predefinite per usare questo account con Analisi di flusso di Azure.
2. Nell'account di archiviazione appena creato creare un contenitore di archiviazione BLOB:
    1. Fare clic su "BLOB" e quindi su "+ Contenitore". 
    2. Immettere un nome e mantenere il contenitore "Privato".


> [!Note]
> Quando viene creata una distribuzione, Analisi di flusso di Azure esporta la definizione del processo in un contenitore di archiviazione. Questa definizione del processo rimane invariata per tutta la durata di una distribuzione. Di conseguenza, se si vuole aggiornare un processo in esecuzione in un dispositivo Edge, è necessario modificare il processo in Analisi di flusso di Azure e quindi creare una nuova distribuzione nell'hub IoT.


#### <a name="set-up-your-iot-edge-environment-on-your-devices"></a>Configurare l'ambiente IoT Edge nei dispositivi
I processi Edge possono essere distribuiti nei dispositivi che eseguono Azure IoT Edge.
A tale scopo, è necessario seguire questa procedura:
- Creare un hub IoT.
- Installare Docker e il runtime di IoT Edge nei dispositivi Edge.
- Configurare i dispositivi come "Dispositivi IoT Edge" nell'hub IoT.

Questi passaggi sono descritti nella documentazione di IoT Edge per [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) o [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).  


####  <a name="deployment-asa-on-your-iot-edge-devices"></a>Distribuzione di Analisi di flusso di Azure nei dispositivi IoT Edge
##### <a name="add-asa-to-your-deployment"></a>Aggiungere Analisi di flusso di Azure alla distribuzione
- Nel portale di Azure aprire l'hub IoT, passare a IoT Edge Explorer e aprire il pannello del dispositivo.
- Selezionare **Set modules** (Imposta moduli) e quindi selezionare **Import Azure Service IoT Edge Module** (Importa modulo IoT Edge del servizio di Azure).
- Selezionare la sottoscrizione e il processo Edge di Analisi di flusso di Azure creato. Selezionare quindi l'account di archiviazione. Fare clic su Save.
![Aggiungere un modulo di Analisi di flusso di Azure nella distribuzione](media/stream-analytics-edge/set_module.png)


> [!Note]
> Durante questo passaggio, Analisi di flusso di Azure richiede l'accesso al contenitore di archiviazione selezionato e quindi crea una cartella denominata "EdgeJobs". Per ogni distribuzione, viene creata una nuova sottocartella nella cartella "EdgeJobs".
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
* Compressione dell'input JSON.
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

Analisi di flusso di Azure in IoT Edge è reso disponibile come immagini Windows e Linux, in esecuzione in architetture ARM o x86-64. 


### <a name="input-and-output"></a>Input e output
#### <a name="input-and-output-streams"></a>Flussi di input e output
I processi Edge di Analisi di flusso di Azure possono ottenere input e output da altri moduli in esecuzione in dispositivi IoT Edge. Per la connessione da e a moduli specifici, è possibile impostare la configurazione di routing in fase di distribuzione. Altre informazioni sono disponibili nella [documentazione relativa alla composizione di un modulo IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-composition).

Per input e output sono supportati i formati CSV e JSON.

Per ogni flusso di input e di output creato nel processo di Analisi di flusso di Azure, viene creato un endpoint corrispondente nel modulo distribuito. Questi endpoint possono essere usati nelle route della distribuzione.



##### <a name="reference-data"></a>Dati di riferimento
I dati di riferimento (noti anche come tabella di ricerca) sono un set di dati limitato di natura statica o che cambia molto lentamente, usato per eseguire una ricerca o per la correlazione con il flusso di dati. Per usare i dati di riferimento in un processo di Analisi di flusso di Azure, si usa in genere un [JOIN dei dati di riferimento](https://msdn.microsoft.com/library/azure/dn949258.aspx) nella query. Per altre informazioni, vedere la [documentazione di Analisi di flusso di Azure relativa ai dati di riferimento](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-use-reference-data).

Per usare i dati di riferimento per Analisi di flusso di Azure in IoT Edge, è necessario seguire questa procedura: 
1. Creare un nuovo input per il processo.
2. Scegliere **Dati di riferimento** come **Tipo di origine**.
3. Impostare il percorso del file, che deve essere un percorso di file **assoluto** nel dispositivo. ![Creazione di dati di riferimento](media/stream-analytics-edge/ReferenceData.png)
4. Abilitare le **unità condivise** nella configurazione di Docker e assicurarsi che l'unità sia abilitata prima di avviare la distribuzione.

Per altre informazioni, vedere la [documentazione di Docker per Windows](https://docs.docker.com/docker-for-windows/#shared-drives).

> [!Note]
> Al momento sono supportati solo dati di riferimento locali.




## <a name="license-and-third-party-notices"></a>Licenza e comunicazioni di terze parti
* [Licenza per Analisi di flusso di Azure in IoT Edge (anteprima)](https://go.microsoft.com/fwlink/?linkid=862827). 
* [Comunicazioni di terze parti per Analisi di flusso di Azure in IoT Edge (anteprima)](https://go.microsoft.com/fwlink/?linkid=862828).

## <a name="get-help"></a>Ottenere aiuto
Per assistenza, provare il [Forum di Analisi di flusso di Azure](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics).


## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni su Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works)
* [Esercitazione su Analisi di flusso di Azure in IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics)
* [Invio di commenti e suggerimenti al team tramite questo sondaggio](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u) 

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
