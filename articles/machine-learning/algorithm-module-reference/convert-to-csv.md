---
title: 'Converti in CSV: Riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo Convert to CSV nel servizio Azure Machine Learning per convertire un set di dati in un formato CSV che può essere scaricato, esportato o condiviso con moduli di script R o Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 2b8f235014efe440e518f1f9c4d0d454f6918955
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128903"
---
# <a name="convert-to-csv-module"></a>Converti in modulo CSV

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio Azure Machine Learning.

Usare questo modulo per convertire un set di dati in un formato CSV che può essere scaricato, esportato o condiviso con moduli di script R o Python.

### <a name="more-about-the-csv-format"></a>Altre informazioni sul formato CSV 

Il formato CSV, che sta per "valori delimitati da virgole", è un formato di file usato da molti strumenti di apprendimento automatico esterni. CSV è un formato di interscambio comune quando si utilizzano linguaggi open source, ad esempio R o Python.

Anche se si esegue la maggior parte del lavoro in Azure Machine Learning, in alcuni casi potrebbe risultare utile convertire il set di dati in un volume condiviso cluster da usare in strumenti esterni. Ad esempio:

+ Scaricare il file CSV per aprirlo con Excel oppure importarlo in un database relazionale.  
+ Salvare il file CSV nell'archiviazione cloud e connettersi a esso da Power BI per creare le visualizzazioni.  
+ Usare il formato CSV per preparare i dati per l'uso in R e Python. È sufficiente fare clic con il pulsante destro del mouse sull'output del modulo per generare il codice necessario per accedere ai dati direttamente da Python o da un notebook di Jupyter. 

Quando si converte un set di dati in formato CSV, il file viene salvato nell'area di lavoro di Azure ML. È possibile usare un'utilità di archiviazione di Azure per aprire e usare direttamente il file. in alternativa, è possibile fare clic con il pulsante destro del mouse sull'output del modulo e scaricare il file CSV nel computer oppure usarlo nel codice R o Python.  

## <a name="how-to-configure-convert-to-csv"></a>Come configurare Convert to CSV

1.  Aggiungere il modulo [Convert to CSV (Converti in CSV](./convert-to-csv.md) ) nell'esperimento. È possibile trovare questo modulo nel gruppo **conversioni formato dati** nell'interfaccia. 

2. Connetterlo a qualsiasi modulo che restituisce un set di dati.   
  
3.  Eseguire l'esperimento.

### <a name="results"></a>Risultati
  

Fare doppio clic sull'output di [Convert to CSV](./convert-to-csv.md)e selezionare una di queste opzioni.  

 + **Set di dati di risultati-Download >** : Apre immediatamente una copia dei dati in formato CSV che è possibile salvare in una cartella locale. Se non si specifica una cartella, viene applicato un nome file predefinito e il file CSV viene salvato nella libreria di **download** locale.


 + **Set di dati di risultati-> Salva come set di dati**: Salva di nuovo il file CSV nell'area di lavoro di Azure ML come set di dati separato.

 + **Genera codice di accesso ai dati**: Azure ML genera due set di codice per accedere ai dati, usando Python o R. Per accedere ai dati, copiare il frammento di codice nell'applicazione. (La*generazione del codice di accesso ai dati sarà presto disponibile).*

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning servizio. 