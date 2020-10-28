---
title: Eseguire un pacchetto SSIS con l'attività Esegui pacchetto SSIS
description: Questo articolo descrive come eseguire un pacchetto di SQL Server Integration Services (SSIS) in una pipeline Azure Data Factory usando l'attività Esegui pacchetto SSIS.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.author: sawinark
author: swinarko
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.date: 07/20/2020
ms.openlocfilehash: 1bd983f7faeff456b04d4b2958236193a827a2cc
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92635015"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>Eseguire un pacchetto SSIS tramite l'attività Esegui pacchetto SSIS in Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Questo articolo descrive come eseguire un pacchetto di SQL Server Integration Services (SSIS) in una pipeline Azure Data Factory usando l'attività Esegui pacchetto SSIS. 

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Creare un runtime di integrazione SSIS di Azure (IR) se non è già stato fatto seguendo le istruzioni dettagliate riportate nell' [esercitazione: provisioning Azure-SSIS IR](./tutorial-deploy-ssis-packages-azure.md).

## <a name="run-a-package-in-the-azure-portal"></a>Eseguire un pacchetto nel portale di Azure
In questa sezione si usa l'interfaccia utente Data Factory o l'app per creare una pipeline Data Factory con un'attività Esegui pacchetto SSIS che esegue il pacchetto SSIS.

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Creare una pipeline con un'attività di esecuzione del pacchetto SSIS
In questo passaggio si usa l'interfaccia utente o l'app Data Factory per creare una pipeline. È necessario aggiungere un'attività Esegui pacchetto SSIS alla pipeline e configurarla per l'esecuzione del pacchetto SSIS. 

1. Nel Data Factory panoramica o home page nel portale di Azure selezionare il riquadro **autore & monitoraggio** per avviare l'interfaccia utente o l'app data factory in una scheda separata. 

   ![Home page di Data factory](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)

   Nella pagina **Attività iniziali** selezionare **Create pipeline** (Crea pipeline). 

   ![Pagina delle attività iniziali](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)

1. Nella casella degli strumenti **attività** espandere **generale** . Trascinare quindi un'attività **Esegui pacchetto SSIS** nell'area di progettazione della pipeline. 

   ![Trascinare l'attività Esegui pacchetto SSIS nell'area di progettazione](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

   Selezionare l'oggetto attività Esegui pacchetto SSIS per configurare le schede **generale** , **Impostazioni** , **parametri SSIS** , **gestioni connessioni** e **sostituzioni proprietà** .

#### <a name="general-tab"></a>Scheda Generale

Nella scheda **generale** dell'attività Esegui pacchetto SSIS completare i passaggi seguenti.

![Impostare le proprietà nella scheda Generale](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

   1. Per **nome** immettere il nome dell'attività Esegui pacchetto SSIS.

   1. Per **Descrizione** immettere la descrizione dell'attività Esegui pacchetto SSIS.

   1. Per **timeout** , immettere il periodo di tempo massimo in cui è possibile eseguire l'attività Esegui pacchetto SSIS. Il valore predefinito è 7 giorni, il formato è D. HH: MM: SS.

   1. Per **riprovare** , immettere il numero massimo di tentativi per l'attività Esegui pacchetto SSIS.

   1. Per **intervallo tentativi** immettere il numero di secondi tra i tentativi per l'attività Esegui pacchetto SSIS. Il valore predefinito è 30 secondi.

   1. Selezionare la casella di controllo **output protetto** per scegliere se si desidera escludere l'output dell'attività Esegui pacchetto SSIS dalla registrazione.

   1. Selezionare la casella di controllo **input protetto** per scegliere se si desidera escludere l'input dell'attività Esegui pacchetto SSIS dalla registrazione.

#### <a name="settings-tab"></a>Scheda Settings

Nella scheda **Impostazioni** dell'attività Esegui pacchetto SSIS completare i passaggi seguenti.

![Impostare le proprietà nella scheda Impostazioni - Modalità automatica](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

   1. Per **Azure-SSIS IR** , selezionare il Azure-SSIS IR designato per eseguire l'attività Esegui pacchetto SSIS.

   1. Per **Descrizione** immettere la descrizione dell'attività Esegui pacchetto SSIS.

   1. Selezionare la casella di controllo **autenticazione di Windows** per scegliere se si desidera utilizzare l'autenticazione di Windows per accedere agli archivi dati, ad esempio le condivisioni file o i server SQL in locale o file di Azure.
   
      Se si seleziona questa casella di controllo, immettere i valori per le credenziali di esecuzione del pacchetto nelle caselle **dominio** , **nome utente** e **password** . Ad esempio, per accedere File di Azure, il dominio è `Azure` , il nome utente è `<storage account name>` e la password è `<storage account key>` .

      In alternativa, è possibile usare i segreti archiviati nel Azure Key Vault come valori. A tale scopo, selezionare la casella di controllo **Azure Key Vault** accanto ad esse. Selezionare o modificare il servizio collegato di Key Vault esistente o crearne uno nuovo. Selezionare quindi il nome e la versione del segreto per il valore. Quando si crea o si modifica il servizio collegato di Key Vault, è possibile selezionare o modificare l'insieme di credenziali delle chiavi esistente o crearne uno nuovo. Assicurarsi di concedere l'accesso Data Factory identità gestita all'insieme di credenziali delle chiavi, se non è già stato fatto. È anche possibile immettere il segreto direttamente nel formato seguente: `<key vault linked service name>/<secret name>/<secret version>` .
      
   1. Selezionare la casella di controllo **Runtime a 32 bit** per scegliere se il pacchetto richiede l'esecuzione del runtime a 32 bit.

   1. Per **percorso pacchetto** selezionare **SSISDB** , **file System (pacchetto)** , **file System (progetto)** , **pacchetto incorporato** o **Archivio pacchetti** . 

##### <a name="package-location-ssisdb"></a>Percorso pacchetto: SSISDB

**SSISDB** quando il percorso del pacchetto viene selezionato automaticamente se è stato effettuato il provisioning del Azure-SSIS IR con un catalogo SSIS (SSISDB) ospitato dal server di database SQL di Azure/istanza gestita oppure è possibile selezionarlo autonomamente. Se questa opzione è selezionata, completare i passaggi seguenti.

   1. Se la Azure-SSIS IR è in esecuzione e la casella di controllo **voci manuali** è deselezionata, esplorare e selezionare le cartelle, i progetti, i pacchetti e gli ambienti esistenti da SSISDB. Selezionare **Aggiorna** per recuperare le cartelle, i progetti, i pacchetti o gli ambienti appena aggiunti da SSISDB, in modo che siano disponibili per l'esplorazione e la selezione. Per individuare e selezionare gli ambienti per le esecuzioni del pacchetto, è necessario configurare i progetti in anticipo per aggiungere tali ambienti come riferimenti dalle stesse cartelle in SSISDB. Per altre informazioni, vedere [creare ed eseguire il mapping di ambienti SSIS](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages).

   1. Per **Livello di registrazione** selezionare un ambito predefinito di registrazione per l'esecuzione del pacchetto. Selezionare la casella di controllo **personalizzata** se si desidera immettere invece il nome di registrazione personalizzato. 

   1. Se il Azure-SSIS IR non è in esecuzione o la casella di controllo **voci manuali** è selezionata, immettere i percorsi del pacchetto e dell'ambiente da SSISDB direttamente nei formati seguenti: `<folder name>/<project name>/<package name>.dtsx` e `<folder name>/<environment name>` .

      ![Impostare le proprietà nella scheda Impostazioni - Modalità manuale](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png)

##### <a name="package-location-file-system-package"></a>Percorso pacchetto: file System (pacchetto)

Il **file System (pacchetto)** come percorso del pacchetto viene selezionato automaticamente se è stato effettuato il provisioning del Azure-SSIS IR senza SSISDB oppure è possibile selezionarlo autonomamente. Se questa opzione è selezionata, completare i passaggi seguenti.

![Impostare le proprietà nella scheda Impostazioni-file System (pacchetto)](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings3.png)
   
   1. Specificare il pacchetto da eseguire fornendo un percorso di Universal Naming Convention (UNC) al file del pacchetto (con `.dtsx` ) nella casella **percorso pacchetto** . È possibile cercare e selezionare il pacchetto selezionando **Esplora file archiviazione** oppure immettere il percorso manualmente. Se ad esempio si archivia il pacchetto in File di Azure, il percorso è `\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx` . 
   
   1. Se il pacchetto viene configurato in un file separato, è necessario specificare anche un percorso UNC del file di configurazione (con `.dtsConfig` ) nella casella **percorso configurazione** . È possibile cercare e selezionare la configurazione selezionando **Esplora file archiviazione** oppure immettere il percorso manualmente. Se, ad esempio, si archivia la configurazione in File di Azure, il percorso è `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig` .

   1. Specificare le credenziali per accedere ai file di configurazione e del pacchetto. Se in precedenza sono stati immessi i valori per le credenziali di esecuzione del pacchetto (per **l'autenticazione di Windows** ), è possibile riutilizzarli selezionando la casella di controllo **corrispondente alle credenziali di esecuzione del pacchetto** . In caso contrario, immettere i valori per le credenziali di accesso al pacchetto nelle caselle **dominio** , **nome utente** e **password** . Se ad esempio si archiviano il pacchetto e la configurazione in File di Azure, il dominio è `Azure` , il nome utente è `<storage account name>` e la password è `<storage account key>` . 

      In alternativa, è possibile usare i segreti archiviati nel Azure Key Vault come valori. A tale scopo, selezionare la casella di controllo **Azure Key Vault** accanto ad esse. Selezionare o modificare il servizio collegato di Key Vault esistente o crearne uno nuovo. Selezionare quindi il nome e la versione del segreto per il valore. Quando si crea o si modifica il servizio collegato di Key Vault, è possibile selezionare o modificare l'insieme di credenziali delle chiavi esistente o crearne uno nuovo. Assicurarsi di concedere l'accesso Data Factory identità gestita all'insieme di credenziali delle chiavi, se non è già stato fatto. È anche possibile immettere il segreto direttamente nel formato seguente: `<key vault linked service name>/<secret name>/<secret version>` . 

      Queste credenziali vengono utilizzate anche per accedere ai pacchetti figlio nell'attività Esegui pacchetto a cui viene fatto riferimento dal rispettivo percorso e da altre configurazioni specificate nei pacchetti. 

   1. Se è stato usato il livello di protezione **EncryptAllWithPassword** o **EncryptSensitiveWithPassword** al momento della creazione del pacchetto tramite SQL Server Data Tools (SSDT), immettere il valore della password nella casella **password di crittografia** . In alternativa, è possibile usare un segreto archiviato nel Azure Key Vault come valore (vedere sopra).
      
      Se è stato usato il livello di protezione **EncryptSensitiveWithUserKey** , immettere nuovamente i valori sensibili nei file di configurazione o nelle schede **parametri SSIS** , **gestioni connessioni** o **override proprietà** (vedere di seguito).
      
      Se è stato usato il livello di protezione **EncryptAllWithUserKey** , non è supportato. È necessario riconfigurare il pacchetto per usare un altro livello di protezione tramite SSDT o l' `dtutil` utilità da riga di comando. 

   1. Per **Livello di registrazione** selezionare un ambito predefinito di registrazione per l'esecuzione del pacchetto. Selezionare la casella di controllo **personalizzata** se si desidera immettere invece il nome di registrazione personalizzato. 
   
   1. Se si desidera registrare le esecuzioni dei pacchetti oltre a usare i provider di log standard che possono essere specificati nel pacchetto, specificare la cartella dei log specificando il percorso UNC nella casella **percorso di registrazione** . È possibile cercare e selezionare la cartella dei log selezionando **Esplora file archiviazione** oppure immettere il percorso manualmente. Se ad esempio si archiviano i log in File di Azure, il percorso di registrazione è `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>` . Viene creata una sottocartella in questo percorso per ogni singola esecuzione del pacchetto, denominata dopo l'ID esecuzione dell'attività Esegui pacchetto SSIS e in cui i file di log vengono generati ogni cinque minuti. 
   
   1. Specificare le credenziali per accedere alla cartella dei log. Se in precedenza sono stati immessi i valori per le credenziali di accesso al pacchetto (vedere sopra), è possibile riutilizzarli selezionando la stessa casella di controllo **credenziali di accesso al pacchetto** . In caso contrario, immettere i valori per le credenziali di accesso alla registrazione nelle caselle **dominio** , **nome utente** e **password** . Se ad esempio si archiviano i log in File di Azure, il dominio è `Azure` , il nome utente è `<storage account name>` e la password è `<storage account key>` . In alternativa, è possibile usare i segreti archiviati nel Azure Key Vault come valori (vedere sopra).
   
Per tutti i percorsi UNC indicati in precedenza, il nome completo del file deve essere composto da meno di 260 caratteri. Il nome della directory deve essere composto da meno di 248 caratteri.

##### <a name="package-location-file-system-project"></a>Percorso pacchetto: file System (progetto)

Se si seleziona **file System (Project)** come percorso del pacchetto, completare i passaggi seguenti.

![Impostare le proprietà nella scheda Impostazioni-file System (progetto)](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings4.png)

   1. Specificare il pacchetto da eseguire fornendo un percorso UNC per il file di progetto (con `.ispac` ) nella casella **percorso progetto** e un file di pacchetto (con `.dtsx` ) dal progetto nella casella **nome pacchetto** . È possibile cercare e selezionare il progetto selezionando **Esplora file archiviazione** oppure immettere il percorso manualmente. Se, ad esempio, si archivia il progetto in File di Azure, il percorso è `\\<storage account name>.file.core.windows.net\<file share name>\<project name>.ispac` .

   1. Specificare le credenziali per accedere ai file del progetto e del pacchetto. Se in precedenza sono stati immessi i valori per le credenziali di esecuzione del pacchetto (per **l'autenticazione di Windows** ), è possibile riutilizzarli selezionando la casella di controllo **corrispondente alle credenziali di esecuzione del pacchetto** . In caso contrario, immettere i valori per le credenziali di accesso al pacchetto nelle caselle **dominio** , **nome utente** e **password** . Se ad esempio si archiviano il progetto e il pacchetto in File di Azure, il dominio è `Azure` , il nome utente è `<storage account name>` e la password è `<storage account key>` . 

      In alternativa, è possibile usare i segreti archiviati nel Azure Key Vault come valori. A tale scopo, selezionare la casella di controllo **Azure Key Vault** accanto ad esse. Selezionare o modificare il servizio collegato di Key Vault esistente o crearne uno nuovo. Selezionare quindi il nome e la versione del segreto per il valore. Quando si crea o si modifica il servizio collegato di Key Vault, è possibile selezionare o modificare l'insieme di credenziali delle chiavi esistente o crearne uno nuovo. Assicurarsi di concedere l'accesso Data Factory identità gestita all'insieme di credenziali delle chiavi, se non è già stato fatto. È anche possibile immettere il segreto direttamente nel formato seguente: `<key vault linked service name>/<secret name>/<secret version>` . 

      Queste credenziali vengono utilizzate anche per accedere ai pacchetti figlio nell'attività Esegui pacchetto a cui viene fatto riferimento dallo stesso progetto. 

   1. Se è stato usato il livello di protezione **EncryptAllWithPassword** o **EncryptSensitiveWithPassword** al momento della creazione del pacchetto tramite SSDT, immettere il valore della password nella casella **password di crittografia** . In alternativa, è possibile usare un segreto archiviato nel Azure Key Vault come valore (vedere sopra).
      
      Se è stato usato il livello di protezione **EncryptSensitiveWithUserKey** , immettere nuovamente i valori sensibili nelle schede **parametri SSIS** , **gestioni connessioni** o **sostituzioni proprietà** (vedere di seguito).
      
      Se è stato usato il livello di protezione **EncryptAllWithUserKey** , non è supportato. È necessario riconfigurare il pacchetto per usare un altro livello di protezione tramite SSDT o l' `dtutil` utilità da riga di comando. 

   1. Per **Livello di registrazione** selezionare un ambito predefinito di registrazione per l'esecuzione del pacchetto. Selezionare la casella di controllo **personalizzata** se si desidera immettere invece il nome di registrazione personalizzato. 
   
   1. Se si desidera registrare le esecuzioni dei pacchetti oltre a usare i provider di log standard che possono essere specificati nel pacchetto, specificare la cartella dei log specificando il percorso UNC nella casella **percorso di registrazione** . È possibile cercare e selezionare la cartella dei log selezionando **Esplora file archiviazione** oppure immettere il percorso manualmente. Se ad esempio si archiviano i log in File di Azure, il percorso di registrazione è `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>` . Viene creata una sottocartella in questo percorso per ogni singola esecuzione del pacchetto, denominata dopo l'ID esecuzione dell'attività Esegui pacchetto SSIS e in cui i file di log vengono generati ogni cinque minuti. 
   
   1. Specificare le credenziali per accedere alla cartella dei log. Se in precedenza sono stati immessi i valori per le credenziali di accesso al pacchetto (vedere sopra), è possibile riutilizzarli selezionando la stessa casella di controllo **credenziali di accesso al pacchetto** . In caso contrario, immettere i valori per le credenziali di accesso alla registrazione nelle caselle **dominio** , **nome utente** e **password** . Se ad esempio si archiviano i log in File di Azure, il dominio è `Azure` , il nome utente è `<storage account name>` e la password è `<storage account key>` . In alternativa, è possibile usare i segreti archiviati nel Azure Key Vault come valori (vedere sopra).
   
Per tutti i percorsi UNC indicati in precedenza, il nome completo del file deve essere composto da meno di 260 caratteri. Il nome della directory deve essere composto da meno di 248 caratteri.

##### <a name="package-location-embedded-package"></a>Percorso pacchetto: pacchetto incorporato

Se si seleziona **pacchetto incorporato** come percorso del pacchetto, completare i passaggi seguenti.

![Impostare le proprietà nella scheda Impostazioni-pacchetto incorporato](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings5.png)

   1. Trascinare e rilasciare il file del pacchetto (con `.dtsx` ) o **caricarlo** da una cartella di file nella casella specificata. Il pacchetto verrà automaticamente compresso e incorporato nel payload dell'attività. Una volta incorporato, è possibile **scaricare** il pacchetto in un secondo momento per la modifica. È anche possibile **parametrizzare** il pacchetto incorporato assegnando il pacchetto a un parametro della pipeline che può essere usato in più attività, ottimizzando quindi le dimensioni del payload della pipeline. L'incorporamento dei file di progetto (con `.ispac` ) non è attualmente supportato, pertanto non è possibile utilizzare parametri SSIS/gestioni connessioni con ambito a livello di progetto nei pacchetti incorporati.
   
   1. Se il pacchetto incorporato non è completamente crittografato e si rileva l'utilizzo dell'attività Esegui pacchetto (EPT), la casella di controllo **Esegui attività pacchetto** verrà selezionata automaticamente e i pacchetti figlio a cui viene fatto riferimento nel percorso di file System verranno aggiunti automaticamente, quindi è possibile incorporarli anche.
   
      Se non è possibile rilevare l'uso di EPT, è necessario selezionare manualmente l' **attività Esegui pacchetto** e aggiungere i pacchetti figlio a cui fa riferimento il percorso di file System uno alla volta, in modo da poterli incorporare anche. Se i pacchetti figlio vengono archiviati nel database di SQL Server (MSDB), non è possibile incorporarli, pertanto è necessario assicurarsi che il Azure-SSIS IR possa accedere a MSDB per recuperarli usando i relativi riferimenti di SQL Server. L'incorporamento dei file di progetto (con `.ispac` ) non è attualmente supportato, pertanto non è possibile usare riferimenti basati sul progetto per i pacchetti figlio.
   
   1. Se è stato usato il livello di protezione **EncryptAllWithPassword** o **EncryptSensitiveWithPassword** al momento della creazione del pacchetto tramite SSDT, immettere il valore della password nella casella **password di crittografia** . 
   
      In alternativa, è possibile usare un segreto archiviato nel Azure Key Vault come valore. A tale scopo, selezionare la casella di controllo **Azure Key Vault** accanto a essa. Selezionare o modificare il servizio collegato di Key Vault esistente o crearne uno nuovo. Selezionare quindi il nome e la versione del segreto per il valore. Quando si crea o si modifica il servizio collegato di Key Vault, è possibile selezionare o modificare l'insieme di credenziali delle chiavi esistente o crearne uno nuovo. Assicurarsi di concedere l'accesso Data Factory identità gestita all'insieme di credenziali delle chiavi, se non è già stato fatto. È anche possibile immettere il segreto direttamente nel formato seguente: `<key vault linked service name>/<secret name>/<secret version>` .
      
      Se è stato usato il livello di protezione **EncryptSensitiveWithUserKey** , immettere nuovamente i valori sensibili nei file di configurazione o nelle schede **parametri SSIS** , **gestioni connessioni** o **override proprietà** (vedere di seguito).
      
      Se è stato usato il livello di protezione **EncryptAllWithUserKey** , non è supportato. È necessario riconfigurare il pacchetto per usare un altro livello di protezione tramite SSDT o l' `dtutil` utilità da riga di comando.

   1. Per **Livello di registrazione** selezionare un ambito predefinito di registrazione per l'esecuzione del pacchetto. Selezionare la casella di controllo **personalizzata** se si desidera immettere invece il nome di registrazione personalizzato. 
   
   1. Se si desidera registrare le esecuzioni dei pacchetti oltre a usare i provider di log standard che possono essere specificati nel pacchetto, specificare la cartella dei log specificando il percorso UNC nella casella **percorso di registrazione** . È possibile cercare e selezionare la cartella dei log selezionando **Esplora file archiviazione** oppure immettere il percorso manualmente. Se ad esempio si archiviano i log in File di Azure, il percorso di registrazione è `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>` . Viene creata una sottocartella in questo percorso per ogni singola esecuzione del pacchetto, denominata dopo l'ID esecuzione dell'attività Esegui pacchetto SSIS e in cui i file di log vengono generati ogni cinque minuti. 
   
   1. Specificare le credenziali per accedere alla cartella dei log immettendo i relativi valori nelle caselle **dominio** , **nome utente** e **password** . Se ad esempio si archiviano i log in File di Azure, il dominio è `Azure` , il nome utente è `<storage account name>` e la password è `<storage account key>` . In alternativa, è possibile usare i segreti archiviati nel Azure Key Vault come valori (vedere sopra).
   
Per tutti i percorsi UNC indicati in precedenza, il nome completo del file deve essere composto da meno di 260 caratteri. Il nome della directory deve essere composto da meno di 248 caratteri.

##### <a name="package-location-package-store"></a>Percorso pacchetto: archivio pacchetti

Se si seleziona **Archivio pacchetti** come percorso del pacchetto, completare i passaggi seguenti.

![Impostare le proprietà nella scheda Impostazioni-archivio pacchetti](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings6.png)
   
   1. Per **Nome archivio pacchetti** selezionare un archivio pacchetti esistente collegato al Azure-SSIS IR.

   1. Consente di specificare il pacchetto da eseguire specificando il relativo percorso (senza `.dtsx` ) nell'archivio pacchetti selezionato nella casella **percorso pacchetto** . Se l'archivio pacchetti selezionato è sopra file system/File di Azure, è possibile cercare e selezionare il pacchetto selezionando **Esplora file archiviazione** . in caso contrario, è possibile immettere il percorso nel formato `<folder name>\<package name>` . È anche possibile importare nuovi pacchetti nell'archivio pacchetti selezionato tramite SQL Server Management Studio (SSMS) simile all' [Archivio pacchetti SSIS legacy](/sql/integration-services/service/package-management-ssis-service?view=sql-server-2017). Per altre informazioni, vedere [Gestire i pacchetti SSIS con archivi pacchetti Azure-SSIS IR](./azure-ssis-integration-runtime-package-store.md).

   1. Se il pacchetto viene configurato in un file separato, è necessario specificare un percorso UNC del file di configurazione (con `.dtsConfig` ) nella casella **percorso configurazione** . È possibile cercare e selezionare la configurazione selezionando **Esplora file archiviazione** oppure immettere il percorso manualmente. Se, ad esempio, si archivia la configurazione in File di Azure, il percorso è `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig` .

   1. Selezionare la casella di controllo **configurazione credenziali di accesso** per scegliere se si desidera specificare le credenziali per accedere al file di configurazione separatamente. Questa operazione è necessaria quando l'archivio pacchetti selezionato si trova nella parte superiore del database di SQL Server (MSDB) ospitato dal Istanza gestita SQL di Azure o non archivia anche il file di configurazione.
   
      Se in precedenza sono stati immessi i valori per le credenziali di esecuzione del pacchetto (per **l'autenticazione di Windows** ), è possibile riutilizzarli selezionando la casella di controllo **corrispondente alle credenziali di esecuzione del pacchetto** . In caso contrario, immettere i valori per le credenziali di accesso alla configurazione nelle caselle **dominio** , **nome utente** e **password** . Se ad esempio si archivia la configurazione in File di Azure, il dominio è `Azure` , il nome utente è `<storage account name>` e la password è `<storage account key>` . 

      In alternativa, è possibile usare i segreti archiviati nel Azure Key Vault come valori. A tale scopo, selezionare la casella di controllo **Azure Key Vault** accanto ad esse. Selezionare o modificare il servizio collegato di Key Vault esistente o crearne uno nuovo. Selezionare quindi il nome e la versione del segreto per il valore. Quando si crea o si modifica il servizio collegato di Key Vault, è possibile selezionare o modificare l'insieme di credenziali delle chiavi esistente o crearne uno nuovo. Assicurarsi di concedere l'accesso Data Factory identità gestita all'insieme di credenziali delle chiavi, se non è già stato fatto. È anche possibile immettere il segreto direttamente nel formato seguente: `<key vault linked service name>/<secret name>/<secret version>` .

   1. Se è stato usato il livello di protezione **EncryptAllWithPassword** o **EncryptSensitiveWithPassword** al momento della creazione del pacchetto tramite SSDT, immettere il valore della password nella casella **password di crittografia** . In alternativa, è possibile usare un segreto archiviato nel Azure Key Vault come valore (vedere sopra).
      
      Se è stato usato il livello di protezione **EncryptSensitiveWithUserKey** , immettere nuovamente i valori sensibili nei file di configurazione o nelle schede **parametri SSIS** , **gestioni connessioni** o **override proprietà** (vedere di seguito).
      
      Se è stato usato il livello di protezione **EncryptAllWithUserKey** , non è supportato. È necessario riconfigurare il pacchetto per usare un altro livello di protezione tramite SSDT o l' `dtutil` utilità da riga di comando. 

   1. Per **Livello di registrazione** selezionare un ambito predefinito di registrazione per l'esecuzione del pacchetto. Selezionare la casella di controllo **personalizzata** se si desidera immettere invece il nome di registrazione personalizzato. 
   
   1. Se si desidera registrare le esecuzioni dei pacchetti oltre a usare i provider di log standard che possono essere specificati nel pacchetto, specificare la cartella dei log specificando il percorso UNC nella casella **percorso di registrazione** . È possibile cercare e selezionare la cartella dei log selezionando **Esplora file archiviazione** oppure immettere il percorso manualmente. Se ad esempio si archiviano i log in File di Azure, il percorso di registrazione è `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>` . Viene creata una sottocartella in questo percorso per ogni singola esecuzione del pacchetto, denominata dopo l'ID esecuzione dell'attività Esegui pacchetto SSIS e in cui i file di log vengono generati ogni cinque minuti. 
   
   1. Specificare le credenziali per accedere alla cartella dei log immettendo i relativi valori nelle caselle **dominio** , **nome utente** e **password** . Se ad esempio si archiviano i log in File di Azure, il dominio è `Azure` , il nome utente è `<storage account name>` e la password è `<storage account key>` . In alternativa, è possibile usare i segreti archiviati nel Azure Key Vault come valori (vedere sopra).
   
Per tutti i percorsi UNC indicati in precedenza, il nome completo del file deve essere composto da meno di 260 caratteri. Il nome della directory deve essere composto da meno di 248 caratteri.

#### <a name="ssis-parameters-tab"></a>Scheda parametri SSIS

Nella scheda **SSIS Parameters** dell'attività Esegui pacchetto SSIS completare i passaggi seguenti.

![Impostare le proprietà nella scheda Parametri per SSIS](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png)

   1. Se il Azure-SSIS IR è in esecuzione, **SSISDB** viene selezionato come percorso del pacchetto e la casella di controllo delle **voci manuali** nella scheda **Impostazioni** è deselezionata, vengono visualizzati i parametri SSIS esistenti nel progetto selezionato e il pacchetto da SSISDB per poter assegnare loro i valori. In caso contrario, è possibile immetterli uno alla volta per assegnare manualmente i valori. Assicurarsi che esistano e siano stati immessi correttamente affinché l'esecuzione del pacchetto abbia esito positivo. 
   
   1. Se è stato usato il livello di protezione **EncryptSensitiveWithUserKey** durante la creazione del pacchetto tramite SSDT e il **file System (pacchetto)** , il **file System (progetto)** , il **pacchetto incorporato** o l' **Archivio pacchetti** è selezionato come percorso del pacchetto, è necessario anche immettere nuovamente i parametri sensibili per assegnare loro i valori in questa scheda. 
   
Quando si assegnano valori ai parametri, è possibile aggiungere contenuto dinamico usando espressioni, funzioni, Data Factory variabili di sistema e Data Factory parametri o variabili della pipeline.

In alternativa, è possibile usare i segreti archiviati nel Azure Key Vault come valori. A tale scopo, selezionare la casella di controllo **Azure Key Vault** accanto ad esse. Selezionare o modificare il servizio collegato di Key Vault esistente o crearne uno nuovo. Selezionare quindi il nome e la versione del segreto per il valore. Quando si crea o si modifica il servizio collegato di Key Vault, è possibile selezionare o modificare l'insieme di credenziali delle chiavi esistente o crearne uno nuovo. Assicurarsi di concedere l'accesso Data Factory identità gestita all'insieme di credenziali delle chiavi, se non è già stato fatto. È anche possibile immettere il segreto direttamente nel formato seguente: `<key vault linked service name>/<secret name>/<secret version>` . 

#### <a name="connection-managers-tab"></a>Scheda Gestioni connessioni

Nella scheda **gestioni connessioni** dell'attività Esegui pacchetto SSIS completare i passaggi seguenti.

![Impostare le proprietà nella scheda Gestori connessioni](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png)

   1. Se il Azure-SSIS IR è in esecuzione, **SSISDB** viene selezionato come percorso del pacchetto e la casella di controllo **voci manuali** nella scheda **Impostazioni** è deselezionata, vengono visualizzate le gestioni connessioni esistenti nel progetto e nel pacchetto selezionati da SSISDB per assegnare valori alle relative proprietà. In caso contrario, è possibile immetterli uno alla volta per assegnare manualmente i valori alle rispettive proprietà. Assicurarsi che esistano e siano stati immessi correttamente affinché l'esecuzione del pacchetto abbia esito positivo. 
   
      È possibile ottenere l' **ambito** , il **nome** e i nomi di **proprietà** corretti per qualsiasi gestione connessione aprendo il pacchetto che lo contiene in SSDT. Dopo l'apertura del pacchetto, selezionare la gestione connessione pertinente per visualizzare i nomi e i valori di tutte le relative proprietà nella finestra **Proprietà** di SSDT. Con queste informazioni, è possibile eseguire l'override dei valori delle proprietà di qualsiasi gestione connessione in fase di esecuzione. 

      ![Ottenere le proprietà della gestione connessione da SSDT](media/how-to-invoke-ssis-package-ssis-activity/ssdt-connection-manager-properties.png)

      Ad esempio, senza modificare il pacchetto originale in SSDT, è possibile convertire i flussi di dati da locale a locale in esecuzione in SQL Server in flussi di dati da locale a cloud in esecuzione nel runtime di integrazione SSIS in ADF eseguendo l'override dei valori delle proprietà **ConnectByProxy** , **ConnectionString** e **ConnectUsingManagedIdentity** nelle gestioni connessioni esistenti in fase di esecuzione.
      
      Queste sostituzioni di run-time possono abilitare Self-Hosted IR (per il runtime di integrazione SSIS) come proxy per l'accesso ai dati in locale, vedere Configurazione dell' [autenticazione AAD](/sql/integration-services/connection-manager/ole-db-connection-manager?view=sql-server-ver15#managed-identities-for-azure-resources-authentication) [come proxy per](./self-hosted-integration-runtime-proxy-ssis.md)il runtime di integrazione SSIS e connessione del database SQL di Azure/istanza gestita con il driver MSOLEDBSQL più recente che abilita a sua volta l'autenticazione Azure Active Directory (AAD) con l'identità gestita di ADF.

      ![Impostare le proprietà da SSDT nella scheda Gestioni connessioni](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers2.png)
   
   1. Se è stato usato il livello di protezione **EncryptSensitiveWithUserKey** durante la creazione del pacchetto tramite SSDT e il **file System (pacchetto)** , il **file System (progetto)** , il **pacchetto incorporato** o l' **Archivio pacchetti** è selezionato come percorso del pacchetto, è necessario anche immettere nuovamente le proprietà di gestione connessione sensibili per assegnare i valori in questa scheda. 

Quando si assegnano valori alle proprietà di gestione connessione, è possibile aggiungere contenuto dinamico usando espressioni, funzioni, Data Factory variabili di sistema e Data Factory parametri o variabili della pipeline. 

In alternativa, è possibile usare i segreti archiviati nel Azure Key Vault come valori. A tale scopo, selezionare la casella di controllo **Azure Key Vault** accanto ad esse. Selezionare o modificare il servizio collegato di Key Vault esistente o crearne uno nuovo. Selezionare quindi il nome e la versione del segreto per il valore. Quando si crea o si modifica il servizio collegato di Key Vault, è possibile selezionare o modificare l'insieme di credenziali delle chiavi esistente o crearne uno nuovo. Assicurarsi di concedere l'accesso Data Factory identità gestita all'insieme di credenziali delle chiavi, se non è già stato fatto. È anche possibile immettere il segreto direttamente nel formato seguente: `<key vault linked service name>/<secret name>/<secret version>` . 

#### <a name="property-overrides-tab"></a>Scheda override proprietà

Nella scheda **override proprietà** dell'attività Esegui pacchetto SSIS completare i passaggi seguenti.

![Impostare le proprietà nella scheda Override proprietà](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png)

   1. Immettere i percorsi delle proprietà esistenti nel pacchetto selezionato uno alla volta per assegnarvi i valori manualmente. Assicurarsi che esistano e siano stati immessi correttamente affinché l'esecuzione del pacchetto abbia esito positivo. Ad esempio, per eseguire l'override del valore della variabile utente, immettere il percorso nel formato seguente: `\Package.Variables[User::<variable name>].Value` . 

      È possibile ottenere il **percorso della proprietà** corretto per qualsiasi proprietà del pacchetto aprendo il pacchetto che lo contiene in SSDT. Dopo aver aperto il pacchetto, selezionarne il flusso di controllo e la proprietà **configurazioni** nella finestra **Proprietà** di SSDT. A questo punto, selezionare il pulsante con i puntini di sospensione ( **...** ) accanto alla proprietà **configurazioni** per aprire la **libreria di configurazioni** dei pacchetti utilizzata normalmente per [creare configurazioni di pacchetto nel modello di distribuzione del pacchetto](/sql/integration-services/packages/legacy-package-deployment-ssis#create-package-configurations). 

      ![Ottenere le proprietà del pacchetto dalla proprietà SSDT-Configurations](media/how-to-invoke-ssis-package-ssis-activity/ssdt-package-properties.png)

      Nella **Libreria configurazioni** pacchetto selezionare la casella di **controllo Abilita configurazioni pacchetto** e il pulsante **Aggiungi...** per aprire la **Configurazione guidata pacchetto** . 
      
      Nella **Configurazione guidata pacchetto** selezionare l'elemento **file di configurazione XML** nel menu a discesa **tipo di configurazione** e il pulsante **specificare le impostazioni di configurazione direttamente** , immettere il nome del file di configurazione e selezionare il pulsante **Avanti >** . 

      ![Ottenere le proprietà del pacchetto da SSDT-configurazioni libreria](media/how-to-invoke-ssis-package-ssis-activity/ssdt-package-properties2.png)

      Infine, selezionare le proprietà del pacchetto di cui si desidera il percorso e il pulsante **avanti >** .  È ora possibile visualizzare, copiare & incollare i percorsi delle proprietà del pacchetto desiderati e salvarli nel file di configurazione. Con queste informazioni, è possibile eseguire l'override dei valori di tutte le proprietà del pacchetto in fase di esecuzione. 

      ![Ottenere le proprietà del pacchetto da SSDT-configurazione guidata](media/how-to-invoke-ssis-package-ssis-activity/ssdt-package-properties3.png)
   
   1. Se è stato usato il livello di protezione **EncryptSensitiveWithUserKey** durante la creazione del pacchetto tramite SSDT e il **file System (pacchetto)** , il **file System (progetto)** , il **pacchetto incorporato** o l' **Archivio pacchetti** è selezionato come percorso del pacchetto, è necessario anche immettere nuovamente le proprietà del pacchetto sensibili per assegnare loro i valori in questa scheda. 
   
Quando si assegnano valori alle proprietà del pacchetto, è possibile aggiungere contenuto dinamico tramite espressioni, funzioni, Data Factory variabili di sistema e Data Factory parametri o variabili della pipeline.

È possibile eseguire l'override dei valori assegnati nei file di configurazione e nella scheda **parametri SSIS** utilizzando le schede **gestioni connessioni** o **sostituzioni proprietà** . È anche possibile eseguire l'override dei valori assegnati nella scheda **gestioni connessioni** usando la scheda **sostituzioni proprietà** .

Per convalidare la configurazione della pipeline, selezionare **convalida** sulla barra degli strumenti. Per chiudere il **report di convalida della pipeline** , selezionare **>>** .

Per pubblicare la pipeline in Data Factory, selezionare **pubblica tutti** . 

### <a name="run-the-pipeline"></a>Eseguire la pipeline
In questo passaggio viene attivata un'esecuzione della pipeline. 

1. Per attivare un'esecuzione della pipeline, selezionare **trigger** sulla barra degli strumenti e selezionare **Attiva ora** . 

   ![Trigger now (Attiva adesso)](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. Nella finestra **Pipeline Run** (Esecuzione di pipeline) selezionare **Fine** . 

### <a name="monitor-the-pipeline"></a>Monitorare la pipeline

1. Passare alla scheda **Monitoraggio** a sinistra. Viene visualizzata l'esecuzione della pipeline e il relativo stato insieme ad altre informazioni, ad esempio l'ora di **inizio dell'esecuzione** . Per aggiornare la visualizzazione, selezionare **Aggiorna** .

   ![Esecuzioni di pipeline](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

2. Selezionare il collegamento **View Activity Runs** (Visualizza le esecuzioni di attività) nella colonna **Azioni** . Viene visualizzata una sola esecuzione di attività perché la pipeline ha una sola attività. Si tratta dell'attività Esegui pacchetto SSIS.

   ![Esecuzioni attività](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

3. Eseguire la query seguente sul database SSISDB nel server SQL per verificare che il pacchetto sia stato eseguito. 

   ```sql
   select * from catalog.executions
   ```

   ![Verificare le esecuzioni del pacchetto](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

4. È anche possibile ottenere l'ID di esecuzione SSISDB dall'output dell'esecuzione dell'attività della pipeline e usare l'ID per verificare i log di esecuzione e i messaggi di errore più completi in SQL Server Management Studio.

   ![Ottenere l'ID di esecuzione.](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

### <a name="schedule-the-pipeline-with-a-trigger"></a>Pianificare la pipeline con un trigger

È anche possibile creare un trigger pianificato per la pipeline in modo che la pipeline venga eseguita in base a una pianificazione, ad esempio ogni ora o ogni giorno. Per un esempio, vedere [Creare una data factory tramite l'interfaccia utente di Azure Data Factory](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="run-a-package-with-powershell"></a>Eseguire un pacchetto con PowerShell
In questa sezione si usa Azure PowerShell per creare una pipeline Data Factory con un'attività Esegui pacchetto SSIS che esegue il pacchetto SSIS. 

Installare i moduli di Azure PowerShell più recenti seguendo le istruzioni dettagliate riportate in [Come installare e configurare Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="create-a-data-factory-with-azure-ssis-ir"></a>Creare una data factory con Azure-SSIS IR
È possibile usare un data factory esistente per cui è già stato eseguito il provisioning di Azure-SSIS IR o creare un nuovo data factory con Azure-SSIS IR. Seguire le istruzioni dettagliate riportate nell' [esercitazione: distribuire pacchetti SSIS in Azure tramite PowerShell](./tutorial-deploy-ssis-packages-azure-powershell.md).

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Creare una pipeline con un'attività di esecuzione del pacchetto SSIS 
In questo passaggio si crea una pipeline con un'attività di esecuzione del pacchetto SSIS. L'attività esegue il pacchetto SSIS. 

1. Creare un file JSON denominato `RunSSISPackagePipeline.json` nella `C:\ADF\RunSSISPackage` cartella con contenuto simile all'esempio seguente.

   > [!IMPORTANT]
   > Prima di salvare il file, sostituire i nomi degli oggetti, le descrizioni e i percorsi, i valori di proprietà o parametri, le password e altri valori delle variabili. 
    
   ```json
   {
       "name": "RunSSISPackagePipeline",
       "properties": {
           "activities": [{
               "name": "MySSISActivity",
               "description": "My SSIS package/activity description",
               "type": "ExecuteSSISPackage",
               "typeProperties": {
                   "connectVia": {
                       "referenceName": "MyAzureSSISIR",
                       "type": "IntegrationRuntimeReference"
                   },
                   "executionCredential": {
                       "domain": "MyExecutionDomain",
                       "username": "MyExecutionUsername",
                       "password": {
                           "type": "SecureString",
                           "value": "MyExecutionPassword"
                       }
                   },
                   "runtime": "x64",
                   "loggingLevel": "Basic",
                   "packageLocation": {
                       "type": "SSISDB",
                       "packagePath": "MyFolder/MyProject/MyPackage.dtsx"
                   },
                   "environmentPath": "MyFolder/MyEnvironment",
                   "projectParameters": {
                       "project_param_1": {
                           "value": "123"
                       },
                       "project_param_2": {
                           "value": {
                               "value": "@pipeline().parameters.MyProjectParameter",
                               "type": "Expression"
                           }
                       }
                   },
                   "packageParameters": {
                       "package_param_1": {
                           "value": "345"
                       },
                       "package_param_2": {
                           "value": {
                               "type": "AzureKeyVaultSecret",
                               "store": {
                                   "referenceName": "myAKV",
                                   "type": "LinkedServiceReference"
                               },
                               "secretName": "MyPackageParameter"
                           }
                       }
                   },
                   "projectConnectionManagers": {
                       "MyAdonetCM": {
                           "username": {
                               "value": "MyConnectionUsername"
                           },
                           "password": {
                               "value": {
                                   "type": "SecureString",
                                   "value": "MyConnectionPassword"
                               }
                           }
                       }
                   },
                   "packageConnectionManagers": {
                       "MyOledbCM": {
                           "username": {
                               "value": {
                                   "value": "@pipeline().parameters.MyConnectionUsername",
                                   "type": "Expression"
                               }
                           },
                           "password": {
                               "value": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyConnectionPassword",
                                   "secretVersion": "MyConnectionPasswordVersion"
                               }
                           }
                       }
                   },
                   "propertyOverrides": {
                       "\\Package.MaxConcurrentExecutables": {
                           "value": 8,
                           "isSensitive": false
                       }
                   }
               },
               "policy": {
                   "timeout": "0.01:00:00",
                   "retry": 0,
                   "retryIntervalInSeconds": 30
               }
           }]
       }
   }
   ```

   Per eseguire i pacchetti archiviati in file system/File di Azure, immettere i valori per le proprietà del pacchetto e del percorso del log come indicato di seguito:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "File",
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyPackage.dtsx",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           }
                       }
                   },
                   "logLocation": {
                       "type": "File",
                       "logPath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyLogFolder",
                       "typeProperties": {
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

   Per eseguire i pacchetti nei progetti archiviati in file system/File di Azure, immettere i valori per le proprietà del percorso del pacchetto nel modo seguente:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "File",
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyProject.ispac:MyPackage.dtsx",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "userName": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

   Per eseguire i pacchetti incorporati, immettere i valori per le proprietà del percorso del pacchetto nel modo seguente:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "InlinePackage",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "packageName": "MyPackage.dtsx",
                           "packageContent":"My compressed/uncompressed package content",
                           "packageLastModifiedDate": "YYYY-MM-DDTHH:MM:SSZ UTC-/+HH:MM"
                       }
                   }
               }
           }
       }
   }
   ```

   Per eseguire i pacchetti archiviati negli archivi pacchetti, immettere i valori per le proprietà del pacchetto e del percorso di configurazione nel modo seguente:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "PackageStore",
                       "packagePath": "myPackageStore/MyFolder/MyPackage",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           },
                           "configurationPath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyConfiguration.dtsConfig",
                           "configurationAccessCredential": {
                               "domain": "Azure",
                               "userName": "MyStorageAccount",
                               "password": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

2. In Azure PowerShell passare alla cartella `C:\ADF\RunSSISPackage`.

3. Per creare la pipeline **RunSSISPackagePipeline** , eseguire il cmdlet **set-AzDataFactoryV2Pipeline** .

   ```powershell
   $DFPipeLine = Set-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                                  -ResourceGroupName $ResGrp.ResourceGroupName `
                                                  -Name "RunSSISPackagePipeline"
                                                  -DefinitionFile ".\RunSSISPackagePipeline.json"
   ```

   Ecco l'output di esempio:

   ```
   PipelineName      : Adfv2QuickStartPipeline
   ResourceGroupName : <resourceGroupName>
   DataFactoryName   : <dataFactoryName>
   Activities        : {CopyFromBlobToBlob}
   Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
   ```

### <a name="run-the-pipeline"></a>Eseguire la pipeline
Usare il cmdlet **Invoke-AzDataFactoryV2Pipeline** per eseguire la pipeline. Il cmdlet restituisce l'ID di esecuzione della pipeline per il monitoraggio futuro.

```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                             -ResourceGroupName $ResGrp.ResourceGroupName `
                                             -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline"></a>Monitorare la pipeline

Eseguire lo script di PowerShell seguente per verificare continuamente lo stato di esecuzione della pipeline fino al termine della copia dei dati. Copiare o incollare lo script seguente nella finestra di PowerShell e premere INVIO. 

```powershell
while ($True) {
    $Run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName `
                                               -DataFactoryName $DataFactory.DataFactoryName `
                                               -PipelineRunId $RunId

    if ($Run) {
        if ($run.Status -ne 'InProgress') {
            Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
            $Run
            break
        }
        Write-Output  "Pipeline is running...status: InProgress"
    }

    Start-Sleep -Seconds 10
}   
```

È anche possibile monitorare la pipeline usando il portale di Azure. Per istruzioni dettagliate, vedere [Monitorare la pipeline](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

### <a name="schedule-the-pipeline-with-a-trigger"></a>Pianificare la pipeline con un trigger
Nel passaggio precedente è stata eseguita la pipeline su richiesta. È anche possibile creare un trigger di pianificazione per eseguire la pipeline in base a una pianificazione, ad esempio ogni ora o ogni giorno.

1. Creare un file JSON denominato `MyTrigger.json` nella `C:\ADF\RunSSISPackage` cartella con il contenuto seguente: 
        
   ```json
   {
       "properties": {
           "name": "MyTrigger",
           "type": "ScheduleTrigger",
           "typeProperties": {
               "recurrence": {
                   "frequency": "Hour",
                   "interval": 1,
                   "startTime": "2017-12-07T00:00:00-08:00",
                   "endTime": "2017-12-08T00:00:00-08:00"
               }
           },
           "pipelines": [{
               "pipelineReference": {
                   "type": "PipelineReference",
                   "referenceName": "RunSSISPackagePipeline"
               },
               "parameters": {}
           }]
       }
   }    
   ```
    
1. In Azure PowerShell passare alla cartella `C:\ADF\RunSSISPackage`.
1. Eseguire il cmdlet **set-AzDataFactoryV2Trigger** , che crea il trigger. 

   ```powershell
   Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                   -DataFactoryName $DataFactory.DataFactoryName `
                                   -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
   ```
1. Per impostazione predefinita, lo stato del trigger è arrestato. Avviare il trigger eseguendo il cmdlet **Start-AzDataFactoryV2Trigger** . 

   ```powershell
   Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                     -DataFactoryName $DataFactory.DataFactoryName `
                                     -Name "MyTrigger" 
   ```
1. Verificare che il trigger sia stato avviato eseguendo il cmdlet **Get-AzDataFactoryV2Trigger** . 

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                   -DataFactoryName $DataFactoryName `
                                   -Name "MyTrigger"     
   ```    
1. Eseguire il comando seguente dopo l'ora successiva. Ad esempio, se l'ora corrente è 15:25 UTC, eseguire il comando alle 16 UTC. 
    
   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                      -DataFactoryName $DataFactoryName `
                                      -TriggerName "MyTrigger" `
                                      -TriggerRunStartedAfter "2017-12-06" `
                                      -TriggerRunStartedBefore "2017-12-09"
   ```

   Eseguire la query seguente sul database SSISDB nel server SQL per verificare che il pacchetto sia stato eseguito. 

   ```sql
   select * from catalog.executions
   ```

## <a name="next-steps"></a>Passaggi successivi
Vedere il post di blog seguente:
- [Modernizza ed Estendi i tuoi flussi di lavoro ETL/ELT con attività SSIS in pipeline di Azure Data Factory](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)