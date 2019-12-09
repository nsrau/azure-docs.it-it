---
title: Eseguire pacchetti di SQL Server Integration Services (SSIS) con l'utilità dtexec abilitata per Azure
description: Informazioni su come eseguire pacchetti SQL Server Integration Services (SSIS) con l'utilità dtexec abilitata per Azure.
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
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931693"
---
# <a name="run-sql-server-integration-services-packages-with-the-azure-enabled-dtexec-utility"></a>Eseguire pacchetti di SQL Server Integration Services con l'utilità dtexec abilitata per Azure
Questo articolo descrive l'utilità del prompt dei comandi dtexec abilitata per Azure (AzureDTExec). Viene usato per eseguire pacchetti SQL Server Integration Services (SSIS) nell'Azure-SSIS Integration Runtime (IR) in Azure Data Factory.

L'utilità dtexec tradizionale viene fornita con SQL Server. Per ulteriori informazioni, vedere [utilità dtexec](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017). Viene spesso richiamato dagli agenti di orchestrazione o dalle utilità di pianificazione di terze parti, ad esempio ActiveBatch e Control-M, per eseguire i pacchetti SSIS in locale. 

La moderna utilità AzureDTExec viene fornita con uno strumento SQL Server Management Studio (SSMS). Può essere richiamato anche dagli agenti di orchestrazione o dalle utilità di pianificazione di terze parti per l'esecuzione di pacchetti SSIS in Azure. Semplifica il lifting, la migrazione o la migrazione dei pacchetti SSIS nel cloud. Dopo la migrazione, se si desidera utilizzare gli agenti di orchestrazione o le utilità di pianificazione di terze parti nelle operazioni quotidiane, è ora possibile richiamare AzureDTExec anziché dtexec.

AzureDTExec esegue i pacchetti come attività Esegui pacchetto SSIS in pipeline Data Factory. Per ulteriori informazioni, vedere [esecuzione di pacchetti SSIS come attività Azure Data Factory](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity). 

AzureDTExec può essere configurato tramite SSMS per l'uso di un'applicazione Azure Active Directory (Azure AD) che genera pipeline nel data factory. Può anche essere configurato per accedere a file System, condivisioni file o File di Azure in cui archiviare i pacchetti. In base ai valori assegnati per le opzioni di chiamata, AzureDTExec genera ed esegue una pipeline Data Factory univoca con un'attività Esegui pacchetto SSIS. Richiamando AzureDTExec con gli stessi valori per le opzioni, viene rieseguita la pipeline esistente.

## <a name="prerequisites"></a>Prerequisiti
Per usare AzureDTExec, scaricare e installare la versione più recente di SSMS, che è la versione 18,3 o successiva. Scaricarlo da [questo sito Web](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

## <a name="configure-the-azuredtexec-utility"></a>Configurare l'utilità AzureDTExec
L'installazione di SSMS nel computer locale installa anche AzureDTExec. Per configurare le impostazioni, avviare SSMS con l'opzione **Esegui come amministratore** . Selezionare quindi **strumenti** > **Esegui la migrazione ad Azure** > **configurare dtexec abilitati per Azure**.

![Configurare il menu dtexec abilitato per Azure](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-menu.png)

Questa azione apre una finestra **AzureDTExecConfig** che deve essere aperta con privilegi amministrativi per la scrittura nel file *AzureDTExec. Settings* . Se SSMS non è stato eseguito come amministratore, viene visualizzata una finestra controllo dell'account utente (UAC). Immettere la password amministratore per elevare i privilegi.

![Configurare le impostazioni dtexec abilitate per Azure](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-settings.png)

Nella finestra **AzureDTExecConfig** immettere le impostazioni di configurazione nel modo seguente:

- **ApplicationID**: immettere l'identificatore univoco dell'app Azure ad creata con le autorizzazioni appropriate per generare pipeline nel data factory. Per altre informazioni, vedere [creare un'app Azure ad e un'entità servizio tramite portale di Azure](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).
- **AuthenticationKey**: immettere la chiave di autenticazione per l'app Azure ad.
- **TenantId**: immettere l'identificatore univoco del tenant di Azure ad, in cui viene creata l'app Azure ad.
- **SubscriptionId**: immettere l'identificatore univoco della sottoscrizione di Azure in cui è stata creata la data factory.
- **ResourceGroup**: immettere il nome del gruppo di risorse di Azure in cui è stata creata la data factory.
- **DataFactory**: immettere il nome del data factory in cui vengono generate le pipeline univoche con l'attività Esegui pacchetto SSIS in base ai valori delle opzioni fornite quando si richiama AzureDTExec.
- **IRName**: immettere il nome del Azure-SSIS IR nel data factory, in cui vengono eseguiti i pacchetti specificati nel percorso Universal Naming Convention (UNC) quando si richiama AzureDTExec.
- **PackageAccessDomain**: immettere le credenziali del dominio per accedere ai pacchetti nel percorso UNC specificato quando si richiama AzureDTExec.
- **PackageAccessUserName**: immettere le credenziali del nome utente per accedere ai pacchetti nel percorso UNC specificato quando si richiama AzureDTExec.
- **PackageAccessPassword**: immettere le credenziali password per accedere ai pacchetti nel percorso UNC specificato quando si richiama AzureDTExec.
- **LogPath**: immettere il percorso UNC della cartella dei log, in cui vengono scritti i file di log delle esecuzioni del pacchetto nel Azure-SSIS IR.
- **LogLevel**: immettere l'ambito selezionato per la registrazione dalle opzioni predefinite **null**, **Basic**, **verbose**o **sulle prestazioni** per le esecuzioni del pacchetto nel Azure-SSIS IR.
- **LogAccessDomain**: immettere le credenziali di dominio per accedere alla cartella dei log nel percorso UNC quando si scrivono i file di log, che è obbligatorio quando si specifica **LogPath** e **LogLevel** non è **null**.
- **LogAccessUserName**: immettere le credenziali del nome utente per accedere alla cartella dei log nel percorso UNC quando si scrivono i file di log, che è necessario quando si specifica **LogPath** e **LogLevel** non è **null**.
- **LogAccessPassword**: immettere le credenziali password per accedere alla cartella dei log nel percorso UNC quando si scrivono i file di log, che è obbligatorio quando si specifica **LogPath** e **LogLevel** non è **null**.
- **PipelineNameHashStrLen**: immettere la lunghezza delle stringhe hash da generare dai valori delle opzioni fornite quando si richiama AzureDTExec. Le stringhe vengono usate per formare nomi univoci per Data Factory pipeline che eseguono i pacchetti nel Azure-SSIS IR. In genere è sufficiente una lunghezza di 32 caratteri.

Per archiviare i pacchetti e i file di log in file System o condivisioni file in locale, aggiungere la Azure-SSIS IR a una rete virtuale connessa alla rete locale in modo da poter recuperare i pacchetti e scrivere i file di log. Per ulteriori informazioni, vedere [aggiungere un Azure-SSIS IR a una rete virtuale](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

Per evitare di visualizzare i valori sensibili scritti nel file *AzureDTExec. Settings* come testo normale, è necessario codificarli in stringhe di codifica Base64. Quando si richiama AzureDTExec, tutte le stringhe con codifica Base64 vengono decodificate nuovamente nei valori originali. È possibile proteggere ulteriormente il file *AzureDTExec. Settings* limitando gli account che possono accedervi.

## <a name="invoke-the-azuredtexec-utility"></a>Richiamare l'utilità AzureDTExec
È possibile richiamare AzureDTExec al prompt della riga di comando e fornire i valori rilevanti per le opzioni specifiche nello scenario del caso d'uso.

L'utilità viene installata in `{SSMS Folder}\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn`. È possibile aggiungere il percorso alla variabile di ambiente ' PATH ' affinché venga richiamato da qualsiasi posizione.

```dos
> cd "C:\Program Files (x86)\Microsoft SQL Server Management Studio 18\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn"
> AzureDTExec.exe  ^
  /F \\MyStorageAccount.file.core.windows.net\MyFileShare\MyPackage.dtsx  ^
  /Conf \\MyStorageAccount.file.core.windows.net\MyFileShare\MyConfig.dtsConfig  ^
  /Conn "MyConnectionManager;Data Source=MyDatabaseServer.database.windows.net;User ID=MyAdminUsername;Password=MyAdminPassword;Initial Catalog=MyDatabase"  ^
  /Set \package.variables[MyVariable].Value;MyValue  ^
  /De MyEncryptionPassword
```

La chiamata di AzureDTExec offre opzioni simili a quelle richiamando dtexec. Per altre informazioni, vedere [dtexec Utility](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017). Ecco le opzioni attualmente supportate:

- **/F [ile]** : carica un pacchetto archiviato in file System, condivisione file o file di Azure. Come valore per questa opzione, è possibile specificare il percorso UNC per il file del pacchetto in file system, condivisione file o File di Azure con la relativa estensione dtsx. Se il percorso UNC specificato contiene spazi, racchiudere l'intero percorso tra virgolette.
- **/Conf [igFile]** : specifica un file di configurazione da cui estrarre i valori. Utilizzando questa opzione, è possibile impostare una configurazione della fase di esecuzione per il pacchetto che differisce da quella specificata in fase di progettazione. È possibile archiviare impostazioni diverse in un file di configurazione XML e quindi caricarle prima dell'esecuzione del pacchetto. Per altre informazioni, vedere [configurazioni di pacchetti SSIS](https://docs.microsoft.com/sql/integration-services/packages/package-configurations?view=sql-server-2017). Per specificare il valore per questa opzione, usare il percorso UNC del file di configurazione in file system, condivisione file o File di Azure con l'estensione dtsConfig. Se il percorso UNC specificato contiene spazi, racchiudere l'intero percorso tra virgolette.
- **/Conn [ection]** : specifica le stringhe di connessione per le gestioni connessioni esistenti nel pacchetto. Utilizzando questa opzione, è possibile impostare le stringhe di connessione di run-time per le gestioni connessioni esistenti nel pacchetto che differiscono da quelle specificate in fase di progettazione. Specificare il valore per questa opzione come indicato di seguito: `connection_manager_name_or_id;connection_string [[;connection_manager_name_or_id;connection_string]...]`.
- **/Set**: esegue l'override della configurazione di un parametro, una variabile, una proprietà, un contenitore, un provider di log, un enumeratore Foreach o una connessione nel pacchetto. Questa opzione può essere specificata più volte. Specificare il valore per questa opzione come indicato di seguito: `property_path;value`. Ad esempio, `\package.variables[counter].Value;1` esegue l'override del valore di `counter` variabile come 1. È possibile utilizzare la **Configurazione guidata pacchetto** per trovare, copiare e incollare il valore di `property_path` per gli elementi del pacchetto di cui si desidera eseguire l'override. Per ulteriori informazioni, vedere [Configurazione guidata pacchetto](https://docs.microsoft.com/sql/integration-services/package-configuration-wizard-ui-reference?view=sql-server-2014).
- **/De [crypt]** : imposta la password di decrittografia per il pacchetto configurato con il livello di protezione **EncryptAllWithPassword**/**EncryptSensitiveWithPassword** .

> [!NOTE]
> La chiamata di AzureDTExec con nuovi valori per le opzioni genera una nuova pipeline, ad eccezione dell'opzione **/de [cript]** .

## <a name="next-steps"></a>Passaggi successivi

Dopo aver richiamato AzureDTExec, le pipeline univoche con l'attività Esegui pacchetto SSIS vengono generate ed eseguite dopo aver richiamato il portale di Data Factory. Per ulteriori informazioni, vedere [esecuzione di pacchetti SSIS come attività Data Factory](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

> [!WARNING]
> È previsto che la pipeline generata venga usata solo da AzureDTExec. Le proprietà o i parametri potrebbero cambiare in futuro, quindi non modificarli o riutilizzarli per altri scopi. Le modifiche potrebbero interrompere AzureDTExec. In tal caso, eliminare la pipeline. AzureDTExec genera una nuova pipeline la volta successiva che viene richiamata.
