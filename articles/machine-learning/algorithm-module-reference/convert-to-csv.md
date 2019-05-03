---
title: 'Converti in CSV: Riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare la funzione Convert per modulo CSV nel servizio Azure Machine Learning per convertire un set di dati in un formato CSV che può essere scaricato, esportato o condivise con i moduli di script R o Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 8b8b6758cc2df7a092ce36e9507f84ac534d0e3d
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028726"
---
# <a name="convert-to-csv-module"></a>Converti in modulo CSV

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio di Azure Machine Learning.

Usare questo modulo per convertire un set di dati in un formato CSV che può essere scaricato, esportato o condivise con i moduli di script R o Python.

### <a name="more-about-the-csv-format"></a>Informazioni sul formato CSV 

Il formato CSV, che è l'acronimo di "comma-separated values", è un formato di file utilizzato da molti esterno strumenti di apprendimento. CSV è un formato di interscambio comune quando si lavora con i linguaggi open source, ad esempio R o Python.

Anche se si esegue la maggior parte del lavoro in Azure Machine Learning, vi sono casi quando può risultare utile per convertire il set di dati in un file CSV da usare in strumenti esterni. Ad esempio: 

+ Scaricare il file CSV per aprirlo con Excel e importarlo in un database relazionale.  
+ Salvare il file CSV per l'archiviazione cloud e la connessione da Power BI per creare visualizzazioni.  
+ Usare il formato CSV per preparare i dati per l'uso in R e Python. Fare doppio clic su semplicemente l'output del modulo per generare il codice necessario per accedere ai dati direttamente da Python o un notebook di Jupyter. 

Quando si converte un set di dati in formato CSV, il file viene salvato nell'area di lavoro di Azure Machine Learning. È possibile usare un'utilità di archiviazione di Azure per aprire e usare direttamente il file, oppure è possibile fare doppio clic su output del modulo e scaricare il file CSV nel computer o usarla nel codice R o Python.  

## <a name="how-to-configure-convert-to-csv"></a>Jak nakonfigurovat Converti in CSV

1.  Aggiungere il [Convert to CSV](./convert-to-csv.md) modulo nell'esperimento. È possibile trovare questo modulo nel **Data Format Conversions** gruppo di nell'interfaccia. 

2. Connetterlo a qualsiasi modulo che genera un set di dati.   
  
3.  Eseguire l'esperimento.

### <a name="results"></a>Risultati
  

Fare doppio clic sull'output del [Convert to CSV](./convert-to-csv.md)e selezionare una delle opzioni seguenti.  

 + **Result Dataset -> Download**: Consente di aprire immediatamente una copia dei dati in formato CSV che è possibile salvare in una cartella locale. Se non si specifica una cartella, un nome file predefinito viene applicato e il file CSV viene salvato in locale **Scarica** libreria.


 + **Result Dataset -> Salva con nome set di dati**: Salva il file CSV nell'area di lavoro di Azure Machine Learning come un set di dati separato.

 + **Generare il codice di accesso ai dati**: Azure Machine Learning genera due set di codice per accedere ai dati, con Python o R. Per accedere ai dati, copiare il frammento di codice nell'applicazione. (*Genera codice di accesso ai dati sarà disponibile a breve.* )

## <a name="next-steps"></a>Passaggi successivi

Vedere le [set di moduli disponibili](module-reference.md) al servizio Azure Machine Learning. 