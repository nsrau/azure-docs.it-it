---
title: Analizzare e monitorare la deriva dei dati sui set di dati (anteprima)Analyze and monitor for data drift on datasets (preview)
titleSuffix: Azure Machine Learning
description: Creare monitoraggi (anteprima) dei set di dati di Azure Machine Learning, monitorare la deriva dei dati nei set di dati e configurare avvisi.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
ms.author: copeters
author: lostmygithubaccount
ms.date: 11/04/2019
ms.openlocfilehash: 401019c537cb0eb51fa6002637e170a79210f7d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617642"
---
# <a name="detect-data-drift-preview-on-datasets"></a>Rilevare la deriva dei dati (anteprima) nei set di datiDetect data drift (preview) on datasets
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In questo articolo viene illustrato come creare monitoraggi del set di dati di Azure Machine Learning (anteprima), monitorare la deriva dei dati e le modifiche statistiche nei set di dati e configurare gli avvisi.

Con i monitoraggi dei set di dati di Azure Machine Learning è possibile:With Azure Machine Learning dataset monitors, you can:
* **Analizza la deriva nei tuoi dati** per capire come cambia nel tempo.
* **Monitorare i dati** del modello per le differenze tra il training e la pubblicazione dei set di dati.
* **Monitorare i nuovi dati** per le differenze tra qualsiasi set di dati di base e di destinazione.
* **Eseguire il profilo delle feature nei dati** per tenere traccia del modo in cui le proprietà statistiche cambiano nel tempo.
* **Impostare avvisi sulla deriva dei dati** per gli avvisi anticipati a potenziali problemi. 

Le metriche e le informazioni dettagliate sono disponibili tramite la risorsa [Di Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) associata all'area di lavoro di Azure Machine Learning.Metrics and insights are available through the Azure Application Insights resource associated with the Azure Machine Learning workspace.

> [!Important]
> Si prega di notare che il monitoraggio dei dati deriva con l'SDK è disponibile in tutte le edizioni, mentre il monitoraggio dei dati deriva attraverso lo studio sul web è enterprise edition solo.

## <a name="prerequisites"></a>Prerequisiti

Per creare e utilizzare i monitordi dati, è necessario:To create and work with dataset monitors, you need:
* Una sottoscrizione di Azure. Se non è disponibile una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).
* Area di lavoro di [Azure Machine Learning](how-to-manage-workspace.md).
* [Azure Machine Learning SDK per Python è installato,](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)che include il pacchetto azureml-datasets.
* Dati strutturati (tabulari) con un timestamp specificato nel percorso del file, nel nome file o nella colonna dei dati.

## <a name="what-is-data-drift"></a>Che cos'è la deriva dei dati?

Nel contesto dell'apprendimento automatico, la deriva dei dati è la modifica dei dati di input del modello che porta alla riduzione delle prestazioni del modello. È uno dei motivi principali per cui l'accuratezza del modello si riduce nel tempo, quindi il monitoraggio della deriva dei dati aiuta a rilevare i problemi di prestazioni del modello.

Le cause della deriva dei dati includono: 

- Modifiche al processo a monte, ad esempio un sensore da sostituire che modifica le unità di misura da pollici a centimetri. 
- Problemi di qualità dei dati, ad esempio un sensore rotto che legge sempre 0.
- Deriva naturale nei dati, come la temperatura media che cambia con le stagioni.
- Cambio in relazione tra le funzioni o spostamento covariato. 

Con i monitoraggi dei set di dati di Azure Machine Learning è possibile configurare avvisi che facilitano il rilevamento dei tipi di dati nei set di dati nel tempo. 

### <a name="dataset-monitors"></a>Monitoraggi set di dati 

È possibile creare un monitoraggio del set di dati per rilevare e avvisare la deriva dei dati sui nuovi dati in un set di dati, analizzare i dati cronologici per la deriva e profilare nuovi dati nel tempo. L'algoritmo di deriva dei dati fornisce una misura generale della modifica dei dati e dell'indicazione delle caratteristiche responsabili di ulteriori indagini. I monitoraggi dei set di dati producono una `timeseries` serie di altre metriche profilando nuovi dati nel set di dati. Gli avvisi personalizzati possono essere impostati su tutte le metriche generate dal monitoraggio tramite [Azure Application Insights.](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) I monitoraggi dei set di dati possono essere utilizzati per rilevare rapidamente i problemi relativi ai dati e ridurre il tempo necessario per eseguire il debug del problema identificando le cause probabili.  

Concettualmente, esistono tre scenari principali per l'impostazione di monitoraggi del set di dati in Azure Machine Learning.Conceptually, there are three primary scenarios for setting up dataset monitors in Azure Machine Learning.

Scenario | Descrizione
---|---
Monitoraggio dei dati di pubblicazione di un modello per la deriva dai dati di training del modelloMonitoring a model's serving data for drift from the model's training data | I risultati di questo scenario possono essere interpretati come il monitoraggio di un proxy per l'accuratezza del modello, dato che l'accuratezza del modello si riduce se i dati di gestione si allontanano dai dati di training.
Monitoraggio di un set di dati di serie temporali per la deriva da un periodo di tempo precedente. | Questo scenario è più generale e può essere utilizzato per monitorare i set di dati coinvolti a monte o a valle della creazione del modello.  Il set di dati di destinazione deve avere una colonna timestamp, mentre il set di dati di base può essere qualsiasi set di dati tabulare con funzionalità comuni al set di dati di destinazione.
Esecuzione di analisi sui dati passati. | Questo scenario può essere utilizzato per comprendere i dati cronologici e prendere decisioni sulle impostazioni per i monitoraggi dei set di dati.

## <a name="how-dataset-can-monitor-data"></a>Come il set di dati può monitorare i datiHow dataset can monitor data

Con Azure Machine Learning, la deriva dei dati viene monitorata tramite i set di dati. Per monitorare la deriva dei dati, viene specificato un set di dati di base, in genere il set di dati di training per un modello. Un set di dati di destinazione, in genere modellare i dati di input, viene confrontato nel tempo con il set di dati di base. Questo confronto significa che il set di dati di destinazione deve avere una colonna timestamp specificata.

### <a name="set-the-timeseries-trait-in-the-target-dataset"></a>Impostare `timeseries` il tratto nel set di dati di destinazioneSet the tratto in the target dataset

Il set di dati `timeseries` di destinazione deve avere il tratto impostato su di esso specificando la colonna timestamp da una colonna nei dati o una colonna virtuale derivata dal modello di percorso dei file. Questa operazione può essere eseguita tramite Python SDK o Azure Machine Learning Studio.This can be done through the Python SDK or Azure Machine Learning studio. È necessario specificare una colonna che rappresenta `timeseries` un timestamp "fine grain" per aggiungere tratto al set di dati. Se i dati sono partizionati in una struttura di cartelle con informazioni sull'ora, ad esempio ''yyyy/MM/dd'', è possibile creare una colonna virtuale tramite l'impostazione del modello di percorso e impostarla come timestamp "granulosa grossolana" per migliorare l'importanza della funzionalità delle serie temporali. 

#### <a name="python-sdk"></a>Python SDK

Il [`Dataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-fine-grain-timestamp--coarse-grain-timestamp-none--validate-false-) metodo [`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-fine-grain-timestamp--coarse-grain-timestamp-none--validate-false-) della classe definisce la colonna timestamp per il set di dati. 

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

Per un esempio completo `timeseries` dell'utilizzo dell'tratti dei set di dati, vedere il [blocco appunti](https://aka.ms/azureml-tsd-notebook) di esempio o la [documentazione sdk](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-fine-grain-timestamp--coarse-grain-timestamp-none--validate-false-)dei dataset .

#### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

Se si crea il set di dati usando Azure Machine Learning Studio, verificare che il percorso dei dati contenga informazioni sul timestamp, includere tutte le sottocartelle con i dati e impostare il formato della partizione. 

Nell'esempio seguente vengono acquisiti tutti i dati nella *sottocartella NoaaIsdFlorida/2019* e il formato della partizione specifica l'anno, il mese e il giorno del timestamp. 

[![Formato partizione](./media/how-to-monitor-datasets/partition-format.png)](media/how-to-monitor-datasets/partition-format-expand.png)

Nelle impostazioni **dello schema** specificare la colonna timestamp da una colonna virtuale o reale nel set di dati specificato:In the Schema settings, specify the timestamp column from a virtual or real column in the specified dataset:

![Timestamp](./media/how-to-monitor-datasets/timestamp.png)

## <a name="dataset-monitor-settings"></a>Impostazioni di Monitoraggio set di dati

Dopo aver creato il set di dati con le impostazioni timestamp specificate, è possibile configurare il monitoraggio del set di dati.

Le varie impostazioni di monitoraggio del set di dati sono suddivise in tre gruppi: **Informazioni di base, Impostazioni monitor** e Impostazioni **riempimento**.

### <a name="basic-info"></a>Info di base

Questa tabella contiene le impostazioni di base utilizzate per il monitoraggio del set di dati.

| Impostazione | Descrizione | Suggerimenti | Modificabile | 
| ------- | ----------- | ---- | ------- | 
| Nome | Nome del monitoraggio del set di dati. | | No |
| Set di dati di base | Set di dati tabulare che verrà usato come base per il confronto del set di dati di destinazione nel tempo. | Il set di dati di base deve avere caratteristiche in comune con il set di dati di destinazione. In genere, la linea di base deve essere impostata sul set di dati di training di un modello o su una sezione del set di dati di destinazione. | No |
| DataSet di destinazione | Set di dati tabulare con la colonna timestamp specificata che verrà analizzata per la deriva dei dati. | Il set di dati di destinazione deve avere funzionalità `timeseries` in comune con il set di dati di base e deve essere un set di dati a cui vengono aggiunti i nuovi dati. È possibile analizzare i dati cronologici nel set di dati di destinazione oppure monitorare nuovi dati. | No | 
| Frequenza | Frequenza che verrà utilizzata per pianificare il processo della pipeline e analizzare i dati cronologici se si esegue un riempimento. Le opzioni includono giornalmente, settimanalmente o mensilmente. | Regolare questa impostazione per includere una dimensione comparabile dei dati rispetto alla linea di base. | No | 
| Funzionalità | Elenco delle funzionalità che verranno analizzate per la deriva dei dati nel tempo. | Impostare sulle feature di output di un modello per misurare la deriva del concetto. Non includere caratteristiche che vanno naturalmente alla deriva nel tempo (mese, anno, indice, ecc.). È possibile riempire e dati esistenti monitor di deriva dopo aver regolato l'elenco delle funzioni. | Sì | 
| Destinazione del calcolo | Destinazione di calcolo di Azure Machine Learning per eseguire i processi di monitoraggio del set di dati. | | Sì | 

### <a name="monitor-settings"></a>Impostazioni del monitor

Queste impostazioni sono per la pipeline di monitoraggio del set di dati pianificata, che verrà creata. 

| Impostazione | Descrizione | Suggerimenti | Modificabile | 
| ------- | ----------- | ---- | ------- |
| Abilitare | Abilitare o disabilitare la pianificazione nella pipeline di monitoraggio del set di datiEnable or disable the schedule on the dataset monitor pipeline | Disattivare la pianificazione per analizzare i dati cronologici con l'impostazione di riempimento. Può essere abilitato dopo la creazione del monitoraggio del set di dati. | Sì | 
| Latenza | Tempo, in poche ore, per l'arrivo dei dati nel set di dati. Ad esempio, se sono necessari tre giorni prima che i dati arrivino nel database SQL incapsulato dal set di dati, impostare la latenza su 72.For example, if it takes three days for data to arrive in the SQL DB the dataset encapsulates, set the latency to 72. | Non può essere modificato dopo la creazione del monitoraggio del set di dati | No | 
| Indirizzi di posta elettronica | Indirizzi e-mail per gli avvisi in base alla violazione della soglia di soglia della percentuale di deriva dei dati. | I messaggi di posta elettronica vengono inviati tramite Monitoraggio di Azure.Emails are sent through Azure Monitor. | Sì | 
| Soglia | Soglia percentuale di deriva dei dati per gli avvisi tramite posta elettronica. | È possibile impostare ulteriori avvisi ed eventi su molte altre metriche nella risorsa di Application Insights associata dell'area di lavoro. | Sì | 

### <a name="backfill-settings"></a>Impostazioni di riempimento

Queste impostazioni servono per l'esecuzione di un backfill sui dati passati per le metriche di deriva dei dati.

| Impostazione | Descrizione | Suggerimenti |
| ------- | ----------- | ---- |
| Data di inizio | Data di inizio del processo di riempimento. | | 
| Data fine | Data di fine del processo di riempimento. | La data di fine non può essere superiore a 31 unità di tempo di frequenza dalla data di inizio. In un monitoraggio set di dati esistente, le metriche possono essere riinserite in base al backup per analizzare i dati cronologici o sostituire le metriche con impostazioni aggiornate. |

## <a name="create-dataset-monitors"></a>Creare monitoraggi del set di datiCreate dataset monitors 

Creare monitoraggi del set di dati per rilevare e avvisare la deriva dei dati in un nuovo set di dati con Azure Machine Learning Studio o Python SDK. 

### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

Per impostare gli avvisi nel monitoraggio del set di dati, l'area di lavoro che contiene il set di dati per cui si vuole creare un monitoraggio deve avere funzionalità Enterprise edition. 

Dopo aver confermato la funzionalità dell'area di lavoro, passare alla home page dell'organizzazione e selezionare la scheda Set di dati a sinistra. Selezionare Monitor per set di dati.

![Elenco monitor](./media/how-to-monitor-datasets/monitor-list.png)

Fare clic sul pulsante **Crea monitor** e continuare la procedura guidata facendo clic su **Avanti**.

![Procedura guidata](./media/how-to-monitor-datasets/wizard.png)

Il monitoraggio del set di dati risultante verrà visualizzato nell'elenco. Selezionarlo per passare alla pagina dei dettagli del monitor.

### <a name="from-python-sdk"></a>Da Python SDK

Vedere la [documentazione](/python/api/azureml-datadrift/azureml.datadrift) di riferimento di Python SDK sulla deriva dei dati per i dettagli completi. 

L'esempio seguente mostra come creare un monitoraggio del set di dati usando Python SDK

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

Per un esempio completo `timeseries` di impostazione di un set di dati e di un rilevatore di deriva dati, vedere il blocco appunti di [esempio.](https://aka.ms/datadrift-notebook)

## <a name="understanding-data-drift-results"></a>Informazioni sui risultati della deriva dei dati

Il monitoraggio dei dati produce due gruppi di risultati: Panoramica della derapata e Dettagli funzionalità. L'animazione seguente mostra i grafici di monitor di derago disponibili in base alla feature e alla metrica selezionate. 

![Video dimostrativo](./media/how-to-monitor-datasets/video.gif)

### <a name="drift-overview"></a>Panoramica della deriva

La sezione **Panoramica della deriva** contiene informazioni di primo livello sull'entità della deriva dei dati e sulle caratteristiche da analizzare ulteriormente. 

| Metrica | Descrizione | Suggerimenti | 
| ------ | ----------- | ---- | 
| Magnitudine deriva dati | Data come percentuale tra il set di dati di base e quello di destinazione nel tempo. Da 0 a 100 dove 0 indica set di dati identici e 100 indica la funzionalità di deriva dei dati di Azure Machine Learning può distinguere completamente i due set di dati. | Il rumore nella percentuale precisa misurata è dovuto alle tecniche di apprendimento automatico utilizzate per generare questa portata. | 
| Contributo alla deriva per caratteristica | Il contributo di ogni funzionalità nel set di dati di destinazione alla grandezza di deriva misurata. |  A causa dello spostamento covariato, la distribuzione sottostante di una funzione non deve necessariamente cambiare per avere un'importanza relativamente elevata. | 

L'immagine seguente è un esempio di grafici visualizzati nei risultati della **panoramica di Drift** in Azure Machine Learning Studio, risultanti da un riempimento di dati di [superficie integrati noAA.](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/) I dati sono `stationName contains 'FLORIDA'`stati campionati in , con gennaio 2019 utilizzato come set di dati di base e tutti i dati 2019 utilizzati come destinazione.
 
![Panoramica della deriva](./media/how-to-monitor-datasets/drift-overview.png)

### <a name="feature-details"></a>Dettagli della funzione

La sezione **Dettagli funzionalità** contiene informazioni dettagliate a livello di feature nella modifica della distribuzione della feature selezionata, nonché altre statistiche nel tempo. 

Anche il set di dati di destinazione viene profilato nel tempo. La distanza statistica tra la distribuzione di base di ogni feature viene confrontata con quella del set di dati di destinazione nel tempo, che è concettualmente simile alla grandezza della deriva dei dati, con l'eccezione che questa distanza statistica è per una singola feature. Min, max, e media sono disponibili anche. 

In Azure Machine Learning Studio, se si fa clic su un punto dati nel grafico, la distribuzione della funzionalità visualizzata verrà regolata di conseguenza. Per impostazione predefinita, mostra la distribuzione del set di dati di base e la distribuzione dell'esecuzione più recente della stessa funzionalità. 

Queste metriche possono anche essere recuperate in Python SDK tramite il `get_metrics()` metodo su un `DataDriftDetector` oggetto. 

#### <a name="numeric-features"></a>Caratteristiche numeriche 

Le funzionalità numeriche vengono profilate in ogni esecuzione di Monitoraggio set di dati. Gli elementi seguenti sono esposti in Azure Machine Learning Studio.The following are exposed in the Azure Machine Learning studio. La densità di probabilità viene visualizzata per la distribuzione.

| Metrica | Descrizione |  
| ------ | ----------- |  
| Distanza Wasserstein | Quantità minima di lavoro per trasformare la distribuzione di base nella distribuzione di destinazione. |
| Valore medio | Valore medio della feature. |
| Valore minimo | Valore minimo della feature. |
| Valore massimo | Valore massimo della funzione. |

![Dettagli della funzione numerici](./media/how-to-monitor-datasets/feature-details.png)

#### <a name="categorical-features"></a>Caratteristiche categoriche 

Le funzionalità numeriche vengono profilate in ogni esecuzione di Monitoraggio set di dati. Gli elementi seguenti sono esposti in Azure Machine Learning Studio.The following are exposed in the Azure Machine Learning studio. Viene visualizzato un istogramma per la distribuzione.

| Metrica | Descrizione |  
| ------ | ----------- |  
| Distanza euclidea | Distanza geometrica tra le distribuzioni della linea di base e di destinazione. |
| Valori univoci | Numero di valori univoci (cardinalità) della funzionalità. |


![Categoria dettagli funzionalità](./media/how-to-monitor-datasets/feature-details2.png)

## <a name="metrics-alerts-and-events"></a>Metriche, avvisi ed eventi

È possibile eseguire query sulle metriche nella risorsa [di Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) associata all'area di lavoro di Machine Learning.Metrics can be queryed in the Azure Application Insights resource associated with your machine learning workspace. Che consente l'accesso a tutte le funzionalità di Application Insights, inclusa la configurazione per le regole di avviso personalizzate e i gruppi di azioni per attivare un'azione, ad esempio una funzione di posta elettronica/SMS/Push/Voice o Azure.Which gives access to all features of Application Insights including set up for custom alert rules and action groups to trigger an action such, an Email/SMS/Push/Voice or Azure Function. Per informazioni dettagliate, fare riferimento alla documentazione completa di Application Insights.Please refer to the complete Application Insights documentation for details. 

Per iniziare, passare al portale di Azure e selezionare la pagina **Panoramica** dell'area di lavoro.  La risorsa di Application Insights associata si trova all'estrema destra:The associated Application Insights resource is on the far at the right:

[![Panoramica del portale di Azure](./media/how-to-monitor-datasets/ap-overview.png)](media/how-to-monitor-datasets/ap-overview-expanded.png)

Selezionare Registri (Analisi) in Monitoraggio nel riquadro sinistro:Select Logs (Analytics) under Monitoring on the left pane:

![Panoramica di Application Insights](./media/how-to-monitor-datasets/ai-overview.png)

Le metriche di `customMetrics`monitoraggio del set di dati vengono archiviate come . È possibile scrivere ed eseguire una query dopo aver impostato un monitoraggio del set di dati per visualizzarli:You can write and run a query after setting up a dataset monitor to view them:

[![Query di analisi dei log](./media/how-to-monitor-datasets/simple-query.png)](media/how-to-monitor-datasets/simple-query-expanded.png)

Dopo aver identificato le metriche per impostare le regole di avviso, creare una nuova regola di avviso:After identifying metrics to set up alert rules, create a new alert rule:

![Nuova regola di avviso](./media/how-to-monitor-datasets/alert-rule.png)

È possibile utilizzare un gruppo di azioni esistente o crearne uno nuovo per definire l'azione da eseguire quando vengono soddisfatte le condizioni impostate:

![Nuovo gruppo di azioni](./media/how-to-monitor-datasets/action-group.png)

## <a name="troubleshooting"></a>Risoluzione dei problemi

Limitazioni e problemi noti:

* L'intervallo di tempo dei processi di backfill è limitato a 31 intervalli dell'impostazione della frequenza del monitor. 
* Limitazione di 200 feature, a meno che non sia stato specificato un elenco di feature (tutte le funzioni utilizzate).
* Le dimensioni di calcolo devono essere sufficientemente grandi per gestire i dati. 
* Verificare che il set di dati condisponga di dati entro la data di inizio e di fine per l'esecuzione di un determinato monitoraggio.
* I monitoraggi dei set di dati funzioneranno solo su set di dati che contengono o più 50 righe. 

Le colonne nel set di dati vengono classificate come categoriche o numeriche in base alle condizioni riportate nella tabella seguente. Se la funzionalità non soddisfa queste condizioni, ad esempio una colonna di tipo stringa con >100 valori univoci, la funzionalità viene eliminata dall'algoritmo di deriva dei dati, ma è comunque profilata. 

| Tipo di funzionalità | Tipo di dati | Condizione | Limitazioni | 
| ------------ | --------- | --------- | ----------- |
| Categorical | stringa, bool, int, float | Il numero di valori univoci nella feature è inferiore a 100 e inferiore al 5% del numero di righe. | Null viene considerato come la propria categoria. | 
| Numerico | int, float | I valori nella feature sono di un tipo di dati numerico e non soddisfano la condizione per una feature di categoria. | Caratteristica eliminata se >15% dei valori sono null. | 

## <a name="next-steps"></a>Passaggi successivi

* Passare allo [studio di Azure Machine Learning](https://ml.azure.com) o al blocco appunti [Python](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datadrift-tutorial/datadrift-tutorial.ipynb) per configurare un monitoraggio del set di dati.
* Informazioni su come configurare la deriva dei dati nei [modelli distribuiti nel servizio Azure Kubernetes.](how-to-monitor-data-drift.md)
* Impostare i monitoraggi della deriva del set di dati con [la griglia degli eventi.](how-to-use-event-grid.md) 
