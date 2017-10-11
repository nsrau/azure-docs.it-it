---
title: Sviluppare assembly U-SQL per processi Azure Data Lake Analytics | Microsoft Docs
description: "Informazioni su come sviluppare assembly da usare più volte in processi di Data Lake Analytics. "
services: data-lake-analytics
documentationcenter: 
author: jejiang
manager: jhubbard
editor: cgronlun
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/30/2016
ms.author: jejiang
ms.openlocfilehash: c49f80f8dcd330d7f46726241e7178351b9cc28f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="develop-u-sql-assemblies-for-azure-data-lake-analytics-jobs"></a>Sviluppare assembly U-SQL per processi di Azure Data Lake Analytics
Informazioni su come attivare code-behind negli assembly da usare più volte nei processi di Data Lake Analytics. 

U-SQL semplifica l'aggiunta di codice personalizzato nei linguaggi .Net, ad esempio C#, VB.Net o F#. È anche possibile distribuire il proprio runtime per supportare altri linguaggi.

Il modo più semplice per usare codice personalizzato consiste nello sfruttare gli strumenti di Data Lake per le funzionalità code-behind di Visual Studio. Per altre informazioni, vedere [Esercitazione: Sviluppare script U-SQL tramite Strumenti di Data Lake per Visual Studio](data-lake-analytics-data-lake-tools-get-started.md). L'uso di code-behind presenta alcuni svantaggi:

- Il codice sorgente viene caricato per ogni invio di script.
- Non è possibile condividere code-behind con altri processi.

Per risolvere questi problemi, è possibile attivare code-behind in assembly e registrare gli assembly nel catalogo di Data Lake Analytics.

## <a name="prerequisites"></a>Prerequisiti
* Visual Studio 2017, Visual Studio 2015, Visual Studio 2013 aggiornamento 4 o Visual Studio 2012 con Visual C++ installato
* Microsoft Azure SDK per .NET versione 2.5 o successiva.  Eseguire l'installazione usando il programma di installazione della piattaforma Web o il programma di installazione di Visual Studio
* Un account di Analisi Data Lake.  Vedere [Introduzione ad Azure Data Lake Analytics con il Portale di Azure](data-lake-analytics-get-started-portal.md).
* Seguire i passaggi dell’esercitazione [Introduzione a U-SQL Studio di Analisi Azure Data Lake](data-lake-analytics-u-sql-get-started.md) .
* Connettersi ad Azure.
* Caricare i dati di origine, vedere [Introduzione a U-SQL Studio di Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md). 

## <a name="develop-assemblies-for-u-sql"></a>Sviluppare assembly per U-SQL

**Per creare e inviare un processo di U-SQL**

1. Scegliere **Nuovo** dal menu **File** e quindi fare clic su **Progetto**.
2. Espandere **Installato**, **Modelli**, **Azure Data Lake**, **U-SQL(ADLA)**, selezionare il modello **Class Library (For U-SQL Application)** (Libreria di classi - Per applicazioni U-SQL) e quindi fare clic su **OK**.
3. Scrivere il codice in Class1.cs.  Di seguito è disponibile un esempio di codice.

        using Microsoft.Analytics.Interfaces;

        namespace USQLApplication_codebehind
        {
            [SqlUserDefinedProcessor]
            public class MyProcessor : IProcessor
            {
                public override IRow Process(IRow input, IUpdatableRow output)
                {
                    output.Set(0, input.Get<string>(0));
                    output.Set(0, input.Get<string>(0));
                    return output.AsReadOnly();
                }
            }
        }
4. Fare clic sul menu **Compila** e quindi fare clic su **Compila soluzione** per creare la dll.

## <a name="register-assemblies"></a>Registrazione di assembly

Vedere [Usare il catalogo Data Lake Analytics(U-SQL)](data-lake-analytics-use-u-sql-catalog.md).


## <a name="use-the-assemblies"></a>Usare gli assembly

Vedere [Usare gli strumenti di Azure Data Lake per Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md).

## <a name="see-also"></a>Vedere anche
* [Introduzione ad Analisi Data Lake di Azure mediante Azure PowerShell](data-lake-analytics-get-started-powershell.md)
* [Introduzione ad Analisi Data Lake mediante il portale di Azure](data-lake-analytics-get-started-portal.md)
* [Utilizzare gli strumenti Data Lake per Visual Studio per lo sviluppo di applicazioni U-SQL](data-lake-analytics-data-lake-tools-get-started.md)
* [Usare il catalogo Data Lake Analytics(U-SQL)](data-lake-analytics-use-u-sql-catalog.md)
* [Usare gli strumenti di Azure Data Lake per Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)