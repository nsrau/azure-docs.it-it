---
title: Eseguire la migrazione di processi SQL Server Integration Services locali (SSIS) a Azure Data Factory
description: In questo articolo viene descritto come eseguire la migrazione di processi SQL Server Integration Services (SSIS) a Azure Data Factory pipeline/attività/trigger utilizzando SQL Server Management Studio.
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
ms.openlocfilehash: b27fe2abc50396b527e61487acf9797db59c1cce
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82627586"
---
# <a name="migrate-sql-server-agent-jobs-to-adf-with-ssms"></a>Eseguire la migrazione di processi SQL Server Agent ad ADF con SSMS

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Quando si esegue la migrazione di [carichi di lavoro di SQL Server Integration Services (SSIS) locali a SSIS in ADF](scenario-ssis-migration-overview.md), dopo la migrazione dei pacchetti SSIS, è possibile eseguire la migrazione in batch di processi SQL Server Agent con il tipo di passaggio del processo di SQL Server Integration Services di pacchetti per Azure Data Factory (ADF) pipeline/attività/pianificazione dei trigger tramite la **migrazione guidata processi SSIS**di SQL Server Management Studio (SSMS).

In generale, per i processi di SQL Agent selezionati con i tipi di passaggio di processo applicabili, **migrazione guidata processi SSIS** può:

- mappare il percorso del pacchetto SSIS locale a cui viene eseguita la migrazione dei pacchetti, accessibili da SSIS in ADF.
    > [!NOTE]
    > Il percorso del pacchetto del file System è supportato solo.
- eseguire la migrazione dei processi applicabili con i passaggi di processo applicabili alle risorse ADF corrispondenti come riportato di seguito:

|Oggetto processo di SQL Agent  |Risorsa ADF  |Note|
|---------|---------|---------|
|Processo di SQL Agent|pipeline     |Nome della pipeline *per \<job name> *cui verrà generato il. <br> <br> I processi di Agent predefiniti non sono applicabili: <li> Processo di manutenzione del server SSIS <li> syspolicy_purge_history <li> collection_set_ * <li> mdw_purge_data_ * <li> sysutility_ *|
|Passaggio del processo SSIS|Attività Esegui pacchetto SSIS|<li> Il nome dell'attività sarà \<step name> . <li> Verrà eseguita la migrazione dell'account proxy utilizzato nel passaggio del processo come autenticazione di Windows per questa attività. <li> Le *Opzioni di esecuzione eccetto l'* uso del runtime a *32 bit* definito nel passaggio del processo verranno ignorate durante la migrazione. <li> La *Verifica* definita nel passaggio del processo verrà ignorata nella migrazione.|
|schedule      |trigger pianifica        |Nome del trigger di pianificazione che verrà *generato per \<schedule name> *. <br> <br> Le opzioni seguenti nella pianificazione del processo di SQL Agent verranno ignorate durante la migrazione: <li> Intervallo di secondo livello. <li> *Avvia automaticamente all'avvio di SQL Server Agent* <li> *Avvia quando la CPU risulta inattiva* <li> giorno della *settimana e* *giorno festivo*<time zone> <br> Di seguito sono riportate le differenze dopo la migrazione della pianificazione del processo di SQL Agent al trigger di pianificazione di ADF: <li> L'esecuzione successiva del trigger di pianificazione di ADF è indipendente dallo stato di esecuzione dell'esecuzione avviata dall'attività precedente. <li> La configurazione della ricorrenza del trigger di pianificazione di ADF è diversa dalla frequenza giornaliera nel processo di SQL Agent.|

- generare modelli di Azure Resource Manager (ARM) nella cartella di output locale e distribuirli in data factory manualmente o in un secondo momento. Per ulteriori informazioni sui modelli di Gestione risorse ADF, vedere [tipi di risorse Microsoft. DataFactory](https://docs.microsoft.com/azure/templates/microsoft.datafactory/allversions).

## <a name="prerequisites"></a>Prerequisiti

La funzionalità descritta in questo articolo richiede SQL Server Management Studio versione 18,5 o successiva. Per ottenere la versione più recente di SSMS, vedere [Scaricare SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15).

## <a name="migrate-ssis-jobs-to-adf"></a>Eseguire la migrazione di processi SSIS ad ADF

1. In Esplora oggetti di SSMS selezionare SQL Server Agent, selezionare processi, quindi fare clic con il pulsante destro del mouse e scegliere **Esegui migrazione di processi SSIS ad ADF**.
![menu](media/how-to-migrate-ssis-job-ssms/menu.png)

1. Accedere ad Azure, selezionare sottoscrizione di Azure, Data Factory e Integration Runtime. Archiviazione di Azure è facoltativa, che viene usata nel passaggio mapping percorso pacchetto se i processi SSIS da migrare hanno pacchetti di file System SSIS.
![menu](media/how-to-migrate-ssis-job-ssms/step1.png)

1. Eseguire il mapping dei percorsi dei pacchetti SSIS e dei file di configurazione nei processi SSIS ai percorsi di destinazione a cui possono accedere le pipeline migrate. In questo passaggio di mapping è possibile:

    1. Selezionare una cartella di origine e quindi **Aggiungi mapping**.
    1. Aggiornare il percorso della cartella di origine. I percorsi validi sono percorsi di cartelle o percorsi di cartelle padre dei pacchetti.
    1. Aggiornare il percorso della cartella di destinazione. Il valore predefinito è il percorso relativo dell'account di archiviazione predefinito, selezionato nel passaggio 1.
    1. Elimina un mapping selezionato tramite **Elimina mapping**.
![2 ](media/how-to-migrate-ssis-job-ssms/step2.png)
 ![ ° passo-1](media/how-to-migrate-ssis-job-ssms/step2-1.png)

1. Selezionare i processi applicabili da migrare e configurare le impostazioni relative all' *attività del pacchetto SSIS eseguita*corrispondente.

    - *Impostazione predefinita*, si applica a tutti i passaggi selezionati per impostazione predefinita. Per ulteriori informazioni su ogni proprietà, vedere la *scheda Impostazioni* per l' [attività Esegui pacchetto SSIS](how-to-invoke-ssis-package-ssis-activity.md) quando il percorso del pacchetto è *file System (pacchetto)*.
    ![passaggio 3-1](media/how-to-migrate-ssis-job-ssms/step3-1.png)
    - *Impostazione del passaggio*, configurare l'impostazione per un passaggio selezionato.
        
        **Applica impostazione predefinita**: è selezionata l'opzione predefinita. Deselezionare questa opzione per configurare solo il passaggio selezionato.  
        Per ulteriori informazioni su altre proprietà, vedere la *scheda Impostazioni* per l' [attività Esegui pacchetto SSIS](how-to-invoke-ssis-package-ssis-activity.md) quando il percorso del pacchetto è *file System (pacchetto)*.
    ![passaggio 3-2](media/how-to-migrate-ssis-job-ssms/step3-2.png)

1. Generare e distribuire il modello ARM.
    1. Selezionare o immettere il percorso di output per i modelli ARM delle pipeline di ADF migrate. La cartella verrà creata automaticamente se non esiste.
    2. Selezionare l'opzione **per distribuire i modelli ARM nel data factory**:
        - Il valore predefinito è deselezionato. È possibile distribuire manualmente i modelli ARM generati in un secondo momento.
        - Selezionare questa selezione per distribuire i modelli ARM generati direttamente data factory.
    ![passaggio4](media/how-to-migrate-ssis-job-ssms/step4.png)

1. Eseguire la migrazione, quindi controllare i risultati.
![passaggio 5](media/how-to-migrate-ssis-job-ssms/step5.png)

## <a name="next-steps"></a>Passaggi successivi

[Eseguire e monitorare la pipeline](how-to-invoke-ssis-package-ssis-activity.md)
