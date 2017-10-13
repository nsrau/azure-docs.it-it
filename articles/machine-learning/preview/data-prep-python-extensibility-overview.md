---
title: "Uso dell'estensibilità di Python per la preparazione dati per Azure Machine Learning | Microsoft Docs"
description: "Questo documento offre una panoramica e alcuni esempi dettagliati di come usare il codice di Python per estendere la funzionalità di preparazione dati"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/07/2017
ms.openlocfilehash: 4e1935a7830b8174796ac12792fbbc0ed110d081
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="data-prep-python-extensions"></a>Estensioni della preparazione dati in Python
Al fine di colmare i vuoti funzionali tra le funzioni incorporate, la preparazione dati include più livelli di estensibilità. In questo documento viene descritta l'estensibilità tramite lo script di Python. 

## <a name="custom-code-steps"></a>Procedura per il codice personalizzato 
La preparazione dati prevede le operazioni personalizzate seguenti tramite le quali gli utenti possono scrivere il codice: 
1. Lettore di file*
2. Writer*
3. Aggiungere una colonna
4. Filtro avanzato
5. Trasformare il flusso di dati
6. Trasformare la partizione

*Queste operazioni non sono attualmente supportate durante l'esecuzione di Spark. 

## <a name="code-block-types"></a>Tipi di blocchi di codice 
Per ognuna di queste operazioni, sono supportati due tipi di blocchi di codice. In primo luogo, viene supportata un'espressione di Python essenziale che viene eseguita così com'è. In secondo luogo, viene supportato un modulo di Python che richiama una particolare funzione con una firma nota nel codice indicato.

Ad esempio, è possibile aggiungere una nuova colonna che calcola il log di un'altra colonna nei due modi seguenti: espressione: 

```python    
    math.log(row["Score"])
```

modulo: 
    
```python
def newvalue(row): 
        return math.log(row["Score"])
```


La trasformazione per aggiungere una colonna in modalità modulo prevede di trovare una funzione denominata `newvalue` che accetta una variabile di riga e restituisce il valore per la colonna. Questo modulo può includere una quantità di codice Python con altre funzioni, importazioni e così via. 

I dettagli di ogni punto di estensione sono descritti nelle sezioni seguenti: 

## <a name="imports"></a>Importazioni 
Se si usa il tipo di blocco Espressione, è ancora possibile aggiungere le istruzioni di importazione al codice, che però devono essere tutte raggruppate nelle prime righe del codice. Versione corretta: 

```python
import math 
import numpy 
math.log(row["Score"])
```
 

Error:  

```python
import math  
math.log(row["Score"])  
import numpy
```
 
 
Se si usa il tipo di blocco Modulo, è possibile seguire tutte le regole normali di Python per l'uso dell'istruzione "import". 

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
  

## <a name="installing-new-packages"></a>Installazione dei nuovi pacchetti
Per usare un pacchetto non installato per impostazione predefinita, è innanzitutto necessario installarlo negli ambienti usati per la preparazione dati. Questa installazione deve essere eseguita sia sul computer locale che su tutte le destinazioni di calcolo su cui si desidera avviare l'esecuzione.

Per installare i pacchetti in una destinazione di calcolo, è necessario modificare il file conda_dependencies.yml che si trova nella cartella aml_config nella radice del progetto.

### <a name="windows"></a>Windows 
Per trovare il percorso in Windows, individuare l'installazione specifica dell'app di Python e la directory degli script; quella predefinita è:  

`C:\Users\<user>\AppData\Local\AmlWorkbench\Python\Scripts.` 

Quindi, eseguire uno dei comandi seguenti: 

`conda install <libraryname>` 

oppure 

`pip install <libraryname> `

### <a name="mac"></a>Mac 
Per trovare il percorso in Mac, individuare l'installazione specifica dell'app di Python e la directory degli script; il percorso predefinito è: 

`/Users/<user>/Library/Caches/AmlWorkbench>/Python/bin` 

Quindi, eseguire uno dei comandi seguenti: 

`./conda install <libraryname>`

oppure 

`./pip install <libraryname>`

## <a name="column-data"></a>Dati della colonna 
I dati della colonna sono accessibili da una riga usando la dot notation o la notazione key-value. I nomi delle colonne che contengono spazi o caratteri speciali non sono accessibili tramite la dot notation. La variabile `row` deve sempre essere definita in entrambe le modalità di estensioni di Python, ovvero Modulo ed Espressione. 

Esempi: 

```python
    row.ColumnA + row.ColumnB  
    row["ColumnA"] + row["ColumnB"]
```

## <a name="file-reader"></a>Lettore di file 
### <a name="purpose"></a>Scopo 
Questo punto di estensione consente di controllare completamente il processo di lettura di un file in un flusso di dati. Il sistema chiama il codice, passando nell'elenco di file che è necessario elaborare e il codice deve creare e restituire un dataframe Pandas. 

>[!NOTE]
>Questo punto di estensione non funziona in Spark. 


### <a name="how-to-use"></a>Utilizzo 
Accedere a questo punto di estensione dalla creazione guidata Apri origine dati. Scegliere File nella prima pagina e quindi scegliere il percorso del file. Nella pagina "Choose File Parameters" (Scegli parametri del file) sfogliare l'elenco a discesa Tipo di file e scegliere "Custom File (Script)" (File personalizzato - Script). 

Il codice viene assegnato al dataframe di Pandas denominato "df" che contiene informazioni sui file da leggere. Se si sceglie di aprire una directory che contiene più file, il dataframe contiene più di una riga.  

Questo dataframe è composto dalle colonne seguenti: 
- Percorso: il file da leggere.
- PathHint: indica dove si trova il file. Valori: "Locale", "AzureBlobStorage", "AzureDataLakeStorage"
- AuthenticationType: il tipo di autenticazione usato per accedere al file. Valori: "Nessuno", "SasToken", "OAuthToken"
- AuthenticationValue: contiene Nessuno o il token da usare.

### <a name="syntax"></a>Sintassi 
Espressione: 

```python
    paths = df['Path'].tolist()  
    df = pd.read_csv(paths[0])
```


Modulo:  
```python
PathHint = Local  
def read(df):  
    paths = df['Path'].tolist()  
    filedf = pd.read_csv(paths[0])  
    return filedf  
```
 

## <a name="writer"></a>Writer 
### <a name="purpose"></a>Scopo 
L'estensione writer consente di controllare completamente il processo di scrittura dei dati da un flusso di dati. Il sistema richiama il codice, passando in un dataframe, il codice può usare il dataframe per scrivere in qualsiasi modo si desideri. 

>[!NOTE]
>Il punto di estensione writer non funziona in Spark. 


### <a name="how-to-use"></a>Utilizzo 
È possibile aggiungere questo punto di estensione usando il blocco "Write Dataflow (Script)" (Scrivi flusso di dati - Script). È disponibile nel menu di primo livello Trasformazioni. 

### <a name="syntax"></a>Sintassi 
Espressione: 

```python
    df.to_csv('c:\\temp\\output.csv')
```

Modulo:

```python
def write(df):  
    df.to_csv('c:\\temp\\output.csv')  
    return df
```
 
 
Questo blocco di scrittura personalizzato può essere presente all'interno di un elenco dei passaggi, pertanto se si usa un modulo e si scrive una funzione questa deve restituire il dataframe che costituisce l'input per il passaggio seguente. 

## <a name="add-column"></a>Aggiungere una colonna 
### <a name="purpose"></a>Scopo
Questo punto di estensione consente di scrivere Python per calcolare una nuova colonna. Il codice che si scrive ha accesso all'intera riga. Deve restituire un nuovo valore di colonna per ogni riga. 

### <a name="how-to-use"></a>Uso
È possibile aggiungere questo punto di estensione usando il blocco "Add Column (Script)" (Aggiungi colonna - Script). È disponibile nel menu di primo livello Trasformazioni, nonché nel menu di scelta rapida della colonna. 

### <a name="syntax"></a>Sintassi
Espressione: 

```python
    math.log(row["Score"])
```

Modulo: 

```python
def newvalue(row):  
     return math.log(row["Score"])
```
 

## <a name="advanced-filter"></a>Filtro avanzato
### <a name="purpose"></a>Scopo 
Questo punto di estensione consente di scrivere un filtro personalizzato. L'utente ha accesso all'intera riga e il codice deve restituire Vero, se include la riga, o Falso, se la esclude. 

### <a name="how-to-use"></a>Utilizzo
È possibile aggiungere questo punto di estensione usando il blocco "Advanced Filter (Script)" (Filtro avanzato - Script). È disponibile nel menu di primo livello Trasformazioni. 

### <a name="syntax"></a>Sintassi

Espressione: 

```python
    row["Score"] > 95
```

Modulo:  

```python
def includerow(row):  
    return row["Score"] > 95
```
 

## <a name="transform-dataflow"></a>Trasformare il flusso di dati
### <a name="purpose"></a>Scopo 
Il punto di estensione consente di trasformare completamente il flusso di dati. L'utente ha accesso a un dataframe di Pandas che contiene tutte le colonne e le righe in elaborazione; il codice deve restituire un dataframe di Pandas con i nuovi dati. 

>[!NOTE]
>In Python tutti i dati da caricare nella memoria di un dataframe di Pandas se viene usata questa estensione. 

In Spark tutti i dati vengono raccolti in un unico nodo del ruolo lavoro. Se i dati hanno dimensioni molto grandi, ciò potrebbe causare l'esecuzione di un ruolo di lavoro con memoria insufficiente. Usarla con cautela.

### <a name="how-to-use"></a>Utilizzo 
È possibile aggiungere questo punto di estensione usando il blocco "Transform Dataflow (Script)" (Trasforma flusso di dati - Script). È disponibile nel menu di primo livello Trasformazioni. 
### <a name="syntax"></a>Sintassi 

Espressione: 

```python
    df['index-column'] = range(1, len(df) + 1)  
    df = df.reset_index()
```
 

Modulo: 

```python
def transform(df):  
    df['index-column'] = range(1, len(df) + 1)  
    df = df.reset_index()  
    return df
```
  

## <a name="transform-partition"></a>Trasformare la partizione  
### <a name="purpose"></a>Scopo 
Questo punto di estensione consente di trasformare una partizione del flusso di dati. L'utente ha accesso a un dataframe di Pandas che contiene tutte le colonne e le righe per la partizione; il codice deve restituire un dataframe di Pandas con i nuovi dati. 

>[!NOTE]
>In Python è possibile avere una sola partizione o più partizioni, a seconda delle dimensioni dei dati. In Spark si usa un dataframe che contiene i dati per una partizione in un nodo del ruolo di lavoro specifico. In entrambi i casi non è possibile presupporre di avere accesso all'intero set di dati. 


### <a name="how-to-use"></a>Utilizzo
È possibile aggiungere questo punto di estensione usando il blocco "Transform Partition (Script)" (Trasforma partizione - Script). È disponibile nel menu di primo livello Trasformazioni. 

### <a name="syntax"></a>Sintassi 

Espressione: 

```python
    df['partition-id'] = index  
    df['index-column'] = range(1, len(df) + 1)  
    df = df.reset_index()
```
 

Modulo: 

```python
def transform(df, index):
    df['partition-id'] = index
    df['index-column'] = range(1, len(df) + 1)
    df = df.reset_index()
    return df
```


## <a name="datapreperror"></a>DataPrepError  
### <a name="error-values"></a>Valori dell'errore  
Nella preparazione dati esiste il concetto di valori dell'errore. La creazione e il motivo per cui esistono i valori dell'errore sono descritti qui <link to error values doc>. 

È possibile riscontrare i valori dell'errore nel codice Python personalizzato. Si tratta di istanze di una classe di Python denominata `DataPrepError`. Questa classe esegue il wrapping di un'eccezione di Python e ha due proprietà che contengono informazioni sull'errore che si è verificato durante l'elaborazione del valore originale, nonché il valore originale. 


### <a name="datapreperror-class-definition"></a>Definizione di classe di DataPrepError
```python 
class DataPrepError(Exception): 
    def __bool__(self): 
        return False 
``` 
La creazione di un DataPrepError nel framework di Python relativo alla preparazione dati in genere è simile a quello riportato nell'immagine seguente: 
```python 
DataPrepError({ 
   'message':'Cannot convert to numeric value', 
   'originalValue': value, 
   'exceptionMessage': e.args[0], 
   '__errorCode__':'Microsoft.DPrep.ErrorValues.InvalidNumericType' 
}) 
``` 
#### <a name="how-to-use"></a>Utilizzo 
È possibile eseguire Python in un punto dell'estensione per generare DataPrepErrors come valori restituiti, usando il metodo di creazione descritto in precedenza. È molto più probabile che i DataPrepErrors vengano rilevati durante l'elaborazione dei dati in un punto dell'estensione. A questo punto, il codice Python personalizzato deve gestire DataPrepError come un tipo di dati valido. 

#### <a name="syntax"></a>Sintassi 
Espressione:  
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
Modulo:  
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
