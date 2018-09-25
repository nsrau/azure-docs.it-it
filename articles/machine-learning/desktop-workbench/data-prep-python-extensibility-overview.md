---
title: Usare l'estensibilità di Python per le preparazioni dati per Azure Machine Learning | Microsoft Docs
description: Questo documento offre una panoramica e alcuni esempi dettagliati di come usare il codice di Python per estendere la funzionalità di preparazione dati
services: machine-learning
author: euangMS
ms.author: euang
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 05/09/2018
ROBOTS: NOINDEX
ms.openlocfilehash: eceeeb30331031c51e5208e301441d17096b4a00
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46972981"
---
# <a name="data-preparations-python-extensions"></a>Estensioni della preparazione dati in Python

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


Al fine di colmare i vuoti funzionali tra le funzioni incorporate, la preparazione dati di Azure Machine Learning include più livelli di estensibilità. In questo documento viene descritta l'estensibilità tramite lo script di Python. 

## <a name="custom-code-steps"></a>Procedura per il codice personalizzato 
La preparazione dati prevede le operazioni personalizzate seguenti tramite le quali gli utenti possono scrivere il codice:

* Aggiungere una colonna
* Filtro avanzato
* Trasformare il flusso di dati
* Trasformare la partizione

## <a name="code-block-types"></a>Tipi di blocchi di codice 
Per ognuna di queste operazioni, sono supportati due tipi di blocchi di codice. In primo luogo, viene supportata un'espressione di Python essenziale che viene eseguita così com'è. In secondo luogo, viene supportato un modulo di Python che richiama una particolare funzione con una firma nota nel codice indicato.

Ad esempio, è possibile aggiungere una nuova colonna che calcola il log di un'altra colonna nei due modi seguenti:

Expression 

```python    
    math.log(row["Score"])
```

Modulo 
    
```python
def newvalue(row): 
        return math.log(row["Score"])
```


La trasformazione per aggiungere una colonna in modalità Modulo prevede di trovare una funzione denominata `newvalue` che accetta una variabile di riga e restituisce il valore per la colonna. Questo modulo può includere una quantità di codice Python con altre funzioni, importazioni e così via.

I dettagli di ogni punto di estensione sono descritti nelle sezioni seguenti. 

## <a name="imports"></a>Importazioni 
Se si usa un blocco di tipo espressione, è comunque possibile aggiungere le istruzioni **import** al codice. Queste istruzioni devono essere raggruppate nelle prime righe del codice.

Versione corretta 

```python
import math 
import numpy 
math.log(row["Score"])
```
 

Tipi di errore  

```python
import math  
math.log(row["Score"])  
import numpy
```
 
 
Se si usa il blocco di tipo modulo, è possibile seguire tutte le regole normali di Python per l'uso dell'istruzione **import**. 

## <a name="default-imports"></a>Istruzioni import predefinite
Le istruzioni import seguenti sono sempre presenti e possono essere usate nel codice. Non è necessario reimportarle. 

```python
import math  
import numbers  
import datetime  
import re  
import pandas as pd  
import numpy as np  
import scipy as sp
```
  

## <a name="install-new-packages"></a>Installare nuovi pacchetti
Per usare un pacchetto non installato per impostazione predefinita, è prima necessario installarlo negli ambienti usati per la preparazione dati. Questa installazione deve essere eseguita sia sul computer locale che su tutte le destinazioni di calcolo su cui si desidera avviare l'esecuzione.

Per installare i pacchetti in una destinazione di calcolo, è necessario modificare il file conda_dependencies.yml che si trova nella cartella aml_config nella radice del progetto.

### <a name="windows"></a>Windows 
Per trovare il percorso in Windows, individuare l'installazione specifica dell'app di Python e la relativa directory degli script. Il percorso predefinito è:  

`C:\Users\<user>\AppData\Local\AmlWorkbench\Python\Scripts` 

Quindi, eseguire uno dei comandi seguenti: 

`conda install <libraryname>` 

oppure 

`pip install <libraryname> `

### <a name="mac"></a>Mac 
Per trovare il percorso in Mac, individuare l'installazione specifica dell'app di Python e la relativa directory degli script. Il percorso predefinito è: 

`/Users/<user>/Library/Caches/AmlWorkbench/Python/bin` 

Quindi, eseguire uno dei comandi seguenti: 

`./conda install <libraryname>`

oppure 

`./pip install <libraryname>`

## <a name="use-custom-modules"></a>Usare moduli personalizzati
In Transform Dataflow (Script) (Trasforma flusso di dati - Script) scrivere il codice Python seguente

```python
import sys
sys.path.append(*<absolute path to the directory containing UserModule.py>*)

from UserModule import ExtensionFunction1
df = ExtensionFunction1(df)
```

In Add Column (Script) (Aggiungi colonna - Script) impostare il tipo di blocco di codice come modulo e scrivere il codice Python seguente

```python 
import sys
sys.path.append(*<absolute path to the directory containing UserModule.py>*)

from UserModule import ExtensionFunction2

def newvalue(row):
    return ExtensionFunction2(row)
```
Per contesti di esecuzione diversi (locale, Docker, Spark), fare in modo che il percorso assoluto punti alla posizione appropriata. Per l'individuazione, è possibile usare "os.getcwd() + relativePath".


## <a name="column-data"></a>Dati della colonna 
I dati della colonna sono accessibili da una riga usando la dot notation o la notazione key-value. I nomi delle colonne che contengono spazi o caratteri speciali non sono accessibili tramite la dot notation. La variabile `row` deve sempre essere definita in entrambe le modalità di estensioni di Python, ovvero Modulo ed Espressione. 

Esempi 

```python
    row.ColumnA + row.ColumnB  
    row["ColumnA"] + row["ColumnB"]
```

## <a name="add-column"></a>Aggiungere una colonna 
### <a name="purpose"></a>Scopo
Il punto di estensione Add Column (Aggiungi colonna) consente di scrivere Python per calcolare una nuova colonna. Il codice che si scrive ha accesso all'intera riga. Deve restituire un nuovo valore di colonna per ogni riga. 

### <a name="how-to-use"></a>Utilizzo
È possibile aggiungere questo punto di estensione usando il blocco "Add Column (Script)" (Aggiungi colonna - Script). È disponibile nel menu di primo livello **Transformations** (Trasformazioni), nonché nel menu di scelta rapida **Column** (Colonna). 

### <a name="syntax"></a>Sintassi
Expression

```python
    math.log(row["Score"])
```

Modulo 

```python
def newvalue(row):  
     return math.log(row["Score"])
```
 

## <a name="advanced-filter"></a>Filtro avanzato
### <a name="purpose"></a>Scopo 
Il punto di estensione Advanced Filter (Filtro avanzato) consente di scrivere un filtro personalizzato. L'utente ha accesso all'intera riga e il codice deve restituire Vero, se include la riga, o Falso, se la esclude. 

### <a name="how-to-use"></a>Utilizzo
È possibile aggiungere questo punto di estensione usando il blocco "Advanced Filter (Script)" (Filtro avanzato - Script). È disponibile nel menu di primo livello **Transformations** (Trasformazioni). 

### <a name="syntax"></a>Sintassi

Expression

```python
    row["Score"] > 95
```

Modulo  

```python
def includerow(row):  
    return row["Score"] > 95
```
 

## <a name="transform-dataflow"></a>Trasformare il flusso di dati
### <a name="purpose"></a>Scopo 
Il punto di estensione Transform Dataflow (Trasforma flusso di dati) consente di trasformare completamente il flusso di dati. Si dispone dell'accesso a un dataframe Pandas che contiene tutte le righe e colonne sottoposte a elaborazione. Il codice deve restituire un dataframe Pandas con i nuovi dati. 

>[!NOTE]
>Se viene usata questa estensione, in Python tutti i dati da caricare nella memoria si trovano in un dataframe Pandas. 
>
>In Spark tutti i dati vengono raccolti in un unico nodo del ruolo lavoro. In presenza di dati di grandi dimensioni, è possibile che il ruolo di lavoro esaurisca la memoria. Usarla con cautela.

### <a name="how-to-use"></a>Utilizzo 
È possibile aggiungere questo punto di estensione usando il blocco "Transform Dataflow (Script)" (Trasforma flusso di dati - Script). È disponibile nel menu di primo livello **Transformations** (Trasformazioni). 
### <a name="syntax"></a>Sintassi 

Expression

```python
    df['index-column'] = range(1, len(df) + 1)  
    df = df.reset_index()
```
 

Modulo 

```python
def transform(df):  
    df['index-column'] = range(1, len(df) + 1)  
    df = df.reset_index()  
    return df
```
  

## <a name="transform-partition"></a>Trasformare la partizione  
### <a name="purpose"></a>Scopo 
Il punto di estensione Transform Partition (Trasforma partizione) consente di trasformare una partizione del flusso di dati. Si dispone dell'accesso a un dataframe Pandas che contiene tutte le righe e colonne per la partizione interessata. Il codice deve restituire un dataframe Pandas con i nuovi dati. 

>[!NOTE]
>In Python è possibile avere una sola partizione o più partizioni, a seconda delle dimensioni dei dati. In Spark si usa un dataframe che contiene i dati per una partizione in un nodo del ruolo di lavoro specifico. In entrambi i casi non è possibile presupporre di avere accesso all'intero set di dati. 


### <a name="how-to-use"></a>Utilizzo
È possibile aggiungere questo punto di estensione usando il blocco "Transform Partition (Script)" (Trasforma partizione - Script). È disponibile nel menu di primo livello **Transformations** (Trasformazioni). 

### <a name="syntax"></a>Sintassi 

Expression 

```python
    df['partition-id'] = index  
    df['index-column'] = range(1, len(df) + 1)  
    df = df.reset_index()
```
 

Modulo 

```python
def transform(df, index):
    df['partition-id'] = index
    df['index-column'] = range(1, len(df) + 1)
    df = df.reset_index()
    return df
```


## <a name="datapreperror"></a>DataPrepError  
### <a name="error-values"></a>Valori dell'errore  
Nella preparazione dati esiste il concetto di valori dell'errore. 

È possibile riscontrare i valori dell'errore nel codice Python personalizzato. Si tratta di istanze di una classe di Python denominata `DataPrepError`. Questa classe esegue il wrapping di un'eccezione di Python e dispone di un paio di proprietà. Le proprietà contengono informazioni sull'errore restituito quando il valore originale viene elaborato, nonché il valore originale. 


### <a name="datapreperror-class-definition"></a>Definizione della classe DataPrepError
```python 
class DataPrepError(Exception): 
    def __bool__(self): 
        return False 
``` 
La creazione di una classe DataPrepError nel framework di Python relativo alla preparazione dati in genere è simile a quello riportato nell'immagine seguente: 
```python 
DataPrepError({ 
   'message':'Cannot convert to numeric value', 
   'originalValue': value, 
   'exceptionMessage': e.args[0], 
   '__errorCode__':'Microsoft.DPrep.ErrorValues.InvalidNumericType' 
}) 
``` 
#### <a name="how-to-use"></a>Utilizzo 
Durante l'esecuzione di Python in un punto dell'estensione è possibile eseguire generare oggetti DataPrepError come valori restituiti usando il metodo di creazione descritto in precedenza. È molto più probabile che gli oggetti DataPrepError vengano rilevati durante l'elaborazione dei dati in un punto dell'estensione. A questo punto, il codice Python personalizzato deve gestire una classe DataPrepError come un tipo di dati valido.

#### <a name="syntax"></a>Sintassi 
Expression 
```python 
    if (isinstance(row["Score"], DataPrepError)): 
        row["Score"].originalValue 
    else: 
        row["Score"] 
``` 
```python 
    if (hasattr(row["Score"], "originalValue")): 
        row["Score"].originalValue 
    else: 
        row["Score"] 
``` 
Modulo 
```python 
def newvalue(row): 
    if (isinstance(row["Score"], DataPrepError)): 
        return row["Score"].originalValue 
    else: 
        return row["Score"] 
``` 
```python 
def newvalue(row): 
    if (hasattr(row["Score"], "originalValue")): 
        return row["Score"].originalValue 
    else: 
        return row["Score"] 
```  
