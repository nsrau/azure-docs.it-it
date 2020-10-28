---
title: Eseguire pacchetti SSIS con l'agente di Istanza gestita SQL di Azure
description: Informazioni su come eseguire pacchetti SSIS con SQL Istanza gestita Agent di Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: lle
author: lle
ms.date: 04/14/2020
ms.openlocfilehash: 98484655dec069c3a284dce0ea83477faf75d9a8
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92637752"
---
# <a name="run-ssis-packages-by-using-azure-sql-managed-instance-agent"></a>Eseguire pacchetti SSIS con SQL Istanza gestita Agent di Azure

Questo articolo descrive come eseguire un pacchetto di SQL Server Integration Services (SSIS) con SQL Istanza gestita Agent di Azure. Questa funzionalità fornisce comportamenti simili a quando si pianificano pacchetti SSIS usando SQL Server Agent nell'ambiente locale.

Con questa funzionalità è possibile eseguire pacchetti SSIS archiviati in SSISDB in un Istanza gestita SQL, un file system come File di Azure o un archivio pacchetti di runtime di integrazione SSIS di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per usare questa funzionalità, [scaricare](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) e installare la versione più recente di SQL Server Management Studio (SSMS). Dettagli supporto versione come riportato di seguito:

- Per eseguire pacchetti in SSISDB o file system, installare SSMS 18,5 o versione successiva.
- Per eseguire i pacchetti nell'archivio pacchetti, installare SSMS versione 18,6 o successiva.

È anche necessario effettuare il [provisioning di un runtime di integrazione SSIS di Azure](./tutorial-deploy-ssis-packages-azure.md) in Azure Data Factory. Usa un Istanza gestita SQL come server endpoint.

## <a name="run-an-ssis-package-in-ssisdb"></a>Eseguire un pacchetto SSIS in SSISDB

In questa procedura si userà SQL Istanza gestita Agent per richiamare un pacchetto SSIS archiviato in SSISDB.

1. Nella versione più recente di SSMS connettersi a un Istanza gestita SQL.
1. Creare un nuovo processo di Agent e un nuovo passaggio di processo. In **SQL Server Agent** fare clic con il pulsante destro del mouse sulla cartella **processi** , quindi scegliere **nuovo processo** .

   ![Selezioni per la creazione di un nuovo processo di Agent](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

1. Nella pagina **nuovo passaggio di processo** selezionare **SQL Server Integration Services pacchetto** come tipo.

   ![Selezioni per la creazione di un nuovo passaggio di processo SSIS](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

1. Nella scheda **pacchetto** selezionare **Catalogo SSIS** come percorso del pacchetto.
1. Poiché SSISDB si trova in un Istanza gestita SQL, non è necessario specificare l'autenticazione.
1. Specificare un pacchetto SSIS da SSISDB.

   ![Scheda pacchetto con selezioni per il tipo di origine del pacchetto](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-ssisdb.png)

1. Nella scheda **configurazione** è possibile:
  
   - Specificare i valori dei parametri in **Parameters** .
   - Eseguire l'override dei valori nelle **gestioni connessioni** .
   - Eseguire l'override della proprietà e scegliere il livello di registrazione in **Avanzate** .

   ![Scheda configurazione con selezioni per il tipo di origine del pacchetto](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-ssisdb-configuration.png)

1. Selezionare **OK** per salvare la configurazione del processo dell'agente.
1. Avviare il processo di Agent per eseguire il pacchetto SSIS.

## <a name="run-an-ssis-package-in-the-file-system"></a>Eseguire un pacchetto SSIS nel file system

In questa procedura viene utilizzato SQL Istanza gestita Agent per eseguire un pacchetto SSIS archiviato nel file system.

1. Nella versione più recente di SSMS connettersi a un Istanza gestita SQL.
1. Creare un nuovo processo di Agent e un nuovo passaggio di processo. In **SQL Server Agent** fare clic con il pulsante destro del mouse sulla cartella **processi** , quindi scegliere **nuovo processo** .

   ![Selezioni per la creazione di un nuovo processo di Agent](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

1. Nella pagina **nuovo passaggio di processo** selezionare **SQL Server Integration Services pacchetto** come tipo.

   ![Selezioni per la creazione di un nuovo passaggio di processo SSIS](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

1. Nella scheda **pacchetto** :

   1. In **percorso pacchetto** selezionare **file System** .

   1. Per il **tipo di origine file** :

      - Se il pacchetto viene caricato in File di Azure, selezionare **condivisione file di Azure** .

        ![Opzioni per il tipo di origine file](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-file-system.png)

        Il percorso del pacchetto è **`\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx`** .

        In **credenziali di accesso al file del pacchetto** immettere il nome dell'account file di Azure e la chiave dell'account per accedere al file di Azure. Il dominio è impostato come **Azure** .

      - Se il pacchetto viene caricato in una condivisione di rete, selezionare **condivisione di rete** .

        Il percorso del pacchetto è il percorso UNC del file di pacchetto con estensione dtsx.

        Immettere il dominio, il nome utente e la password corrispondenti per accedere al file del pacchetto di condivisione di rete.
   1. Se il file del pacchetto è crittografato con una password, selezionare **password di crittografia** e immettere la password.
1. Nella scheda **configurazioni** immettere il percorso del file di configurazione se è necessario un file di configurazione per eseguire il pacchetto SSIS.
   Se si archivia la configurazione in File di Azure, il percorso di configurazione sarà **`\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`** .
1. Nella scheda **Opzioni di esecuzione** è possibile scegliere se usare l' **autenticazione di Windows** o il runtime a **32 bit** per eseguire il pacchetto SSIS.
1. Nella scheda **registrazione** è possibile scegliere il percorso di registrazione e le credenziali di accesso di registrazione corrispondenti per archiviare i file di log. 
   Per impostazione predefinita, il percorso di registrazione corrisponde al percorso della cartella del pacchetto e le credenziali di accesso alla registrazione sono le stesse delle credenziali di accesso al pacchetto.
   Se si archiviano i log in File di Azure, il percorso di registrazione sarà **`\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`** .
1. Nella scheda **Imposta valori** è possibile immettere il percorso e il valore della proprietà per eseguire l'override delle proprietà del pacchetto.

   Ad esempio, per eseguire l'override del valore della variabile utente, immettere il percorso nel formato seguente: **`\Package.Variables[User::<variable name>].Value`** .
1. Selezionare **OK** per salvare la configurazione del processo dell'agente.
1. Avviare il processo di Agent per eseguire il pacchetto SSIS.

## <a name="run-an-ssis-package-in-the-package-store"></a>Eseguire un pacchetto SSIS nell'archivio pacchetti

In questa procedura viene utilizzato SQL Istanza gestita Agent per eseguire un pacchetto SSIS archiviato nell'archivio pacchetti di Azure-SSIS IR.

1. Nella versione più recente di SSMS connettersi a un Istanza gestita SQL.
1. Creare un nuovo processo di Agent e un nuovo passaggio di processo. In **SQL Server Agent** fare clic con il pulsante destro del mouse sulla cartella **processi** , quindi scegliere **nuovo processo** .

   ![Selezioni per la creazione di un nuovo processo di Agent](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

1. Nella pagina **nuovo passaggio di processo** selezionare **SQL Server Integration Services pacchetto** come tipo.

   ![Selezioni per la creazione di un nuovo passaggio di processo SSIS](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

1. Nella scheda **pacchetto** :

   1. In **percorso pacchetto** selezionare **Archivio pacchetti** .

   1. Per il **percorso del pacchetto** :

      Il percorso del pacchetto è **`<package store name>\<folder name>\<package name>`** .

      ![Opzioni per il tipo di archivio pacchetti](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-package-store.png)

   1. Se il file del pacchetto è crittografato con una password, selezionare **password di crittografia** e immettere la password.
1. Nella scheda **configurazioni** immettere il percorso del file di configurazione se è necessario un file di configurazione per eseguire il pacchetto SSIS.
   Se si archivia la configurazione in File di Azure, il percorso di configurazione sarà **`\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`** .
1. Nella scheda **Opzioni di esecuzione** è possibile scegliere se usare l' **autenticazione di Windows** o il runtime a **32 bit** per eseguire il pacchetto SSIS.
1. Nella scheda **registrazione** è possibile scegliere il percorso di registrazione e le credenziali di accesso di registrazione corrispondenti per archiviare i file di log.
   Per impostazione predefinita, il percorso di registrazione corrisponde al percorso della cartella del pacchetto e le credenziali di accesso alla registrazione sono le stesse delle credenziali di accesso al pacchetto.
   Se si archiviano i log in File di Azure, il percorso di registrazione sarà **`\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`** .
1. Nella scheda **Imposta valori** è possibile immettere il percorso e il valore della proprietà per eseguire l'override delle proprietà del pacchetto.

   Ad esempio, per eseguire l'override del valore della variabile utente, immettere il percorso nel formato seguente: **`\Package.Variables[User::<variable name>].Value`** .
1. Selezionare **OK** per salvare la configurazione del processo dell'agente.
1. Avviare il processo di Agent per eseguire il pacchetto SSIS.

## <a name="cancel-ssis-package-execution"></a>Annulla l'esecuzione del pacchetto SSIS

Per annullare l'esecuzione del pacchetto da un processo di SQL Istanza gestita Agent, seguire questa procedura anziché arrestare direttamente il processo di Agent:

1. Trovare il **JobID** di SQL agent da **msdb.dbo.sysprocessi** .
1. Trovare il **EXECUTIONID** SSIS corrispondente in base all'ID processo, usando questa query:
   ```sql
   select * from '{table for job execution}' where  parameter_value = 'SQL_Agent_Job_{jobId}' order by execution_id desc
   ```
   Se i pacchetti SSIS sono in SSISDB, utilizzare **ssisdb.internal.execution_parameter_values** come tabella per l'esecuzione del processo. Se i pacchetti SSIS sono in file system, utilizzare **ssisdb.internal.execution_parameter_values_noncatalog** .
1. Fare clic con il pulsante destro del mouse sul catalogo SSISDB, quindi selezionare **operazioni attive** .

   !["Operazioni attive" nel menu di scelta rapida per il catalogo SSISDB](./media/how-to-invoke-ssis-package-managed-instance-agent/catalog-active-operations.png)

1. Arrestare l'operazione corrispondente in base a **ExecutionID** .

## <a name="next-steps"></a>Passaggi successivi
È inoltre possibile pianificare i pacchetti SSIS utilizzando Azure Data Factory. Per istruzioni dettagliate, vedere [Azure Data Factory trigger di evento](how-to-create-event-trigger.md).