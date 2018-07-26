---
title: Eseguire il debug locale del codice di Azure Data Lake Analytics | Microsoft Docs
description: Informazioni su come usare Strumenti Azure Data Lake per Visual Studio per eseguire il debug di processi U-SQL sulla workstation locale.
services: data-lake-analytics
documentationcenter: ''
author: yanancai
manager: jhubbard
editor: cgronlun
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/03/2018
ms.author: yanacai
ms.openlocfilehash: 181512c12c1e72e6aa8205aabd5ea22816d4c5df
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/06/2018
ms.locfileid: "37890813"
---
# <a name="debug-azure-data-lake-analytics-code-locally"></a>Eseguire il debug locale del codice di Azure Data Lake Analytics

È possibile usare Strumenti Azure Data Lake per Visual Studio per eseguire il debug del codice di Azure Data Lake Analytics, esattamente come nel servizio Azure Data Lake.

Informazioni sulla [modalità di esecuzione dello script U-SQL sul computer locale](data-lake-analytics-data-lake-tools-local-run.md).

## <a name="debug-scripts-and-c-assemblies-locally"></a>Eseguire il debug degli script e delle assembly C# in locale

È possibile eseguire il debug degli assembly C# senza inviarli e registrarli al servizio Azure Data Lake Analytics. È possibile impostare dei punti di interruzione sia nei file dietro il codice, sia nel progetto C# a cui si fa riferimento.

### <a name="to-debug-local-code-in-code-behind-file"></a>Per eseguire il debug del codice locale nel file code-behind

1. Impostare dei punti di interruzione nel file dietro il codice.
2. Premere F5 per eseguire il debug dello script in locale.

> [!NOTE]
   > La procedura seguente funziona solo in Visual Studio 2015. Nella versione precedente di Visual Studio potrebbe essere necessario aggiungere manualmente i file .pdb.  
   >
   >

### <a name="to-debug-local-code-in-a-referenced-c-project"></a>Per eseguire il debug del codice locale in un progetto C# a cui si fa riferimento

1. Creare un progetto Assembly C# e compilarlo per generare l’output dll.
2. Registrare la dll utilizzando un'istruzione U-SQL:

        CREATE ASSEMBLY assemblyname FROM @"..\..\path\to\output\.dll";
        
3. Impostare i punti di interruzione nel codice C#.
4. Premere F5 per eseguire il debug dello script con riferimento alla DLL C# in locale.


## <a name="next-steps"></a>Passaggi successivi

- Per visualizzare una query più complessa, vedere [Analizzare i log dei siti Web con Analisi Azure Data Lake](data-lake-analytics-analyze-weblogs.md).
- Per visualizzare i dettagli del processo, vedere [Usare Job Browser e Job View (Visualizzazione processo) per i processi di Azure Data Lake Analytics](data-lake-analytics-data-lake-tools-view-jobs.md).
- Per usare la visualizzazione esecuzioni vertici, vedere [Usare la visualizzazione esecuzioni vertici in Azure Data Lake Tools per Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
