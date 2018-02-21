---
title: Trovare le esecuzioni con la precisione migliore e la durata minima in Azure Machine Learning Workbench | Microsoft Docs
description: Caso d'uso end-to-end per trovare la precisione migliore tramite l'interfaccia della riga di comando usando Azure Machine Learning Workbench
services: machine-learning
author: totekp
ms.author: kefzhou
manager: akannava
ms.reviewer: akannava, haining, mldocs, garyericson, jasonwhowell
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/29/2017
ms.openlocfilehash: 40e066fe602e8c4680043158f1d401a884e07c19
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2017
---
# <a name="find-runs-with-the-best-accuracy-and-lowest-duration"></a>Trovare le esecuzioni con la precisione migliore e la durata minima
Date più esecuzioni, un caso d'uso consiste ad esempio nel trovare le esecuzioni con la precisione migliore. Uno degli approcci perseguibili prevede l'uso dell'interfaccia della riga di comando con una query [JMESPath](http://jmespath.org/). Per altre informazioni su come usare JMESPath nell'interfaccia della riga di comando di Azure, vedere [Uso di query JMESPath con l'interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest). Nell'esempio seguente vengono create quattro esecuzioni con i valori di precisione 0, 0,98, 1 e 1. Le esecuzioni vengono filtrate se si trovano nell'intervallo `[MaxAccuracy-Threshold, MaxAccuracy]` in cui `Threshold = .03`.

## <a name="sample-data"></a>Dati di esempio
Se non sono disponibili esecuzioni con un valore `Accuracy`, svolgere la procedura seguente per generare esecuzioni idonee per la query.

Per prima cosa, creare un file Python in Azure Machine Learning Workbench, denominarlo `log_accuracy.py` e incollarlo nel codice seguente:
```python
from azureml.logging import get_azureml_logger

logger = get_azureml_logger()

accuracy_value = 0.5

if len(sys.argv) > 1:
     accuracy_value = float(sys.argv[1])

logger.log("Accuracy", accuracy_value)
```

Creare quindi un file `run.py` e incollarlo nel codice seguente:
```python
import os

accuracy_values = [0, 0.98, 1.0, 1.0]
for value in accuracy_values:
    os.system('az ml experiment submit -c local ./log_accuracy.py {}'.format(value))
```

Aprire infine l'interfaccia della riga di comando tramite Workbench ed eseguire il comando `python run.py` per inviare quattro esperimenti. Al termine dello script, nella scheda `Run History` dovrebbero essere presenti altre quattro esecuzioni.

## <a name="query-the-run-history"></a>Eseguire query sulla cronologia di esecuzione
Il primo comando trova il valore di precisione massima.
```powershell
az ml history list --query '@[?Accuracy != null] | max_by(@, &Accuracy).Accuracy'
```

Usando questo valore di precisione massima pari a `1` e un valore di soglia pari a `0.03`, il secondo comando filtra le esecuzioni usando `Accuracy` e quindi le ordina per `duration` in modo crescente.
```powershell
az ml history list --query '@[?Accuracy >= sum(`[1, -0.03]`)] | sort_by(@, &duration)'
```
> [!NOTE]
> Se si vuole un controllo rigoroso del limite superiore, è necessario usare il formato di query ``@[?Accuracy >= sum(`[$max_accuracy_value, -$threshold]`) && Accuracy <= `$max_accuracy_value`]``

Se si usa PowerShell, il codice seguente userà variabili locali per archiviare i valori di soglia e di precisione massima:
```powershell
$threshold = 0.03
$max_accuracy_value = az ml history list --query '@[?Accuracy != null] | max_by(@, &Accuracy).Accuracy'
$find_runs_query = '@[?Accuracy >= sum(`[{0}, -{1}]`)] | sort_by(@, &duration)' -f $max_accuracy_value, $threshold
az ml history list --query $find_runs_query
```

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulla registrazione, vedere [Come usare Cronologia di esecuzione e le metriche del modello in Azure Machine Learning Workbench](how-to-use-run-history-model-metrics.md).    
