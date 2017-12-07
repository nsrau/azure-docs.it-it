---
title: Come esportare database U-SQL usando Strumenti Azure Data Lake per Visual Studio | Microsoft Docs
description: Informazioni su come usare Strumenti Azure Data Lake per Visual Studio per esportare un database U-SQL e contemporaneamente importarlo in un account locale.
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
ms.openlocfilehash: 9f11e07f7fbaf2b708d6fbc8a746238745132bda
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/29/2017
---
# <a name="how-to-export-u-sql-database"></a>Come esportare un database U-SQL

Questo documento spiega come usare [Strumenti Azure Data Lake per Visual Studio](http://aka.ms/adltoolsvs) per esportare un database U-SQL come singolo script U-SQL con risorse scaricate. Durante lo stesso processo, è possibile anche importare il database esportato nell'account locale.

I clienti hanno in genere più ambienti per sviluppo, test e produzione. Questi ambienti sono ospitati sia in un account locale nel computer locale degli sviluppatori che nell'account di Azure Data Lake Analytics in Azure. Quando sviluppano e ottimizzano le query U-SQL in ambienti di sviluppo e test, gli sviluppatori devono in genere ricreare tutto nel database di produzione. L'**Esportazione guidata database** aiuta ad accelerare questo processo. Usando la procedura guidata, gli sviluppatori possono clonare l'ambiente di database esistente e i dati di esempio esistenti in altri account di Azure Data Lake Analytics.

## <a name="export-steps"></a>Procedura di esportazione

### <a name="step-1-right-click-the-database-in-server-explorer-and-click-export"></a>Passaggio 1: Fare clic con il pulsante destro del mouse sul database in Esplora server e scegliere "Esporta"

Tutti gli account di Azure Data Lake Analytics per cui si hanno le autorizzazioni sono elencati in Esplora server. Espandere l'account contenente il database da esportare, fare clic con il pulsante destro del mouse sul database e scegliere **Esporta**. Se il menu di scelta rapida non viene visualizzato, è necessario [aggiornare lo strumento all'ultima versione](http://aka.ms/adltoolsvs).

![Esportazione database in Strumenti Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-export-database/export-database.png)

### <a name="step-2-configure-the-objects-you-want-to-export"></a>Passaggio 2: Configurare gli oggetti da esportare

Talvolta un database è grande, ma è sufficiente una piccola parte di esso, quindi è possibile configurare il subset di oggetti da esportare nell'esportazione guidata. Si noti che l'operazione di esportazione viene completata eseguendo un processo U-SQL e quindi l'esportazione dall'account di Azure comporta un costo.

![Esportazione guidata database di Strumenti Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard.png)

### <a name="step-3-check-the-objects-list-and-more-configurations"></a>Passaggio 3: Controllare l'elenco di oggetti e altre configurazioni

In questo passaggio è possibile controllare gli oggetti selezionati nella parte superiore della finestra di dialogo. Se ci sono errori, è possibile fare clic su Indietro per tornare indietro e configurare di nuovo gli oggetti da esportare.

È anche possibile definire altre configurazioni per la destinazione di esportazione. Le descrizioni di queste configurazioni sono elencate nella tabella seguente:

|Configurazione|Descrizione|
|-------------|-----------|
|Nome destinazione|Questo nome indica dove si vuole salvare le risorse di database esportate, ad esempio assembly, file aggiuntivi e dati di esempio. Verrà creata una cartella con questo nome nella cartella radice dei dati locale.|
|Directory del progetto|Questo percorso indica dove si vuole salvare lo script U-SQL esportato, che include tutte le definizioni degli oggetti di database.|
|Solo schema|Se si seleziona questa opzione, vengono esportate solo le definizioni di database e le risorse (ad esempio assembly e file aggiuntivi).|
|Schema e dati|Se si seleziona questa opzione, vengono esportati le definizioni di database, le risorse e i dati. Vengono esportate le prime N righe delle tabelle.|
|Importare automaticamente nel database locale|Selezionando questa configurazione, al termine dell'esportazione il database esportato verrà importato automaticamente nel database locale.|

![Configurazione dell'Esportazione guidata database di Strumenti Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-configuration.png)

### <a name="step-4-check-the-export-results"></a>Passaggio 4: Controllare i risultati dell'esportazione

Dopo avere definito le impostazioni ed eseguito l'esportazione, è possibile trovare i risultati nella finestra di log della procedura guidata. Tramite il log contrassegnato da un rettangolo rosso nella schermata seguente, è possibile trovare il percorso dello script U-SQL e delle risorse di database, inclusi assembly, file aggiuntivi e dati di esempio, esportati.

![Completamento dell'Esportazione guidata database di Strumenti Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-completed.png)

## <a name="how-to-import-the-exported-database-to-local-account"></a>Come importare il database esportato in un account locale

Il modo più comodo per eseguire questa operazione consiste nel selezionare l'opzione **Importare automaticamente nel database locale** durante il processo di esportazione nel passaggio 3. Se ci si dimentica di farlo, è possibile trovare lo script U-SQL esportato tramite il log di esportazione ed eseguire lo script U-SQL localmente per importare il database nell'account locale.

## <a name="how-to-import-the-exported-database-to-azure-data-lake-analytics-account"></a>Come importare il database esportato nell'account di Azure Data Lake Analytics

Per importare il database in un altro account di Azure Data Lake Analytics sono necessari due passaggi:

1. Caricare le risorse esportate, inclusi assembly, file aggiuntivi e dati di esempio, nell'account di Azure Data Lake Store predefinito dell'account di Azure Data Lake Analytics in cui si vuole eseguire l'importazione. È possibile trovare la cartella delle risorse esportate nella cartella radice dei dati locale e caricare l'intera cartella nella radice dell'account di archivio predefinito.
2. Inviare lo script U-SQL esportato nell'account di Azure Data Lake Analytics dove si vuole importare il database al termine del caricamento.

## <a name="known-limitation"></a>Limitazioni note

Attualmente, se si seleziona **Schema e dati** nella procedura guidata, lo strumento esegue un processo U-SQL per esportare i dati archiviati nelle tabelle. Per questo motivo, il processo di esportazione dei dati potrebbe essere lento e comportare un costo. 

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni sul database U-SQL](https://msdn.microsoft.com/library/azure/mt621299.aspx) 
* [Eseguire test e debug di processi U-SQL tramite l'esecuzione locale e l'SDK U-SQL di Azure Data Lake](data-lake-analytics-data-lake-tools-local-run.md)


