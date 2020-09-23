---
title: Come mantenere la privacy dei dati usando i pacchetti WhiteNoise (anteprima)
titleSuffix: Azure Machine Learning
description: Informazioni su come applicare le procedure consigliate per la privacy differenziale ai modelli Azure Machine Learning usando i pacchetti WhiteNoise.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: slbird
author: slbird
ms.reviewer: luquinta
ms.date: 07/09/2020
ms.openlocfilehash: 355d96fe5a617effab89fbd038f7f1785215f88f
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90897684"
---
# <a name="use-differential-privacy-in-azure-machine-learning-preview"></a>USA privacy differenziale in Azure Machine Learning (anteprima)



Informazioni su come applicare le procedure consigliate per la privacy differenziale ai modelli Azure Machine Learning usando i pacchetti Python WhiteNoise.

La privacy differenziale è la definizione per eccellenza di privacy. I sistemi che rispettano questa definizione di privacy forniscono garanzie sicure contro un'ampia gamma di attacchi di ricostruzione e reidentificazione dei dati, inclusi gli attacchi da avversari in possesso di informazioni ausiliarie. Altre informazioni sul [funzionamento della privacy differenziale](./concept-differential-privacy.md).

> [!NOTE]
> Si noti che il Toolkit viene rinominato e verrà introdotto il nuovo nome nelle prossime settimane. 

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).
- [Python 3](https://www.python.org/downloads/)

## <a name="install-whitenoise-packages"></a>Installare i pacchetti WhiteNoise

### <a name="standalone-installation"></a>Installazione autonoma

Le librerie sono progettate per funzionare da cluster Spark distribuiti e possono essere installate esattamente come qualsiasi altro pacchetto.

Le istruzioni seguenti presuppongono che sia stato eseguito il mapping dei comandi `python` e `pip` su `python3` e `pip3`.

Usare pip per installare i [pacchetti Python WhiteNoise](https://pypi.org/project/opendp-whitenoise/).

`pip install opendp-whitenoise`

Per verificare che i pacchetti siano installati, avviare un prompt di Python e digitare:

```python
import opendp.whitenoise.core
import opendp.whitenoise.sql
```

Se le importazioni hanno esito positivo, le librerie vengono installate e saranno pronte per l'uso.

### <a name="docker-image"></a>Immagine Docker

È anche possibile usare i pacchetti WhiteNoise con Docker.

Eseguire il pull dell'immagine `opendp/whitenoise` per usare le librerie all'interno di un contenitore Docker che include Spark, Jupyter e il codice di esempio.

```sh
docker pull opendp/whitenoise:privacy
```

Dopo aver eseguito il pull dell'immagine, avviare il server Jupyter:

```sh
docker run --rm -p 8989:8989 --name whitenoise-run opendp/whitenoise:privacy
```

Verrà avviato un server Jupyter sulla porta `8989` su `localhost`, con la password `pass@word99`. Supponendo di aver usato la riga di comando riportata in precedenza per avviare il contenitore con il nome `whitenoise-privacy`, è possibile aprire un terminale bash nel server Jupyter eseguendo:

```sh
docker exec -it whitenoise-run bash
```

L'istanza di Docker cancella tutti gli stati alla chiusura, quindi si perderanno tutti i notebook creati nell'istanza in esecuzione. Per risolvere questo problema, è possibile eseguire il montaggio di associazione di una cartella locale nel contenitore all'avvio:

```sh
docker run --rm -p 8989:8989 --name whitenoise-run --mount type=bind,source=/Users/your_name/my-notebooks,target=/home/privacy/my-notebooks opendp/whitenoise:privacy
```

Tutti i notebook creati nella cartella *my-notebooks* verranno archiviati nel file system locale.

## <a name="perform-data-analysis"></a>Eseguire l'analisi dei dati

Per preparare una versione privata in modo differenziale, è necessario scegliere un'origine dati, una statistica e alcuni parametri sulla privacy, che indichino il livello di protezione della privacy.  

Questo esempio fa riferimento ai California Public Use Microdata (PUMS), che rappresentano i record resi anonimi dei dati demografici dei cittadini:

```python
import os
import sys
import numpy as np
import opendp.whitenoise.core as wn

data_path = os.path.join('.', 'data', 'PUMS_california_demographics_1000', 'data.csv')
var_names = ["age", "sex", "educ", "race", "income", "married", "pid"]
```

In questo esempio viene calcolata la media e la varianza dell'età.  Viene usato un totale `epsilon` di 1,0 (epsilon è il nostro parametro sulla privacy, distribuendo il nostro budget per la privacy tra le due quantità che si desidera calcolare. Altre informazioni sulle [metriche della privacy](concept-differential-privacy.md#differential-privacy-metrics).

```python
with wn.Analysis() as analysis:
    # load data
    data = wn.Dataset(path = data_path, column_names = var_names)

    # get mean of age
    age_mean = wn.dp_mean(data = wn.cast(data['age'], type="FLOAT"),
                          privacy_usage = {'epsilon': .65},
                          data_lower = 0.,
                          data_upper = 100.,
                          data_n = 1000
                         )
    # get variance of age
    age_var = wn.dp_variance(data = wn.cast(data['age'], type="FLOAT"),
                             privacy_usage = {'epsilon': .35},
                             data_lower = 0.,
                             data_upper = 100.,
                             data_n = 1000
                            )
analysis.release()

print("DP mean of age: {0}".format(age_mean.value))
print("DP variance of age: {0}".format(age_var.value))
print("Privacy usage: {0}".format(analysis.privacy_usage))
```

I risultati dovrebbero essere simili ai seguenti:

```text
DP mean of age: 44.55598845931517
DP variance of age: 231.79044646429134
Privacy usage: approximate {
  epsilon: 1.0
}
```

Per questo esempio è necessario tenere presenti alcuni aspetti importanti.  In primo luogo, l'oggetto `Analysis` rappresenta un grafico di elaborazione dati.  In questo esempio, la media e la varianza vengono calcolate dallo stesso nodo di origine.  Tuttavia, è possibile includere espressioni più complesse che combinano input con output in modo arbitrario.

Il grafico di analisi include i metadati `data_upper` e `data_lower`, specificando i limiti inferiore e superiore per le età.  Questi valori vengono usati per calibrare con precisione il rumore per garantire la privacy differenziale.  Questi valori vengono usati anche in alcuni casi di gestione degli outlier o dei valori mancanti.

Infine, il grafico di analisi tiene traccia del budget totale dedicato alla privacy.

È possibile usare la libreria per comporre grafici di analisi più complessi, con diversi meccanismi, statistiche e funzioni di utilità:

| Statistiche    | Meccanismi | Servizi pubblici  |
| ------------- |------------|------------|
| Conteggio         | Gaussiano   | Cast       |
| Istogramma     | Geometrica  | Fissaggio   |
| Media          | Laplace    | Digitalizzazione   |
| Quantili     |            | Filtro     |
| SUM           |            | Imputazione |
| Varianza/covarianza |      | Trasformare  |

Per ulteriori informazioni, vedere il [notebook di analisi dei dati](https://github.com/opendifferentialprivacy/whitenoise-samples/blob/master/analysis/basic_data_analysis.ipynb) .

## <a name="approximate-utility-of-differentially-private-releases"></a>Utilità approssimativa di versioni private differenziali

Poiché la privacy differenziale funziona tramite la calibratura del rumore, l'utilità delle versioni può variare a seconda del rischio per la privacy.  In genere, il rumore necessario per proteggere ogni individuo diventa irrilevante man mano che le dimensioni del campione aumentano, ma sovraccaricano il risultato per le versioni destinate a un singolo utente.  Gli analisti possono esaminare le informazioni di accuratezza per una versione per determinare quanto è utile la versione:

```python
with wn.Analysis() as analysis:
    # load data
    data = wn.Dataset(path = data_path, column_names = var_names)

    # get mean of age
    age_mean = wn.dp_mean(data = wn.cast(data['age'], type="FLOAT"),
                          privacy_usage = {'epsilon': .65},
                          data_lower = 0.,
                          data_upper = 100.,
                          data_n = 1000
                         )
analysis.release()

print("Age accuracy is: {0}".format(age_mean.get_accuracy(0.05)))
```

Il risultato di tale operazione dovrebbe essere simile al seguente:

```text
Age accuracy is: 0.2995732273553991
```

In questo esempio viene calcolata la media come descritto in precedenza e viene usata la funzione `get_accuracy` per richiedere l'accuratezza di `alpha` di 0,05. Un valore `alpha` di 0,05 rappresenta un intervallo del 95%, in quel valore rilasciato rientrerà entro i limiti di accuratezza restituiti circa il 95% del tempo.  In questo esempio, l'accuratezza segnalata è 0,3, il che significa che il valore rilasciato sarà entro un intervallo di larghezza pari a 0,6, circa il 95% del tempo.  Non è corretto considerare questo valore come una barra di errore, poiché il valore rilasciato non rientra nell'intervallo di accuratezza indicato alla velocità specificata da `alpha` e i valori non compresi nell'intervallo possono essere esterni in entrambe le direzioni.

Gli analisti possono eseguire query `get_accuracy` per valori diversi di `alpha` per ottenere un intervallo di confidenza più stretto o più ampio, senza incorrere in costi di privacy aggiuntivi.

## <a name="generate-a-histogram"></a>Generare un istogramma

La funzione `dp_histogram` integrata crea istogrammi privati differenziali su uno dei tipi di dati seguenti:

- Una variabile continua, in cui il set di numeri deve essere suddiviso in bin
- Una variabile booleana o dicotomica che può assumere solo due valori
- Una variabile categorica, in cui sono presenti categorie distinte enumerate come stringhe

Di seguito è riportato un esempio di `Analysis` che specifica i bin per un istogramma a variabile continua:

```python
income_edges = list(range(0, 100000, 10000))

with wn.Analysis() as analysis:
    data = wn.Dataset(path = data_path, column_names = var_names)

    income_histogram = wn.dp_histogram(
            wn.cast(data['income'], type='int', lower=0, upper=100),
            edges = income_edges,
            upper = 1000,
            null_value = 150,
            privacy_usage = {'epsilon': 0.5}
        )
```

Poiché gli individui sono suddivisi in modo disgiunto tra i bin degli istogrammi, il costo della privacy viene sostenuto solo una volta per istogramma, anche se l'istogramma include molti bin.

Per altre informazioni sugli istogrammi, vedere il [notebook degli istogrammi](https://github.com/opendifferentialprivacy/whitenoise-samples/blob/master/analysis/histograms.ipynb).

## <a name="generate-a-covariance-matrix"></a>Genera una matrice di covarianza

WhiteNoise offre tre diverse funzionalità con la relativa funzione `dp_covariance`:

- Covarianza tra due vettori
- Matrice di covarianza di una matrice
- Matrice di covarianza incrociata di una coppia di matrici

Di seguito è riportato un esempio di calcolo di una covarianza scalare:

```python
with wn.Analysis() as analysis:
    wn_data = wn.Dataset(path = data_path, column_names = var_names)

    age_income_cov_scalar = wn.dp_covariance(
      left = wn.cast(wn_data['age'], 
      type = "FLOAT"), 
      right = wn.cast(wn_data['income'], 
      type = "FLOAT"), 
      privacy_usage = {'epsilon': 1.0},
      left_lower = 0., 
      left_upper = 100., 
      left_n = 1000, 
      right_lower = 0., 
      right_upper = 500_000.,
      right_n = 1000)
```

Per altre informazioni, vedere il [notebook della covarianza](
https://github.com/opendifferentialprivacy/whitenoise-samples/blob/master/analysis/covariance.ipynb)

## <a name="next-steps"></a>Passaggi successivi

- Esplorare i [notebook di esempio di WhiteNoise](https://github.com/opendifferentialprivacy/whitenoise-samples/tree/master/analysis).