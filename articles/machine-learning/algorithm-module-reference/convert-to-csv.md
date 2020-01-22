---
title: 'Converti in CSV: riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Convert to CSV in Azure Machine Learning per convertire un set di dati in un formato CSV che può essere scaricato, esportato o condiviso con moduli di script R o Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 0e92201552b4d448a619a801d2ee64032c8bbefe
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76314437"
---
# <a name="convert-to-csv-module"></a>Converti in modulo CSV

Questo articolo descrive un modulo in Azure Machine Learning Designer.

Usare questo modulo per convertire un set di dati in un formato CSV che può essere scaricato, esportato o condiviso con moduli di script R o Python.

### <a name="more-about-the-csv-format"></a>Altre informazioni sul formato CSV 

Il formato CSV, che sta per "valori delimitati da virgole", è un formato di file usato da molti strumenti di apprendimento automatico esterni. CSV è un formato di interscambio comune quando si utilizzano linguaggi open source, ad esempio R o Python.

Anche se si esegue la maggior parte del lavoro in Azure Machine Learning, in alcuni casi potrebbe risultare utile convertire il set di dati in un volume condiviso cluster da usare in strumenti esterni. Ad esempio:

+ Scaricare il file CSV per aprirlo con Excel oppure importarlo in un database relazionale.  
+ Salvare il file CSV nell'archiviazione cloud e connettersi a esso da Power BI per creare le visualizzazioni.  
+ Usare il formato CSV per preparare i dati per l'uso in R e Python. 

Quando si converte un set di dati in formato CSV, il volume CSV viene salvato nell'area di lavoro di Azure ML. È possibile usare un'utilità di archiviazione di Azure per aprire e usare direttamente il file. È anche possibile accedere al volume CSV nella finestra di progettazione selezionando il modulo **Convert to CSV** , quindi selezionare l'icona dell'istogramma nella scheda **output** nel riquadro di destra per visualizzare l'output. È possibile scaricare il file CSV dalla cartella dei risultati in una directory locale.  

## <a name="how-to-configure-convert-to-csv"></a>Come configurare Convert to CSV


1.  Aggiungere il modulo Convert to CSV alla pipeline. È possibile trovare questo modulo nel gruppo **trasformazione dati** nella finestra di progettazione. 

2. Connetterlo a qualsiasi modulo che restituisce un set di dati.   
  
3.  Eseguire la pipeline.

### <a name="results"></a>Risultati
  

Selezionare la scheda **output** nel riquadro di destra di **Converti in CSV**e selezionare una di queste icone sotto gli output della **porta**.  

+ **Registra set di dati**: selezionare l'icona e salvare di nuovo il file CSV nell'area di lavoro di Azure ml come set di dati separato. È possibile trovare il set di dati come modulo nell'albero del modulo nella sezione set di dati **personali** .

 + **Visualizza output**: selezionare l'icona a forma di occhio e seguire le istruzioni per esplorare la cartella **Results_dataset** e scaricare il file Data. csv.

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 