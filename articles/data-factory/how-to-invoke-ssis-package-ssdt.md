---
title: Eseguire pacchetti SSIS da SSDTExecute SSIS packages from SSDT
description: Informazioni su come eseguire pacchetti SSIS in Azure da SSDT.Learn how to execute SSIS packages in Azure from SSDT.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: sawinark
author: swinarko
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 07/31/2019
ms.openlocfilehash: 11e76fea87c60ae2b56cc15d5827be6e1b2b5a01
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399439"
---
# <a name="execute-ssis-packages-in-azure-from-ssdt"></a>Eseguire pacchetti SSIS in Azure da SSDTExecute SSIS packages in Azure from SSDT

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Questo articolo descrive la funzionalità dei progetti di SQL Server Integration Services (SSIS) abilitati per Azure in SQL Server Data Tools (SSDT)SQL Server Data Tools (SSDT), che consente di eseguire pacchetti in Azure-SSIS Integration Runtime (IR) in Azure Data Factory (ADF).  You can use this feature to test your existing SSIS packages before you lift & shift/migrate them to Azure or to develop new SSIS packages to run in Azure.

Con questa funzionalità, è possibile creare un nuovo codice a- per le applicazioni Azure-SSIS o collegarne uno esistente ai progetti SSIS e quindi eseguire i pacchetti su di essi.  Supportiamo i pacchetti in esecuzione da distribuire nel catalogo SSIS (SSISDB) nel modello di distribuzione del progetto e quelli da distribuire in file system/condivisioni file/file file/File di Azure nel modello di distribuzione dei pacchetti. 

## <a name="prerequisites"></a>Prerequisiti
Per utilizzare questa funzionalità, scaricare e installare l'estensione SSDT più recente con l'estensione Progetti SSIS per Visual Studio da [qui](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects) o come programma di installazione autonomo da [qui](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer).

## <a name="azure-enable-ssis-projects"></a>Progetti SSIS abilitati per AzureAzure-enable SSIS projects
In SSDT è possibile creare nuovi progetti SSIS abilitati per Azure usando il modello Progetto Integration Services (abilitato per Azure).On SSDT, you can create new Azure-enabled SSIS projects using the **Integration Services Project (Azure-Enabled) template.**

![Nuovo progetto SSIS abilitato per Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-new-project.png)

In alternativa, è possibile abilitare Azure ai progetti SSIS esistenti facendo clic con il pulsante destro del mouse sul nodo del progetto nel riquadro Esplora soluzioni di SSDT per visualizzare un menu e quindi selezionare la voce di menu Abilitato per Azure.Alternatively, you can Azure-enable your existing SSIS projects by right-clicking on the project node in Solution Explorer panel of SSDT to pop up a menu and then selecting the **Azure-Enabled** menu item.

![Progetto SSIS esistente abilitato per AzureAzure-Enable Existing SSIS Project](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-existing-project.png)

Per abilitare i progetti SSIS esistenti, è necessario impostare la versione del server di destinazione in modo che sia la versione più recente supportata dal componente di gestione delle versioni dei messaggi di gestione per Azure-SSIS, attualmente **SQL Server 2017**, pertanto se non è già stato fatto, verrà visualizzata una finestra di dialogo per eseguire questa operazione.

## <a name="connect-azure-enabled-projects-to-ssis-in-azure-data-factory"></a>Connettere progetti abilitati per Azure a SSIS in Azure Data FactoryConnect Azure-enabled projects to SSIS in Azure Data Factory
Connettendo i progetti abilitati per Azure a SSIS in ADF, è possibile caricare i pacchetti in File di Azure ed eseguirli nel runtime di ir Azure-SSIS.  A tale scopo, attenersi alla seguente procedura:

1. Fare clic con il pulsante destro del mouse sul progetto o sul nodo **Risorse di Azure collegate** nel riquadro Esplora soluzioni di SSDT per visualizzare un menu e selezionare la voce di menu **Connetti a SSIS in Azure Data Factory** per avviare **SSIS nella Connessione guidata ADF**.

   ![Connettersi a SSIS in ADFConnect to SSIS in ADF](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-existing-project2.png)

2. Nella pagina Introduzione a **SSIS in ADF,** esaminare l'introduzione e fare clic sul pulsante **Avanti** per continuare.

   ![Introduzione a SSIS in ADF](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard.png)

3. Nella pagina Seleziona runtime di **ircalo SSIS in ADF** selezionare il componente di gestione per l'ADF e il runtime di audio AdF esistenti per eseguire i pacchetti o crearne di nuovi, se non ne sono disponibili.
   - Per selezionare il proprio iR Azure-SSIS esistente, selezionare prima la sottoscrizione di Azure pertinente e ADF.
   - Se si seleziona l'ADF esistente che non dispone di alcun iR Azure-SSIS, fare clic sul pulsante **Crea iR SSIS** per crearne uno nuovo nel portale/app ADF.
   - Se si seleziona la sottoscrizione di Azure esistente che non dispone di ADF, fare clic sul pulsante Crea runtime di integrazione **SSIS** per avviare **l'Integration Runtime Creation Wizard**, in cui è possibile immettere il percorso e il prefisso per creare automaticamente un nuovo gruppo di risorse di Azure, Data Factory e Il runtime di integrazione SSIS per conto dell'utente, denominati nel modello seguente: **YourPrefix-RG/DF/IR-YourCreationTime**.
   
   ![Selezionare SSIS IR in ADF](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard2.png)

4. Nella pagina **Seleziona archiviazione di Azure** selezionare l'account di archiviazione di Azure esistente per caricare i pacchetti in File di Azure o crearne uno nuovo se non ne è stato.
   - Per selezionare l'account di archiviazione di Azure esistente, selezionare prima la sottoscrizione di Azure pertinente.
   - Se si seleziona la stessa sottoscrizione di Azure del componente di richiamata Azure-SSIS che non dispone di alcun account di archiviazione di Azure, fare clic sul pulsante **Crea archiviazione di Azure** per crearne automaticamente una nuova sotto sede dell'utente nella stessa posizione del calo di proprietà Azure-SSIS, denominato combinando un prefisso del nome IR Di Azure SSIS e la data di creazione.
   - Se si seleziona una sottoscrizione di Azure diversa che non dispone di alcun account di archiviazione di Azure, fare clic sul pulsante **Crea archiviazione di Azure** per crearne una nuova nel portale di Azure.If you select a different Azure subscription that does not have any Azure Storage account, click on the Create Azure Storage button to create a new one on Azure portal.
   
   ![Selezionare Archiviazione di Azure](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard3.png)

5. Fare clic sul pulsante **Connetti** per completare la connessione.  Il livello di ir creaoggetti Azure-SSIS selezionato e l'account di Archiviazione di Azure verranno visualizzati nel nodo **Risorse di Azure collegate** nel riquadro Esplora soluzioni di SSDT.  Aggiorneremo anche lo stato del tuo iR Azure-SSIS, mentre puoi gestirlo facendo clic con il pulsante destro del mouse sul relativo nodo per visualizzare un menu e quindi selezionando la voce di menu **Start , Stop, Gestisci** che ti porta al portale/app ADF per farlo.

## <a name="execute-ssis-packages-in-azure"></a>Eseguire pacchetti SSIS in AzureExecute SSIS packages in Azure
### <a name="starting-package-executions"></a>Avvio delle esecuzioni dei pacchetti
Dopo aver connesso i progetti a SSIS in ADF, è possibile eseguire pacchetti in Azure-SSIS IR.After connecting your projects to SSIS in ADF, you can execute packages on Azure-SSIS IR.  Sono disponibili due opzioni per avviare le esecuzioni del pacchetto:You have two options to start package executions:
-  Fare clic sul pulsante **Start** nella barra degli strumenti SSDT per visualizzare un menu a discesa e selezionare la voce di menu **Esegui in Azure** 

   ![Esegui in AzureExecute in Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package.png)

-  Fare clic con il pulsante destro del mouse sul nodo del pacchetto nel riquadro Esplora soluzioni di SSDT per visualizzare un menu e selezionare la voce di menu Esegui pacchetto in Azure.Right-click on the package node in Solution Explorer panel of SSDT to pop up a menu and select the **Execute Package in Azure** menu item.

   ![Eseguire il pacchetto in AzureExecute Package in Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package2.png)

> [!NOTE]
> Per eseguire i pacchetti in Azure è necessario disporre di un iR Azure-SSIS in esecuzione, pertanto se il componente di accesso Azure-SSIS viene arrestato, verrà visualizzata una finestra di dialogo per avviarlo.  Escludendo qualsiasi tempo di installazione personalizzato, questo processo deve essere completato entro 5 minuti, ma potrebbe richiedere circa 20 - 30 minuti per il runtime di disponibilità del runtime di installazione Azure-SSIS che partecipa a una rete virtuale.  Dopo aver eseguito i pacchetti in Azure, è possibile arrestare il controllo di accesso Azure-SSIS per gestire il costo di esecuzione facendo clic con il pulsante destro del mouse sul relativo nodo nel riquadro Esplora soluzioni di SSDT per visualizzare un menu e quindi selezionando la voce di menu **Start,Stop-Manage** che consente di accedere al portale/app ADF per eseguire questa operazione.

### <a name="checking-package-execution-logs"></a>Controllo dei log di esecuzione dei pacchetti
Quando avvii l'esecuzione del pacchetto, formatteremo e visualizzeremo il relativo log nella finestra Progress di SSDT.  Per un pacchetto a esecuzione prolungata, aggiorneremo periodicamente il relativo log in base ai minuti.  È possibile interrompere l'esecuzione del pacchetto facendo clic sul pulsante **Stop** nella barra degli strumenti SSDT che lo annullerà immediatamente.  È inoltre possibile trovare temporaneamente i dati non elaborati del `\\<YourConnectedAzureStorage>.file.core.windows.net\ssdtexecution\<YourProjectName-FirstConnectTime>\<YourPackageName-tmp-ExecutionTime>\logs`log nel percorso UNC (Universal Naming Convention): , ma verranno puliti dopo un giorno.

### <a name="switching-package-protection-level"></a>Commutazione del livello di protezione del pacchetto
L'esecuzione di pacchetti SSIS in Azure non supporta i livelli di protezione EncryptSensitiveWithUserKey EncryptAllWithUserKey.Executing SSIS packages in Azure does not support **EncryptSensitiveWithUserKey**/**EncryptAllWithUserKey** protection levels.  Di conseguenza, se i pacchetti sono configurati con quelli, verranno temporaneamente commutati in **EncryptSensitiveWithPassword**/**EncryptAllWithPassword**, rispettivamente, con password generate in modo casuale quando si caricano i pacchetti in file di Azure per l'esecuzione nel componente di accesso Azure-SSIS.

> [!NOTE]
> Se i pacchetti contengono attività Esegui pacchetto che fanno riferimento ad altri pacchetti configurati con livelli di protezione **EncryptSensitiveWithUserKey**/**EncryptAllWithUserKey,** è necessario riconfigurare manualmente gli altri pacchetti in modo che utilizzino rispettivamente **EncryptSensitiveWithPassword**/**EncryptAllWithPassword**prima di eseguire i pacchetti.

Se i pacchetti sono già configurati con i livelli di protezione**EncryptAllWithPassword** di **EncryptSensitiveWithPassword,**/verranno mantenuti invariati, ma verranno comunque utilizzati password generate casualmente quando si caricano i pacchetti in File di Azure per l'esecuzione nel componente di accesso Azure-SSIS.

### <a name="using-package-configuration-file"></a>Utilizzo del file di configurazione del pacchettoUsing package configuration file
Se si usano i file di configurazione dei pacchetti nel modello di distribuzione dei pacchetti per modificare i valori delle variabili in fase di esecuzione, tali file verranno caricati automaticamente in File di Azure per l'esecuzione nel runtime di ir Azure-SSIS.

### <a name="using-execute-package-task"></a>Utilizzo dell'attività Esegui pacchettoUsing Execute Package Task
Se i pacchetti contengono attività Esegui pacchetto che fanno riferimento ad altri pacchetti archiviati in file system locali, è necessario eseguire le seguenti impostazioni aggiuntive:

1. Caricare gli altri pacchetti in File di Azure con lo stesso account di archiviazione di Azure connesso ai progetti e ottenere il nuovo percorso UNC, ad esempio`\\test.file.core.windows.net\ssdtexecution\Package1.dtsx`

2. Sostituire il percorso del file degli altri pacchetti in Gestione connessione file delle attività Esegui pacchetto con il nuovo percorso UNC
   - Se il computer che esegue SSDT non può accedere al nuovo percorso UNC, è possibile modificare il percorso del file nel pannello Proprietà di Gestione connessione file
   - In alternativa, è possibile utilizzare una variabile per il percorso del file per assegnare il valore corretto in fase di esecuzioneAlternatively, you can use a variable for the file path to assign the right value at run time

Se i pacchetti contengono attività Esegui pacchetto che fanno riferimento ad altri pacchetti nello stesso progetto, non è necessaria alcuna configurazione aggiuntiva.

## <a name="next-steps"></a>Passaggi successivi
Quando si è soddisfatti dell'esecuzione dei pacchetti in Azure da SSDT, è possibile distribuirli ed eseguirli come attività Esegui pacchetto SSIS nelle pipeline ADF, vedere [Eseguire pacchetti SSIS come eseguire attività del pacchetto SSIS nelle pipeline ADF](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).
