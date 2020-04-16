---
title: Eseguire pacchetti SSIS dall'agente di istanza gestita SQL di AzureExecute SSIS packages by Azure SQL Managed Instance Agent
description: Informazioni su come eseguire pacchetti SSIS da Azure SQL Managed Instance Agent.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: lle
author: lle
ms.date: 04/14/2020
ms.openlocfilehash: b3b7a25149a9d075c81b30307ade2beb71907637
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394722"
---
# <a name="execute-ssis-packages-by-azure-sql-managed-instance-agent"></a>Eseguire pacchetti SSIS dall'agente di istanza gestita SQL di AzureExecute SSIS packages by Azure SQL Managed Instance Agent
Questo articolo descrive come eseguire un pacchetto di SQL Server Integration Services (SSIS) usando l'agente di istanza gestita sql di Azure.This article describes how to run a SQL Server Integration Services (SSIS) package by using Azure SQL Managed Instance Agent. Questa funzionalità fornisce comportamenti simili, proprio come quando si pianificano i pacchetti SSIS da Sql Server Agent nell'ambiente precedente.

Con questa funzionalità, è possibile eseguire pacchetti SSIS archiviati in SSISDB dell'istanza gestita SQL di Azure o file system, ad esempio file di Azure.With this feature, you can run SSIS packages that are stored in SSISDB of Azure SQL Managed Instance or File System such as Azure Files.

## <a name="prerequisites"></a>Prerequisiti
Per utilizzare questa funzionalità, scaricare e installare la versione più recente di SSMS, ovvero la versione 18.5 o successiva. Scaricarlo da [questo sito Web](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

È inoltre necessario eseguire il provisioning di un runtime di integrazione Azure-SSIS in Azure Data Factory, che usa l'istanza gestita SQL di Azure come server endpoint. Se non è già stato eseguito il provisioning, eseguirne il provisioning seguendo le istruzioni riportate [nell'esercitazione](tutorial-create-azure-ssis-runtime-portal.md). 

## <a name="run-ssis-packages-in-ssisdb-by-azure-sql-managed-instance-agent"></a>Eseguire pacchetti SSIS in SSISDB dall'agente di istanza gestita SQL di AzureRun SSIS packages in SSISDB by Azure SQL Managed Instance Agent
In this step, you use Azure SQL Managed Instance Agent to invoke SSIS packages that is stored in SSISDB in Azure SQL Managed Instance.
1. Nella versione più recente di SSMS connettersi all'istanza gestita SQL di Azure.In the latest version of SSMS, connect to Azure SQL Managed Instance.
2. Creare un nuovo processo dell'agente e un nuovo passaggio di processo.

![Nuovo processo agente](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

3. Nella pagina **Nuovo passaggio di processo** scegliere Tipo di pacchetto di SQL Server Integration **Services.**

![Nuovo passaggio di processo SSIS](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

4. Nella scheda **Pacchetto** scegliere **Catalogo SSIS** come tipo di origine del pacchetto.
5. Poiché SSISDB si trova nella stessa istanza gestita SQL di Azure, non è necessario specificare l'autenticazione.
6. Specificare un pacchetto SSIS dal sSISDB.

![Tipo di origine pacchetto - Catalogo SSISPackage Source Type - SSIS Catalog](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-ssisdb.png)

7. Nella scheda **Configurazioni** è possibile specificare i valori dei **parametri,** eseguire l'override dei valori in **Gestioni connessioni**, eseguire l'override **di Proprietà** e scegliere Livello **di registrazione**.

![Tipo di origine pacchetto - Configurazione del catalogo SSISPackage Source Type - SSIS Catalog Configuration](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-ssisdb-configuration.png)

8. Dopo aver completato tutte le configurazioni precedenti, fare clic **su OK** per salvare la configurazione del processo dell'agente.
9. Avviare il processo dell'agente per eseguire il pacchetto SSIS.


## <a name="run-ssis-packages-in-file-system-by-azure-sql-managed-instance-agent"></a>Eseguire pacchetti SSIS nel file system dall'agente dell'istanza gestita SQL di AzureRun SSIS packages in file system by Azure SQL managed instance agent
In questo passaggio si usa Azure SQL Managed Instance Agent per richiamare i pacchetti SSIS archiviati nel file system per l'esecuzione.
1. Nella versione più recente di SSMS connettersi all'istanza gestita SQL di Azure.In the latest version of SSMS, connect to Azure SQL Managed Instance.
2. Creare un nuovo processo dell'agente e un nuovo passaggio di processo.

   ![Nuovo processo agente](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

3. Nella pagina **Nuovo passaggio di processo** scegliere Tipo di pacchetto di SQL Server Integration **Services.**

   ![Nuovo passaggio di processo SSIS](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

4. Nella scheda **Pacchetto** scegliere **File system** come tipo di origine del pacchetto.

   ![Tipo di origine pacchetto - File system](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-file-system.png)

   1. Se il pacchetto viene caricato in File di Azure, scegliere **Condivisione file di Azure** come tipo di origine file.
      - Il percorso del pacchetto è ** \\ <storage account name>\< \<.file.core.windows.net nome di condivisione file>nome del pacchetto>.dtsx**
      - Digitare il nome dell'account file di Azure e la chiave dell'account in Credenziali di accesso al file del pacchetto per accedere al file di Azure.Type in the Azure file account name and account key in **Package file access credential** to access the Azure file. Il dominio è impostato come **Azure**.
   2. Se il pacchetto viene caricato in una condivisione di rete, scegliere **Condivisione di** rete come tipo di origine file.
      - Il percorso del pacchetto è il **percorso UNC** del file del pacchetto con estensione dtsx.
      - Digitare il **dominio,** **il nome utente**e la **password** corrispondenti per accedere al file del pacchetto di condivisione di rete.
   3. Se il file del pacchetto è crittografato con password, selezionare **Password di crittografia** e digitare la password.

 5. Nella scheda **Configurazioni** digitare il percorso del file di **configurazione** se è necessario un file di configurazione per eseguire il pacchetto SSIS.
 6. Nella scheda Opzioni di **esecuzione** è possibile scegliere se utilizzare **l'autenticazione** di Windows o il runtime a **32 bit** per eseguire il pacchetto SSIS.
 7. Nella scheda **Registrazione** è possibile scegliere il **percorso di registrazione** e le credenziali di accesso alla registrazione corrispondenti per archiviare i file di registro. Per impostazione predefinita, il percorso di registrazione sarà lo stesso percorso della cartella del pacchetto e le credenziali di accesso alla registrazione corrisponderanno alle credenziali di accesso al pacchetto.
 8. Nella scheda **Imposta valori** è possibile digitare il percorso e il **valore** della **proprietà** per eseguire l'override delle proprietà del pacchetto.
 Ad esempio, per eseguire l'override del valore della variabile utente, immettere il percorso nel seguente formato: **"Package.Variables[User::<variable name>]. Valore**.
 9. Dopo aver completato tutte le configurazioni precedenti, fare clic **su OK** per salvare la configurazione del processo dell'agente.
 10. Avviare il processo dell'agente per eseguire il pacchetto SSIS.


 ## <a name="cancel-ssis-package-execution"></a>Annullare l'esecuzione del pacchetto SSISCancel SSIS package execution
 Per annullare l'esecuzione del pacchetto da un processo dell'agente gestito SQL di Azure, è necessario seguire i passaggi seguenti anziché arrestare direttamente il processo dell'agente.
 1. Trovare l'id **processo** dell'agente SQL da **msdb.dbo.sysjobs**.
 2. Trovare **l'id esedezio** SSIS corrispondente in base all'ID processo tramite la query seguente:Find corresponding SSIS executionId based on the job ID by below query:
    ```sql
    select * from ssisdb.internal.execution_parameter_values_noncatalog where  parameter_value = 'SQL_Agent_Job_{jobId}' order by execution_id desc
    ```
 3. Selezionare **Operazioni attive** in Catalogo SSIS.

    ![Tipo di origine pacchetto - File system](./media/how-to-invoke-ssis-package-managed-instance-agent/catalog-active-operations.png)

 4. Interrompere l'operazione corrispondente in base **a executionId**.

## <a name="next-steps"></a>Passaggi successivi
 È anche possibile pianificare i pacchetti SSIS usando Azure Data Factory.You can also schedule SSIS packages using Azure Data Factory. Per istruzioni dettagliate, vedere Trigger di evento di Azure Data Factory.For step-by-step [instructions,](how-to-create-event-trigger.md)see Azure Data Factory Event Trigger . 