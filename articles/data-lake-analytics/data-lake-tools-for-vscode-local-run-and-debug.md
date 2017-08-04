---
title: Strumenti Azure Data Lake per Visual Studio - Esecuzione locale e debug locale di U-SQL con Visual Studio Code | Microsoft Docs
description: Informazioni su come usare gli strumenti Azure Data Lake per Visual Studio Code per l'esecuzione locale e il debug locale.
Keywords: VScode,strumenti Azure Data Lake,Esecuzione locale,Debug locale,Debug Locale,anteprima file di archiviazione,caricamento nel percorso di archiviazione
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
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: e5e767a6f31be052c9bae059b49b31d59fdfd894
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---

# <a name="u-sql-local-run-and-local-debug-with-visual-studio-code"></a>Esecuzione locale e debug locale di U-SQL con Visual Studio Code

## <a name="prerequisites"></a>Prerequisiti
- Strumenti di Azure Data Lake per Visual Studio Code. Per istruzioni, vedere [Usare Strumenti Azure Data Lake per Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md).
- C# per Visual Studio Code (per eseguire il debug locale di U-SQL).

   ![Gli Strumenti Azure Data Lake per Visual Studio Code installano vscodeCsharp](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-install-ms-vscodecsharp.png)
   
   > [!NOTE]
   > Attualmente le funzionalità di esecuzione locale e debug locale di U-SQL supportano solo gli utenti di Windows. 


## <a name="set-up-u-sql-local-run-environment"></a>Configurare un ambiente di esecuzione locale di U-SQL

1. Aprire il riquadro comandi premendo **CTRL+MAIUSC+P**, quindi digitare **ADL: Download LocalRun Dependency** (ADL: scarica dipendenza esecuzione locale) per scaricare i pacchetti.  

   ![DownloadLocalRun](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/DownloadLocalRun.png)

2. Trovare i pacchetti di dipendenza nel percorso visualizzato nel pannello di output in basso, quindi installare BuildTools e Win10SDK 10240. ad esempio:  
`C:\Users\xxx\.vscode\extensions\usqlextpublisher.usql-vscode-ext-x.x.x\LocalRunDependency
`  
  ![LocateDependency](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/LocateDependencyPath.png)

- Installare BuildTools: seguire le istruzioni della procedura guidata per completare l'installazione.   

  ![InstallBuildTools](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallBuildTools.png)
- Installare Win10SDK 10240: seguire le istruzioni per completare l'installazione.  

  ![InstallWin10SDK](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallWin10SDK.png)
3. Impostare la variabile di ambiente: impostare **SCOPE_CPP_SDK** su:  
`C:\Users\xxx\.vscode\extensions\usqlextpublisher.usql-vscode-ext-x.x.x\LocalRunDependency\CppSDK_3rdparty
`  
Riavviare il sistema operativo per attivare le impostazioni della variabile di ambiente.  

   ![ConfigSCOPE_CPP_SDK](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/ConfigScopeCppSDk.png)

## <a name="start-local-run-service-and-submit-u-sql-job-to-local-account"></a>Avviare il servizio di esecuzione locale e inviare il processo di U-SQL all'account locale 
Ai nuovi utenti viene richiesto di eseguire **ADL: Download LocalRun Dependency** (ADL: scarica dipendenza esecuzione locale) per scaricare i pacchetti, se l'operazione non è ancora stata eseguita.
1. Premere **CTRL+MAIUSC+P** per aprire il riquadro comandi e immettere **ADL: Start Local Run Service** (ADL: avvia servizio di esecuzione locale).
2. Dato che si accede per la prima volta, accettare il Contratto di licenza (EULA). 

   ![Accettare il Contratto di licenza ](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/AcceptEULA.png)   
3. Viene visualizzata la console dei comandi. Dato che si accede per la prima volta immettere 3, quindi specificare un percorso di cartella locale per l'input e l'output di dati. Per le altre opzioni è sufficiente usare il valore predefinito. 

   ![Console dei comandi per esecuzione locale di Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-cmd.png)
4. Premere CTRL+MAIUSC+P per aprire il riquadro comandi, immettere **ADL: Submit Job** (ADL: Invia processo), quindi selezionare **Local** (Locale) per inviare il processo all'account locale.

   ![Selezionare Local (Locale) in Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-select-local.png)
5. Dopo l'invio del processo è possibile visualizzare i dettagli di invio facendo clic su jobUrl nella finestra di output. È anche possibile visualizzare lo stato di invio del processo nella console dei comandi: digitare 7 per altre informazioni sul processo.

   ![Output dell'esecuzione locale di Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-result.png)
   ![Stato comandi dell'esecuzione locale di Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-localrun-cmd-status.png) 



## <a name="start-local-debug-for-u-sql-job"></a>Avviare il debug locale per il processo U-SQL  
Ai nuovi utenti viene richiesto di eseguire **ADL: Download LocalRun Dependency** (ADL: scarica dipendenza esecuzione locale) per scaricare i pacchetti, se l'operazione non è ancora stata eseguita.
  
1. Premere **CTRL+MAIUSC+P** per aprire il riquadro comandi e immettere **ADL: Start Local Run Service** (ADL: avvia servizio di esecuzione locale). Viene visualizzata la finestra dei comandi. Verificare che **DataRoot** sia impostata.
3. Impostare un punto di interruzione nel codice C# sottostante.
4. Tornare all'editor di script, premere **CTRL+MAIUSC+P** per aprire il riquadro comandi e immettere **Local Debug** (Debug locale) per avviare il servizio di debug locale.

![Risultati debug locale di Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-debug-result.png)


## <a name="next-steps"></a>Passaggi successivi
- Per informazioni sull'uso di Strumenti Azure Data Lake per Visual Studio Code, vedere [Usare Strumenti Azure Data Lake per Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md).
- Per le informazioni introduttive su Data Lake Analytics, vedere [Esercitazione: Introduzione a Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md).
- Per informazioni sull'uso degli strumenti di Data Lake per Visual Studio, vedere [Esercitazione: Sviluppare script U-SQL tramite Strumenti di Data Lake per Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Per informazioni sullo sviluppo di assembly, vedere [Develop U-SQL assemblies for Azure Data Lake Analytics jobs](data-lake-analytics-u-sql-develop-assemblies.md) (Sviluppare assembly U-SQL per i processi di Azure Data Lake Analytics).
