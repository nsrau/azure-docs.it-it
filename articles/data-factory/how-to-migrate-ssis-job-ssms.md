---
title: Eseguire la migrazione dei processi di SQL Server Integration Services (SSIS) locali in Azure Data FactoryMigrate on-premises SQL Server Integration Services (SSIS) jobs to Azure Data Factory
description: Questo articolo descrive come eseguire la migrazione dei processi di SQL Server Integration Services (SSIS) alle pipeline/attività/trigger di Azure Data Factory tramite SQL Server Management StudioSQL Server Management Studio.This article describes how to migrate SQL Server Integration Services (SSIS) jobs to Azure Data Factory pipelines/activities/triggers by using SQL Server Management StudioSQL Server Management Studio.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 4/7/2020
ms.openlocfilehash: 6e357e98d6c5190c6dfef675dc1ab9cf30a717c1
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81455088"
---
# <a name="migrate-sql-server-agent-jobs-to-adf-with-ssms"></a>Eseguire la migrazione dei processi di SQL Server Agent ad ADF con SSMSMigrate SQL Server Agent jobs to ADF with SSMS

Quando si esegue la migrazione dei carichi di lavoro di [SQL Server Integration Services (SSIS) locali a SSIS in ADF](scenario-ssis-migration-overview.md), dopo la migrazione dei pacchetti SSIS, è possibile eseguire la migrazione batch dei processi di SQL Server Agent con il tipo di passaggio di SQL Server Integration Services Package a Azure Data Factory (ADF) pipeline/attività/trigger di pianificazione tramite LA Migrazione guidata processo **SSIS**di SQL Server Management Studio (SSMS). .

In generale, per i processi di SQL agent selezionati con tipi di passaggio di processo applicabili, la **Migrazione guidata processo SSIS** può:In general, for selected SQL agent jobs with applicable job step types, SSIS Job Migration Wizard can:

- eseguire il mapping del percorso del pacchetto SSIS locale al punto in cui viene eseguita la migrazione dei pacchetti, accessibili da SSIS in ADF.
    > [!NOTE]
    > Il percorso del pacchetto del file system è supportato solo.
- eseguire la migrazione dei processi applicabili con i passaggi di processo applicabili alle risorse ADF corrispondenti come indicato di seguito:

|Oggetto processo di SQL Agent  |Risorsa ADF  |Note|
|---------|---------|---------|
|Processo di SQL Agent|pipeline     |Il nome della pipeline verrà *generato per \<il nome *del processo>. <br> <br> I processi agente incorporati non sono applicabili: <li> Processo di manutenzione del server SSISSSIS Server Maintenance Job <li> syspolicy_purge_history <li> collection_set_ <li> mdw_purge_data_ <li> sysutility_' di lavoro|
|Passaggio del processo SSISSSIS job step|Eseguire l'attività del pacchetto SSISExecute SSIS package activity|<li> Il nome dell'attività sarà \<il nome del passaggio>. <li> L'account proxy utilizzato nel passaggio di processo verrà migrato come autenticazione di Windows di questa attività. <li> *Le opzioni di esecuzione,* ad eccezione di Usa runtime a *32 bit* definito nel passaggio di processo, verranno ignorate durante la migrazione. <li> *La verifica* definita nel passaggio di processo verrà ignorata durante la migrazione.|
|schedule      |trigger pianifica        |Il nome del trigger di pianificazione verrà *Generato per \<il nome della pianificazione>*. <br> <br> Le opzioni seguenti nella pianificazione dei processi di SQL Agent verranno ignorate durante la migrazione:Below options in SQL Agent job schedule will be ignored in migration: <li> Intervallo di secondo livello. <li> *Avvia automaticamente all'avvio di SQL Server Agent* <li> *Avvia quando la CPU risulta inattiva* <li> *giorno della settimana* e *del fine settimana*<time zone> <br> Di seguito sono riportate le differenze dopo la migrazione della pianificazione dei processi di SQL Agent al trigger di pianificazione ADF: <li> L'esecuzione successiva di ADF Schedule Trigger è indipendente dallo stato di esecuzione dell'esecuzione attivata dall'antecedente. <li> La configurazione della ricorrenza di ADF Schedule Trigger è diversa dalla frequenza giornaliera nel processo dell'agente SQL.|

- generare modelli di Azure Resource Manager (ARM) nella cartella di output locale e distribuirli manualmente in Data Factory.Generate Azure Resource Manager (ARM) templates in local output folder, and deploy to data factory directly or later manually. Per ulteriori informazioni sui modelli di Gestione risorse ADF, vedere [Tipi di risorse Microsoft.DataFactory](https://docs.microsoft.com/azure/templates/microsoft.datafactory/allversions).

## <a name="prerequisites"></a>Prerequisiti

La funzionalità descritta in questo articolo richiede SQL Server Management Studio versione 18.5 o successiva. Per ottenere la versione più recente di SSMS, vedere [Scaricare SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15).

## <a name="migrate-ssis-jobs-to-adf"></a>Eseguire la migrazione dei processi SSIS ad ADFMigrate SSIS jobs to ADF

1. In SSMS, in Esplora oggetti, selezionare Agente SQL Server, selezionare Processi, quindi fare clic con il pulsante destro del mouse e selezionare **Esegui migrazione processi SSIS in ADF**.
![Menu](media/how-to-migrate-ssis-job-ssms/menu.png)

1. Accedere ad Azure, selezionare Sottoscrizione di Azure, Data Factory e Runtime di integrazione. Archiviazione di Azure è facoltativo, usato nel passaggio di mapping del percorso del pacchetto se i processi SSIS di cui eseguire la migrazione dispongono di pacchetti del file system SSIS.
![Menu](media/how-to-migrate-ssis-job-ssms/step1.png)

1. Eseguire il mapping dei percorsi dei pacchetti SSIS e dei file di configurazione nei processi SSIS ai percorsi di destinazione a cui possono accedere le pipeline migrate. In questo passaggio di mapping è possibile:In this mapping step, you can:

    1. Selezionare una cartella di origine, quindi **Aggiungi mapping**.
    1. Aggiornare il percorso della cartella di origine. I percorsi validi sono i percorsi delle cartelle o i percorsi delle cartelle padre dei pacchetti.
    1. Aggiornare il percorso della cartella di destinazione. L'impostazione predefinita è il percorso relativo dell'account di archiviazione predefinito, selezionato nel passaggio 1.Default path the default Storage account, which is selected in step 1.
    1. Eliminare un mapping selezionato tramite **Elimina mapping**.
![passo2](media/how-to-migrate-ssis-job-ssms/step2.png)
![passo2-1](media/how-to-migrate-ssis-job-ssms/step2-1.png)

1. Selezionare i processi applicabili di cui eseguire la migrazione e configurare le impostazioni *dell'attività del pacchetto SSIS eseguito*corrispondente.

    - *Impostazione predefinita*, si applica a tutti i passaggi selezionati per impostazione predefinita. Per ulteriori informazioni su ogni proprietà, vedere *scheda Impostazioni* per l'attività Esegui [pacchetto SSIS](how-to-invoke-ssis-package-ssis-activity.md) quando il percorso del pacchetto è *File System (pacchetto)*.
    ![step3-1](media/how-to-migrate-ssis-job-ssms/step3-1.png)
    - *Step Setting*, configurare l'impostazione per un passaggio selezionato.
        
        **Applica impostazione predefinita**: l'impostazione predefinita è selezionata. Deselezionare per configurare l'impostazione solo per il passaggio selezionato.  
        Per ulteriori informazioni su altre proprietà, vedere *scheda Impostazioni* per l'attività Esegui [pacchetto SSIS](how-to-invoke-ssis-package-ssis-activity.md) quando il percorso del pacchetto è *File System (pacchetto)*.
    ![step3-2](media/how-to-migrate-ssis-job-ssms/step3-2.png)

1. Generare e distribuire il modello ARM.
    1. Selezionare o immettere il percorso di output per i modelli ARM delle pipeline ADF migrate. La cartella verrà creata automaticamente se non esiste.
    2. Selezionare l'opzione **Distribuisci modelli ARM alla data factory:**
        - L'impostazione predefinita è deselezionata. È possibile distribuire manualmente i modelli ARM generati in un secondo momento.
        - Selezionare questa casella di controllo per distribuire direttamente i modelli ARM generati nella data factory.
    ![passaggio4](media/how-to-migrate-ssis-job-ssms/step4.png)

1. Eseguire la migrazione, quindi controllare i risultati.
![step5 (5)](media/how-to-migrate-ssis-job-ssms/step5.png)

## <a name="next-steps"></a>Passaggi successivi

[Esegui e monitora pipeline](how-to-invoke-ssis-package-ssis-activity.md)
