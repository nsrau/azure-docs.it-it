---
title: 'Converti in CSV: Riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Converti in CSV in Azure Machine Learning per convertire un set di dati in un formato CSV che può essere scaricato, esportato o condiviso con moduli di script R o Python.Learn how to use the Convert to CSV module in Azure Machine Learning to convert a dataset into a CSV format that can be downloaded, exported, or shared with R or Python script modules.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: fc2a043e8f1565cf5fe45ba0b072ad015076635e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477681"
---
# <a name="convert-to-csv-module"></a>Converti in modulo CSV

Questo articolo descrive un modulo nella finestra di progettazione di Azure Machine Learning (anteprima).

Utilizzare questo modulo per convertire un set di dati in un formato CSV che può essere scaricato, esportato o condiviso con i moduli di script R o Python.Use this module to convert a dataset to convert a CSV format that can be downloaded, exported, or shared with R or Python script modules.

### <a name="more-about-the-csv-format"></a>Ulteriori informazioni sul formato CSV 

Il formato CSV, che sta per "valori separati da virgola", è un formato di file utilizzato da molti strumenti di apprendimento automatico esterni. CSV è un formato di interscambio comune quando si lavora con linguaggi open source come R o Python.

Anche se si esegue la maggior parte del lavoro in Azure Machine Learning, in alcuni casi potrebbe essere utile convertire il set di dati in CSV da usare in strumenti esterni. Ad esempio:

+ Scaricare il file CSV per aprirlo con Excel o importarlo in un database relazionale.  
+ Salvare il file CSV nell'archiviazione cloud e connettersi a esso da Power BI per creare visualizzazioni.  
+ Usare il formato CSV per preparare i dati per l'uso in R e Python. 

Quando si converte un set di dati in CSV, il file CSV viene salvato nell'area di lavoro di Azure ML. È possibile usare un'utilità di archiviazione di Azure per aprire e usare direttamente il file. Puoi anche accedere al file CSV nella finestra di progettazione selezionando il modulo Converti in **CSV,** quindi selezionare l'icona dell'istogramma nella scheda **Uscite** nel pannello di destra per visualizzare l'output. È possibile scaricare il file CSV dalla cartella Risultati in una directory locale.  

## <a name="how-to-configure-convert-to-csv"></a>Come configurare Converti in CSV


1.  Aggiungere il modulo Converti in CSV alla pipeline. È possibile trovare questo modulo nel gruppo Trasformazione dati nella finestra di **progettazione.** 

2. Connetterlo a qualsiasi modulo che genera un set di dati.   
  
3.  Inviare la pipeline.

### <a name="results"></a>Risultati
  

Selezionare la scheda **Uscite** nel pannello destro di **Converti in CSV**e selezionare una di queste icone sotto Output **porta**.  

+ Registra set di **dati:** selezionare l'icona e salvare nuovamente il file CSV nell'area di lavoro di Azure ML come set di dati separato. È possibile trovare il set di dati come modulo nell'albero dei moduli nella sezione **Set di dati personali.**

 + **Visualizza output**: Selezionare l'icona a occhio e seguire le istruzioni per sfogliare la cartella **Results_dataset** e scaricare il file data.csv.

## <a name="next-steps"></a>Passaggi successivi

Vedere il set di moduli disponibili per Azure Machine Learning.See the [set of modules available](module-reference.md) to Azure Machine Learning. 