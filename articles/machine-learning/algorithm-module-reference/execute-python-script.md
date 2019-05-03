---
title: 'Eseguire lo Script di Python: Riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo Execute Python Script nel servizio Azure Machine Learning per eseguire il codice Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 6e61ed5a18e69b107b78bf2042de21d14cd6beb5
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029131"
---
# <a name="execute-python-script-module"></a>Eseguire il modulo di Script Python

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio di Azure Machine Learning.

Usare questo modulo per eseguire il codice Python. Per altre informazioni sui principi di architettura e progettazione di Python, vedere [l'articolo seguente.](https://docs.microsoft.com/azure/machine-learning/machine-learning-execute-python-scripts)

Con Python, è possibile eseguire le attività che non sono attualmente supportate per i moduli esistenti, ad esempio:

+ Visualizzazione di dati tramite `matplotlib`
+ Usare le librerie Python per enumerare i set di dati e i modelli nell'area di lavoro
+ La lettura, il caricamento e la modifica dei dati da origini non supportate dal [Import Data](./import-data.md) modulo
+ Eseguire il proprio codice di apprendimento avanzato 


Azure Machine Learning Usa la distribuzione Anaconda di Python, che sono disponibili numerose utilità comuni per l'elaborazione dati. Anaconda versione verrà aggiornata automaticamente. Versione corrente è:
 -  Distribuzione anaconda 4.5 e versione successiva per Python 3.6 

I pacchetti pre-installati sono:
-  asn1crypto==0.24.0
- attrs==19.1.0
- azure-common==1.1.18
- azure-storage-blob==1.5.0
- azure-storage-common==1.4.0
- certifi==2019.3.9
- cffi==1.12.2
- chardet==3.0.4
- cryptography==2.6.1
- distro==1.4.0
- idna==2.8
- jsonschema==3.0.1
- lightgbm==2.2.3
- more-itertools==6.0.0
- numpy==1.16.2
- pandas==0.24.2
- Pillow==6.0.0
- pip==19.0.3
- pyarrow==0.12.1
- pycparser==2.19
- pycryptodomex==3.7.3
- pyrsistent==0.14.11
- python-dateutil==2.8.0
- pytz==2018.9
- requests==2.21.0
- scikit-learn==0.20.3
- scipy==1.2.1
- setuptools==40.8.0
- six==1.12.0
- torch==1.0.1.post2
- torchvision==0.2.2.post3
- urllib3==1.24.1
- wheel==0.33.1 

 Per installare altri pacchetti non incluso nell'elenco pre-installato, ad esempio *scikit-misc*, aggiungere il codice seguente allo script: 

 ```python
import os
os.system(f"pip install scikit-misc")
```

## <a name="how-to-use"></a>Utilizzo

Il **Execute Python Script** modulo contiene codice Python di esempio che è possibile usare come punto di partenza. Per configurare il **Execute Python Script** modulo, si forniscono un set di input e codice Python da eseguire nel **lo script Python** casella di testo.

1. Aggiungere il **Execute Python Script** modulo nell'esperimento.

2. Aggiungere e connettere sul **Dataset1** qualsiasi set di dati dall'interfaccia che si desidera utilizzare per l'input. Fare riferimento a questo set di dati nello script Python come **DataFrame1**.

    Utilizzo di un set di dati è facoltativo, se si desidera generare i dati usando Python, o Usa codice Python per importare i dati direttamente nel modulo.

    Questo modulo supporta l'aggiunta di un secondo set di dati sul **Dataset2**. Il secondo set di dati nello script Python come DataFrame2 riferimento.

    Set di dati archiviati in Azure Machine Learning vengono convertiti automaticamente in **pandas** frame di dati quando caricati con questo modulo.

    ![Esecuzione della mappa di input di Python](media/module/python-module.png)

4. Per includere nuovi pacchetti Python o codice, aggiungere il file compresso contenente queste risorse personalizzate sul **bundle di Script**. L'input per **bundle di Script** deve essere un file compresso già caricato nell'area di lavoro. 

    Qualsiasi file contenuti nell'archivio compresso caricato può essere utilizzato durante l'esecuzione dell'esperimento. Se l'archivio include una struttura di directory, la struttura viene mantenuta, ma è necessario anteporre una directory denominata **src** al percorso.

5. Nel **script di Python** casella di testo digitare o incollare uno script Python valido.

    Il **script di Python** casella di testo viene prepopolata con alcune istruzioni di commenti e codice di esempio per l'accesso ai dati e l'output. **È necessario modificare o sostituire questo codice.** Assicurarsi di seguire le convenzioni di Python su rientro e maiuscole e minuscole.

    + Lo script deve contenere una funzione denominata `azureml_main` come punto di ingresso per questo modulo.
    + La funzione di punto di ingresso può contenere fino a due argomenti di input: `Param<dataframe1>` e `Param<dataframe2>`
    + I file compressi connessi alla terza porta di input vengano decompresso e archiviati nella directory, `.\Script Bundle`, che viene aggiunto anche a Python `sys.path`. 

    Pertanto, se il file con estensione zip contiene `mymodule.py`, importarlo usando `import mymodule`.

    + Due set di dati può essere restituito per l'interfaccia che deve essere una sequenza di tipo `pandas.DataFrame`. È possibile creare altri output nel codice Python e scriverli direttamente in archiviazione di Azure.

6. Eseguire l'esperimento, o selezionare il modulo e fare clic su **Esegui selezionati** per eseguire lo script di Python.

    Tutti i dati e il codice viene caricato in una macchina virtuale ed eseguire utilizzando l'ambiente Python specificato.

## <a name="results"></a>Risultati

Come un intervallo di pandas, è necessario specificare i risultati dei calcoli eseguiti dal codice Python incorporato. Frame di dati, che viene convertito automaticamente nel formato di set di dati di Azure Machine Learning, in modo che è possibile usare i risultati con altri moduli nell'esperimento.

Il modulo restituisce due set di dati:  
  
+ **Risultati 1 set di dati**, definito dai dataframe di pandas restituita prima nello script di Python

+ **Generare set di dati 2**, definito dal secondo dataframe di pandas restituito nello script di Python


## <a name="next-steps"></a>Passaggi successivi

Vedere le [set di moduli disponibili](module-reference.md) al servizio Azure Machine Learning. 