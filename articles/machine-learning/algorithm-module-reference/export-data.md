---
title: 'Esporta dati: Riferimento modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Esporta dati in Azure Machine Learning per salvare risultati, dati intermedi e dati di lavoro dalle pipeline in destinazioni di archiviazione cloud all'esterno di Azure Machine Learning.Learn how to use the Export Data module in Azure Machine Learning to save results, intermediate data, and working data from your pipelines into cloud storage destinations outside Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 807771fd4018c9666f059c965370ebc36d0105df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456302"
---
# <a name="export-data-module"></a>Modulo Esporta dati

Questo articolo descrive un modulo nella finestra di progettazione di Azure Machine Learning (anteprima).

Usare questo modulo per salvare risultati, dati intermedi e dati di lavoro dalle pipeline in destinazioni di archiviazione cloud. 

Questo modulo supporta l'esportazione dei dati nei seguenti servizi dati cloud:

- Contenitore BLOB di AzureAzure Blob Container
- Condivisione file di Azure
- Azure Data Lake
- Azure Data Lake Gen2

Prima di esportare i dati, è necessario registrare un archivio dati nell'area di lavoro di Azure Machine Learning.Before exporting your data, you need to first register a datastore in your Azure Machine Learning workspace. Per altre informazioni, vedere [Accedere ai dati nei servizi di archiviazione](../how-to-access-data.md)di Azure.For more information, see Access data in Azure storage services.

## <a name="how-to-configure-export-data"></a>Come configurare Esporta dati

1. Aggiungere il modulo **Esporta dati** alla pipeline nella finestra di progettazione. È possibile trovare questo modulo nella categoria **Input e output.**

1. Connettere **i dati** di esportazione al modulo che contiene i dati che si desidera esportare.

1. Selezionare Esporta dati per aprire il riquadro **Proprietà.Select** **Export Data** to open the Properties pane.

1. Per **Datastore**, selezionare un archivio dati esistente dall'elenco a discesa. È inoltre possibile creare un nuovo archivio dati. Verificare come visitando i dati di [Access nei servizi di archiviazione](../how-to-access-data.md)di Azure .

1. La casella di controllo **Rigenera output**, decide se eseguire il modulo per rigenerare l'output in fase di esecuzione. 

    Per impostazione predefinita è deselezionata, il che significa che se il modulo è stato eseguito con gli stessi parametri in precedenza, il sistema riutilizzerà l'output dell'ultima esecuzione per ridurre la fase di esecuzione. 

    Se è selezionata, il sistema eseguirà nuovamente il modulo per rigenerare l'output.

1. Definire il percorso nell'archivio dati in cui si trovano i dati. Il percorso è un percorso relativo. I percorsi vuoti o i percorsi URL non sono consentiti.


1. Per **Formato file**, selezionare il formato in cui archiviare i dati.
 
1. Inviare la pipeline.

## <a name="next-steps"></a>Passaggi successivi

Vedere il set di moduli disponibili per Azure Machine Learning.See the [set of modules available](module-reference.md) to Azure Machine Learning. 
