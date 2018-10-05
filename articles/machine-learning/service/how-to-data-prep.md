---
title: Preparare i dati con il Software Development Kit Machine Learning Data Prep per Python di Azure
description: Informazioni su come usare il Software Development Kit Machine Learning Data Prep per Python di Azure per caricare dati di diversi formati, trasformarli per migliorarne l'usabilità e scriverli in una posizione accessibile ai propri modelli.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 09/24/2018
ms.openlocfilehash: 673b8ddf3ed07507cbce4b54e9b8a488cb3ac689
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2018
ms.locfileid: "47166606"
---
# <a name="prepare-data-for-modeling-with-azure-machine-learning"></a>Preparare i dati per la modellazione con Azure Machine Learning
 
La preparazione dei dati è una parte importante del flusso di lavoro dell'apprendimento automatico. Un accesso semplice a dati puliti in un formato facilmente fruibile migliorerà la definizione e l'efficacia dei modelli. 

È possibile preparare i dati in Python usando [ Azure Machine Learning Data Prep SDK](https://docs.microsoft.com/python/api/overview/azure/dataprep?view=azure-dataprep-py). 

## <a name="data-preparation-pipeline"></a>Pipeline di preparazione dei dati

I passaggi di preparazione dei dati principali sono:

1. [Caricare dati](how-to-load-data.md), che possono essere in vari formati
2. [Trasformare](how-to-transform-data.md) i dati in una struttura più utilizzabile
3. [Scrivere](how-to-write-data.md) tali dati in un percorso accessibile ai propri modelli

![Preparazione dei dati](./media/concept-data-preparation/data-prep-process.png)

## <a name="next-steps"></a>Passaggi successivi
Consultare un [notebook di esempio](https://github.com/Microsoft/PendletonDocs/blob/master/Scenarios/GettingStarted/getting-started.ipynb) sulla preparazione dei dati con il Software Development Kit Azure Machine Learning Data Prep.
