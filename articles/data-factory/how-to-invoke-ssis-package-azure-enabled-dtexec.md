---
title: Eseguire pacchetti di SQL Server Integration Services (SSIS) con l'utilità dtexec abilitata per AzureExecute SQL Server Integration Services (SSIS) packages with the Azure-enabled dtexec utility
description: Informazioni su come eseguire pacchetti di SQL Server Integration Services (SSIS) con l'utilità dtexec abilitata per Azure.Learn how to execute SQL Server Integration Services (SSIS) packages with the Azure-enabled dtexec utility.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/21/2019
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.openlocfilehash: a5540eea91937319a6ac947b50698ccaa8b25847
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74931693"
---
# <a name="run-sql-server-integration-services-packages-with-the-azure-enabled-dtexec-utility"></a>Eseguire pacchetti di SQL Server Integration Services con l'utilità dtexec abilitata per AzureRun SQL Server Integration Services packages with the Azure-enabled dtexec utility
Questo articolo descrive l'utilità del prompt dei comandi dtexec (AzureDTExec) abilitata per Azure.This article describes the Azure-enabled dtexec (AzureDTExec) command prompt utility. Viene usato per eseguire pacchetti di SQL Server Integration Services (SSIS) in Azure-SSIS Integration Runtime (IR) in Azure Data Factory.It's used to run SQL Server Integration Services (SSIS) packages on the Azure-SSIS Integration Runtime (IR) in Azure Data Factory.

L'utilità dtexec tradizionale viene fornito con SQL Server. Per ulteriori informazioni, vedere [utilità dtexec](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017). Viene spesso richiamato da agenti di orchestrazione o utilità di pianificazione di terze parti, ad esempio ActiveBatch e Control-M, per eseguire pacchetti SSIS in locale. 

La moderna utilità AzureDTExec viene fornita con uno strumento di SQL Server Management Studio (SSMS). Può anche essere richiamato da agenti di orchestrazione o utilità di pianificazione di terze parti per eseguire pacchetti SSIS in Azure.It also be invoked by third-party orchestrators or schedulers to run SSIS packages in Azure. Facilita il sollevamento e lo spostamento o la migrazione dei pacchetti SSIS nel cloud. Dopo la migrazione, se si vuole continuare a usare agenti di orchestrazione o utilità di pianificazione di terze parti nelle operazioni quotidiane, è ora possibile richiamare AzureDTExec anziché dtexec.

AzureDTExec esegue i pacchetti come attività Esegui pacchetto SSIS nelle pipeline di Data Factory.AzureDTExec runs your packages as Execute SSIS Package activities in Data Factory pipelines. Per altre informazioni, vedere Eseguire pacchetti SSIS come attività di Azure Data Factory.For more information, see [Run SSIS packages as Azure Data Factory activities.](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) 

AzureDTExec può essere configurato tramite SSMS per usare un'applicazione Azure Active Directory (Azure AD) che genera pipeline nella data factory. Può anche essere configurato per accedere a file system, condivisioni file o file di Azure in cui vengono archiviati i pacchetti. In base ai valori specificati per le opzioni di chiamata, AzureDTExec genera ed esegue una pipeline di Data Factory univoca con un'attività Esegui pacchetto SSIS. La chiamata di AzureDTExec con gli stessi valori per le relative opzioni esegue nuovamente la pipeline esistente.

## <a name="prerequisites"></a>Prerequisiti
Per usare AzureDTExec, scaricare e installare la versione più recente di SSMS, ovvero la versione 18.3 o successiva. Scaricarlo da [questo sito Web](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

## <a name="configure-the-azuredtexec-utility"></a>Configurare l'utilità AzureDTExecConfigure the AzureDTExec utility
L'installazione di SSMS nel computer locale installa anche AzureDTExec.Installing SSMS on your local machine also installs AzureDTExec. Per configurarne le impostazioni, avviare SSMS con l'opzione **Esegui come amministratore.** Selezionare **quindi Strumenti** > eseguire la migrazione a**Azure Configurare DTExec abilitato per Azure.****Migrate to Azure** > 

![Configurare il menu dtexec abilitato per AzureConfigure Azure-enabled dtexec menu](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-menu.png)

Questa azione apre una finestra **di AzureDTExecConfig** che deve essere aperta con privilegi amministrativi per scrivere nel file *AzureDTExec.settings.This* action opens a AzureDTExecConfig window that needs to be opened with administrative privileges for it to write into the AzureDTExec.settings file. Se SSMS non è stato eseguito come amministratore, viene visualizzata una finestra Controllo dell'account utente. Immettere la password di amministratore per elevare i privilegi.

![Configurare le impostazioni dtexec abilitate per AzureConfigure Azure-enabled dtexec settings](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-settings.png)

Nella finestra AzureDTExecConfig immettere le impostazioni di configurazione come indicato di seguito:In the **AzureDTExecConfig** window, enter your configuration settings as follows:

- **ApplicationId:** immettere l'identificatore univoco dell'app Azure AD creata con le autorizzazioni appropriate per generare pipeline nella data factory. Per altre informazioni, vedere [Creare un'app Azure AD e un'entità servizio tramite il portale](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)di Azure.For more information, see Create an Azure AD app and service principal via Azure portal.
- **AuthenticationKey:** immettere la chiave di autenticazione per l'app Azure AD.
- **TenantId:** immettere l'identificatore univoco del tenant di Azure AD, in cui viene creata l'app Azure AD.
- **SubscriptionId:** immettere l'identificatore univoco della sottoscrizione di Azure, in cui è stata creata la data factory.
- **ResourceGroup:** immettere il nome del gruppo di risorse di Azure in cui è stata creata la data factory.
- **DataFactory:** immettere il nome della data factory in cui vengono generate pipeline univoche con l'attività Esegui pacchetto SSIS in base ai valori delle opzioni fornite quando si richiama AzureDTExec.
- **IRName**: immettere il nome del runtime di accesso Azure-SSIS nella data factory, in cui verranno eseguiti i pacchetti specificati nel percorso UNC (Universal Naming Convention).
- **PackageAccessDomain:** immettere le credenziali di dominio per accedere ai pacchetti nel percorso UNC specificato quando si richiama AzureDTExec.PackageAccessDomain : Enter the domain credential to access your packages in their UNC path that's specified when you invoke AzureDTExec.
- **PackageAccessUserName:** immettere le credenziali del nome utente per accedere ai pacchetti nel percorso UNC specificato quando si richiama AzureDTExec.PackageAccessUserName : Enter the username credential to access your packages in their UNC path that's specified when you invoke AzureDTExec.
- **PackageAccessPassword:** immettere le credenziali della password per accedere ai pacchetti nel percorso UNC specificato quando si richiama AzureDTExec.PackageAccessPassword : Enter the password credential to access your packages in their UNC path that's specified when you invoke AzureDTExec.
- **LogPath**: Immettere il percorso UNC della cartella di log, in cui vengono scritti i file di log delle esecuzioni del pacchetto nel componente di accesso Azure-SSIS.
- **LogLevel**: immettere l'ambito di registrazione selezionato dalle opzioni **predefinite null**, **Basic**, **Verbose**o **Performance** per le esecuzioni del pacchetto nel componente di accesso Azure-SSIS.
- **LogAccessDomain**: immettere le credenziali di dominio per accedere alla cartella di registro nel percorso UNC quando si scrivono i file di registro, operazione necessaria quando si specifica **LogPath** e **LogLevel** non è **null**.
- **LogAccessUserName**: immettere le credenziali del nome utente per accedere alla cartella di registro nel relativo percorso UNC quando si scrivono i file di registro, operazione necessaria quando si specifica **LogPath** e **LogLevel** non è **null**.
- **LogAccessPassword**: Immettere le credenziali della password per accedere alla cartella di registro nel percorso UNC quando si scrivono i file di registro, operazione necessaria quando si specifica **LogPath** e **LogLevel** non è **null**.
- **PipelineNameHashStrLen**: immettere la lunghezza delle stringhe hash da generare dai valori delle opzioni fornite quando si richiama AzureDTExec. PipelineNameHashStrLen : Enter the length of hash strings to be generated from the values of options you provide when you invoke AzureDTExec. Le stringhe vengono usate per formare nomi univoci per le pipeline di Data Factory che eseguono i pacchetti nel runtime di irrichiesta Di Azure-SSIS. Di solito una lunghezza di 32 caratteri è sufficiente.

Per archiviare i pacchetti e i file di log in file system o condivisioni file in locale, aggiungere il componente di integrazione azure-SSIS a una rete virtuale connessa alla rete locale in modo che possa recuperare i pacchetti e scrivere i file di log. Per altre informazioni, vedere [Aggiungere un componente di riprodotto Azure-SSIS a una rete virtuale.](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)

Per evitare di mostrare i valori sensibili scritti nel file AzureDTExec.settings in testo normale, vengono codificati in stringhe di codifica Base64.To avoid showing sensitive values written in the *AzureDTExec.settings* file in plain text, we cocode them into strings of Base64 encoding. Quando si richiama AzureDTExec, tutte le stringhe con codifica Base64 vengono decodificate nei valori originali. È possibile proteggere ulteriormente il file *AzureDTExec.settings* limitando gli account che possono accedervi.

## <a name="invoke-the-azuredtexec-utility"></a>Richiamare l'utilità AzureDTExec
È possibile richiamare AzureDTExec al prompt della riga di comando e fornire i valori rilevanti per opzioni specifiche nello scenario dei casi d'uso.

L'utilità viene `{SSMS Folder}\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn`installata all'indirizzo . È possibile aggiungere il relativo percorso alla variabile di ambiente 'PATH' affinché venga richiamato da qualsiasi luogo.

```dos
> cd "C:\Program Files (x86)\Microsoft SQL Server Management Studio 18\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn"
> AzureDTExec.exe  ^
  /F \\MyStorageAccount.file.core.windows.net\MyFileShare\MyPackage.dtsx  ^
  /Conf \\MyStorageAccount.file.core.windows.net\MyFileShare\MyConfig.dtsConfig  ^
  /Conn "MyConnectionManager;Data Source=MyDatabaseServer.database.windows.net;User ID=MyAdminUsername;Password=MyAdminPassword;Initial Catalog=MyDatabase"  ^
  /Set \package.variables[MyVariable].Value;MyValue  ^
  /De MyEncryptionPassword
```

La chiamata di AzureDTExec offre opzioni simili a quelle di richiamare dtexec. Per altre informazioni, vedere [dtexec Utility](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017). Di seguito sono riportate le opzioni attualmente supportate:

- **/F[ile]**: carica un pacchetto archiviato nel file system, nella condivisione file o nei file di Azure. Come valore di questa opzione, è possibile specificare il percorso UNC per il file del pacchetto nel file system, nella condivisione file o nei file di Azure con estensione dtsx. Se il percorso UNC specificato contiene uno spazio, racchiudere l'intero percorso tra virgolette.
- **/Conf[igFile]**: Specifica un file di configurazione da cui estrarre i valori. Utilizzando questa opzione, è possibile impostare una configurazione in fase di esecuzione per il pacchetto diversa da quella specificata in fase di progettazione. È possibile archiviare impostazioni diverse in un file di configurazione XML e quindi caricarle prima dell'esecuzione del pacchetto. Per ulteriori informazioni, vedere Configurazioni dei [pacchetti SSIS](https://docs.microsoft.com/sql/integration-services/packages/package-configurations?view=sql-server-2017). Per specificare il valore di questa opzione, usare il percorso UNC per il file di configurazione nel file system, nella condivisione file o nei file di Azure con l'estensione dtsConfig. Se il percorso UNC specificato contiene uno spazio, racchiudere l'intero percorso tra virgolette.
- **/Conn[ection]**: Specifica le stringhe di connessione per le gestioni connessioni esistenti nel pacchetto. Utilizzando questa opzione, è possibile impostare le stringhe di connessione di runtime per le gestioni connessioni esistenti nel pacchetto che differiscono da quelle specificate in fase di progettazione. Specificare il valore di `connection_manager_name_or_id;connection_string [[;connection_manager_name_or_id;connection_string]...]`questa opzione come segue: .
- **/Set**: esegue l'override della configurazione di un parametro, una variabile, una proprietà, un contenitore, un provider di log, un enumeratore Foreach o una connessione nel pacchetto. Questa opzione può essere specificata più volte. Specificare il valore di `property_path;value`questa opzione come segue: . Ad esempio, esegue l'override del valore della variabile come 1.For example, `\package.variables[counter].Value;1` overrides the value of `counter` variable as 1. È possibile utilizzare la Configurazione guidata **pacchetto** per trovare, copiare e incollare il valore di `property_path` per gli elementi nel pacchetto di cui si desidera eseguire l'override del valore. Per ulteriori informazioni, vedere [Configurazione guidata pacchetto](https://docs.microsoft.com/sql/integration-services/package-configuration-wizard-ui-reference?view=sql-server-2014).
- **/De[crypt]**: imposta la password di decrittografia per il pacchetto configurato con il livello di protezione **EncryptAllWithPassword**/**EncryptSensitiveWithPassword.**

> [!NOTE]
> La chiamata di AzureDTExec con nuovi valori per le relative opzioni genera una nuova pipeline, ad eccezione dell'opzione **/De[cript]**.

## <a name="next-steps"></a>Passaggi successivi

Dopo che le pipeline univoche con l'attività Esegui pacchetto SSIS in esse vengono generate ed eseguite dopo aver richiamato AzureDTExec, possono essere monitorate nel portale di Data Factory.After unique pipelines with the Execute SSIS Package activity in them are generated and run after you invoke AzureDTExec, they can be monitored on the Data Factory portal. Per altre informazioni, vedere [Eseguire pacchetti SSIS come attività di Data Factory](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

> [!WARNING]
> La pipeline generata deve essere usata solo da AzureDTExec.The generated pipeline is expected to be used only by AzureDTExec. Le sue proprietà o parametri potrebbero cambiare in futuro, quindi non modificarli o riutilizzarli per altri scopi. Le modifiche potrebbero interrompere AzureDTExec.Modifications might break AzureDTExec. In questo caso, eliminare la pipeline. AzureDTExec genera una nuova pipeline al successivo richiamo.
