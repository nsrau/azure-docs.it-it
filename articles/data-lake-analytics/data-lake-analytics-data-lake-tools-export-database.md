---
title: Esportare un database U-SQL usando Strumenti Azure Data Lake per Visual Studio | Microsoft Docs
description: Informazioni su come usare Strumenti Azure Data Lake per Visual Studio per esportare un database U-SQL e importarlo automaticamente in un account locale.
services: data-lake-analytics
documentationcenter: 
author: yanancai
manager: 
editor: 
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/27/2017
ms.author: yanacai
ms.openlocfilehash: 441606258f9541c9552925e7c0cbc9b3a9effb4d
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2017
---
# <a name="export-a-u-sql-database"></a>Esportare un database U-SQL

Questo documento spiega come usare [Strumenti Azure Data Lake per Visual Studio](http://aka.ms/adltoolsvs) per esportare un database U-SQL come singolo script U-SQL e le risorse scaricate. Durante lo stesso processo, è possibile importare il database esportato in un account locale.

I clienti usano in genere più ambienti per sviluppo, test e produzione. Questi ambienti sono ospitati sia in un account locale, nel computer locale di uno sviluppatore, che in un account di Azure Data Lake Analytics in Azure. 

Quando si sviluppano e si ottimizzano le query U-SQL negli ambienti di sviluppo e test, gli sviluppatori devono spesso ricreare il proprio lavoro in un database di produzione. La procedura Esportazione guidata database accelera questo processo. Grazie a questa procedura guidata, gli sviluppatori possono clonare l'ambiente di database e i dati di esempio esistenti in altri account di Azure Data Lake Analytics.

## <a name="export-steps"></a>Procedura di esportazione

### <a name="step-1-export-the-database-in-server-explorer"></a>Passaggio 1: Esportare il database in Esplora server

Tutti gli account di Data Lake Analytics per cui si dispone di autorizzazioni sono elencati in Esplora server. Per esportare il database:

1. In Esplora server espandere l'account che contiene il database che si vuole esportare.
2. Fare clic con il pulsante destro del mouse sul database e quindi selezionare **Esporta**. 
   
    ![Esplora server - Esportare un database](./media/data-lake-analytics-data-lake-tools-export-database/export-database.png)

     Se l'opzione di menu **Esporta** non è disponibile, è necessario [aggiornare lo strumento alla versione più recente](http://aka.ms/adltoolsvs).

### <a name="step-2-configure-the-objects-that-you-want-to-export"></a>Passaggio 2: Configurare gli oggetti da esportare

Se si è interessati solo a una piccola parte di un database di grandi dimensioni, è possibile configurare il sottoinsieme di oggetti che si vuole esportare con l'esportazione guidata. 

L'operazione di esportazione viene completata eseguendo un processo U-SQL. L'esportazione da un account di Azure comporta quindi un costo.

![Esportazione guidata database - Selezionare gli oggetti da esportare](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard.png)

### <a name="step-3-check-the-objects-list-and-other-configurations"></a>Passaggio 3: Controllare l'elenco di oggetti e altre configurazioni

In questo passaggio, è possibile verificare gli oggetti selezionati nella casella **Esportare l'elenco di oggetti**. In caso di errore, selezionare **Indietro** per tornare alla pagina precedente e configurare correttamente gli oggetti che si vuole esportare.

È anche possibile configurare altre impostazioni per il database di destinazione dell'esportazione. Le descrizioni di configurazione sono elencate nella tabella seguente:

|Configurazione|Descrizione|
|-------------|-----------|
|Nome destinazione|Questo nome indica dove si vuole salvare le risorse di database esportate. Alcuni esempi sono gli assembly, i file aggiuntivi e i dati di esempio. Verrà creata una cartella con questo nome nella cartella radice dei dati locale.|
|Directory del progetto|Questo percorso definisce dove si vuole salvare lo script U-SQL esportato. Tutte le definizioni di oggetto di database vengono salvate in questa posizione.|
|Solo schema|Se si seleziona questa opzione, vengono esportate solo le definizioni di database e le risorse (ad esempio assembly e file aggiuntivi).|
|Schema e dati|Se si seleziona questa opzione, vengono esportati i dati, le definizioni di database e le risorse. Vengono esportate le prime N righe delle tabelle.|
|Importare automaticamente nel database locale|Se si seleziona questa opzione, il database esportato viene automaticamente importato nel database locale al termine dell'esportazione.|

![Esportazione guidata database - Esportare l'elenco di oggetti e altre configurazioni](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-configuration.png)

### <a name="step-4-check-the-export-results"></a>Passaggio 4: Controllare i risultati dell'esportazione

Al termine dell'esportazione, è possibile visualizzare i risultati esportati nella finestra del log della procedura guidata. L'esempio riportato di seguito illustra come trovare lo script U-SQL e le risorse di database esportati, inclusi gli assembly, i file aggiuntivi e i dati di esempio:

![Esportazione guidata database - Esportare i risultati](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-completed.png)

## <a name="import-the-exported-database-to-a-local-account"></a>Importare il database esportato in un account locale

Il modo più semplice per importare il database esportato consiste nel selezionare la casella di controllo **Importare automaticamente nel database locale** durante il processo di esportazione descritto nel passaggio 3. Se la casella di controllo non è stata selezionata, individuare prima di tutto lo script U-SQL esportato nel registro di esportazione. Eseguire quindi lo script U-SQL localmente per importare il database nell'account locale.

## <a name="import-the-exported-database-to-a-data-lake-analytics-account"></a>Importare il database esportato in un account di Data Lake Analytics

Per importare il database in un account di Azure Data Lake Analytics diverso:

1. Caricare le risorse esportate, inclusi assembly, file aggiuntivi e dati di esempio, nell'account di Azure Data Lake Store predefinito dell'account di Data Lake Analytics in cui si vuole eseguire l'importazione. È possibile trovare la cartella di risorse esportate nella cartella radice dei dati locali. Caricare l'intera cartella nella radice dell'account di Data Lake Store predefinito.
2. Al termine del caricamento, inviare lo script U-SQL esportato all'account di Azure Data Lake Analytics dove si vuole importare il database.

## <a name="known-limitations"></a>Limitazioni note

Se attualmente si seleziona l'opzione **Schema e dati** nel passaggio 3, lo strumento esegue un processo U-SQL per esportare i dati archiviati in tabelle. Per questo motivo, il processo di esportazione dei dati potrebbe risultare lento e prevedere un costo. 

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni sui database U-SQL](https://msdn.microsoft.com/library/azure/mt621299.aspx) 
* [Eseguire test e debug di processi U-SQL tramite l'esecuzione locale e l'SDK U-SQL di Azure Data Lake](data-lake-analytics-data-lake-tools-local-run.md)


