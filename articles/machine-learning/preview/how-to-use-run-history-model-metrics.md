---
title: Come usare Cronologia di esecuzione e le metriche del modello in Azure Machine Learning Workbench | Microsoft Docs
description: "Guida all'utilizzo della funzionalità Cronologia di esecuzione e delle metriche del modello di Azure Machine Learning Workbench"
services: machine-learning
author: rastala
ms.author: roastala
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/07/2017
ms.openlocfilehash: 2d1bce6463ac7880fd9091b4f3f2cbb226ea516d
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="how-to-use-run-history-and-model-metrics-in-azure-machine-learning-workbench"></a>Come usare Cronologia di esecuzione e le metriche del modello in Azure Machine Learning Workbench

Azure Machine Learning Workbench supporta esperimenti di data science tramite la funzionalità di **Cronologia di esecuzione**  e le **metriche del modello**.
**Cronologia di esecuzione** consente di tenere traccia dell'output degli esperimenti di machine learning e quindi di filtrarne e confrontarne i risultati.
**Le metriche del modello** possono essere registrate in qualsiasi punto degli script, e consentono di tracciare i valori più importanti negli esperimenti di data science eseguiti.
Questo articolo descrive come usare al meglio queste funzionalità per aumentare la velocità e la qualità degli esperimenti di data science.

## <a name="prerequisites"></a>prerequisiti
Per proseguire con questa guida è necessario:
* [Creare e installare istanze di Azure Machine Learning](quickstart-installation.md)
- [Creare un progetto](quickstart-installation.md)


## <a name="azure-ml-logging-api-overview"></a>Cenni preliminari sull'API di registrazione di Azure Machine Learning
L'[API di registrazione di Azure Machine Learning](reference-logging-api.md) è disponibile tramite il modulo **azureml.logging** di Python (installato con Azure Machine Learning Workbench.) Dopo aver importato il modulo, è possibile usare il metodo **get_azureml_logger** per creare un'istanza dell'oggetto **logger**.
È quindi possibile usare il metodo **log** del logger per archiviare le coppie chiave/valore generate tramite gli script Python.
Al momento sono supportate le metriche del modello di registrazione di tipo scalare ed elenco, come illustrato.

```Python
# create a logger instance in already set up environment 
from azureml.logging import get_azureml_logger
logger = get_azureml_logger()

# log scalar (any integer or floating point type is fine)
logger.log("simple value", 7)


# log list
logger.log("all values", [5, 6, 7])
```
Usare il logger nei progetti di Azure Machine Learning Workbench è facile; questo articolo illustra come fare.

## <a name="create-a-project-in-azure-ml-workbench"></a>Creare un progetto in Azure Machine Learning Workbench
Se non si dispone già di un progetto, è possibile creare uno nella [guida introduttiva alla creazione e all'installazione ](quickstart-installation.md). Dal **dashboard del progetto**, aprire lo script **iris_sklearn.py** come illustrato.

![accesso a uno script dalla scheda file](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-01b.png)

È possibile usare questo script come guida per l'implementazione prevista della metrica del modello di registrazione in Azure Machine Learning.

## <a name="parameterize-and-log-model-metrics-from-script"></a>Impostare parametri e registrare metriche del modello da script
Nello script **iris_sklearn.py**, il modello previsto per importare e creare il logger in Python può essere ridotto alle righe di codice seguenti.

```Python
from azureml.logging import get_azureml_logger
run_logger = get_azureml_logger()
```

Dopo averlo creato, è possibile chiamare il metodo **log** con qualsiasi coppia nome/valore.

Dopo aver completato lo sviluppo, è spesso utile impostare i parametri degli script, in modo che sia possibile passare i valori tramite la riga di comando.
L'esempio seguente illustra come accettare parametri della riga di comando (se presenti) usando le librerie standard di Python.
Questo script accetta un singolo parametro per la velocità di regolarizzazione (*reg*) usata per adattare un modello di classificazione nel tentativo di aumentare l'*accuratezza* senza overfitting.
Queste variabili vengono quindi registrate come *velocità di regolarizzazione* e *accuratezza*, in modo che sia possibile identificare facilmente il modello con risultati ottimali.

```Python
# change regularization rate and you will likely get a different accuracy.
reg = 0.01
# load regularization rate from argument if present
if len(sys.argv) > 1:
    reg = float(sys.argv[1])

print("Regularization rate is {}".format(reg))

# log the regularization rate
run_logger.log("Regularization Rate", reg)

# train a logistic regression model on the training set
clf1 = LogisticRegression(C=1/reg).fit(X_train, Y_train)
print (clf1)

# evaluate the test set
accuracy = clf1.score(X_test, Y_test)
print ("Accuracy is {}".format(accuracy))

# log accuracy
run_logger.log("Accuracy", accuracy)
```

Eseguire la procedura seguente negli script per ottimizzare l'uso della **Cronologia di esecuzione**.

## <a name="launch-runs-from-project-dashboard"></a>Avvio eseguito dal dashboard del progetto
Tornare al **dashboard del progetto**, dove è possibile avviare un'**esecuzione registrata** selezionando lo script **iris_sklearn.py** e immettendo il parametro della **velocità di regolarizzazione** nella casella di modifica **Argomenti**.

![immissione dei parametri e avvio delle esecuzioni](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-05.png)

Poiché l'avvio delle esecuzioni registrate non blocca Azure Machine Learning Workbench, è possibile avviare più esecuzioni in parallelo.
Lo stato di ogni esecuzione registrata è visibile nel **riquadro Processi**, come mostrato.

![esecuzioni registrate nel riquadro Processi](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-06.png)

Ciò agevola l'impiego ottimale delle risorse senza dover eseguire in sequenza ogni processo.

## <a name="view-results-in-run-history"></a>Visualizzare i risultati nella cronologia di esecuzione
Lo stato di avanzamento e risultati delle esecuzioni registrate possono essere analizzati nella **Cronologia di esecuzione** di Azure Machine Learning.
**Cronologia di esecuzione** fornisce tre viste distinte:
- dashboard
- Dettagli
- Confronto

La vista **Dashboard** mostra i dati di tutte le esecuzioni di uno script specifico, in formato grafico e tabulare.
La vista **Dettagli** mostra tutti i dati generati da un'esecuzione specifica di uno script specifico, incluse le metriche registrate e i file di output (ad esempio i plot sottoposti a rendering). La vista **Confronto** mostra i risultati affiancati di due o tre esecuzioni e include anche metriche registrate e file di output.

Con otto esecuzioni registrate di **iris_sklearn.py**, i valori dei risultati del parametro **velocità di regolarizzazione** e **accuratezza** sono stati registrati per illustrare come usare le viste Cronologia di esecuzione.

### <a name="run-history-dashboard"></a>Dashboard Cronologia di esecuzione
I risultati delle otto esecuzioni sono visibili nel **dashboard Cronologia di esecuzione**.
Poiché **iris_sklearn.py** registra la *velocità di regolarizzazione* e l'*accuratezza*, per impostazione predefinita il **dashboard Cronologia di esecuzione** visualizza i grafici relativi a questi valori.

![dashboard cronologia di esecuzione](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-07.png)

È possibile personalizzare il **dashboard Cronologia di esecuzione** in modo che i valori registrati vengono visualizzati anche nella griglia.  Fare clic sull'icona di **personalizzazione** per visualizzare la finestra di dialogo **List View Customization** (Personalizzazione visualizzazione elenco), come illustrato.

![personalizzazione della griglia del dashboard cronologia di esecuzione](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-08.png)

Tutti i valori registrati durante le esecuzioni registrate sono disponibili per la visualizzazione. Selezionare i valori relativi alla **velocità di regolarizzazione** e all'**accuratezza** per aggiungerli alla griglia.

![valori registrati nella griglia personalizzata](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-09.png)

È facile individuare le esecuzioni interessanti passando il mouse sui grafici.  In questo caso, l'esecuzione 7 ha restituito un buon livello di accuratezza associato a una breve durata.

![osservazione di esecuzioni interessanti](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-10.png)

Fare clic su un punto associato all'esecuzione 7 in qualsiasi grafico oppure sul collegamento all'esecuzione 7 nella griglia per visualizzare i **dettagli della cronologia di esecuzione**.

### <a name="run-history-details"></a>Dettagli della cronologia di esecuzione
In questa vista vengono visualizzati tutti i risultati dell'esecuzione 7 e tutti gli elementi da questa generati.

![dettagli della cronologia di esecuzione](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-11.png)

La vista **Run History Details** (Dettagli cronologia di esecuzione) consente di **scaricare** tutti i file scritti nella cartella **./output**. Questi file sono supportati dall'archiviazione cloud di Azure Machine Learning Workbench per la Cronologia di esecuzione, ma l'argomento è oggetto di un altro articolo.

Infine, in **Dettagli della cronologia di esecuzione** è possibile ripristinare il progetto allo stato in cui era al momento dell'esecuzione.
Fare clic sul pulsante **Ripristina** per visualizzare una finestra di dialogo di conferma, come illustrato.

![conferma esecuzione ripristino](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-13.png)

Inviando la conferma, i file possono essere sovrascritti o rimossi, quindi usare questa funzionalità con attenzione.

### <a name="run-history-comparison"></a>Confronto della cronologia di esecuzione
Selezionare due o tre esecuzioni nel **Dashboard della cronologia di esecuzione** e fare clic su **Confronta** per visualizzare la vista **Run History Comparison** (Confronto cronologia di esecuzione).
Anche facendo clic su **Confronta** e selezionando un'esecuzione tra quelle presenti nella vista **Run History Details** (Dettagli cronologia di esecuzione) verrà aperta la vista **Run History Comparison** (Confronto cronologia di esecuzione).
In entrambi i casi la vista **Run History Comparison** (Confronto cronologia di esecuzione) consente di visualizzare i risultati registrati e gli elementi di due o tre esecuzioni affiancati.

![confronto della cronologia di esecuzione](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-12.png)

Questa visualizzazione è utile soprattutto per il confronto dei plot, ma in generale consente di confrontare qualsiasi proprietà delle esecuzioni.

### <a name="command-line-interface"></a>Interfaccia della riga di comando
Azure Machine Learning Workbench permette di accedere a Cronologia di esecuzione tramite la relativa **interfaccia della riga di comando**.
Per accedere all'**interfaccia della riga di comando**, fare clic sul menu **Apri prompt dei comandi** come illustrato.

![aprire un prompt dei comandi](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-14.png)

I comandi disponibili per Cronologia di esecuzione sono accessibili tramite `az ml history`; la guida in linea è disponibile con l'aggiunta del flag `-h`.
```
$ az ml history -h

Group
    az ml history: View run history of machine learning experiments.

Commands:
    compare : Compare two runs.
    download: Download all the artifacts from a run into the destination path.
    info    : Details about one run.
    last    : Get detail about most recent run.
    list    : List runs.
    promote : Promote Artifacts.
```
Questi comandi forniscono le stesse funzionalità e restituiscono gli stessi dati mostrati nelle **viste della cronologia di esecuzione**.
È ad esempio possibile visualizzare i risultati dell'ultima esecuzione come un oggetto JSON.
```
$ az ml history last
{
  "Accuracy": 0.6792452830188679,
  "Regularization Rate": 0.078125,
  "attachments": "control_log, control_log.txt, driver_log, driver_log.txt, pid.txt, dataprep/backgroundProcess.log, dataprep/backgroundProcess_Engine.log, dataprep/backgroundProcess_EngineHost.log, dataprep/backgroundProcess_ProjectProvider.log, dataprep/backgroundProcess_Sampling.log, dataprep/backgroundProcess_Telemetry.log, outputs/cm.png, outputs/model.pkl, outputs/sdk_debug.txt, outputs/roc.png",
  "created_utc": "2017-09-08T00:58:01.611105+00:00",
  "description": null,
  "duration": "0:00:04.892389",
  "end_time_utc": "2017-09-08T00:58:07.951403+00:00",
  "experiment_id": "ce92d0a9-3e2c-4d51-85de-93ef22249ce1",
  "heartbeat_enabled": true,
  "hidden": false,
  "name": null,
  "parent_run_id": null,
  "properties": {
    "CommitId": "e77a5f0df2af1a482bbe39b70bfbb16b62228cb3"
  },
  "run_id": "IrisDemo_1504832281116",
  "run_number": 8,
  "script_name": "iris_sklearn.py",
  "start_time_utc": "2017-09-08T00:58:03.059014+00:00",
  "status": "Completed",
  "target": "local",
  "user_id": "e9fafe06-b0e4-4154-8374-aae34f9977b2"
}
```
È anche possibile visualizzare l'elenco di tutte le esecuzioni in formato tabulare.
```
$ az ml history list -o table
  Accuracy    Regularization Rate  Duration        Run_id                  Script_name      Start_time_utc                    Status
----------  ---------------------  --------------  ----------------------  ---------------  --------------------------------  ---------
  0.679245               0.078125  0:00:04.892389  IrisDemo_1504832281116  iris_sklearn.py  2017-09-08T00:58:03.059014+00:00  Completed
  0.679245               0.15625   0:00:04.734956  IrisDemo_1504832255198  iris_sklearn.py  2017-09-08T00:57:38.507196+00:00  Completed
  0.660377               0.3125    0:00:04.913762  IrisDemo_1504832234904  iris_sklearn.py  2017-09-08T00:57:16.849881+00:00  Completed
  0.660377               0.625     0:00:06.107764  IrisDemo_1504832210767  iris_sklearn.py  2017-09-08T00:56:54.602813+00:00  Completed
  0.641509               1.25      0:00:04.742727  IrisDemo_1504832171373  iris_sklearn.py  2017-09-08T00:56:13.879280+00:00  Completed
  0.660377               2.5       0:00:04.915401  IrisDemo_1504832148526  iris_sklearn.py  2017-09-08T00:55:52.937083+00:00  Completed
  0.641509               5         0:00:04.730627  IrisDemo_1504832127172  iris_sklearn.py  2017-09-08T00:55:29.612382+00:00  Completed
  0.641509              10         0:00:06.059082  IrisDemo_1504832109906  iris_sklearn.py  2017-09-08T00:55:14.739806+00:00  Completed

```
L'**interfaccia della riga di comando** rappresenta un metodo alternativo per sfruttare tutte potenzialità di Azure Machine Learning Workbench.

## <a name="next-steps"></a>Passaggi successivi
Queste funzionalità sono disponibili per semplificare gli esperimenti di data science.
Ci auguriamo che possano essere utili. Tutti i commenti e suggerimenti saranno apprezzati.
Si tratta solo dell'implementazione iniziale e sono già previsti molti miglioramenti,
che verranno costantemente aggiunti ad Azure Machine Learning Workbench. 
