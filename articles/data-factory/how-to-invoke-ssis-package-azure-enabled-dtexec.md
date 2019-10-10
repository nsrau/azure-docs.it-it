---
title: Eseguire pacchetti di SQL Server Integration Services (SSIS) con l'utilità dtexec abilitata per Azure | Microsoft Docs
description: Informazioni su come eseguire pacchetti SQL Server Integration Services (SSIS) con l'utilità dtexec abilitata per Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/21/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 740e53728356755bcc42e1e0aafb64992b30e113
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72249023"
---
# <a name="run-sql-server-integration-services-ssis-packages-with-azure-enabled-dtexec-utility"></a>Eseguire pacchetti di SQL Server Integration Services (SSIS) con l'utilità dtexec abilitata per Azure
Questo articolo descrive l'utilità del prompt dei comandi **dtexec** abilitata per Azure (**AzureDTExec**).  Viene usato per eseguire pacchetti SSIS in Azure-SSIS Integration Runtime (IR) in Azure Data Factory (ADF).

L'utilità **dtexec** tradizionale viene fornita con SQL Server. per altre informazioni, vedere la documentazione dell' [utilità dtexec](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017) .  Viene spesso richiamato dagli agenti di orchestrazione/utilità di pianificazione di terze parti, ad esempio batch attivo, controllo-M e così via, per l'esecuzione di pacchetti SSIS in locale.  La moderna utilità **AzureDTExec** viene fornita con lo strumento SQL Server Management Studio (SSMS).  Può anche essere richiamato da agenti di orchestrazione/utilità di pianificazione di terze parti per eseguire pacchetti SSIS in Azure.  Semplifica il sollevamento & lo spostamento/migrazione dei pacchetti SSIS nel cloud.  Dopo la migrazione, se si vuole usare gli agenti di orchestrazione o le utilità di pianificazione di terze parti nelle operazioni quotidiane, è ora possibile richiamare **AzureDTExec** anziché **dtexec**.

**AzureDTExec** eseguirà i pacchetti come attività Esegui pacchetto SSIS nelle pipeline di ADF, vedere l'articolo [eseguire pacchetti SSIS come attività ADF](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) per altre informazioni.  Può essere configurato tramite SSMS per l'uso di un'applicazione Azure Active Directory (AAD) che genera pipeline nel file ADF.  Può anche essere configurato per accedere a file System/condivisioni file/File di Azure in cui archiviare i pacchetti.  In base ai valori configurati per le relative opzioni di chiamata, **AzureDTExec** genererà ed eseguirà un'unica pipeline ADF con l'attività Esegui pacchetto SSIS.  La chiamata di **AzureDTExec** con gli stessi valori per le opzioni rieseguirà la pipeline esistente.

## <a name="prerequisites"></a>Prerequisiti
Per usare **AzureDTExec**, scaricare e installare la versione più recente di SSMS (versione 18,3 o successiva) da [qui](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

## <a name="configure-azuredtexec-utility"></a>Configurare l'utilità AzureDTExec
L'installazione di SSMS nel computer locale installerà anche **AzureDTExec**.  Per configurare le impostazioni, avviare SSMS con l'opzione **Esegui come amministratore** e selezionare gli strumenti della voce di menu a discesa **a cascata-> migrare ad Azure-> configurare dtexec abilitati per Azure**.

![Configurare il menu dtexec abilitato per Azure](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-menu.png)

Verrà visualizzata la finestra **AzureDTExecConfig** che deve essere aperta con privilegi amministrativi per la scrittura nel file **AzureDTExec. Settings** .  Se SSMS non è stato eseguito come amministratore, viene visualizzata una finestra controllo dell'account utente per consentire all'utente di immettere la password amministratore per elevare i privilegi.

![Configurare le impostazioni dtexec abilitate per Azure](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-settings.png)

Nella finestra **AzureDTExecConfig** è possibile immettere le impostazioni di configurazione nel modo seguente:

- **ApplicationID**: Immettere l'identificatore univoco dell'app AAD creata con le autorizzazioni appropriate per generare le pipeline nel file ADF, vedere [creare un'app AAD e un'entità servizio tramite portale di Azure](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) articolo per altre informazioni.

- **AuthenticationKey**: Immettere la chiave di autenticazione per l'app AAD.

- **TenantId**: Immettere l'identificatore univoco del tenant AAD in cui viene creata l'app AAD.

- **SubscriptionId**: Immettere l'identificatore univoco della sottoscrizione di Azure in cui è stato creato il file ADF.

- **Gruppo di risorse**: Immettere il nome del gruppo di risorse di Azure in cui è stato creato il file ADF.

- **DataFactory**:  Immettere il nome del file ADF, in cui le pipeline univoche con l'attività Esegui pacchetto SSIS vengono generate in base ai valori delle opzioni fornite durante la chiamata di **AzureDTExec**.

- **IRName**: Immettere il nome del Azure-SSIS IR nel file ADF, in cui vengono eseguiti i pacchetti specificati nel percorso di Universal Naming Convention (UNC) quando viene richiamato **AzureDTExec** .

- **PackageAccessDomain**: Immettere le credenziali del dominio per accedere ai pacchetti nel percorso UNC specificato quando si richiama **AzureDTExec**.

- **PackageAccessUserName**:  Immettere le credenziali del nome utente per accedere ai pacchetti nel percorso UNC specificato quando si richiama **AzureDTExec**.

- **PackageAccessPassword**: Immettere le credenziali password per accedere ai pacchetti nel percorso UNC specificato quando si richiama **AzureDTExec**.

- **LogPath**:  Immettere il percorso UNC della cartella di log in cui verranno scritti i file di log delle esecuzioni dei pacchetti in Azure-SSIS IR.

- **LogLevel**:  Immettere l'ambito selezionato per la registrazione dalle opzioni di**prestazioni** predefinite /**Basic**/ con **valori** **dettagliati**/ per le esecuzioni dei pacchetti in Azure-SSIS IR.

- **LogAccessDomain**: Immettere le credenziali del dominio per accedere alla cartella dei log nel relativo percorso UNC durante la scrittura dei file di log, necessario quando si specifica **LogPath** e **LogLevel** non è **null**.

- **LogAccessUserName**: Immettere le credenziali del nome utente per accedere alla cartella dei log nel percorso UNC durante la scrittura dei file di log, necessario quando si specifica **LogPath** e **LogLevel** non è **null**.

- **LogAccessPassword**: Immettere le credenziali password per accedere alla cartella dei log nel percorso UNC durante la scrittura dei file di log, necessario quando si specifica **LogPath** e **LogLevel** non è **null**.

- **PipelineNameHashStrLen**: Immettere la lunghezza delle stringhe hash da generare dai valori delle opzioni fornite quando si richiama **AzureDTExec**.  Le stringhe verranno utilizzate per creare nomi univoci per le pipeline ADF che eseguono i pacchetti in Azure-SSIS IR.  In genere è sufficiente una lunghezza di 32 caratteri.

Se si prevede di archiviare i pacchetti e i file di log in file System o condivisioni file in locale, è necessario aggiungere la Azure-SSIS IR a una VNet connessa alla rete locale, in modo da poter recuperare i pacchetti e scrivere i file di log, vedere [aggiungere Azure-SSIS IR a un VNet](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network) Per ulteriori informazioni.

Per evitare di visualizzare i valori sensibili scritti nel file **AzureDTExec. Settings** come testo normale, verranno codificati in stringhe di codifica Base64.  Quando si richiama **AzureDTExec**, tutte le stringhe con codifica Base64 verranno decodificate nuovamente nei valori originali.  È possibile proteggere ulteriormente il file **AzureDTExec. Settings** limitando gli account che possono accedervi.

## <a name="invoke-azuredtexec-utility"></a>Richiama utilità AzureDTExec
È possibile richiamare **AzureDTExec** al prompt della riga di comando e fornire i valori rilevanti per le opzioni specifiche nello scenario del caso d'uso.

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

La chiamata di **AzureDTExec** offre opzioni simili a quelle richiamando **dtexec**. per ulteriori informazioni, vedere la documentazione dell' [utilità dtexec](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017) .  Ecco le opzioni attualmente supportate:

- **/F [ile]** : Carica un pacchetto archiviato in file system/condivisione file/File di Azure.  Come valore per questa opzione, è possibile specificare il percorso UNC per il file del pacchetto in file system/condivisione file/File di Azure con l'estensione dtsx.  Se il percorso UNC specificato contiene spazio, è necessario racchiudere l'intero percorso tra virgolette.

- **/Conf [igFile]** : Specifica un file di configurazione da cui estrarre i valori.  Utilizzando questa opzione, è possibile impostare una configurazione della fase di esecuzione per il pacchetto che differisce da quella specificata in fase di progettazione.  È possibile archiviare impostazioni diverse in un file di configurazione XML e quindi caricarle prima dell'esecuzione del pacchetto.  Per altre informazioni, vedere l'articolo [configurazioni di pacchetti SSIS](https://docs.microsoft.com/sql/integration-services/packages/package-configurations?view=sql-server-2017) .  Come valore per questa opzione, è possibile specificare il percorso UNC per il file di configurazione in file system/condivisione file/File di Azure con l'estensione dtsConfig.  Se il percorso UNC specificato contiene spazio, è necessario racchiudere l'intero percorso tra virgolette.

- **/Conn [ection]** : Specifica le stringhe di connessione per le gestioni connessioni esistenti nel pacchetto.  Utilizzando questa opzione, è possibile impostare le stringhe di connessione di run-time per le gestioni connessioni esistenti nel pacchetto che differiscono da quelle specificate in fase di progettazione.  Come valore per questa opzione, è possibile specificarlo come segue: `connection_manager_name_or_id;connection_string [[;connection_manager_name_or_id;connection_string]...]`.

- **/Set**: Esegue l'override della configurazione di un parametro, una variabile, una proprietà, un contenitore, un provider di log, un enumeratore Foreach o una connessione nel pacchetto.  Questa opzione può essere specificata più volte.  Come valore per questa opzione, è possibile specificarlo come segue: `property_path;value`, ad esempio `\package.variables[counter].Value;1` esegue l'override del valore della variabile `counter` come 1.  È possibile utilizzare la configurazione guidata pacchetto per trovare, copiare e incollare il valore di `property_path` per gli elementi del pacchetto di cui si desidera eseguire l'override, vedere la documentazione relativa alla [Configurazione guidata pacchetti](https://docs.microsoft.com/sql/integration-services/package-configuration-wizard-ui-reference?view=sql-server-2014) per ulteriori informazioni.

- **/De [crypt]** : Imposta la password di decrittografia per il pacchetto configurato con il livello di protezione **EncryptAllWithPassword**/**EncryptSensitiveWithPassword** .

> [!NOTE]
> La chiamata di **AzureDTExec** con nuovi valori per le opzioni genererà una nuova pipeline, ad eccezione dell'opzione **/de [cript]** .

## <a name="next-steps"></a>Passaggi successivi

Una volta che le pipeline univoche con l'attività Esegui pacchetto SSIS sono generate ed eseguite dopo la chiamata di **AzureDTExec**, è possibile monitorarle nel portale di ADF. Per altre informazioni, vedere [eseguire pacchetti SSIS come attività di ADF](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) .

> [!WARNING]
> È previsto che la pipeline generata venga usata solo da **AzureDTExec**. Le proprietà e i parametri possono cambiare in futuro, quindi non è necessario modificarli o riutilizzarli per altri scopi, che potrebbero interrompere **AzureDTExec**. In tal caso, è sempre possibile eliminare la pipeline e **AzureDTExec** genererà una nuova pipeline la volta successiva che viene richiamata.
