---
title: Strumenti Azure Data Lake per Visual Studio - Esecuzione locale e debug locale di U-SQL con Visual Studio Code | Microsoft Docs
description: Informazioni su come usare Strumenti Azure Data Lake per Visual Studio Code per l'esecuzione locale e il debug locale.
Keywords: VScode, Strumenti Azure Data Lake, Esecuzione locale, debug locale, Debug Locale, anteprima file di archiviazione, caricamento nel percorso di archiviazione
services: data-lake-analytics
documentationcenter: 
author: jejiang
manager: DJ
editor: jejiang
tags: azure-portal
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: big-data
ms.date: 07/14/2017
ms.author: jejiang
ms.openlocfilehash: 367e4ba792f83d6ee246208306e4c09b69cb49ef
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2017
---
# <a name="u-sql-local-run-and-local-debug-with-visual-studio-code"></a>Esecuzione locale e debug locale di U-SQL con Visual Studio Code

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare queste procedure, verificare che i prerequisiti seguenti siano disponibili:
- Strumenti di Azure Data Lake per Visual Studio Code. Per istruzioni, vedere [Usare Strumenti Azure Data Lake per Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md).
- C# per Visual Studio Code (per eseguire il debug locale di U-SQL).

   ![Installare C# in Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-install-ms-vscodecsharp.png)
   
   > [!NOTE]
   > Attualmente le funzionalità di esecuzione locale e debug locale di U-SQL supportano solo gli utenti di Windows. 


## <a name="set-up-the-u-sql-local-run-environment"></a>Configurare un ambiente di esecuzione locale di U-SQL

1. Premere CTRL+MAIUSC+P per aprire il riquadro comandi, quindi digitare **ADL: Download LocalRun Dependency** (ADL: scarica dipendenza esecuzione locale) per scaricare i pacchetti.  

   ![Scaricare i pacchetti di dipendenza ADL LocalRun](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/DownloadLocalRun.png)

2. Trovare i pacchetti di dipendenza nel percorso visualizzato nel pannello **Output**, quindi installare BuildTools e Win10SDK 10240. Di seguito è fornito un esempio di percorso:  
`C:\Users\xxx\.vscode\extensions\usqlextpublisher.usql-vscode-ext-x.x.x\LocalRunDependency
`  
  ![Individuare i pacchetti di dipendenza](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/LocateDependencyPath.png)

   a. Per installare BuildTools, seguire le istruzioni della procedura guidata.   

  ![Installare BuildTools](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallBuildTools.png)

   b. Per installare Win10SDK 10240, seguire le istruzioni della procedura guidata.  

  ![Installare Win10SDK 10240](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallWin10SDK.png)

3. Configurare la variabile di ambiente. Impostare la variabile di ambiente **SCOPE_CPP_SDK** per:  
`C:\Users\xxx\.vscode\extensions\usqlextpublisher.usql-vscode-ext-x.x.x\LocalRunDependency\CppSDK_3rdparty
`  
4. Riavviare il sistema operativo per assicurarsi che le impostazioni della variabile di ambiente abbiano effetto.  

   ![Assicurarsi che la variabile di ambiente SCOPE_CPP_SDK sia installata](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/ConfigScopeCppSDk.png)

## <a name="start-the-local-run-service-and-submit-the-u-sql-job-to-a-local-account"></a>Avviare il servizio di esecuzione locale e inviare il processo U-SQL a un account locale 
Al primo utilizzo viene chiesto di scaricare i pacchetti di dipendenza LocalRun se non sono già installati.
1. Premere CTRL+MAIUSC+P per aprire il riquadro comandi e immettere **ADL: Start Local Run Service** (ADL: avvia servizio di esecuzione locale).
2. Selezionare **Accetto** per accettare le condizioni di licenza software Microsoft per la prima volta. 

   ![Accettare le condizioni di licenza software Microsoft](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/AcceptEULA.png)   
3. Viene aperta la console dei comandi. Dato che si accede per la prima volta immettere **3** e quindi individuare il percorso di cartella locale per l'input e l'output di dati. Per le altre opzioni è possibile usare il valore predefinito. 

   ![Console dei comandi per esecuzione locale di Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-cmd.png)
4. Premere CTRL+MAIUSC+P per aprire il riquadro comandi, immettere **ADL: Submit Job** (ADL: Invia processo), quindi selezionare **Local** (Locale) per inviare il processo all'account locale.

   ![Selezionare Local (Locale) in Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-select-local.png)
5. Dopo aver inviato il processo è possibile visualizzare i dettagli di invio. Per visualizzare i dettagli di invio selezionare **jobUrl** nella finestra **Output**. È possibile visualizzare lo stato di invio del processo anche dalla console dei comandi. Immettere **7** nella console dei comandi se si desidera conoscere ulteriori dettagli del processo.

   ![Output dell'esecuzione locale di Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-result.png)
   ![Stato comandi dell'esecuzione locale di Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-localrun-cmd-status.png) 


## <a name="start-a-local-debug-for-the-u-sql-job"></a>Avviare il debug locale per il processo U-SQL  
Al primo utilizzo viene chiesto di scaricare i pacchetti di dipendenza LocalRun se non sono già installati.
  
1. Premere CTRL+MAIUSC+P per aprire il riquadro comandi e immettere **ADL: Start Local Run Service** (ADL: avvia servizio di esecuzione locale). Viene aperta la console dei comandi. Assicurarsi che **DataRoot** sia impostata.
3. Impostare un punto di interruzione nel code-behind C#.
4. Tornare all'editor di script, premere CTRL+MAIUSC+P per aprire il riquadro comandi e immettere **Local Debug** (Debug locale) per avviare il servizio di debug locale.

![Risultati debug locale di Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-debug-result.png)


## <a name="next-steps"></a>Passaggi successivi
- Per informazioni sull'uso di Strumenti Azure Data Lake per Visual Studio Code, vedere [Usare Strumenti Azure Data Lake per Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md).
- Per informazioni introduttive su Data Lake Analytics, vedere [Esercitazione: Introduzione a Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md).
- Per informazioni sull'uso degli strumenti di Data Lake per Visual Studio, vedere [Esercitazione: Sviluppare script U-SQL tramite Strumenti di Data Lake per Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Per informazioni sullo sviluppo di assembly, vedere [Develop U-SQL assemblies for Azure Data Lake Analytics jobs](data-lake-analytics-u-sql-develop-assemblies.md) (Sviluppare assembly U-SQL per i processi di Azure Data Lake Analytics).
