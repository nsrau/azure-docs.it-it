---
title: Gestire i pacchetti con Azure-SSIS Integration Runtime archivio pacchetti
description: Informazioni su come gestire i pacchetti con Azure-SSIS Integration Runtime archivio pacchetti.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 09/06/2020
ms.openlocfilehash: 84a7a205e52ba37eb6fcb3b624e0f71a9b9bbc10
ms.sourcegitcommit: 59ea8436d7f23bee75e04a84ee6ec24702fb2e61
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/07/2020
ms.locfileid: "89505489"
---
# <a name="manage-packages-with-azure-ssis-integration-runtime-package-store"></a>Gestire i pacchetti con Azure-SSIS Integration Runtime archivio pacchetti

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Per sollevare & spostare i carichi di lavoro della SQL Server Integration Services locale (SSIS) nel cloud, è possibile effettuare il provisioning di Azure-SSIS Integration Runtime (IR) in Azure Data Factory (ADF). Per altre informazioni, vedere effettuare il [provisioning di un Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure). Azure-SSIS IR supporta:

- Esecuzione di pacchetti distribuiti nel catalogo SSIS (SSISDB) ospitato da un server di database SQL di Azure o da un'istanza gestita (modello di distribuzione del progetto)
- Esecuzione di pacchetti distribuiti nel file system, in File di Azure o in un database SQL Server (MSDB) ospitato da Istanza gestita di database SQL di Azure (modello di distribuzione del pacchetto)

Quando si usa il modello di distribuzione del pacchetto, è possibile scegliere se eseguire il provisioning della Azure-SSIS IR con gli archivi di pacchetti. Forniscono un livello di gestione dei pacchetti oltre a file system, File di Azure o MSDB ospitati da Istanza gestita SQL di Azure. Azure-SSIS IR archivio pacchetti consente di importare/esportare, eliminare/eseguire pacchetti e monitorare/arrestare l'esecuzione di pacchetti tramite SQL Server Management Studio (SSMS) simile all' [Archivio pacchetti SSIS legacy](https://docs.microsoft.com/sql/integration-services/service/package-management-ssis-service?view=sql-server-2017). 

## <a name="connect-to-azure-ssis-ir"></a>Connetti a Azure-SSIS IR

Una volta eseguito il provisioning del Azure-SSIS IR, è possibile connettersi a esso per esplorare gli archivi pacchetti in SSMS.

![Connetti a Azure-SSIS IR](media/azure-ssis-integration-runtime-package-store/ssms-package-store-connect.png)

Nella finestra di **Esplora oggetti** di SSMS selezionare **Azure-SSIS Integration Runtime** nel menu a discesa **Connetti** . Successivamente, accedere ad Azure e selezionare la sottoscrizione pertinente, ADF e Azure-SSIS IR di cui è stato effettuato il provisioning con gli archivi di pacchetti. Il Azure-SSIS IR verrà visualizzato con i nodi **in esecuzione** e i **pacchetti archiviati** sottostanti. Espandere il nodo **pacchetti archiviati** per visualizzare gli archivi dei pacchetti sottostanti. Espandere gli archivi pacchetti per visualizzare le cartelle e i pacchetti sottostanti. È possibile che venga richiesto di immettere le credenziali di accesso per gli archivi pacchetti, se SSMS non riesce a connettersi automaticamente. Se, ad esempio, si espande un archivio pacchetti su MSDB, potrebbe essere richiesto di connettersi prima al Istanza gestita SQL di Azure.

![Connettersi a Istanza gestita SQL di Azure](media/azure-ssis-integration-runtime-package-store/ssms-package-store-connect2.png)

## <a name="manage-folders-and-packages"></a>Gestione di cartelle e pacchetti

Dopo la connessione al Azure-SSIS IR in SSMS, è possibile fare clic con il pulsante destro del mouse su qualsiasi archivio, cartella o pacchetto di pacchetti per visualizzare un menu e selezionare **nuova cartella**, **Importa pacchetto**, **Esporta pacchetto**, **Elimina**o **Aggiorna**.

   ![Gestione di cartelle e pacchetti](media/azure-ssis-integration-runtime-package-store/ssms-package-store-manage.png)

   *  Selezionare **nuova cartella** per creare una nuova cartella per i pacchetti importati.

   *  Selezionare **Importa pacchetto** per importare pacchetti da **File System**, **SQL Server** (msdb) o dall' **Archivio pacchetti SSIS** legacy nell'archivio pacchetti.

      ![Importare il pacchetto](media/azure-ssis-integration-runtime-package-store/ssms-package-store-import.png)

      A seconda del **percorso del pacchetto** da cui eseguire l'importazione, selezionare il tipo di autenticazione **Server**pertinente / **Authentication type**, immettere le credenziali di accesso, se necessario, selezionare il **percorso del pacchetto**e immettere il nuovo **nome del pacchetto**. Quando si importano pacchetti, il livello di protezione non può essere modificato. Per modificarlo, utilizzare SQL Server Data Tools (SSDT) o l' `dtutil` utilità della riga di comando.

      > [!NOTE]
      > L'importazione di pacchetti SSIS in Azure-SSIS IR archivi pacchetti può essere eseguita una sola volta e copiarli semplicemente nel database MSDB/file system/File di Azure sottostante, conservando al tempo stesso la versione SQL Server/SSIS. 
      >
      > Dato che Azure-SSIS IR è attualmente basato su **SQL Server 2017**, l'esecuzione di pacchetti di versioni inferiori ne eseguirà l'aggiornamento nei pacchetti SSIS 2017 in fase di esecuzione. L'esecuzione di pacchetti con versioni successive non è supportata.
      >
      > Inoltre, poiché gli archivi di pacchetti SSIS legacy sono associati a una versione specifica di SQL Server e sono accessibili solo in SSMS per tale versione, i pacchetti di versioni precedenti negli archivi di pacchetti SSIS legacy devono essere esportati in file system prima di poter essere importati negli archivi di pacchetti Azure-SSIS IR usando SSMS 2019 o versioni successive.
      >
      > In alternativa, per importare più pacchetti SSIS in Azure-SSIS IR archivi pacchetti durante il cambio del livello di protezione, è possibile usare l'utilità della riga di comando [dtutil](https://docs.microsoft.com/sql/integration-services/dtutil-utility?view=sql-server-2017) , vedere [distribuzione di più pacchetti con dtutil](#deploying-multiple-packages-with-dtutil).

   *  Selezionare **Esporta pacchetto** per esportare i pacchetti dall'archivio pacchetti nel **file System**, nel **SQL Server** (msdb) o nell' **Archivio pacchetti SSIS**legacy.

      ![Esporta pacchetto](media/azure-ssis-integration-runtime-package-store/ssms-package-store-export.png)

      A seconda del **percorso del pacchetto** in cui eseguire l'esportazione, selezionare il tipo di autenticazione **Server**pertinente / **Authentication type**, immettere le credenziali di accesso, se necessario, e selezionare il **percorso del pacchetto**. Quando si esportano i pacchetti, se sono crittografati, immettere le password per decrittografarli per primi e quindi è possibile modificare il livello di protezione, ad esempio per evitare di archiviare dati sensibili o crittografarli o tutti i dati con la chiave utente o la password.

      > [!NOTE]
      > L'esportazione di pacchetti SSIS da Azure-SSIS IR archivi pacchetti può essere eseguita una sola volta e in questo modo, senza cambiare il livello di protezione, sarà sufficiente copiarli conservando la versione SQL Server/SSIS. in caso contrario, verranno aggiornati nei pacchetti SSIS 2019 o versioni successive.
      >
      > Dato che Azure-SSIS IR è attualmente basato su **SQL Server 2017**, l'esecuzione di pacchetti di versioni inferiori ne eseguirà l'aggiornamento nei pacchetti SSIS 2017 in fase di esecuzione. L'esecuzione di pacchetti con versioni successive non è supportata.
      >
      > In alternativa, per esportare più pacchetti SSIS da Azure-SSIS IR archivi pacchetti durante il cambio del livello di protezione, è possibile usare l'utilità della riga di comando [dtutil](https://docs.microsoft.com/sql/integration-services/dtutil-utility?view=sql-server-2017) , vedere [distribuzione di più pacchetti con dtutil](#deploying-multiple-packages-with-dtutil).

   *  Selezionare **Elimina** per eliminare le cartelle o i pacchetti esistenti dall'archivio pacchetti.

   *  Selezionare **Aggiorna** per visualizzare le cartelle o i pacchetti appena aggiunti nell'archivio pacchetti.

## <a name="execute-packages"></a>Esegui pacchetti

Dopo la connessione al Azure-SSIS IR in SSMS, è possibile fare clic con il pulsante destro del mouse su tutti i pacchetti archiviati per visualizzare un menu e selezionare **Esegui pacchetto**.  Verrà visualizzata la finestra di dialogo **utilità di esecuzione pacchetti** , in cui è possibile configurare le esecuzioni dei pacchetti in Azure-SSIS IR come eseguire le attività del pacchetto SSIS nelle pipeline di ADF.

![Utilità di esecuzione pacchetti pagine 1 & 2](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute.png)

![Utilità di esecuzione pacchetti pagine 3 & 4](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute2.png)

Le pagine **generale**, **configurazioni**, **Opzioni di esecuzione**e **registrazione** della finestra di dialogo **utilità di esecuzione pacchetti** corrispondono alla scheda  **Impostazioni** dell'attività Esegui pacchetto SSIS. In queste pagine è possibile immettere la password di crittografia per il pacchetto e accedere alle informazioni per il file di configurazione del pacchetto. È anche possibile immettere le credenziali e le proprietà di esecuzione del pacchetto, nonché le informazioni di accesso per la cartella dei log.  La pagina **Imposta valori** della finestra di dialogo **utilità di esecuzione pacchetti** corrisponde alla scheda **override proprietà** dell'attività Esegui pacchetto SSIS, in cui è possibile immettere le proprietà del pacchetto esistenti di cui eseguire l'override. Per ulteriori informazioni, vedere [esecuzione di pacchetti SSIS come attività Esegui pacchetto SSIS nelle pipeline di ADF](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

Quando si seleziona il pulsante **Esegui** , viene generata e attivata automaticamente una nuova pipeline ADF con l'attività Esegui pacchetto SSIS. Se una pipeline ADF con le stesse impostazioni esiste già, verrà rieseguita e non verrà generata una nuova pipeline. La pipeline ADF e l'attività Esegui pacchetto SSIS verranno rispettivamente denominate `Pipeline_SSMS_YourPackageName_HashString` e `Activity_SSMS_YourPackageName` .

![Pulsante Utilità di esecuzione pacchetti](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute3.png)

![Attività Esegui pacchetto SSIS](media/azure-ssis-integration-runtime-package-store/ssis-activity-package-store.png)

## <a name="monitor-and-stop-running-packages"></a>Monitorare e arrestare l'esecuzione di pacchetti

Dopo la connessione al Azure-SSIS IR in SSMS, è possibile espandere il nodo **pacchetti in esecuzione** per visualizzare i pacchetti attualmente in esecuzione sotto.  Fare clic con il pulsante destro del mouse su uno di essi per visualizzare un menu e selezionare **Arresta** o **Aggiorna**.

   ![Monitorare e arrestare l'esecuzione di pacchetti](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor.png)

   *  Selezionare **Interrompi** per annullare la pipeline ADF attualmente in esecuzione che esegue il pacchetto come attività Esegui pacchetto SSIS.

   *  Selezionare **Aggiorna** per visualizzare i pacchetti di nuova esecuzione dagli archivi dei pacchetti.

## <a name="monitor-azure-ssis-ir-and-edit-package-stores"></a>Monitorare Azure-SSIS IR e modificare gli archivi pacchetti

Dopo la connessione al Azure-SSIS IR in SSMS, è possibile fare clic con il pulsante destro del mouse su di esso per visualizzare un menu e selezionare **Vai a Azure Data Factory portale** o **Aggiorna**.

   ![Vai al portale di ADF](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor2.png)

   *  Selezionare **Vai al portale Azure Data Factory** per aprire la pagina **Integration Runtimes** dell'hub di monitoraggio ADF, in cui è possibile monitorare la Azure-SSIS IR. Nel riquadro **archivi pacchetti** è possibile visualizzare il numero di archivi pacchetti collegati al Azure-SSIS IR.  Se si seleziona tale numero, viene visualizzata una finestra in cui è possibile modificare i servizi collegati ADF in cui sono archiviate le informazioni di accesso per gli archivi dei pacchetti.

      ![Modifica archivi pacchetti](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor3.png)

   *  Selezionare **Aggiorna** per visualizzare le cartelle o i pacchetti appena aggiunti negli archivi pacchetti ed eseguendo i pacchetti dagli archivi dei pacchetti.

## <a name="deploying-multiple-packages-with-dtutil"></a>Distribuzione di più pacchetti con dtutil

Per sollevare & spostare i carichi di lavoro SSIS locali in SSIS in ADF mantenendo il modello di distribuzione del pacchetto legacy, è necessario distribuire i pacchetti da file system, da MSDB ospitati da SQL Server o da archivi di pacchetti SSIS legacy in File di Azure, MSDB ospitati da Istanza gestita SQL di Azure o archivi di pacchetti di Azure-SSIS IR. Allo stesso tempo, se non è già stato fatto, è necessario impostare il livello di protezione da crittografia tramite chiave utente su non crittografato o crittografia tramite password.

È possibile usare l'utilità della riga di comando [dtutil](https://docs.microsoft.com/sql/integration-services/dtutil-utility?view=sql-server-2017) fornita con l'installazione SQL Server/SSIS per distribuire più pacchetti in batch. È associato a una versione specifica di SSIS, quindi se lo si usa per distribuire pacchetti con versioni precedenti senza cambiare il livello di protezione, sarà sufficiente copiarli mantenendo la versione SSIS. Se lo si usa per distribuirli e cambiare il livello di protezione allo stesso tempo, li aggiornerà nella versione SSIS.

 Dato che Azure-SSIS IR è attualmente basato su **SQL Server 2017**, l'esecuzione di pacchetti di versioni inferiori ne eseguirà l'aggiornamento nei pacchetti SSIS 2017 in fase di esecuzione. L'esecuzione di pacchetti con versioni successive non è supportata.

Di conseguenza, per evitare gli aggiornamenti della fase di esecuzione, la distribuzione dei pacchetti da eseguire su Azure-SSIS IR nel modello di distribuzione del pacchetto deve usare dtutil 2017 che viene fornito con l'installazione di SQL Server/SSIS 2017. Per questo scopo è possibile scaricare e installare gratuitamente [SQL Server/SSIS 2017 Developer Edition](https://go.microsoft.com/fwlink/?linkid=853016) . Una volta installato, è possibile trovare dtutil 2017 in questa cartella: `YourLocalDrive:\Program Files\Microsoft SQL Server\140\DTS\Binn` .

### <a name="deploying-multiple-packages-from-file-system-on-premises-into-azure-files-with-dtutil"></a>Distribuzione di più pacchetti da file system locali in File di Azure con dtutil

 Per distribuire più pacchetti da file system in File di Azure e cambiare il livello di protezione allo stesso tempo, è possibile eseguire i comandi seguenti al prompt dei comandi. Sostituire tutte le stringhe specifiche del caso.
  
```dos
REM Persist the access credentials for Azure Files on your local machine
cmdkey /ADD:YourStorageAccountName.file.core.windows.net /USER:azure\YourStorageAccountName /PASS:YourStorageAccountKey

REM Connect Azure Files to a drive on your local machine
net use Z: \\YourStorageAccountName.file.core.windows.net\YourFileShare /PERSISTENT:Yes

REM Go to a local folder where you store your packages
cd YourLocalDrive:\...\YourPackageFolder

REM Run dtutil in a loop to deploy your packages from the local folder into Azure Files while switching their protection level
for %f in (*.dtsx) do dtutil.exe /FILE %f /ENCRYPT FILE;Z:\%f;2;YourEncryptionPassword
```

Per eseguire i comandi precedenti in un file batch, sostituire `%f` con `%%f` .

Per distribuire più pacchetti da archivi di pacchetti SSIS legacy oltre a file system in File di Azure e cambiare il livello di protezione allo stesso tempo, è possibile usare gli stessi comandi, ma sostituire `YourLocalDrive:\...\YourPackageFolder` con una cartella locale usata dagli archivi pacchetti SSIS legacy: `YourLocalDrive:\Program Files\Microsoft SQL Server\YourSQLServerDefaultCompatibilityLevel\DTS\Packages\YourPackageFolder` . Ad esempio, se l'archivio pacchetti SSIS legacy è associato a SQL Server 2016, passare a `YourLocalDrive:\Program Files\Microsoft SQL Server\130\DTS\Packages\YourPackageFolder` .  È possibile trovare il valore per `YourSQLServerDefaultCompatibilityLevel` da un [elenco di livelli di compatibilità predefiniti SQL Server](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level?view=sql-server-ver15#arguments).

Se sono stati configurati Azure-SSIS IR archiviazioni di pacchetti nella parte superiore di File di Azure, i pacchetti distribuiti verranno visualizzati quando ci si connette al Azure-SSIS IR in SSMS 2019 o versioni successive.

### <a name="deploying-multiple-packages-from-msdb-on-premises-into-msdb-in-azure-with-dtutil"></a>Distribuzione di più pacchetti da MSDB in locale in MSDB in Azure con dtutil

 Per distribuire più pacchetti da MSDB ospitati da SQL Server o archivi di pacchetti SSIS legacy in msdb in MSDB ospitati da SQL Istanza gestita di Azure e cambiare il livello di protezione allo stesso tempo, è possibile connettersi al SQL Server in SSMS, fare clic con il pulsante destro del mouse sul `Databases->System Databases->msdb` nodo nella **Esplora oggetti** di SSMS per aprire una nuova finestra di **query** ed eseguire lo script T-SQL seguente. Sostituire tutte le stringhe specifiche del caso:  
  
```sql
BEGIN
  SELECT 'dtutil /SQL '+f.foldername+'\'+NAME+' /ENCRYPT SQL;'+f.foldername+'\'+NAME+';2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourSQLAuthUsername /DestPassword YourSQLAuthPassword'
  FROM msdb.dbo.sysssispackages p
  inner join msdb.dbo.sysssispackagefolders f
  ON p.folderid = f.folderid
END
```

Per usare l'endpoint privato/pubblico del istanza gestita SQL di Azure, sostituire `YourSQLManagedInstanceEndpoint` con `YourSQLMIName.YourDNSPrefix.database.windows.net` / `YourSQLMIName.public.YourDNSPrefix.database.windows.net,3342` , rispettivamente.

Lo script genererà le righe di comando di dtutil per tutti i pacchetti in MSDB, che è possibile selezionare, copiare & incollare ed eseguire al prompt dei comandi.

![Genera righe di comando dtutil](media/azure-ssis-integration-runtime-package-store/sql-server-msdb-to-sql-mi-msdb.png)

```dos
dtutil /SQL YourFolder\YourPackage1 /ENCRYPT SQL;YourFolder\YourPackage1;2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourUserName /DestPassword YourPassword
dtutil /SQL YourFolder\YourPackage2 /ENCRYPT SQL;YourFolder\YourPackage2;2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourUserName /DestPassword YourPassword
dtutil /SQL YourFolder\YourPackage3 /ENCRYPT SQL;YourFolder\YourPackage3;2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourUserName /DestPassword YourPassword
```

Se sono stati configurati Azure-SSIS IR archivi pacchetti in MSDB, i pacchetti distribuiti verranno visualizzati quando ci si connette al Azure-SSIS IR in SSMS 2019 o versioni successive.

### <a name="deploying-multiple-packages-from-msdb-on-premises-into-azure-files-with-dtutil"></a>Distribuzione di più pacchetti da MSDB in locale a File di Azure con dtutil

 Per distribuire più pacchetti da MSDB ospitati da SQL Server o archivi di pacchetti SSIS legacy su MSDB in File di Azure e cambiare il livello di protezione allo stesso tempo, è possibile connettersi al SQL Server in SSMS, fare clic con il pulsante destro del mouse sul `Databases->System Databases->msdb` nodo nella **Esplora oggetti** di SSMS per aprire una nuova finestra di **query** ed eseguire lo script T-SQL seguente. Sostituire tutte le stringhe specifiche del caso:  
  
```sql
BEGIN
  SELECT 'dtutil /SQL '+f.foldername+'\'+NAME+' /ENCRYPT FILE;Z:\'+f.foldername+'\'+NAME+'.dtsx;2;YourEncryptionPassword' 
  FROM msdb.dbo.sysssispackages p
  inner join msdb.dbo.sysssispackagefolders f
  ON p.folderid = f.folderid
END
```

Lo script genererà le righe di comando di dtutil per tutti i pacchetti in MSDB, che è possibile selezionare, copiare & incollare ed eseguire al prompt dei comandi.

```dos
REM Persist the access credentials for Azure Files on your local machine
cmdkey /ADD:YourStorageAccountName.file.core.windows.net /USER:azure\YourStorageAccountName /PASS:YourStorageAccountKey

REM Connect Azure Files to a drive on your local machine
net use Z: \\YourStorageAccountName.file.core.windows.net\YourFileShare /PERSISTENT:Yes

REM Multiselect, copy & paste, and run the T-SQL-generated dtutil command lines to deploy your packages from MSDB on premises into Azure Files while switching their protection level
dtutil /SQL YourFolder\YourPackage1 /ENCRYPT FILE;Z:\YourFolder\YourPackage1.dtsx;2;YourEncryptionPassword
dtutil /SQL YourFolder\YourPackage2 /ENCRYPT FILE;Z:\YourFolder\YourPackage2.dtsx;2;YourEncryptionPassword
dtutil /SQL YourFolder\YourPackage3 /ENCRYPT FILE;Z:\YourFolder\YourPackage3.dtsx;2;YourEncryptionPassword
```

Se sono stati configurati Azure-SSIS IR archiviazioni di pacchetti nella parte superiore di File di Azure, i pacchetti distribuiti verranno visualizzati quando ci si connette al Azure-SSIS IR in SSMS 2019 o versioni successive.

## <a name="next-steps"></a>Passaggi successivi

È possibile rieseguire/modificare le pipeline di ADF generate automaticamente con le attività Esegui pacchetto SSIS o crearne di nuove nel portale di ADF. Per ulteriori informazioni, vedere [esecuzione di pacchetti SSIS come attività Esegui pacchetto SSIS nelle pipeline di ADF](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).
