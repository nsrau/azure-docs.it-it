---
title: Eseguire il debug locale del codice di Azure Data Lake Analytics
description: Informazioni su come usare Strumenti Azure Data Lake per Visual Studio per eseguire il debug di processi U-SQL sulla workstation locale.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 07/03/2018
ms.openlocfilehash: 0827311218202de447e5cf27356e00c4da020e94
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61472992"
---
# <a name="debug-azure-data-lake-analytics-code-locally"></a>Eseguire il debug locale del codice di Azure Data Lake Analytics

È possibile usare Strumenti Azure Data Lake per Visual Studio per eseguire il codice di Azure Data Lake Analytics ed effettuarne il debug sulla workstation locale, esattamente come nel servizio Azure Data Lake Analytics.

Informazioni sulla [modalità di esecuzione dello script U-SQL sul computer locale](data-lake-analytics-data-lake-tools-local-run.md).

## <a name="debug-scripts-and-c-assemblies-locally"></a>Eseguire il debug degli script e delle assembly C# in locale

È possibile eseguire il debug degli assembly C# senza inviarli e registrarli nel servizio Azure Data Lake Analytics. È possibile impostare i punti di interruzione sia nel file code-behind sia in un progetto C# a cui viene fatto riferimento.

### <a name="debug-local-code-in-a-code-behind-file"></a>Eseguire il debug del codice locale in un file code-behind

1. Impostare i punti di interruzione nel file code-behind.
2. Premere **F5** per eseguire il debug dello script in locale.

> [!NOTE]
   > La procedura seguente funziona solo in Visual Studio 2015. Nelle versioni precedenti di Visual Studio potrebbe essere necessario aggiungere manualmente i file **PDB**.  
   >
   >

### <a name="debug-local-code-in-a-referenced-c-project"></a>Eseguire il debug del codice locale in un progetto C# a cui viene fatto riferimento

1. Creare un progetto di assembly C# e compilarlo per generare il file **DLL** di output.
2. Registrare il file **DLL** usando un'istruzione U-SQL:

        CREATE ASSEMBLY assemblyname FROM @"..\..\path\to\output\.dll";
        
3. Impostare i punti di interruzione nel codice C#.
4. Premere **F5** per eseguire il debug dello script facendo riferimento al file **DLL** C# in locale.


## <a name="next-steps"></a>Passaggi successivi

- Per un esempio di query più complessa, vedere [Analizzare i log dei siti Web con Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
- Per visualizzare i dettagli del processo, vedere [Usare Job Browser e Job View (Visualizzazione processo) per i processi di Azure Data Lake Analytics](data-lake-analytics-data-lake-tools-view-jobs.md).
- Per usare la visualizzazione esecuzioni vertici, vedere [Usare la visualizzazione esecuzioni vertici in Azure Data Lake Tools per Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
