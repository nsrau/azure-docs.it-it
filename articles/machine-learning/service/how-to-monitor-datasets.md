---
title: Analizza e monitora la deviazione dei dati nei set di dati (anteprima)
titleSuffix: Azure Machine Learning
description: Creazione di set di dati di Azure Machine Learning monitoraggi (anteprima), monitoraggio della deviazione dei dati nei set di dati e configurazione degli avvisi.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
ms.author: copeters
author: lostmygithubaccount
ms.date: 11/04/2019
ms.openlocfilehash: 10532ba2b43e40c4ffa2990e924947046d03b576
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74539194"
---
# <a name="detect-data-drift-preview-on-datasets"></a>Rileva Drift dei dati (anteprima) nei set di dati
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

In questo articolo viene illustrato come creare monitor del set di dati di Azure Machine Learning (anteprima), monitorare la tendenza ai dati e le modifiche statistiche nei set di dati e configurare gli avvisi.

Con Azure Machine Learning monitoraggi del set di dati, è possibile:
* **Analizza la deviazione nei dati** per comprenderne le variazioni nel tempo.
* **Monitorare i dati del modello** per individuare le differenze tra il training e la conservazione dei set di dati
* **Monitora i nuovi dati** per le differenze tra qualsiasi set di dati di base e di destinazione.
* **Profilare le funzionalità nei dati** per tenere traccia del cambiamento delle proprietà statistiche nel tempo.
* **Configurare gli avvisi sulla tendenza dei dati** per gli avvisi iniziali relativi a potenziali problemi. 

Le metriche e le informazioni dettagliate sono disponibili tramite la risorsa [applicazione Azure Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) associata all'area di lavoro del servizio Azure Machine Learning.

> [!Important]
> Si noti che il monitoraggio della tendenza dei dati con l'SDK è disponibile in tutte le edizioni, mentre il monitoraggio della deriva dei dati attraverso lo studio sul Web è solo Enterprise Edition.

## <a name="prerequisites"></a>Prerequisiti

Per creare e usare i monitoraggi del set di dati, è necessario:
* Una sottoscrizione di Azure. Se non si dispone di una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).
* [Area di lavoro Azure Machine Learning](how-to-manage-workspace.md).
* [SDK Azure Machine Learning per Python installato](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), che include il pacchetto azureml-DataSets.
* Dati strutturati (tabulari) con un timestamp specificato nel percorso del file, nel nome del file o nella colonna nei dati.

## <a name="what-is-data-drift"></a>Che cos'è la deriva dei dati?

Nel contesto di Machine Learning, la deriva dei dati è la modifica dei dati di input del modello che comporta un peggioramento delle prestazioni del modello. Si tratta di uno dei motivi principali per cui l'accuratezza del modello diminuisce nel tempo, di conseguenza il monitoraggio della deriva dei dati consente di rilevare problemi di prestazioni del modello.

Le cause della deriva dei dati includono: 

- Le modifiche al processo upstream, ad esempio un sensore sostituito, consentono di modificare le unità di misura da cm a centimetri. 
- Problemi relativi alla qualità dei dati, ad esempio un sensore rotto che legge sempre 0.
- Drift naturale nei dati, ad esempio la variazione della temperatura media con le stagioni.
- Modificare la relazione tra le funzionalità o il turno covariato. 

Con Azure Machine Learning monitoraggi del set di dati è possibile configurare avvisi che assistono al rilevamento della deriva dei dati nei set di dati nel tempo. 

### <a name="dataset-monitors"></a>Monitoraggi del set di dati 

È possibile creare un monitor del set di dati per rilevare e inviare avvisi alla deviazione dei dati sui nuovi dati in un set di dati, analizzare i dati cronologici per la deriva e profilare nuovi dati nel tempo. L'algoritmo di drifting dei dati fornisce una misura complessiva del cambiamento dei dati e indica le funzionalità che sono responsabili di un'ulteriore analisi. I monitoraggi dei DataSet producono una serie di altre metriche tramite la profilatura di nuovi dati nel set di dati `timeseries`. Gli avvisi personalizzati possono essere impostati su tutte le metriche generate dal monitoraggio tramite [applicazione Azure informazioni dettagliate](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). I monitoraggi del set di dati possono essere usati per intercettare rapidamente i problemi di dati e ridurre il tempo necessario per eseguire il debug del problema identificando le cause probabili  

A livello concettuale, esistono tre scenari principali per la configurazione dei monitoraggi del set di dati in Azure Machine Learning.

Scenario | Description
---|---
Monitoraggio dei dati di gestione di un modello per la deriva dai dati di training del modello | I risultati di questo scenario possono essere interpretati come monitoraggio di un proxy per l'accuratezza del modello, dato che l'accuratezza del modello peggiora se i dati di gestione sono derivati dai dati di training.
Monitoraggio di un set di dati della serie temporale per la deriva da un periodo di tempo precedente. | Questo scenario è più generale e può essere usato per monitorare i set di impostazioni che coinvolgono upstream o downstream della compilazione del modello.  Il set di dati di destinazione deve contenere una colonna timestamp, mentre il set di dati di base può essere qualsiasi set di dati tabulare con caratteristiche in comune con il set di dati di destinazione.
Esecuzione dell'analisi sui dati passati. | Questo scenario può essere utilizzato per comprendere i dati cronologici e per informare le decisioni in impostazioni per i monitoraggi del set di dati.

## <a name="how-dataset-can-monitor-data"></a>Come il set di dati può monitorare i dati

Con Azure Machine Learning, la deriva dei dati viene monitorata tramite i set di dati. Per monitorare la deriva dei dati, viene specificato un set di dati di base, in genere il set di dati di training per un modello. Un set di dati di destinazione. in genere i dati di input vengono confrontati nel tempo con il set di dati di base. Questo confronto indica che è necessario specificare una colonna timestamp per il set di dati di destinazione.

### <a name="set-the-timeseries-trait-in-the-target-dataset"></a>Impostazione del tratto `timeseries` nel set di dati di destinazione

Per il set di dati di destinazione è necessario impostare il tratto di `timeseries` specificando la colonna timestamp da una colonna nei dati o una colonna virtuale derivata dal modello di percorso dei file. Questa operazione può essere eseguita tramite Python SDK o Azure Machine Learning Studio. È necessario specificare una colonna che rappresenta un timestamp "fine" per aggiungere `timeseries` tratto al set di dati. Se i dati vengono partizionati in una struttura di cartelle con le informazioni temporali, ad esempio ' {AAAA/MM/GG}', è possibile creare una colonna virtuale tramite l'impostazione del modello di percorso e impostarla come timestamp a granularità grossolana per migliorare l'importanza della funzionalità della serie temporale. 

#### <a name="python-sdk"></a>Python SDK

Il metodo [`Dataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-fine-grain-timestamp--coarse-grain-timestamp-none--validate-false-) class ' [`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-fine-grain-timestamp--coarse-grain-timestamp-none--validate-false-) definisce la colonna timestamp per il set di dati. 

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

Per un esempio completo dell'uso del tratto `timeseries` dei set di impostazioni, vedere il [notebook di esempio](https://aka.ms/azureml-tsd-notebook) o la documentazione di [DataSets SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-fine-grain-timestamp--coarse-grain-timestamp-none--validate-false-).

#### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku-inline.md)]

Se si crea il set di dati usando Azure Machine Learning Studio, verificare che il percorso dei dati contenga informazioni sul timestamp, includere tutte le sottocartelle con dati e impostare il formato della partizione. 

Nell'esempio seguente vengono presi tutti i dati nella sottocartella *NoaaIsdFlorida/2019* e il formato della partizione specifica l'anno, il mese e il giorno del timestamp. 

[formato partizione ![](media/how-to-monitor-datasets/partition-format.png)](media/how-to-monitor-datasets/partition-format-expand.png)

Nelle impostazioni **dello schema** specificare la colonna timestamp da una colonna virtuale o reale nel set di dati specificato:

![Timestamp](media/how-to-monitor-datasets/timestamp.png)

## <a name="dataset-monitor-settings"></a>Impostazioni di monitoraggio set di dati

Dopo aver creato il set di dati con le impostazioni timestamp specificate, è possibile configurare il monitoraggio del set di dati.

Le varie impostazioni di monitoraggio del set di dati sono suddivise in tre gruppi: **informazioni di base, impostazioni di monitoraggio** e **impostazioni di recupero**dati.

### <a name="basic-info"></a>Info di base

Questa tabella contiene le impostazioni di base utilizzate per il monitoraggio del set di dati.

| Impostazione | Description | Suggerimenti | Modificabile | 
| ------- | ----------- | ---- | ------- | 
| name | Nome del monitoraggio del set di dati. | | No |
| Set di dati Baseline | Set di dati tabulare che verrà usato come base per il confronto del set di dati di destinazione nel tempo. | Il set di dati di base deve avere funzionalità in comune con il set di dati di destinazione. In genere, la linea di base deve essere impostata sul set di dati di training di un modello o su una sezione del set di dati di destinazione. | No |
| Set di dati di destinazione | Set di dati tabulare con la colonna timestamp specificata, che verrà analizzata per la deriva dei dati. | Il set di dati di destinazione deve avere funzionalità in comune con il set di dati di base e deve essere un set di dati `timeseries` a cui vengono aggiunti nuovi dati. I dati cronologici nel set di dati di destinazione possono essere analizzati oppure è possibile monitorare nuovi dati. | No | 
| Frequenza | Frequenza che verrà usata per pianificare il processo della pipeline e analizzare i dati cronologici se si esegue un recupero dati. Le opzioni includono giornaliera, settimanale o mensile. | Modificare questa impostazione in modo da includere una dimensione paragonabile di dati alla linea di base. | No | 
| database elastico | Elenco di funzionalità che verranno analizzate per la deviazione dei dati nel tempo. | Impostare sulle funzionalità di output di un modello per misurare la tendenza del concetto. Non includere funzioni che si spostano naturalmente nel tempo (mese, anno, indice e così via). Dopo aver modificato l'elenco di funzionalità, è possibile indicizzazione e monitoraggio della deviazione dati esistente. | SÌ | 
| Destinazione del calcolo | Azure Machine Learning la destinazione di calcolo per eseguire i processi di monitoraggio del set di dati. | | SÌ | 

### <a name="monitor-settings"></a>Impostazioni di monitoraggio

Queste impostazioni sono per la pipeline di monitoraggio set di dati pianificata, che verrà creata. 

| Impostazione | Description | Suggerimenti | Modificabile | 
| ------- | ----------- | ---- | ------- |
| Abilitare | Abilitare o disabilitare la pianificazione nella pipeline di monitoraggio del set di dati | Disabilitare la pianificazione per analizzare i dati cronologici con l'impostazione di recupero dati. Può essere abilitata dopo la creazione del monitor del set di dati. | SÌ | 
| Latency | Tempo, in ore, necessario per l'arrivo dei dati nel set di dati. Se, ad esempio, sono necessari tre giorni per l'arrivo dei dati nel database SQL incapsulato dal set di dati, impostare la latenza su 72. | Non può essere modificato dopo la creazione del monitor del set di dati | No | 
| Indirizzi di posta elettronica | Indirizzi di posta elettronica per gli avvisi in base alla violazione della soglia percentuale di drifting dei dati. | I messaggi di posta elettronica vengono inviati tramite monitoraggio di Azure. | SÌ | 
| Soglia | Soglia percentuale di spostamento dei dati per gli avvisi di posta elettronica. | È possibile impostare altri avvisi ed eventi su molte altre metriche nella risorsa Application Insights associata dell'area di lavoro. | SÌ | 

### <a name="backfill-settings"></a>Impostazioni di recupero

Queste impostazioni sono destinate all'esecuzione di un'operazione di recupero dati nei dati precedenti per le metriche di spostamento dei dati.

| Impostazione | Description | Suggerimenti |
| ------- | ----------- | ---- |
| Data di inizio | Data di inizio del processo di recupero. | | 
| Data di fine | Data di fine del processo di recupero. | La data di fine non può superare 31 * unità di frequenza di tempo dalla data di inizio. In un monitor del set di dati esistente, è possibile riempire le metriche per analizzare i dati cronologici o sostituire le metriche con le impostazioni aggiornate. |

## <a name="create-dataset-monitors"></a>Crea monitoraggi DataSet 

Creare monitor del set di dati per rilevare e inviare avvisi alla deviazione dei dati in un nuovo set di dati con Azure Machine Learning Studio o Python SDK. 

### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku-inline.md)]

Per impostare gli avvisi sul monitoraggio del set di dati, l'area di lavoro che contiene il set di dati per cui si desidera creare un monitoraggio deve avere funzionalità di Enterprise Edition. 

Una volta confermata la funzionalità dell'area di lavoro, passare alla Home page di studio e selezionare la scheda set di impostazioni a sinistra. Selezionare monitoraggi del set di dati.

![Elenco monitoraggio](media/how-to-monitor-datasets/monitor-list.png)

Fare clic sul pulsante **+ Crea monitoraggio** e continuare la procedura guidata facendo clic su **Avanti**.

![Procedura guidata](media/how-to-monitor-datasets/wizard.png)

Il monitoraggio del set di dati risultante verrà visualizzato nell'elenco. Selezionarlo per passare alla pagina dei dettagli del monitoraggio.

### <a name="from-python-sdk"></a>Da Python SDK

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

Per un esempio completo di configurazione di un set di dati `timeseries` e un rilevatore di drifting dei dati, vedere il [notebook di esempio](https://aka.ms/datadrift-notebook).

## <a name="understanding-data-drift-results"></a>Informazioni sui risultati della deviazione dei dati

Il monitoraggio dati produce due gruppi di risultati: Panoramica della deriva e dettagli delle funzionalità. L'animazione seguente mostra i grafici di monitoraggio della deriva disponibili in base alla funzionalità e alla metrica selezionate. 

![Video demo](media/how-to-monitor-datasets/video.gif)

### <a name="drift-overview"></a>Panoramica della deviazione

La sezione **Panoramica della deviazione** contiene informazioni di base sulla grandezza della deriva dei dati e sulle funzionalità da approfondire. 

| Metrica | Description | Suggerimenti | 
| ------ | ----------- | ---- | 
| Grandezza della deviazione dati | Dato come percentuale tra il set di dati di base e di destinazione nel tempo. Compreso tra 0 e 100, dove 0 indica set di dati identici e 100 indica che la funzionalità di spostamento dei dati Azure Machine Learning può indicare completamente i due set di dati separati. | Il rumore nella percentuale esatta misurata è previsto a causa delle tecniche di Machine Learning usate per generare questa grandezza. | 
| Contributo alla deriva per funzionalità | Contributo di ogni funzionalità nel set di dati di destinazione alla grandezza della deriva misurata. |  A causa di uno spostamento covariato, la distribuzione sottostante di una funzionalità non deve necessariamente essere modificata per avere un'importanza relativamente elevata della funzionalità. | 

L'immagine seguente è un esempio di grafici visualizzati nei risultati della **Panoramica della deriva** in Azure Machine Learning Studio, risultante da un recupero [dati della superficie integrata NOAA](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/). I dati sono stati campionati in `stationName contains 'FLORIDA'`, con gennaio 2019 usato come set di dati di base e tutti i dati 2019 usati come destinazione.
 
![Panoramica della deviazione](media/how-to-monitor-datasets/drift-overview.png)

### <a name="feature-details"></a>Dettagli delle funzionalità

La sezione **Dettagli funzionalità contiene informazioni dettagliate** a livello di funzionalità per la modifica della distribuzione della funzionalità selezionata, nonché altre statistiche, nel corso del tempo. 

Anche il set di dati di destinazione viene profilato nel tempo. La distanza statistica tra la distribuzione di base di ogni funzionalità viene confrontata con il set di dati di destinazione nel tempo, concettualmente simile alla grandezza della deriva dei dati, con l'eccezione che questa distanza statistica è destinata a una singola funzionalità. Sono disponibili anche min, Max e Mean. 

In Azure Machine Learning Studio, se si fa clic su un punto dati nel grafico, la distribuzione della funzionalità da visualizzare verrà modificata di conseguenza. Per impostazione predefinita, Mostra la distribuzione del set di dati di base e la distribuzione dell'esecuzione più recente della stessa funzionalità. 

Queste metriche possono anche essere recuperate in Python SDK tramite il metodo `get_metrics()` su un oggetto `DataDriftDetector`. 

#### <a name="numeric-features"></a>Funzionalità numeriche 

Le funzionalità numeriche vengono profilate in ogni esecuzione del monitoraggio del set di dati. Le seguenti sono esposte in Azure Machine Learning Studio. Viene visualizzata la densità di probabilità per la distribuzione.

| Metrica | Description |  
| ------ | ----------- |  
| Distanza Wasserstein | Quantità minima di lavoro per trasformare la distribuzione di base nella distribuzione di destinazione. |
| Valore medio | Valore medio della funzionalità. |
| Valore minimo | Valore minimo della funzionalità. |
| Valore massimo | Valore massimo della funzionalità. |

![Dettagli funzionalità numerici](media/how-to-monitor-datasets/feature-details.png)

#### <a name="categorical-features"></a>Funzionalità categoriche 

Le funzionalità numeriche vengono profilate in ogni esecuzione del monitoraggio del set di dati. Le seguenti sono esposte in Azure Machine Learning Studio. Viene visualizzato un istogramma per la distribuzione.

| Metrica | Description |  
| ------ | ----------- |  
| Distanza euclideo | Distanza geometrica tra le distribuzioni di base e di destinazione. |
| Valori univoci | Numero di valori univoci (cardinalità) della funzionalità. |


![Dettagli delle funzionalità categoriche](media/how-to-monitor-datasets/feature-details2.png)

## <a name="metrics-alerts-and-events"></a>Metriche, avvisi ed eventi

È possibile eseguire query sulle metriche nella risorsa [applicazione Azure Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) associata all'area di lavoro di machine learning. Che consente di accedere a tutte le funzionalità di Application Insights inclusa la configurazione per le regole di avviso personalizzate e i gruppi di azioni per attivare un'azione come, un messaggio di posta elettronica/SMS/push/Voice o una funzione di Azure. Per informazioni dettagliate, fare riferimento alla documentazione completa Application Insights. 

Per iniziare, passare alla portale di Azure e selezionare la pagina **Panoramica** dell'area di lavoro.  La risorsa Application Insights associata si trova all'estrema destra:

[Panoramica di portale di Azure ![](media/how-to-monitor-datasets/ap-overview.png)](media/how-to-monitor-datasets/ap-overview-expanded.png)

Selezionare logs (Analytics) in monitoraggio nel riquadro sinistro:

![Panoramica di Application Insights](media/how-to-monitor-datasets/ai-overview.png)

Le metriche di monitoraggio del set di dati vengono archiviate come `customMetrics`. È possibile scrivere ed eseguire una query dopo aver configurato un monitor del set di dati per visualizzarli:

[query di ![log Analytics](media/how-to-monitor-datasets/simple-query.png)](media/how-to-monitor-datasets/simple-query-expanded.png)

Dopo aver identificato le metriche per configurare le regole di avviso, creare una nuova regola di avviso:

![Nuova regola di avviso](media/how-to-monitor-datasets/alert-rule.png)

È possibile usare un gruppo di azioni esistente o crearne uno nuovo per definire l'azione da intraprendere quando vengono soddisfatte le condizioni del set:

![Nuovo gruppo di azioni](media/how-to-monitor-datasets/action-group.png)

## <a name="troubleshooting"></a>risoluzione dei problemi

Limitazioni e problemi noti:

* L'intervallo di tempo per i processi di recupero è limitato a 31 intervalli dell'impostazione di frequenza del monitoraggio. 
* Limitazione delle funzionalità di 200, a meno che non sia specificato un elenco di funzionalità (tutte le funzionalità usate).
* La dimensione di calcolo deve essere sufficientemente grande da poter gestire i dati. 
* Verificare che il set di dati includa dati entro la data di inizio e di fine per una determinata esecuzione del monitoraggio.

Le colonne, o funzionalità, nel set di dati vengono classificate come categoriche o numeriche in base alle condizioni riportate nella tabella seguente. Se la funzionalità non soddisfa queste condizioni, ad esempio una colonna di tipo String con > 100 valori univoci, la funzionalità viene eliminata dall'algoritmo di Drift dei dati, ma viene comunque profilata. 

| Tipo di funzionalità | Tipo di dati | Condizione | Limitazioni | 
| ------------ | --------- | --------- | ----------- |
| Categorical | String, bool, int, float | Il numero di valori univoci nella funzionalità è minore di 100 e minore del 5% del numero di righe. | Il valore null viene considerato come una categoria specifica. | 
| Numerico | int, float | I valori nella funzionalità sono di un tipo di dati numerico e non soddisfano la condizione per una funzionalità categorica. | Funzionalità eliminata se > 15% dei valori sono null. | 

## <a name="next-steps"></a>Passaggi successivi

* Passare a [Azure Machine Learning Studio](https://ml.azure.com) o al [notebook di Python](https://aka.ms/datadrift-notebook) per configurare un monitoraggio del set di dati.
* Vedere come configurare la tendenza dei dati nei [modelli distribuiti nel servizio Azure Kubernetes](how-to-monitor-data-drift.md).
* Configurare i monitoraggi per la desincronizzazione del set di dati con [griglia di eventi](how-to-use-event-grid.md). 