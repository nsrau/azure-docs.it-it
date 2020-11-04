---
title: Analizza e monitora la deviazione dei dati nei set di dati (anteprima)
titleSuffix: Azure Machine Learning
description: Creazione di set di dati di Azure Machine Learning monitoraggi (anteprima), monitoraggio della deviazione dei dati nei set di dati e configurazione degli avvisi.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.author: copeters
author: lostmygithubaccount
ms.date: 06/25/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 8ee2280aba99606d9e31a0e565a67cd6202df3c2
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93317013"
---
# <a name="detect-data-drift-preview-on-datasets"></a>Rileva Drift dei dati (anteprima) nei set di dati


> [!IMPORTANT]
> Il rilevamento della tendenza dei dati nei set di dati è attualmente disponibile in anteprima pubblica.
> La versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Informazioni su come monitorare la tendenza dei dati e impostare gli avvisi quando la deriva è alta.  

Con Azure Machine Learning monitor del set di dati (anteprima), è possibile:
* **Analizza la deviazione nei dati** per comprenderne le variazioni nel tempo.
* **Monitorare i dati del modello** per individuare le differenze tra il training e la conservazione dei set di dati  Iniziare [raccogliendo i dati del modello dai modelli distribuiti](how-to-enable-data-collection.md).
* **Monitora i nuovi dati** per le differenze tra qualsiasi set di dati di base e di destinazione.
* **Profilare le funzionalità nei dati** per tenere traccia del cambiamento delle proprietà statistiche nel tempo.
* **Configurare gli avvisi sulla tendenza dei dati** per gli avvisi iniziali relativi a potenziali problemi. 

Per creare il monitoraggio viene usato un [set di dati di Azure Machine Learning](how-to-create-register-datasets.md) . Il set di dati deve includere una colonna timestamp.

È possibile visualizzare le metriche di spostamento dei dati con Python SDK o in Azure Machine Learning Studio.  Altre metriche e informazioni dettagliate sono disponibili tramite la risorsa [applicazione Azure Insights](../azure-monitor/app/app-insights-overview.md) associata all'area di lavoro Azure Machine Learning.

## <a name="prerequisites"></a>Prerequisiti

Per creare e usare i monitoraggi del set di dati, è necessario:
* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).
* [Area di lavoro Azure Machine Learning](how-to-manage-workspace.md).
* [SDK Azure Machine Learning per Python installato](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py), che include il pacchetto azureml-DataSets.
* Dati strutturati (tabulari) con un timestamp specificato nel percorso del file, nel nome del file o nella colonna nei dati.

## <a name="what-is-data-drift"></a>Che cos'è la deriva dei dati?

La tendenza dei dati è uno dei motivi principali per cui l'accuratezza del modello diminuisce nel tempo.  Per i modelli di Machine Learning, la deriva dei dati è la modifica dei dati di input del modello che comporta un peggioramento delle prestazioni del modello.  Il monitoraggio della deriva dei dati consente di rilevare questi problemi di prestazioni del modello.

Le cause della deriva dei dati includono:

- Le modifiche al processo upstream, ad esempio un sensore sostituito, consentono di modificare le unità di misura da cm a centimetri. 
- Problemi relativi alla qualità dei dati, ad esempio un sensore rotto che legge sempre 0.
- Drift naturale nei dati, ad esempio la variazione della temperatura media con le stagioni.
- Modificare la relazione tra le funzionalità o il turno covariato. 

Azure Machine Learning semplifica il rilevamento della deriva calcolando una singola metrica che astrae la complessità dei set di impostazioni confrontati.  Questi set di impostazioni possono avere centinaia di funzionalità e decine di migliaia di righe. Una volta rilevata la deriva, è possibile eseguire il drill-down delle funzionalità che causano la deriva.  È quindi possibile esaminare le metriche a livello di funzionalità per eseguire il debug e isolare la causa radice della deviazione.

Questo approccio dall'alto verso il basso consente di monitorare facilmente i dati anziché le tecniche tradizionali basate su regole. Le tecniche basate su regole, ad esempio l'intervallo di dati consentito o i valori univoci consentiti, possono richiedere molto tempo e soggette a errori.

In Azure Machine Learning, è possibile usare i monitoraggi del set di dati per rilevare e inviare avvisi per la deviazione.
  
### <a name="dataset-monitors"></a>Monitoraggi del set di dati 

Con un monitor del set di dati è possibile:

* Rilevare e inviare avvisi alla deviazione dei dati sui nuovi dati in un set di dati.
* Analizza i dati cronologici per la deviazione.
* Profilare nuovi dati nel tempo.

L'algoritmo di drifting dei dati fornisce una misura complessiva del cambiamento dei dati e indica le funzionalità che sono responsabili di un'ulteriore analisi. I monitoraggi dei DataSet producono una serie di altre metriche tramite la profilatura di nuovi dati nel set di dati `timeseries` . 

Gli avvisi personalizzati possono essere impostati su tutte le metriche generate dal monitoraggio tramite [applicazione Azure informazioni dettagliate](../azure-monitor/app/app-insights-overview.md). I monitoraggi del set di dati possono essere usati per intercettare rapidamente i problemi di dati e ridurre il tempo necessario per eseguire il debug del problema identificando le cause probabili  

A livello concettuale, esistono tre scenari principali per la configurazione dei monitoraggi del set di dati in Azure Machine Learning.

Scenario | Descrizione
---|---
Monitorare i dati di servizio di un modello per la deriva dai dati di training | I risultati di questo scenario possono essere interpretati come monitoraggio di un proxy per l'accuratezza del modello, in quanto l'accuratezza del modello peggiora quando il servizio di gestione dei dati deriva dai dati di training.
Monitorare un set di dati della serie temporale per la deriva da un periodo di tempo precedente. | Questo scenario è più generale e può essere usato per monitorare i set di impostazioni che coinvolgono upstream o downstream della compilazione del modello.  Il set di dati di destinazione deve contenere una colonna timestamp. Il set di dati di base può essere qualsiasi set di dati tabulare con caratteristiche in comune con il set di dati di destinazione.
Eseguire l'analisi sui dati precedenti. | Questo scenario può essere utilizzato per comprendere i dati cronologici e per informare le decisioni in impostazioni per i monitoraggi del set di dati.

I monitoraggi dei DataSet dipendono dai servizi di Azure seguenti.

|Servizio di Azure  |Descrizione  |
|---------|---------|
| *Set di dati* | Drift USA Machine Learning set di dati per recuperare i dati di training e confrontare i dati per il training del modello.  La generazione del profilo dei dati viene usata per generare alcune delle metriche segnalate, ad esempio min, Max, distinct values, distinct values Count. |
| *Pipeline e calcolo di Azureml* | Il processo di calcolo della deviazione è ospitato nella pipeline azureml.  Il processo viene attivato su richiesta o pianificato per l'esecuzione in un calcolo configurato al momento della creazione del monitoraggio della deviazione.
| *Application Insights*| Drift emette metriche per Application Insights appartenenti all'area di lavoro di machine learning.
| *Archiviazione BLOB di Azure*| Drift emette metriche in formato JSON nell'archivio BLOB di Azure.

## <a name="how-dataset-monitors-data"></a>Come il DataSet monitora i dati

Usare Machine Learning set di dati per monitorare la tendenza dei dati. Specificare un set di dati di base, in genere il set di dati di training per un modello. Un set di dati di destinazione. in genere i dati di input vengono confrontati nel tempo con il set di dati di base. Questo confronto indica che è necessario specificare una colonna timestamp per il set di dati di destinazione.

## <a name="create-target-dataset"></a>Crea set di dati di destinazione

Per il set di `timeseries` dati di destinazione è necessario impostare il tratto specificando la colonna timestamp da una colonna nei dati o una colonna virtuale derivata dal modello di percorso dei file. Creare il set di dati con un timestamp tramite [Python SDK](#sdk-dataset) o [Azure Machine Learning Studio](#studio-dataset). È necessario specificare una colonna che rappresenta un timestamp per aggiungere `timeseries` tratto al set di dati. Se i dati sono partizionati in una struttura di cartelle con informazioni temporali, ad esempio ' {AAAA/MM/GG}', creare una colonna virtuale attraverso l'impostazione del modello di percorso e impostarla come timestamp della partizione per migliorare l'importanza della funzionalità della serie temporale.

### <a name="python-sdk"></a><a name="sdk-dataset"></a>Python SDK

Il [`Dataset`](/python/api/azureml-core/azureml.data.tabulardataset?preserve-view=true&view=azure-ml-py#&preserve-view=truewith-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-) metodo della classe [`with_timestamp_columns()`](/python/api/azureml-core/azureml.data.tabulardataset?preserve-view=true&view=azure-ml-py#&preserve-view=truewith-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-)  definisce la colonna timestamp per il set di dati.

```python 
from azureml.core import Workspace, Dataset, Datastore

# get workspace object
ws = Workspace.from_config()

# get datastore object 
dstore = Datastore.get(ws, 'your datastore name')

# specify datastore paths
dstore_paths = [(dstore, 'weather/*/*/*/*/data.parquet')]

# specify partition format
partition_format = 'weather/{state}/{date:yyyy/MM/dd}/data.parquet'

# create the Tabular dataset with 'state' and 'date' as virtual columns 
dset = Dataset.Tabular.from_parquet_files(path=dstore_paths, partition_format=partition_format)

# assign the timestamp attribute to a real or virtual column in the dataset
dset = dset.with_timestamp_columns('date')

# register the dataset as the target dataset
dset = dset.register(ws, 'target')
```

Per un esempio completo dell'uso del `timeseries` tratto dei set di impostazioni, vedere il [notebook di esempio](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb) o la documentazione di [DataSets SDK](/python/api/azureml-core/azureml.data.tabulardataset?preserve-view=true&view=azure-ml-py#&preserve-view=truewith-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-).

### <a name="azure-machine-learning-studio"></a><a name="studio-dataset"></a>Azure Machine Learning Studio

Se si crea il set di dati usando Azure Machine Learning Studio, verificare che il percorso dei dati contenga informazioni sul timestamp, includere tutte le sottocartelle con dati e impostare il formato della partizione.

Nell'esempio seguente vengono presi tutti i dati nella sottocartella *NoaaIsdFlorida/2019* e il formato della partizione specifica l'anno, il mese e il giorno del timestamp.

[![Formato partizione](./media/how-to-monitor-datasets/partition-format.png)](media/how-to-monitor-datasets/partition-format-expand.png)

Nelle impostazioni **dello schema** specificare la colonna timestamp da una colonna virtuale o reale nel set di dati specificato:

:::image type="content" source="media/how-to-monitor-datasets/timestamp.png" alt-text="Imposta il timestamp":::

Se i dati sono partizionati in base alla data, come in questo caso, è anche possibile specificare il partition_timestamp.  In questo modo è possibile elaborare più efficacemente le date.

:::image type="content" source="media/how-to-monitor-datasets/timeseries-partitiontimestamp.png" alt-text="Timestamp partizione":::


## <a name="create-dataset-monitors"></a>Crea monitoraggi DataSet

Creare monitor del set di dati per rilevare e inviare avvisi alla deviazione dei dati in un nuovo set di dati.  Usare [Python SDK](#sdk-monitor) o [Azure Machine Learning Studio](#studio-monitor).

### <a name="python-sdk"></a><a name="sdk-monitor"></a>Python SDK

Per informazioni dettagliate, vedere la [documentazione di riferimento di Python SDK relativa alla disattivazione dei dati](/python/api/azureml-datadrift/azureml.datadrift) . 

L'esempio seguente illustra come creare un monitor del set di dati usando Python SDK

```python
from azureml.core import Workspace, Dataset
from azureml.datadrift import DataDriftDetector
from datetime import datetime

# get the workspace object
ws = Workspace.from_config()

# get the target dataset
dset = Dataset.get_by_name(ws, 'target')

# set the baseline dataset
baseline = target.time_before(datetime(2019, 2, 1))

# set up feature list
features = ['latitude', 'longitude', 'elevation', 'windAngle', 'windSpeed', 'temperature', 'snowDepth', 'stationName', 'countryOrRegion']

# set up data drift detector
monitor = DataDriftDetector.create_from_datasets(ws, 'drift-monitor', baseline, target, 
                                                      compute_target='cpu-cluster', 
                                                      frequency='Week', 
                                                      feature_list=None, 
                                                      drift_threshold=.6, 
                                                      latency=24)

# get data drift detector by name
monitor = DataDriftDetector.get_by_name(ws, 'drift-monitor')

# update data drift detector
monitor = monitor.update(feature_list=features)

# run a backfill for January through May
backfill1 = monitor.backfill(datetime(2019, 1, 1), datetime(2019, 5, 1))

# run a backfill for May through today
backfill1 = monitor.backfill(datetime(2019, 5, 1), datetime.today())

# disable the pipeline schedule for the data drift detector
monitor = monitor.disable_schedule()

# enable the pipeline schedule for the data drift detector
monitor = monitor.enable_schedule()
```

Per un esempio completo di configurazione di un `timeseries` set di dati e un rilevatore di drifting dati, vedere il [notebook di esempio](https://aka.ms/datadrift-notebook).

### <a name="azure-machine-learning-studio"></a><a name="studio-monitor"></a> Azure Machine Learning Studio

1. Passare alla [Home page di studio](https://ml.azure.com).
1. Selezionare la scheda **set di impostazioni** a sinistra. 
1. Selezionare **monitoraggi del set di dati**.
   ![Elenco monitoraggio](./media/how-to-monitor-datasets/monitor-list.png)

1. Fare clic sul pulsante **+ Crea monitoraggio** e continuare la procedura guidata facendo clic su **Avanti**.  

:::image type="content" source="media/how-to-monitor-datasets/wizard.png" alt-text="Creazione guidata monitoraggio":::

* **Selezionare set di dati di destinazione**.  Il set di dati di destinazione è un set di dati tabulare con la colonna timestamp specificata, che verrà analizzata per la deriva dei dati. Il set di dati di destinazione deve avere funzionalità in comune con il set di dati di base e deve essere un `timeseries` set di dati a cui vengono aggiunti nuovi dati. I dati cronologici nel set di dati di destinazione possono essere analizzati oppure è possibile monitorare nuovi dati.

* **Selezionare set di dati Baseline.**  Consente di selezionare il set di dati tabulare da utilizzare come linea di base per il confronto del set di dati di destinazione nel tempo.  Il set di dati di base deve avere funzionalità in comune con il set di dati di destinazione.  Selezionare un intervallo di tempo per usare una sezione del set di dati di destinazione o specificare un set di dati separato da usare come linea di base.

* **Impostazioni di monitoraggio**.  Queste impostazioni sono per la pipeline di monitoraggio set di dati pianificata, che verrà creata. 

    | Impostazione | Descrizione | Suggerimenti | Modificabile | 
    | ------- | ----------- | ---- | ------- |
    | Nome | Nome del monitoraggio del set di dati. | | No |
    | Funzionalità | Elenco di funzionalità che verranno analizzate per la deviazione dei dati nel tempo. | Impostare sulle funzionalità di output di un modello per misurare la tendenza del concetto. Non includere funzioni che si spostano naturalmente nel tempo (mese, anno, indice e così via). Dopo aver modificato l'elenco di funzionalità, è possibile indicizzazione e monitoraggio della deviazione dati esistente. | Sì | 
    | Destinazione del calcolo | Azure Machine Learning la destinazione di calcolo per eseguire i processi di monitoraggio del set di dati. | | Sì | 
    | Abilitare | Abilitare o disabilitare la pianificazione nella pipeline di monitoraggio del set di dati | Disabilitare la pianificazione per analizzare i dati cronologici con l'impostazione di recupero dati. Può essere abilitata dopo la creazione del monitor del set di dati. | Sì | 
    | Frequenza | Frequenza che verrà usata per pianificare il processo della pipeline e analizzare i dati cronologici se si esegue un recupero dati. Le opzioni includono giornaliera, settimanale o mensile. | Ogni esecuzione Confronta i dati nel set di dati di destinazione in base alla frequenza: <li>Giornaliero: confrontare il giorno completo più recente nel set di dati di destinazione con la linea di base <li>Settimanale: confrontare la settimana completa più recente (lunedì-domenica) nel set di dati di destinazione con la linea di base <li>Mensile: confrontare il mese completo più recente nel set di dati di destinazione con la linea di base | No | 
    | Latenza | Tempo, in ore, necessario per l'arrivo dei dati nel set di dati. Se, ad esempio, sono necessari tre giorni per l'arrivo dei dati nel database SQL incapsulato dal set di dati, impostare la latenza su 72. | Non può essere modificato dopo la creazione del monitor del set di dati | No | 
    | Indirizzi di posta elettronica | Indirizzi di posta elettronica per gli avvisi in base alla violazione della soglia percentuale di drifting dei dati. | I messaggi di posta elettronica vengono inviati tramite monitoraggio di Azure. | Sì | 
    | Soglia | Soglia percentuale di spostamento dei dati per gli avvisi di posta elettronica. | È possibile impostare altri avvisi ed eventi su molte altre metriche nella risorsa Application Insights associata dell'area di lavoro. | Sì |

Dopo aver completato la procedura guidata, il monitor del set di dati risultante verrà visualizzato nell'elenco. Selezionarlo per passare alla pagina dei dettagli del monitoraggio.

## <a name="understand-data-drift-results"></a>Informazioni sui risultati della deviazione dati

Questa sezione illustra i risultati del monitoraggio di un set di dati, disponibile nella pagina **set**  /  di **dati monitoraggi DataSet** in Azure studio.  È possibile aggiornare le impostazioni e analizzare i dati esistenti per un periodo di tempo specifico in questa pagina.  

Inizia con le informazioni di primo livello sulla grandezza della deviazione dei dati e su un'evidenziazione delle funzionalità da approfondire.

:::image type="content" source="media/how-to-monitor-datasets/drift-overview.png" alt-text="Panoramica della deviazione":::


| Metrica | Descrizione | 
| ------ | ----------- | 
| Grandezza della deviazione dati | Percentuale della deviazione tra la linea di base e il set di dati di destinazione nel tempo. Compreso tra 0 e 100, 0 indica set di dati identici e 100 indica che il modello di Azure Machine Learning di spostamento dei dati può indicare completamente i due set di dati separati. Il rumore nella percentuale esatta misurata è previsto a causa delle tecniche di Machine Learning usate per generare questa grandezza. | 
| Principali funzionalità di drifting | Mostra le funzionalità del set di dati che sono state spostate al massimo e contribuiscono pertanto maggiormente alla metrica della magnitudo della deriva. A causa di uno spostamento covariato, la distribuzione sottostante di una funzionalità non deve necessariamente essere modificata per avere un'importanza relativamente elevata della funzionalità. |
| Soglia | I dati che superano la soglia impostata attivano gli avvisi. Questa configurazione può essere configurata nelle impostazioni di monitoraggio. | 

### <a name="drift-magnitude-trend"></a>Tendenza Magnitude Drift

Vedere come il set di dati differisce dal set di dati di destinazione nel periodo di tempo specificato.  Più vicino al 100%, maggiore è la differenza tra i due set di impostazioni.

:::image type="content" source="media/how-to-monitor-datasets/drift-magnitude.png" alt-text="Tendenza Magnitude Drift":::

### <a name="drift-magnitude-by-features"></a>Drift Magnitude by features

Questa sezione contiene informazioni dettagliate a livello di funzionalità per la modifica della distribuzione della funzionalità selezionata, nonché altre statistiche, nel tempo.

Anche il set di dati di destinazione viene profilato nel tempo. La distanza statistica tra la distribuzione di base di ogni funzionalità viene confrontata con il set di dati di destinazione nel tempo.  Concettualmente, questa situazione è simile alla grandezza della deriva dei dati.  Questa distanza statistica è tuttavia destinata a una singola funzionalità invece che a tutte le funzionalità. Sono disponibili anche min, Max e Mean.

In Azure Machine Learning Studio fare clic su una barra nel grafico per visualizzare i dettagli del livello di funzionalità per tale data. Per impostazione predefinita, viene visualizzata la distribuzione del set di dati di base e la distribuzione dell'esecuzione più recente della stessa funzionalità.

:::image type="content" source="media/how-to-monitor-datasets/drift-by-feature.gif" alt-text="Drift Magnitude by features":::

Queste metriche possono anche essere recuperate in Python SDK tramite il `get_metrics()` metodo su un `DataDriftDetector` oggetto.

### <a name="feature-details"></a>Dettagli delle funzionalità

Infine, scorrere verso il basso per visualizzare i dettagli di ogni singola funzionalità.  Utilizzare gli elenchi a discesa sopra il grafico per selezionare la funzionalità e selezionare la metrica che si desidera visualizzare.

:::image type="content" source="media/how-to-monitor-datasets/numeric-feature.gif" alt-text="Grafico delle funzionalità numeriche e confronto":::

Le metriche nel grafico dipendono dal tipo di funzionalità.

* Funzionalità numeriche

    | Metrica | Descrizione |  
    | ------ | ----------- |  
    | Distanza Wasserstein | Quantità minima di lavoro per trasformare la distribuzione di base nella distribuzione di destinazione. |
    | Valore medio | Valore medio della funzionalità. |
    | Valore minimo | Valore minimo della funzionalità. |
    | Valore massimo | Valore massimo della funzionalità. |

* Funzionalità categoriche
    
    | Metrica | Descrizione |  
    | ------ | ----------- |  
    | Distanza euclideo     |  Calcolato per le colonne categoriche. La distanza euclidea viene calcolata su due vettori, generati dalla distribuzione empirica della stessa colonna categorica di due set di impostazioni. 0 indica che non esiste alcuna differenza nelle distribuzioni empiriche.  Maggiore è la deviazione da 0, maggiore è la tendenza della colonna. Le tendenze possono essere osservate da un tracciato di serie temporali di questa metrica e possono essere utili per individuare una funzionalità di spostamento.  |
    | Valori univoci | Numero di valori univoci (cardinalità) della funzionalità. |

In questo grafico selezionare una singola data per confrontare la distribuzione delle funzionalità tra la destinazione e questa data per la funzionalità visualizzata. Per le funzionalità numeriche, vengono visualizzate due distribuzioni di probabilità.  Se la funzionalità è numerica, viene visualizzato un grafico a barre.

:::image type="content" source="media/how-to-monitor-datasets/select-date-to-compare.gif" alt-text="Selezionare una data da confrontare con la destinazione":::

## <a name="metrics-alerts-and-events"></a>Metriche, avvisi ed eventi

È possibile eseguire query sulle metriche nella risorsa [applicazione Azure Insights](../azure-monitor/app/app-insights-overview.md) associata all'area di lavoro di machine learning. È possibile accedere a tutte le funzionalità di Application Insights inclusa la configurazione per le regole di avviso personalizzate e i gruppi di azioni per attivare un'azione come, un messaggio di posta elettronica/SMS/push/Voice o una funzione di Azure. Per informazioni dettagliate, vedere la documentazione completa Application Insights. 

Per iniziare, passare alla [portale di Azure](https://portal.azure.com) e selezionare la pagina **Panoramica** dell'area di lavoro.  La risorsa Application Insights associata si trova all'estrema destra:

[![Panoramica del portale di Azure](./media/how-to-monitor-datasets/ap-overview.png)](media/how-to-monitor-datasets/ap-overview-expanded.png)

Selezionare logs (Analytics) in monitoraggio nel riquadro sinistro:

![Panoramica di Application Insights](./media/how-to-monitor-datasets/ai-overview.png)

Le metriche del monitoraggio del set di dati vengono archiviate come `customMetrics` . È possibile scrivere ed eseguire una query dopo aver configurato un monitor del set di dati per visualizzarli:

[![Query di log Analytics](./media/how-to-monitor-datasets/simple-query.png)](media/how-to-monitor-datasets/simple-query-expanded.png)

Dopo aver identificato le metriche per configurare le regole di avviso, creare una nuova regola di avviso:

![Nuova regola di avviso](./media/how-to-monitor-datasets/alert-rule.png)

È possibile usare un gruppo di azioni esistente o crearne uno nuovo per definire l'azione da intraprendere quando vengono soddisfatte le condizioni del set:

![Nuovo gruppo di azioni](./media/how-to-monitor-datasets/action-group.png)

## <a name="next-steps"></a>Passaggi successivi

* Passare a [Azure Machine Learning Studio](https://ml.azure.com) o al [notebook di Python](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datadrift-tutorial/datadrift-tutorial.ipynb) per configurare un monitoraggio del set di dati.
* Vedere come configurare la tendenza dei dati nei [modelli distribuiti nel servizio Azure Kubernetes](./how-to-enable-data-collection.md).
* Configurare i monitoraggi per la desincronizzazione del set di dati con [griglia di eventi](how-to-use-event-grid.md). 
* Se si verificano problemi, vedere questi suggerimenti comuni per la [risoluzione dei](resource-known-issues.md#data-drift) problemi.