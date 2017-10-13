---
title: Importare dati in Machine Learning Studio | Microsoft Docs
description: Come importare dati in Azure Machine Learning Studio da varie origini dati. Informazioni sui tipi di dati e i formati di dati supportati.
keywords: dati di importazione,formato dati,tipi di dati,origini dati,dati di training
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: c194ee3b-838c-4efe-bb2a-c1d052326216
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: garye;bradsev
ms.openlocfilehash: 98a8fa15922ef100a7f8cadd92fb7ffef0d3bcde
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="import-your-training-data-into-azure-machine-learning-studio-from-various-data-sources"></a>Importare dati di training in Azure Machine Learning Studio da varie origini dati
Per usare i propri dati in Machine Learning Studio per sviluppare ed eseguire il training di una soluzione di analisi predittiva, è possibile: 

* Caricare anticipatamente i dati da un **file locale** nel disco rigido per creare un modulo di set di dati nell'area di lavoro
* Accedere ai dati da una delle diverse **origini dati online** mentre l'esperimento viene eseguito tramite il modulo [Import Data][import-data] 
* Usare i dati da un altro **esperimento** di Azure Machine Learning salvato come un set di dati.
* Usare i dati da un **database di SQL Server** locale

Ciascuna di queste opzioni viene descritta in uno degli argomenti del menu sottostante. Questi argomenti illustrano come importare dati da diverse origini dati da utilizzare in Machine Learning Studio. 

[!INCLUDE [import-data-into-aml-studio-selector](../../../includes/machine-learning-import-data-into-aml-studio.md)]

> [!NOTE]
> In Machine Learning Studio sono disponibili numerosi set di dati di esempio che è possibile usare per questo scopo. Per informazioni, vedere [Usare i set di dati di esempio in Azure Machine Learning Studio](use-sample-datasets.md).
> 
> 

Questo argomento introduttivo illustra anche come ottenere dati pronti per l'uso in Machine Learning Studio e descrive i tipi e i formati di dati supportati. 

> [!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]
> 
> 

## <a name="get-data-ready-for-use-in-azure-machine-learning-studio"></a>Preparare i dati da usare in Azure Machine Learning Studio
Machine Learning Studio è progettato per lavorare con dati tabulari o rettangolari, ad esempio dati di testo che rappresentano dati delimitati o strutturati di un database, anche se in alcuni casi potrebbero essere usati dati non rettangolari.

È consigliabile che i dati siano relativamente puliti. Ciò significa che, prima di caricare i dati nell’esperimento, è opportuno risolvere eventuali problemi, ad esempio stringhe non racchiuse tra virgolette.

In Machine Learning Studio sono tuttavia disponibili moduli che consentono di eseguire alcune modifiche dei dati all'interno dell'esperimento. A seconda degli algoritmi di Machine Learning usati, potrebbe essere necessario stabilire come gestire problemi strutturali di dati, ad esempio valori mancanti e dati di tipo sparse. A tale scopo, esistono moduli che possono rivelarsi utili. Vedere la sezione **Trasformazione dei dati** della tavolozza dei moduli per i moduli che eseguono queste funzioni.

In qualsiasi momento dell'esperimento, è possibile visualizzare o scaricare i dati prodotti da un modulo facendo clic sulla porta di output. A seconda del modulo, potrebbero essere disponibili opzioni di download diverse, oppure l'utente potrebbe essere in grado di visualizzare i dati dal browser Web in Machine Learning Studio.

## <a name="data-formats-and-data-types-supported"></a>Tipi di dati e formati di dati supportati
È possibile importare diversi tipi di dati nell'esperimento, a seconda del meccanismo usato per importare dati e della relativa provenienza:

* Testo normale (con estensione txt)
* Valori delimitati da virgole (CSV) con un'intestazione (con estensione csv) o senza intestazione (con estensione nh.csv)
* Valori separati da tabulazioni (TSV) con un'intestazione (con estensione tsv) o senza intestazione (con estensione nh.tsv)
* File di Excel
* Tabella di Azure
* Tabella Hive
* Tabella di database SQL
* Valori OData
* Dati SVMLight (con estensione svmlight) (vedere la [definizione di SVMLight](http://svmlight.joachims.org/) per informazioni sul formato)
* Dati ARFF (Attribute Relation File Format) (con estensione arff) (vedere la [definizione di ARFF](http://weka.wikispaces.com/ARFF) per informazioni sul formato)
* File ZIP (con estensione zip)
* File dell’oggetto o dell'area di lavoro R (con estensione RData)

Se si importano dati in un formato quale ARFF che include metadati, Machine Learning Studio usa i metadati per definire l'intestazione e il tipo di dati di ogni colonna.

Se si importano dati in un formato quale TSV o CSV che non include metadati, Machine Learning Studio deduce il tipo di dati per ogni colonna tramite il campionamento dei dati. Se nemmeno i dati presentano intestazioni di colonna, Machine Learning Studio fornisce nomi predefiniti.

È possibile specificare in modo esplicito o modificare le intestazioni e i tipi di dati delle colonne tramite l'[Editor metadati][edit-metadata].

I seguenti **tipi di dati** vengono riconosciuti da Machine Learning Studio:

* String
* Integer
* Double
* Boolean
* DateTime
* TimeSpan

Machine Learning Studio usa un tipo di dati interno denominato ***Tabella dati*** per passare i dati tra i moduli. È possibile convertire in modo esplicito i dati in formato Tabella dati tramite il modulo [Convertire in set di dati][convert-to-dataset].

I moduli che accettano formati diversi da Tabella dati convertiranno i dati in Tabella dati in modo automatico, prima di passare al modulo successivo.

Se necessario, è possibile convertire il formato Tabella dati in CSV, TSV, ARFF o SVMLight usando altri moduli di conversione.
Esaminare la sezione **Conversioni di formati di dati** della tavolozza dei moduli per i moduli che eseguono queste funzioni.

<!-- Module References -->
[convert-to-dataset]: https://msdn.microsoft.com/library/azure/72bf58e0-fc87-4bb1-9704-f1805003b975/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
