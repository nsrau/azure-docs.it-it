---
title: API di rilevamento delle anomalie di Machine Learning | Microsoft Docs
description: "API di rilevamento delle anomalie è un esempio compilato con Microsoft Azure Machine Learning che consente di rilevare anomalie nei dati della serie temporale con i valori numerici disposti in modo uniforme nel tempo."
services: machine-learning
documentationcenter: 
author: alokkirpal
manager: jhubbard
editor: cgronlun
ms.assetid: 52fafe1f-e93d-47df-a8ac-9a9a53b60824
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/05/2017
ms.author: alok;rotimpe
ms.translationtype: Human Translation
ms.sourcegitcommit: 80be19618bd02895d953f80e5236d1a69d0811af
ms.openlocfilehash: 5ddc02905fbb7f9f7c5deb9b970101788d824642
ms.contentlocale: it-it
ms.lasthandoff: 06/07/2017

---

# <a name="machine-learning-anomaly-detection-api"></a>API di rilevamento delle anomalie di Machine Learning
## <a name="overview"></a>Panoramica
[API di rilevamento delle anomalie](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2) è un esempio compilato con Azure Machine Learning che consente di rilevare anomalie nei dati della serie temporale con i valori numerici disposti in modo uniforme nel tempo.

Questa API può rilevare i seguenti tipi di modelli di anomalie nei dati delle serie temporali:

* **Tendenze positive e negative**: ad esempio, quando si monitora l'utilizzo della memoria di elaborazione, una tendenza verso l'alto può risultare interessante perché può indicare una perdita di memoria.
* **Modifiche dell'intervallo dinamico di valori**: ad esempio, quando si monitorano le eccezioni generate da un servizio cloud, le eventuali modifiche dell'intervallo dinamico di valori possono indicare un'instabilità dell'integrità del servizio.
* **Picchi e flessioni**: ad esempio, quando si monitora il numero di errori di accesso a un servizio o il numero di transazioni completate in un sito di e-commerce, i picchi o le flessioni possono indicare un comportamento anomalo.

Queste funzionalità di rilevamento di Machine Learning tengono traccia delle modifiche dei valori nel tempo e segnalano quelle in corso sotto forma di punteggi delle anomalie. Non richiedono la sintonizzazione della soglia ad hoc e i punteggi possono essere utilizzati per controllare la frequenza falsa positiva. L'API di rilevamento anomalie è utile in diversi scenari, come il monitoraggio del servizio che tiene traccia degli indicatori KPI nel tempo, il monitoraggio dell'utilizzo mediante metriche, come il numero di ricerche e i numeri di clic, il monitoraggio delle prestazioni nel tempo usando contatori come memoria, CPU, letture di file e così via.

L'offerta per il rilevamento anomalie include strumenti utili per iniziare.

* L' [applicazione Web](http://anomalydetection-aml.azurewebsites.net/) consente di valutare e visualizzare i risultati delle API di rilevamento anomalie sui dati.

> [!NOTE]
> Provare la **soluzione IT Anomaly Insights** supportata da [questa API](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2)
> 
> Per distribuire questa soluzione end-to-end nella sottoscrizione di Azure <a href="https://gallery.cortanaintelligence.com/Solution/Anomaly-Detection-Pre-Configured-Solution-1" target="_blank">**Iniziare da qui >**</a>
> 
>

## <a name="api-deployment"></a>Distribuzione API
Per utilizzare l'API, è necessario distribuirlo alla sottoscrizione di Azure dove verrà ospitato come servizio web di Machine Learning di Azure.  È possibile effettuare questa operazione in [Cortana Intelligence Gallery](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2).  Con questa operazione due servizi Web di AzureML (e le relative risorse) verranno distribuite alla sottoscrizione di Azure, uno per il rilevamento delle anomalie con rilevamento della stagionalità e l'altro senza rilevamento della stagionalità.  Dopo aver completato la distribuzione, sarà possibile gestire le API dalla pagina dei [servizi Web di AzureML](https://services.azureml.net/webservices/).  In questa pagina è possibile trovare le posizioni endpoint, le chiavi API, nonché il codice di esempio per la chiamata all'API.  Le istruzioni più dettagliate sono disponibili [qui](https://docs.microsoft.com/en-us/azure/machine-learning/machine-learning-manage-new-webservice).

## <a name="scaling-the-api"></a>Scalabilità dell'API
Per impostazione predefinita, la distribuzione disporrà di un piano di fatturazione di sviluppo/test gratuito che include 1.000 operazioni e 2 ore di calcolo al mese.  È possibile passare a un altro piano in base alle proprie esigenze.  I dettagli sui prezzi dei vari piani sono disponibili [qui](https://azure.microsoft.com/en-us/pricing/details/machine-learning/) in "Prezzi API Web di produzione".

## <a name="managing-aml-plans"></a>Gestione dei piani AML 
È possibile gestire il piano di fatturazione [qui](https://services.azureml.net/plans/).  Il nome del piano si basa sul nome del gruppo di risorse scelto durante la distribuzione dell'API, oltre a una stringa univoca per la sottoscrizione.  Le istruzioni su come aggiornare il piano sono disponibili [qui](https://docs.microsoft.com/en-us/azure/machine-learning/machine-learning-manage-new-webservice) nella sezione "Gestione dei piani di fatturazione".

## <a name="api-definition"></a>Definizione dell'API
Il servizio Web offre un’API basata su REST in HTTPS che può essere usata in modi diversi, ad esempio come applicazione Web o mobile, R, Python, Excel, ecc.  I dati delle serie temporali vengono inviati al servizio tramite una chiamata API REST che esegue una combinazione dei tre tipi di anomalie descritte di seguito.

## <a name="calling-the-api"></a>Chiamata all'API
Per chiamare l'API, è necessario conoscere la posizione endpoint e la chiave API.  Entrambi gli elementi, insieme al codice di esempio per chiamare l'API, sono disponibili nella pagina dei [servizi Web di AzureML](https://services.azureml.net/webservices/).  Passare all'API desiderato e quindi fare clic sulla scheda "Consumo" per individuarli.  Si noti che è possibile chiamare l'API come API Swagger (vale a dire con il parametro URL `format=swagger`) o come un API non Swagger (ovvero senza il parametro URL `format`).  Il codice di esempio usa il formato Swagger.  Di seguito viene riportato un esempio di richiesta e risposta in formato non Swagger.  Questi esempi sono endpoint di stagionalità.  L'endpoint di non stagionalità è simile.

### <a name="sample-request-body"></a>Corpo della richiesta di esempio
La richiesta contiene due oggetti: `Inputs` e `GlobalParameters`.  Nella richiesta di esempio riportata di seguito, alcuni parametri vengono inviati in modo esplicito, mentre per altri questo non avviene (scorrere verso il basso per un elenco completo dei parametri per ogni endpoint).  I parametri che non vengono inviati in modo esplicito nella richiesta useranno i valori predefiniti indicati di seguito.

    {
                "Inputs": {
                        "input1": {
                                "ColumnNames": ["Time", "Data"],
                                "Values": [
                                        ["5/30/2010 18:07:00", "1"],
                                        ["5/30/2010 18:08:00", "1.4"],
                                        ["5/30/2010 18:09:00", "1.1"]
                                ]
                        }
                },
        "GlobalParameters": {
            "tspikedetector.sensitivity": "3",
            "zspikedetector.sensitivity": "3",
            "bileveldetector.sensitivity": "3.25",
            "detectors.spikesdips": "Both"
        }
    }

### <a name="sample-response"></a>Risposta di esempio
Si noti che, per visualizzare il campo `ColumnNames`, è necessario includere `details=true` come parametro URL nella richiesta.  Vedere le tabelle di seguito per il significato di ognuno di questi campi.

    {
        "Results": {
            "output1": {
                "type": "table",
                "value": {
                    "Values": [
                        ["5/30/2010 6:07:00 PM", "1", "1", "0", "0", "-0.687952590518378", "0", "-0.687952590518378", "0", "-0.687952590518378", "0"],
                        ["5/30/2010 6:08:00 PM", "1.4", "1.4", "0", "0", "-1.07030497733224", "0", "-0.884548154298423", "0", "-1.07030497733224", "0"],
                        ["5/30/2010 6:09:00 PM", "1.1", "1.1", "0", "0", "-1.30229513613974", "0", "-1.173800281031", "0", "-1.30229513613974", "0"]
                    ],
                    "ColumnNames": ["Time", "OriginalData", "ProcessedData", "TSpike", "ZSpike", "BiLevelChangeScore", "BiLevelChangeAlert", "PosTrendScore", "PosTrendAlert", "NegTrendScore", "NegTrendAlert"],
                    "ColumnTypes": ["DateTime", "Double", "Double", "Double", "Double", "Double", "Int32", "Double", "Int32", "Double", "Int32"]
                }
            }
        }
    }


## <a name="score-api"></a>API Score
Questa API viene usata per eseguire il rilevamento anomalie nei dati delle serie temporali non stagionali. L'API esegue una serie di funzionalità di rilevamento anomalie sui dati e restituisce i punteggi delle anomalie. La figura seguente illustra un esempio di anomalie che l'API Score può rilevare. Questa serie temporale presenta due modifiche di livello distinte e 3 picchi. I punti rossi mostrano il momento in cui viene rilevata la modifica di livello, mentre i punti neri indicano i picchi rilevati.
![API Score][1]

### <a name="detectors"></a>Funzionalità di rilevamento
L'API di rilevamento anomalie supporta funzionalità di rilevamento in 3 categorie generali. Informazioni dettagliate su specifici parametri di input e output per ogni funzionalità di rilevamento sono disponibili nella tabella seguente.

| Categoria di rilevamento | Funzionalità di rilevamento | Descrizione | Parametri di input | Output |
| --- | --- | --- | --- | --- |
| Rilevamento picchi |Rilevamento picchi TSpike |Rileva picchi e flessioni in base alla distanza dei valori dal primo e dal terzi quartile |*tspikedetector.sensitivity:* accetta il valore intero nell'intervallo 1-10, predefinito: 3; i valori più elevati accetteranno valori più estremi, riducendo però la sensibilità |TSpike: valori binari, '1' se viene rilevato un picco o una flessione. In caso contrario '0'. |
| Rilevamento picchi | Rilevamento picchi ZSpike |Rileva picchi e flessioni in base alla distanza dei punti dati dalla loro media |*zspikedetector.sensitivity:* accetta il valore intero nell'intervallo 1-10, predefinito: 3; i valori più elevati accetteranno valori più estremi, riducendo la sensibilità |ZSpike: valori binari, '1' se viene rilevato un picco o una flessione. In caso contrario '0'. | |
| Rilevamento di tendenza lenta |Rilevamento di tendenza lenta |Rileva la tendenza positiva lenta in base alla sensibilità impostata. |*trenddetector.sensitivity:* soglia relativa al punteggio di rilevamento. Impostazione predefinita: 3,25. I valori compresi tra 3,25 e 5 rappresentano un intervallo di selezione ragionevole. Più alto è il valore, minore sarà la sensibilità |tscore: numero mobile che rappresenta il punteggio dell'anomalia nella tendenza. |
| Rilevamento della modifica di livello | Rilevamento bidirezionale della modifica di livello |Rileva la modifica di livello verso l'alto e verso il basso in base alla sensibilità impostata. |*bileveldetector.sensitivity:* soglia relativa al punteggio di rilevamento. Impostazione predefinita: 3,25. I valori compresi tra 3,25 e 5 rappresentano un intervallo di selezione ragionevole. Più alto è il valore, minore sarà la sensibilità |rpscore: numero mobile che rappresenta il punteggio dell'anomalia nella modifica di livello verso l'alto e verso il basso. | |

### <a name="parameters"></a>parameters
Informazioni più dettagliate su questi parametri di input sono elencate nella tabella seguente:

| Parametri di input | Descrizione | Impostazione predefinita | Tipo | Intervallo valido | Intervallo consigliato |
| --- | --- | --- | --- | --- | --- |
| detectors.historyWindow |Cronologia, in numero di punti dati, usata per il calcolo del punteggio delle anomalie |500 |integer |10-2000 |In base alle serie temporali. |
| detectors.spikesdips | Se rilevare solo i picchi, le flessioni o entrambi |Entrambi |enumerato |Entrambi, picchi e flessioni |Entrambi |
| bileveldetector.sensitivity |Sensibilità per il rilevamento delle modifiche di livello bidirezionali. |3.25 |double |Nessuna |3.25-5 (Meno valori, maggiore sensibilità) |
| trenddetector.sensitivity |Sensibilità per il rilevamento di tendenza positiva. |3.25 |double |Nessuna |3.25-5 (Meno valori, maggiore sensibilità) |
| tspikedetector.sensitivity |Sensibilità per il rilevamento di picchi TSpike. |3 |integer |1-10 |3-5 (Minori sono i valori, maggiore è la sensibilità) |
| zspikedetector.sensitivity |Sensibilità per il rilevamento di picchi ZSpike |3 |integer |1-10 |3-5 (Minori sono i valori, maggiore è la sensibilità) |
| postprocess.tailRows |Numero di punti dati più recenti da mantenere nei risultati di output. |0 |integer |0 (mantiene tutti i punti dati) o specificare il numero di punti da mantenere nei risultati. |N/D |

### <a name="output"></a>Output
L'API esegue tutte le funzionalità di rilevamento sui dati delle serie temporali e restituisce i punteggi delle anomalie e gli indicatori di picco binari per ogni punto temporizzato. La tabella seguente include l'elenco di output dell'API. 

| Output | Descrizione |
| --- | --- |
| Time |Timestamp di dati non elaborati o dati aggregati (e/o) attribuiti se viene applicata l'aggregazione (e/o) l'attribuzione di dati mancanti. |
| Dati |Valori di dati non elaborati o dati aggregati (e/o) attribuiti se viene applicata l'aggregazione (e/o) l'attribuzione di dati mancanti. |
| Tspike |Indicatore binario per indicare se viene rilevato un picco dalla funzionalità di rilevamento di TSpike. |
| Zspike |Indicatore binario per indicare se viene rilevato un picco dalla funzionalità di rilevamento di ZSpike. |
| rpscore |Numero mobile che rappresenta il punteggio dell'anomalia nella modifica di livello bidirezionale. |
| rpalert |Valore 1/0 che indica la presenza di un'anomalia nella modifica di livello bidirezionale, in base alla sensibilità di input. |
| tscore |Numero mobile che rappresenta il punteggio dell'anomalia nella tendenza positiva. |
| talert |Valore 1/0 che indica la presenza di un'anomalia nella tendenza positiva, in base alla sensibilità di input. |

## <a name="scorewithseasonality-api"></a>API ScoreWithSeasonality
L'API ScoreWithSeasonality viene usata per eseguire il rilevamento di anomalie in serie temporali che includono modelli stagionali. Questa API è utile per rilevare le deviazioni nei modelli stagionali.  
La figura seguente illustra un esempio di anomalie rilevate in una serie temporale stagionale. La serie temporale presenta un picco (primo punto nero), due flessioni (secondo punto nero e uno alla fine) e una modifica di livello (punto rosso). Si noti che sia la flessione al centro della serie temporale sia la modifica di livello sono rilevabili solo dopo la rimozione dei componenti stagionali dalla serie.
![Stagionalità API][2]

### <a name="detectors"></a>Funzionalità di rilevamento
I rilevatori nell'endpoint stagionalità sono simili a quelli nell'endpoint non stagionalità, ma con i nomi dei parametri leggermente diversi (elencati di seguito).

### <a name="parameters"></a>parameters

Informazioni più dettagliate su questi parametri di input sono elencate nella tabella seguente:

| Parametri di input | Descrizione | Impostazione predefinita | Tipo | Intervallo valido | Intervallo consigliato |
| --- | --- | --- | --- | --- | --- |
| preprocess.aggregationInterval |Intervallo in secondi per l'aggregazione di serie temporali di input. |0, non viene eseguita alcuna aggregazione. |integer |0: ignora l'aggregazione. In caso contrario > 0. |Da 5 minuti a 1 giorno, in base alle serie temporali. |
| preprocess.aggregationFunc |Funzione usata per aggregare i dati nel parametro AggregationInterval specificato. |mean |enumerato |mean, sum, length |N/D |
| preprocess.replaceMissing |Valori usati per l'attribuzione dei dati mancanti. |lkv (ultimo valore noto) |enumerato |zero, lkv, mean |N/D |
| detectors.historyWindow |Cronologia, in numero di punti dati, usata per il calcolo del punteggio delle anomalie |500 |integer |10-2000 |In base alle serie temporali. |
| detectors.spikesdips | Se rilevare solo i picchi, le flessioni o entrambi |Entrambi |enumerato |Entrambi, picchi e flessioni |Entrambi |
| bileveldetector.sensitivity |Sensibilità per il rilevamento delle modifiche di livello bidirezionali. |3.25 |double |Nessuna |3.25-5 (Meno valori, maggiore sensibilità) |
| postrenddetector.sensitivity |Sensibilità per il rilevamento di tendenza positiva. |3.25 |double |Nessuna |3.25-5 (Meno valori, maggiore sensibilità) |
| negtrenddetector.sensitivity |Sensibilità per il rilevamento di tendenza negativa. |3.25 |double |Nessuna |3.25-5 (Meno valori, maggiore sensibilità) |
| tspikedetector.sensitivity |Sensibilità per il rilevamento di picchi TSpike. |3 |integer |1-10 |3-5 (Minori sono i valori, maggiore è la sensibilità) |
| zspikedetector.sensitivity |Sensibilità per il rilevamento di picchi ZSpike |3 |integer |1-10 |3-5 (Minori sono i valori, maggiore è la sensibilità) |
| seasonality.enable |Se è necessario eseguire analisi di stagionalità. |true |boolean |true, false |In base alle serie temporali. |
| seasonality.numSeasonality |Numero massimo di cicli periodici da rilevare. |1 |integer |1, 2 |Da 1 a 2 |
| seasonality.transform |Se i componenti stagionali (e) di tendenza devono essere rimossi prima di applicare il rilevamento delle anomalie. |deseason |enumerato |none, deseason, deseasontrend |N/D |
| postprocess.tailRows |Numero di punti dati più recenti da mantenere nei risultati di output. |0 |integer |0 (mantiene tutti i punti dati) o specificare il numero di punti da mantenere nei risultati. |N/D |

### <a name="output"></a>Output
L'API esegue tutte le funzionalità di rilevamento sui dati delle serie temporali e restituisce i punteggi delle anomalie e gli indicatori di picco binari per ogni punto temporizzato. La tabella seguente include l'elenco di output dell'API. 

| Output | Descrizione |
| --- | --- |
| Time |Timestamp di dati non elaborati o dati aggregati (e/o) attribuiti se viene applicata l'aggregazione (e/o) l'attribuzione di dati mancanti. |
| OriginalData |Valori di dati non elaborati o dati aggregati (e/o) attribuiti se viene applicata l'aggregazione (e/o) l'attribuzione di dati mancanti. |
| ProcessedData |Uno dei seguenti:  <ul><li>Serie temporali regolate in base alla stagionalità in caso di rilevamento di una stagionalità significativa e di selezione dell'opzione deseason</li><li>Serie temporali regolate in base alla stagionalità e senza tendenza, se è stata rilevata una stagionalità significativa ed è selezionata l'opzione deseasontrend</li><li>In caso contrario, è uguale a OriginalData</li> |
| Tspike |Indicatore binario per indicare se viene rilevato un picco dalla funzionalità di rilevamento di TSpike. |
| Zspike |Indicatore binario per indicare se viene rilevato un picco dalla funzionalità di rilevamento di ZSpike. |
| BiLevelChangeScore |Numero mobile che rappresenta il punteggio dell'anomalia nella modifica di livello |
| BiLevelChangeAlert |Valore 1/0 che indica la presenza di un'anomalia nella modifica di livello in base alla sensibilità di input |
| PosTrendScore |Numero mobile che rappresenta il punteggio dell'anomalia nella tendenza positiva. |
| PosTrendAlert |Valore 1/0 che indica la presenza di un'anomalia nella tendenza positiva, in base alla sensibilità di input. |
| NegTrendScore |Numero mobile che rappresenta il punteggio dell'anomalia nella tendenza negativa |
| NegTrendAlert |Valore 1/0 che indica la presenza di un'anomalia nella tendenza negativa, in base alla sensibilità di input |

[1]: ./media/machine-learning-apps-anomaly-detection-api/anomaly-detection-score.png
[2]: ./media/machine-learning-apps-anomaly-detection-api/anomaly-detection-seasonal.png


