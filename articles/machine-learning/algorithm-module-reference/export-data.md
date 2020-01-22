---
title: 'Esporta dati: riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Export Data (Esporta dati) in Azure Machine Learning per salvare i risultati, i dati intermedi e i dati di lavoro dalle pipeline in destinazioni di archiviazione cloud all'esterno di Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 7f8d3bb6452ee3260e5a89feb37c374418fc6943
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76312159"
---
# <a name="export-data-module"></a>Modulo Export Data

Questo articolo descrive un modulo in Azure Machine Learning Designer.

Usare questo modulo per salvare i risultati, i dati intermedi e i dati di lavoro dalle pipeline in destinazioni di archiviazione cloud all'esterno Azure Machine Learning. 

Questo modulo supporta l'esportazione dei dati nei servizi cloud data seguenti:

- Contenitore BLOB di Azure
- Condivisione file di Azure
- Azure Data Lake
- Azure Data Lake Gen2

Prima di esportare i dati, prima di tutto è necessario registrare un archivio dati nell'area di lavoro Azure Machine Learning. Per ulteriori informazioni, vedere [la pagina relativa alla modalità di accesso ai dati](../how-to-access-data.md).

## <a name="how-to-configure-export-data"></a>Come configurare i dati di esportazione

1. Aggiungere il modulo **Export Data** alla pipeline nella finestra di progettazione. È possibile trovare questo modulo nella categoria **input e output** .

1. Connettere **Esporta dati** al modulo che contiene i dati che si desidera esportare.

1. Selezionare **Esporta dati** per aprire il riquadro **Proprietà** .

1. Per **archivio dati**selezionare un archivio dati esistente dall'elenco a discesa. È anche possibile creare un nuovo archivio dati. Verificare la modalità di [accesso ai dati](../how-to-access-data.md)

1. Definire il percorso nell'archivio dati in cui scrivere i dati. 


1. Per **formato file**selezionare il formato in cui archiviare i dati.
 
1. Eseguire la pipeline.

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 