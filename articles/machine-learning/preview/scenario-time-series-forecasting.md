---
title: Previsione in serie temporale della domanda di energia | Microsoft Docs
description: Come applicare l'apprendimento automatico per prevedere la domanda di energia in base a una serie temporale in Azure Machine Learning Workbench.
services: machine-learning
documentationcenter: 
author: anta
manager: ireiter
editor: anta
ms.assetid: 
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: anta
ms.openlocfilehash: 17903df93e11b8d1a5b9c6fbe5fd8e53302f45f4
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2017
---
# <a name="energy-demand-time-series-forecasting"></a>Previsione in serie temporale della domanda di energia


La previsione in serie temporale è l'attività di stimare valori futuri in una sequenza di osservazioni ordinata nel tempo. Si tratta di un'esigenza comune, che ha applicazioni in diversi settori. Ad esempio, le società di vendita al dettaglio devono prevedere le vendite future dei prodotti in modo da poter organizzare in modo efficiente le catene di approvvigionamento per soddisfare la domanda. Allo stesso modo, le aziende di spedizione devono stimare la domanda dei propri servizi in modo da pianificare in anticipo i requisiti relativi alla forza lavoro e gli itinerari. In molti casi, il rischio finanziario associato a previsioni inaccurate può essere significativo. Di conseguenza, la previsione è spesso un'attività aziendale critica.

Questo esempio mostra in che modo eseguire previsioni in serie temporale tramite l'applicazione di tecniche di apprendimento automatico. Viene descritto ogni passaggio del processo di modellazione, tra cui:
- Preparazione dei dati per pulizia e formattazione.
- Creazione di caratteristiche per i modelli di apprendimento automatico da dati delle serie temporali non elaborati.
- Training di diversi modelli di apprendimento automatico.
- Valutazione dei modelli tramite il confronto delle prestazioni in base a un set di dati di test reso disponibile.
- Operazionalizzazione del modello migliore, rendendolo disponibile tramite un servizio Web per generare previsioni su richiesta.

Azure Machine Learning Workbench semplifica il processo di modellazione in ogni fase: 
- L'esempio mostra in che modo l'ambiente notebook di Jupyter, disponibile direttamente tramite Workbench, può semplificare lo sviluppo di codice Python. Il processo di sviluppo del modello può essere spiegato agli altri in modo più chiaro tramite grafici e annotazioni di markdown. Questi notebook possono essere visualizzati, modificati ed eseguiti direttamente da Workbench.
- I modelli di cui è stato eseguito il training possono essere resi permanenti e caricati nell'archiviazione BLOB. In questo modo, i data scientist possono tenere traccia degli oggetti modello di cui è stato eseguito il training e fare in modo che siano conservati e recuperabili se necessario.
- È possibile registrare le metriche durante l'esecuzione di uno script Python, permettendo ai data scientist di mantenere un record dei punteggi delle prestazioni del modello.
- Workbench genera tabelle personalizzabili delle metriche registrate, permettendo al data scientist di confrontare facilmente le metriche delle prestazioni del modello. Vengono generati automaticamente grafici per identificare rapidamente il modello dalle prestazioni migliori.
- L'esempio mostra infine come operazionalizzare un modello sottoposto a training distribuendolo in un servizio Web in tempo reale.

## <a name="link-to-the-gallery-github-repository"></a>Collegamento al repository GitHub delle raccolte
Il repository GitHub pubblico per questo scenario reale contiene tutti i materiali, inclusi gli esempi di codice necessari per questo esempio:

[https://github.com/Azure/MachineLearningSamples-EnergyDemandTimeSeriesForecasting](https://github.com/Azure/MachineLearningSamples-EnergyDemandTimeSeriesForecasting)


## <a name="use-case-overview"></a>Panoramica del caso d'uso

Questo scenario è incentrato sulla previsione della domanda di energia, il cui obiettivo è stimare il carico futuro in una rete energetica. Si tratta di un'operazione aziendale critica per le aziende del settore energetico, in quanto gli operatori devono mantenere il giusto equilibrio tra l'energia consumata in una rete e quella fornita alla rete. La fornitura di una quantità eccessiva di alimentazione alla rete può produrre sprechi o errori tecnici. Tuttavia, se viene fornita alimentazione insufficiente, si può provocare un blackout, lasciando i clienti senza elettricità. In genere, gli operatori della rete possono adottare decisioni a breve termine per gestire la fornitura di energia alla rete e mantenere bilanciato il carico. Di conseguenza, un'accurata previsione a breve termine della domanda di energia è essenziale perché l'operatore possa prendere queste decisioni in tutta sicurezza.

Questo scenario presenta in modo dettagliato la creazione di una soluzione di apprendimento automatico per la previsione della domanda di energia. Il training della soluzione è stato eseguito su un set di dati pubblico del [New York Independent System Operator (NYISO)](http://www3.dps.ny.gov/W/PSCWeb.nsf/All/298372E2CE4764E885257687006F39DF?OpenDocument), che gestisce la rete elettrica dello stato di New York. Il set di dati include i dati relativi alla domanda di elettricità ora per ora per la città di New York in un periodo di cinque anni. È stato inoltre recuperato un set di dati aggiuntivo contenente le condizioni meteo ora per ora nella città di New York durante lo stesso periodo di tempo da [darksky.net](https://darksky.net).

## <a name="prerequisites"></a>prerequisiti

- Un [account di Azure](https://azure.microsoft.com/free/) (sono disponibili versioni di valutazione gratuite).
- Una copia di [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) installata seguendo la [guida introduttiva all'installazione](./quickstart-installation.md) per installare il programma e creare un'area di lavoro.
- Questo esempio presuppone l'esecuzione di Azure Machine Learning Workbench su Windows 10 con il [motore Docker](https://www.docker.com/) installato in locale. Se si usa macOS, le istruzioni sono in gran parte uguali.
- Operazionalizzazione di Azure Machine Learning installato con un ambiente di distribuzione locale configurato e un account di gestione del modello creato seguendo questa [guida](./model-management-configuration.md).
- Per questo esempio è necessario aggiornare l'installazione di Pandas alla versione 0.20.3 o successiva e installare matplotlib. Scegliere *Open Command Prompt* (Apri prompt dei comandi) dal menu *File* in Workbench ed eseguire i comandi seguenti per installare queste dipendenze:

    ```
    conda install "pandas>=0.21.1"
    ```
    
## <a name="create-a-new-workbench-project"></a>Creare un nuovo progetto Workbench

Creare un nuovo progetto usando questo esempio come modello:
1.  Aprire Azure Machine Learning Workbench
2.  Nella pagina **Projects** (Progetti) fare clic sul segno **+** e selezionare **New Project** (Nuovo progetto)
3.  Nel riquadro **Create New Project** (Crea nuovo progetto) specificare le informazioni per il nuovo progetto
4.  Nella casella di ricerca **Search Project Templates** (Cerca modelli di progetto) digitare "Energy Demand Time Series Forecasting" (Previsione in serie temporale della domanda di energia) e selezionare il modello
5.  Fare clic su **Crea**


## <a name="data-description"></a>Descrizione dei dati

Questo esempio offre due set di dati scaricati tramite il blocco appunti `1-data-preparation.ipynb`: `nyc_demand.csv` e `nyc_weather.csv`.

**nyc_demand.csv** contiene i valori relativi alla domanda di energia ora per ora nella città di New York per gli anni 2012-2017. I dati hanno la semplice struttura seguente:

| Timestamp | demand |
| --- | --- |
| 2012-01-01 00:00:00 | 4937.5 |
| 2012-01-01 01:00:00 | 4752.1 |
| 2012-01-01 02:00:00 | 4542.6 |
| 2012-01-01 03:00:00 | 4357.7 |

I valori relativi alla domanda sono espressi in megawattora (MWh). Di seguito viene presentato il grafico della domanda di energia per un periodo di 7 giorni nel mese di luglio 2017:

![Domanda di energia](./media/scenario-time-series-forecasting/energy_demand.png  "Domanda di energia")

**nyc_weather.csv** contiene i valori orari relativi al meteo per la città di New York negli anni 2012-2017:

| Timestamp | precip | temp
| --- | --- | --- |
| 2012-01-01 00:00:00 | 0.0 | 46.13 |
| 2012-01-01 01:00:00 | 0.01 | 45.89 |
| 2012-01-01 02:00:00 | 0.05 | 45.04 |
| 2012-01-01 03:00:00 | 0.02 | 45.03 |

*precip* è una misura in percentuale del livello di precipitazioni. I valori di *temp* (temperatura) sono stati ridimensionati in modo da rientrare tutti nell'intervallo [0, 100].

## <a name="scenario-structure"></a>Struttura dello scenario

Quando si apre il progetto per la prima volta in Azure Machine Learning Workbench, passare al riquadro *Files* (File) sul lato sinistro. Con l'esempio vengono forniti i file di codice seguenti:
- `1-data-preparation.ipynb`: questo notebook di Jupyter scarica ed elabora i dati per prepararli per la modellazione. Questo è il primo notebook da eseguire.
- `2-linear-regression.ipynb`: questo notebook esegue il training di un modello di regressione lineare sui dati di training.
- `3-ridge.ipynb`: esegue il training di un modello di regressione ridge.
- `4-ridge-poly2.ipynb`: esegue il training di un modello di regressione ridge su caratteristiche polinomiali di grado 2.
- `5-mlp.ipynb`: esegue il training di una rete neurale di percezione a più livelli.
- `6-dtree.ipynb`: esegue il training di un modello di albero delle decisioni.
- `7-gbm.ipynb`: esegue il training di un modello di computer con boosting a gradienti.
- `8-evaluate-model.py`: questo script carica un modello sottoposto a training e lo usa per eseguire stime per un set di dati di test reso disponibile. Lo script genera metriche di valutazione del modello per poter confrontare le prestazioni di modelli diversi.
- `9-forecast-output-exploration.ipynb`: questo notebook produce visualizzazioni delle previsioni generate dai modelli di apprendimento automatico.
- `10-deploy-model.ipynb`: questo notebook mostra come operazionalizzare un modello di previsione sottoposto a training in un servizio Web in tempo reale.
- `evaluate-all-models.py`: questo script valuta tutti i modelli sottoposti a training. Lo script offre un'alternativa all'esecuzione di `8-evaluate-model.py` per ogni modello sottoposto a training singolarmente.

### <a name="1-data-preparation-and-cleaning"></a>1. Preparazione e pulizia dei dati

Per iniziare a eseguire l'esempio, prima di tutto fare clic su `1-data-preparation.ipynb` per aprire il notebook in modalità di anteprima. Fare clic su ***Start Notebook Server*** (Avvia server notebook) per avviare un server notebook di Jupyter e il kernel Python nel computer. È possibile eseguire i notebook da Workbench oppure usare il browser passando a [http://localhost:8888](http://localhost:8888). Si noti che è necessario modificare il kernel in *NOME_PROGETTO local*. Modificare questa impostazione nel menu *Kernel* nel notebook in *Change kernel* (Modifica kernel). Premere ***MAIUSC+INVIO*** per eseguire il codice in singole celle del notebook oppure scegliere *Run All* (Esegui tutto) dal menu *Cell* (Cella) per eseguire l'intero notebook.

il notebook scarica prima di tutto i dati da un contenitore di archiviazione BLOB ospitato in Azure. I dati vengono quindi archiviati nel computer in `AZUREML_NATIVE_SHARE_DIRECTORY`. Questo percorso è accessibile da qualsiasi notebook o script eseguito da Workbench e di conseguenza è ideale per archiviare i dati e i modelli sottoposti a training.

Dopo aver scaricato i dati, il notebook li pulisce colmando i vuoti nella serie temporale e inserendo i valori mancanti tramite l'interpolazione. La pulizia dei dati della serie temporale in questo modo ottimizza la quantità di dati disponibili per il training dei modelli.

Dai dati non elaborati puliti vengono create diverse caratteristiche del modello. Queste caratteristiche possono essere suddivise in due gruppi:

- Le **caratteristiche basate sul tempo** vengono derivate dalla variabile *timestamp*, ad esempio *month*, *dayofweek* e *hour*.
- Le **caratteristiche di ritardo** sono valori posticipati nel tempo degli effettivi valori di temperatura o relativi alla domanda. Queste caratteristiche hanno lo scopo di acquisire le dipendenze condizionali tra periodi di tempo consecutivi nel modello.

Il set di dati delle caratteristiche risultante può quindi essere usato per lo sviluppo di modelli di previsione.

### <a name="2-model-development"></a>2. Sviluppo del modello

Un primo approccio di modellazione può essere un semplice modello di regressione lineare. Il notebook `2-linear-regression.ipynb` mostra come eseguire il training di un modello di previsione di regressione lineare per la domanda di energia futura. In particolare, verrà eseguito il training del modello per stimare la domanda di energia un'ora (*t+1*) in anticipo rispetto al periodo di tempo corrente (*t*). Tuttavia, questo modello costituito da un solo passaggio può essere applicato in modo ricorsivo in fase di test per generare previsioni per i periodi di tempo futuri, *t+n*.

Per eseguire il training di un modello, viene creata una pipeline predittiva che comporta tre passaggi distinti:

- **Una trasformazione dei dati**: i formati necessari per i dati di input possono variare a seconda dell'algoritmo di apprendimento automatico. In questo caso, il modello di regressione lineare richiede che alle variabili di categoria sia applicata la codifica one-hot.
- **Una routine di convalida incrociata**: spesso un modello di apprendimento automatico includerà uno o più iperparametri che devono essere ottimizzati tramite la sperimentazione. È possibile usare la convalida incrociata per individuare il set ottimale di valori di parametro. il modello viene sottoposto a training e valutato ripetutamente in diverse sezioni del set di dati. I parametri migliori sono quelli che ottengono le prestazioni ottimali per il modello considerando la media tra le sezioni di convalida incrociata. Questo processo è descritto più dettagliatamente in `2-linear-regression.ipynb`.
- **Training del modello finale**: viene eseguito il training del modello sull'intero set di dati, usando il miglior set di iperparametri.

È stata inclusa una serie di sei modelli diversi nei notebook numerati da 2 a 7. Ogni notebook esegue il training di un modello diverso e lo archivia nel percorso `AZUREML_NATIVE_SHARE_DIRECTORY`. Dopo aver eseguito il training di tutti i modelli sviluppati per questo scenario, è possibile valutarli e confrontarli come descritto nella sezione successiva.

### <a name="3-model-evaluation-and-comparison"></a>3. Valutazione e confronto dei modelli

È possibile usare un modello sottoposto a training per eseguire previsioni per periodi di tempo futuri. È consigliabile valutare questi modelli in base a un set di dati di test reso disponibile. Valutando i diversi modelli in base allo stesso set di dati non visto, è possibile confrontarne correttamente le prestazioni e identificare il modello migliore. In questo scenario il modello sottoposto a training viene applicato in modo ricorsivo per prevedere più intervalli temporali in futuro. In particolare, vengono generate le previsioni fino a un massimo di sei ore (*t+6*) in anticipo rispetto all'ora corrente *t*. Queste stime vengono valutate rispetto all'effettiva domanda osservata per ogni periodo di tempo.

Per valutare un modello, aprire lo script `8-evaluate-model.py` dal riquadro *Files* (File) in Workbench. Verificare che l'opzione *Run Configuration* (Configurazione esecuzione) sia impostata su **local** (locale) e quindi digitare il nome del modello nel campo *Arguments* (Argomenti). Il nome del modello deve corrispondere esattamente alla variabile *model_name* impostata all'inizio del notebook in cui viene eseguito il training del modello. Ad esempio, immettere linear_regression per valutare il modello di regressione lineare di cui è stato eseguito il training. In alternativa, dopo che è stato eseguito il training di tutti i modelli, è possibile valutarli tutti insieme con un solo comando eseguendo `evaluate-all-models.py`. Per eseguire questo script, aprire un prompt dei comandi da Workbench ed eseguire il comando seguente:

```
python evaluate-all-models.py
```
Lo script `8-evaluate-model.py` esegue le operazioni seguenti:

- Carica una pipeline del modello sottoposto a training dal disco
- Esegue stime in base al set di dati di test
- Calcola le metriche delle prestazioni dei modelli e le registra
- Salva le stime del set di dati di test in `AZUREML_NATIVE_SHARE_DIRECTORY` per l'ispezione e l'analisi successive
- Salva la pipeline del modello sottoposto a training nella cartella *outputs*.

> [!Note]
> Il salvataggio del modello nella cartella *outputs* copia automaticamente l'oggetto modello nell'account di archiviazione BLOB di Azure associato all'account di sperimentazione. Questo significa che sarà sempre possibile recuperare l'oggetto modello salvato dal BLOB, anche se si cambia computer o contesto di calcolo.

Passare al riquadro *Run History* (Cronologia di esecuzione) e fare clic su `8-evaluate-model.py`. Si noteranno alcuni grafici che mostrano diverse metriche delle prestazioni del modello:

- **ME**: errore medio della previsione. Può essere interpretato come deviazione media della previsione.
- **MPE**: [errore medio percentuale](https://en.wikipedia.org/wiki/Mean_percentage_error) (valore ME espresso come percentuale della domanda effettiva)
- **MSE**: [errore quadratico medio](https://en.wikipedia.org/wiki/Mean_squared_error)
- **RMSE**: radice errore quadratico medio (radice quadrata di MSE)
- **MAPE**: [errore medio assoluto percentuale](https://en.wikipedia.org/wiki/Mean_absolute_percentage_error)
- **sMAPE**: [errore medio assoluto simmetrico percentuale](https://en.wikipedia.org/wiki/Symmetric_mean_absolute_percentage_error)
- **MAPE_base**: valore MAPE di una previsione di base in cui la stima è uguale all'ultimo valore di domanda noto.
- **MdRAE**: errore assoluto relativo mediano. Rapporto mediano tra l'errore di previsione e l'errore di previsione di base. Un valore minore di 1 indica che la previsione ha prestazioni migliori di quella di base.

Tutte le metriche indicate sopra fanno riferimento alla previsione *t+1*. Oltre alle metriche precedenti, si potrà osservare anche un set di metriche corrispondenti con il suffisso *_horizon*. Si tratta della media delle metriche in tutti i periodi nell'intervallo di previsione dal periodo *t+1* al periodo *t+6*.

Se le metriche non vengono visualizzate nell'area del grafico, fare clic sul simbolo di ingranaggio nell'angolo superiore destro. Verificare che siano selezionate le metriche delle prestazioni del modello desiderate. Le metriche verranno visualizzate anche in una tabella al di sotto dei grafici. Anche questa tabella è personalizzabile facendo clic sul simbolo di ingranaggio. Ordinare la tabella in base a una metrica delle prestazioni per identificare il modello migliore. Per esaminare la variazione delle prestazioni delle previsioni dal periodo *t+1* al periodo *t+6*, fare clic sulla voce per il modello nella tabella. I grafici che visualizzano le metriche MAPE, MPE e MdRAE per il periodo di previsione vengono mostrati in *Visualizations* (Visualizzazioni).

Quando si valutano i modelli di previsione, può essere molto utile visualizzare le stime di output. In questo modo, il data scientist può determinare se la previsione generata è realistica. Questo permette anche di identificare i problemi nella previsione se, ad esempio, ha prestazioni insoddisfacenti in determinati periodi di tempo. Il notebook `9-forecast-output-exploration.ipynb` produrrà visualizzazioni delle previsioni generate per il set di dati di test.

### <a name="4-deployment"></a>4. Distribuzione

È possibile operazionalizzare il modello migliore distribuendolo come servizio Web in tempo reale. Questo servizio Web può quindi essere richiamato per generare previsioni su richiesta quando diventano disponibili nuovi dati. In questo scenario deve essere generata una nuova previsione ogni ora per stimare la domanda di energia nell'ora successiva. Per eseguire questa attività, è possibile distribuire un servizio Web che accetta come input una matrice di caratteristiche per una determinata ora e restituisce la domanda stimata come output.

In questo esempio viene distribuito un servizio Web in un computer Windows 10. Assicurarsi di aver completato le operazioni preliminari per la distribuzione locale definite in questa [guida introduttiva](https://github.com/Azure/Machine-Learning-Operationalization/blob/master/documentation/getting-started.md) per l'interfaccia della riga di comando di operazionalizzazione. Dopo aver configurato l'ambiente locale e l'account di gestione del modello, eseguire il notebook `10-deploy-model.ipynb` per distribuire il servizio Web.

## <a name="conclusion"></a>Conclusioni

Questo esempio mostra come creare una soluzione di previsione in serie temporale end-to-end per prevedere la domanda di energia. Molti dei principi esplorati in questo esempio possono essere estesi ad altri settori e scenari di previsione.

Questo scenario mostra in che modo un data scientist può usare Azure Machine Learning Workbench per sviluppare soluzioni reali con utili funzionalità come l'ambiente notebook di Jupyter e la registrazione delle metriche. L'esempio descrive anche come operazionalizzare e distribuire un modello usando l'interfaccia della riga di comando di operazionalizzazione di Azure Machine Learning. Dopo la distribuzione, l'API del servizio Web permette a sviluppatori e data engineer di integrare il modello di previsione in una pipeline di dati più estesa.
