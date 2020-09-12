---
title: Eseguire pacchetti SSIS da SSDT
description: Informazioni su come eseguire pacchetti SSIS in Azure da SSDT.
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
ms.date: 09/06/2020
ms.openlocfilehash: fb5b5cb0ac4a9ace7b5de5e92308da58fd2b1fec
ms.sourcegitcommit: 59ea8436d7f23bee75e04a84ee6ec24702fb2e61
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/07/2020
ms.locfileid: "89504945"
---
# <a name="execute-ssis-packages-in-azure-from-ssdt"></a>Eseguire pacchetti SSIS in Azure da SSDT

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Questo articolo descrive la funzionalità dei progetti SQL Server Integration Services (SSIS) abilitati per Azure in SQL Server Data Tools (SSDT). Consente di valutare la compatibilità cloud dei pacchetti SSIS ed eseguirli in Azure-SSIS Integration Runtime (IR) in Azure Data Factory (ADF). È possibile usare questa funzionalità per testare i pacchetti esistenti prima di eseguire il & di spostamento/migrazione in Azure o per sviluppare nuovi pacchetti da eseguire in Azure.

Con questa funzionalità è possibile associare un Azure-SSIS IR appena creato/esistente ai progetti SSIS, quindi eseguirvi i pacchetti.  Sono supportati i pacchetti in esecuzione da distribuire nel catalogo SSIS (SSISDB) ospitato dal server di database SQL di Azure o dall'istanza gestita nel modello di distribuzione del progetto. Sono inoltre supportati i pacchetti in esecuzione da distribuire nel database file system/File di Azure/SQL Server (MSDB) ospitato dall'istanza gestita di SQL di Azure nel modello di distribuzione del pacchetto. 

## <a name="prerequisites"></a>Prerequisiti

Per usare questa funzionalità, scaricare e installare la versione più recente di SSDT con l'estensione di progetti SSIS per Visual Studio (VS) da [qui](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects). In alternativa, è anche possibile scaricare e installare la versione più recente di SSDT come programma di installazione autonomo da [qui](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer).

## <a name="azure-enable-ssis-projects"></a>Azure-abilitare i progetti SSIS

### <a name="creating-new-azure-enabled-ssis-projects"></a>Creazione di nuovi progetti SSIS abilitati per Azure

In SSDT è possibile creare nuovi progetti SSIS abilitati per Azure usando il modello di **Integration Services progetto (abilitato per Azure)** .

   ![Nuovo progetto SSIS abilitato per Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-new-project.png)

Dopo aver creato il progetto abilitato per Azure, verrà richiesto di connettersi a SSIS in Azure Data Factory.

   ![Richiesta di connessione Azure-SSIS IR](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-integration-runtime-connect-prompt.png)

Per connettersi immediatamente alla Azure-SSIS IR, vedere la pagina relativa alla [connessione a Azure-SSIS IR](#connectssisir) per altri dettagli. È anche possibile connettersi in un secondo momento facendo clic con il pulsante destro del mouse sul nodo del progetto nella finestra Esplora soluzioni di SSDT per visualizzare un menu. Selezionare quindi il sottomenu **Connetti a SSIS in Azure Data Factory** elemento in **SSIS nel Azure Data Factory** .

### <a name="azure-enabling-existing-ssis-projects"></a><a name="azureenableproject"></a> Azure-abilitazione di progetti SSIS esistenti

Per i progetti SSIS esistenti, è possibile abilitarli in Azure attenendosi alla procedura seguente:

1. Fare clic con il pulsante destro del mouse sul nodo del progetto nella finestra Esplora soluzioni di SSDT per visualizzare un menu. Selezionare quindi l'elemento del **progetto abilitato per Azure** in **SSIS in Azure Data Factory** sottomenu per avviare la **creazione guidata progetto abilitata per Azure**.

   ![Azure-Abilita progetto SSIS esistente](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-for-existing-project.png)

2. Nella pagina **Seleziona configurazione di Visual Studio** selezionare la configurazione di vs esistente per applicare le impostazioni di esecuzione del pacchetto in Azure. È anche possibile crearne uno nuovo, se non è già stato fatto, vedere [creazione di una nuova configurazione di Visual](https://docs.microsoft.com/visualstudio/ide/how-to-create-and-edit-configurations?view=vs-2019)Studio. Si consiglia di disporre di almeno due diverse configurazioni di Visual Studio per le esecuzioni di pacchetti negli ambienti locali e cloud, in modo che sia possibile abilitare Azure per il progetto in base alla configurazione cloud. In questo modo, se il progetto o i pacchetti sono stati parametrizzati, è possibile assegnare valori diversi al progetto o ai parametri del pacchetto in fase di esecuzione in base ai diversi ambienti di esecuzione (nel computer locale o in Azure). Vedere, ad esempio, [cambiare ambiente di esecuzione dei pacchetti](#switchenvironment).

   ![Selezionare la configurazione di Visual Studio](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-select-visual-studio-configurations.png)

3. Per abilitare Azure per i progetti SSIS esistenti è necessario impostare la versione del server di destinazione in modo che sia la versione più recente supportata da Azure-SSIS IR. Azure-SSIS IR è attualmente basato su **SQL Server 2017**. Assicurarsi che i pacchetti non contengano componenti aggiuntivi non supportati in SQL Server 2017. Assicurarsi inoltre che tutti i componenti aggiuntivi compatibili siano stati installati anche nella Azure-SSIS IR tramite le configurazioni personalizzate, vedere [personalizzazione del Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup). Selezionare il pulsante **Avanti** per continuare.

   ![Cambia versione del server di destinazione](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-switch-target-server-version-step.png)

4. Vedere [connessione a Azure-SSIS IR](#connectssisir) per completare la connessione del progetto al Azure-SSIS IR.

## <a name="connect-azure-enabled-projects-to-ssis-in-azure-data-factory"></a><a name="connectssisir"></a> Connettere i progetti abilitati per Azure a SSIS in Azure Data Factory

Connettendo i progetti abilitati per Azure a SSIS in ADF, è possibile caricare i pacchetti in File di Azure ed eseguirli su Azure-SSIS IR. Questa operazione può essere eseguita attenendosi alla procedura seguente:

1. Nella pagina **Introduzione a SSIS in ADF** esaminare l'introduzione e selezionare il pulsante **Avanti** per continuare.

   ![Introduzione a SSIS in ADF](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard.png)

2. Nella pagina selezionare un runtime di integrazione **SSIS in ADF** selezionare il file ADF esistente e Azure-SSIS IR per eseguire i pacchetti. È anche possibile crearne di nuovi se non sono presenti.
   - Per selezionare il Azure-SSIS IR esistente, selezionare prima la sottoscrizione di Azure pertinente e l'ADF.
   - Se si seleziona il file ADF esistente privo di Azure-SSIS IR, selezionare il pulsante **create SSIS IR** per crearne uno nuovo nel portale di ADF. Una volta creato, è possibile tornare a questa pagina per selezionare il nuovo Azure-SSIS IR.
   - Se si seleziona la sottoscrizione di Azure esistente che non dispone di ADF, selezionare il pulsante **Crea IR SSIS** per avviare la **creazione guidata Integration Runtime**. Nella procedura guidata è possibile immettere il percorso e il prefisso designati per creare automaticamente un nuovo gruppo di risorse di Azure, Data Factory e runtime di integrazione SSIS per conto dell'utente, denominato con il modello seguente: **YourPrefix-RG/DF/IR-YourCreationTime**. Una volta creato, è possibile tornare a questa pagina per selezionare il nuovo ADF e Azure-SSIS IR.

   ![Selezionare IR SSIS in ADF](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard2.png)

3. Nella pagina **Seleziona archiviazione di Azure** selezionare l'account di archiviazione di Azure esistente per caricare i pacchetti in file di Azure. È anche possibile crearne uno nuovo, se non è disponibile.
   - Per selezionare l'account di archiviazione di Azure esistente, selezionare prima di tutto la sottoscrizione di Azure pertinente.
   - Se si seleziona la stessa sottoscrizione di Azure del Azure-SSIS IR che non ha un account di archiviazione di Azure, selezionare il pulsante **Crea archiviazione di Azure** . Verrà automaticamente creato un nuovo per conto dell'utente nella stessa posizione del Azure-SSIS IR, denominato combinando un prefisso del nome del Azure-SSIS IR e la data di creazione. Una volta creato, è possibile tornare a questa pagina per selezionare il nuovo account di archiviazione di Azure.
   - Se si seleziona una sottoscrizione di Azure diversa che non ha un account di archiviazione di Azure, selezionare il pulsante **Crea archiviazione di Azure** per crearne uno nuovo in portale di Azure. Una volta creato, è possibile tornare a questa pagina per selezionare il nuovo account di archiviazione di Azure.

   ![Selezionare Archiviazione di Azure](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard3.png)

4. Selezionare il pulsante **Connetti** per completare la connessione del progetto a Azure-SSIS IR. Verranno visualizzati il Azure-SSIS IR e l'account di archiviazione di Azure selezionati nel nodo **risorse di Azure collegato** Esplora soluzioni finestra di SSDT. Inoltre, verrà aggiornato e visualizzato periodicamente lo stato del Azure-SSIS IR. È possibile gestire il Azure-SSIS IR facendo clic con il pulsante destro del mouse sul relativo nodo per visualizzare un menu e quindi selezionando l'elemento **Start\Stop\Manage** che porta al portale di ADF.

## <a name="assess-ssis-projectpackages-for-executions-in-azure"></a>Valutare project\packages SSIS per le esecuzioni in Azure

### <a name="assessing-single-or-multiple-packages"></a>Valutazione di uno o più pacchetti

Prima di eseguire i pacchetti in Azure, è possibile valutarli per individuare eventuali problemi di compatibilità cloud potenziali. Sono inclusi i blocchi di migrazione e informazioni aggiuntive che è necessario conoscere. 
-  Sono disponibili le opzioni per valutare singoli pacchetti uno alla volta o tutti i pacchetti contemporaneamente nel progetto.

   ![Valuta pacchetto](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assess-package.png)

   ![Valutazione progetto](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assess-project.png)

-  Nella finestra **report di valutazione** di SSDT è possibile trovare tutti i potenziali problemi di compatibilità cloud esposti, ognuno con una descrizione e una raccomandazione dettagliate. È anche possibile esportare il report di valutazione in un file CSV che può essere condiviso con chiunque debba attenuare tali problemi. 

   ![Report di valutazione](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assess-project-result.png)

### <a name="suppressing-assessment-rules"></a>Eliminazione delle regole di valutazione

Quando si è certi che alcuni potenziali problemi di compatibilità del cloud non sono applicabili o sono stati adeguatamente attenuati nei pacchetti, è possibile escludere le regole di valutazione rilevanti che la esportano. Ciò ridurrà il rumore nei report di valutazione successivi.
-  Selezionare il collegamento **Configura eliminazione regola di valutazione** nella finestra **rapporto di valutazione** di SSDT per visualizzare la finestra **impostazioni di eliminazione regola di valutazione** , in cui è possibile selezionare le regole di valutazione da eliminare.

   ![Impostazioni di eliminazione della regola di valutazione](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assessment-rule-suppression-settings.png)

-  In alternativa, fare clic con il pulsante destro del mouse sul nodo del progetto nella finestra Esplora soluzioni di SSDT per visualizzare un menu. Selezionare l'elemento **impostazioni abilitato per Azure** in **SSIS nel sottomenu Azure Data Factory** per visualizzare una finestra contenente le pagine delle proprietà del progetto. Selezionare la proprietà di **ID regola di valutazione eliminata** nella sezione **impostazioni abilitate per Azure** . Infine, selezionare il pulsante con i puntini di sospensione (**...**) per visualizzare la finestra **impostazioni di eliminazione della regola di valutazione** , in cui è possibile selezionare le regole di valutazione da eliminare.

   ![Impostazioni abilitate per Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-azure-enabled-setting.png)

   ![Impostazioni di eliminazione della regola di valutazione tramite le impostazioni abilitate per Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assessment-rule-suppression-settings-via-azure-enabled-settings.png)

## <a name="execute-ssis-packages-in-azure"></a>Esecuzione di pacchetti SSIS in Azure

### <a name="configuring-azure-enabled-settings"></a><a name="azureenabledsettings"></a> Configurazione delle impostazioni abilitate per Azure

Prima di eseguire i pacchetti in Azure, è possibile configurare le impostazioni abilitate per Azure. Ad esempio, è possibile abilitare l'autenticazione di Windows sul Azure-SSIS IR per accedere agli archivi dati locali o cloud attenendosi alla procedura seguente:

1. Fare clic con il pulsante destro del mouse sul nodo del progetto nella finestra Esplora soluzioni di SSDT per visualizzare un menu. Selezionare quindi l'elemento **impostazioni abilitato per Azure** in **SSIS nel sottomenu Azure Data Factory** per visualizzare una finestra contenente le pagine delle proprietà del progetto.

   ![Impostazioni abilitate per Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-azure-enabled-setting.png)

2. Selezionare la proprietà **Abilita autenticazione Windows** nella sezione **impostazioni abilitate per Azure** e quindi selezionare **true** nel menu a discesa. Selezionare quindi la proprietà **credenziali di autenticazione di Windows** e quindi fare clic sul pulsante con i puntini di sospensione (**...**) per visualizzare la finestra **credenziali di autenticazione di Windows** .

   ![Abilitare l'autenticazione di Windows](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-windows-authentication-open.png)

3. Immettere le credenziali di autenticazione di Windows. Per accedere File di Azure, ad esempio, è possibile immettere `Azure` `YourStorageAccountName` rispettivamente, e `YourStorageAccountKey` per **dominio**, **nome utente**e **password**.

   ![Credenziali di autenticazione di Windows](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-windows-authentication-credential.png)

### <a name="starting-package-executions"></a>Avvio delle esecuzioni di pacchetti

Dopo la connessione dei progetti abilitati per Azure a SSIS in ADF, la valutazione della compatibilità del cloud e la mitigazione di potenziali problemi, è possibile eseguire/testare i pacchetti in Azure-SSIS IR.
-  Selezionare il pulsante **Avvia** sulla barra degli strumenti di SSDT per menu a discesa. Selezionare quindi l'elemento **Execute in Azure** .

   ![Esegui in Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package.png)

-  In alternativa, fare clic con il pulsante destro del mouse sul nodo del pacchetto nella finestra Esplora soluzioni di SSDT per visualizzare un menu. Selezionare quindi l'elemento **Esegui pacchetto in Azure** .

   ![Esegui pacchetto in Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package2.png)

> [!NOTE]
> Per eseguire i pacchetti in Azure è necessario disporre di un Azure-SSIS IR in esecuzione. Pertanto, se il Azure-SSIS IR viene arrestato, viene visualizzata una finestra di dialogo per avviarla. Escludendo qualsiasi tempo di installazione personalizzato, questo processo dovrebbe essere completato entro 5 minuti, ma per Azure-SSIS IR aggiunta a una rete virtuale potrebbero essere necessari circa 20-30 minuti. Dopo aver eseguito i pacchetti in Azure, è possibile arrestare il Azure-SSIS IR per gestire il costo di esecuzione facendo clic con il pulsante destro del mouse sul relativo nodo nella finestra di Esplora soluzioni di SSDT per visualizzare un menu e quindi selezionando l'elemento **Start\Stop\Manage** che porta al portale di ADF a tale scopo.

### <a name="using-execute-package-task"></a>Utilizzo dell'attività Esegui pacchetto

Se i pacchetti contengono attività Esegui pacchetto che fanno riferimento a pacchetti figlio archiviati in file system locali, attenersi alla procedura seguente:

1. Caricare i pacchetti figlio in File di Azure nello stesso account di archiviazione di Azure connesso ai progetti e ottenere il nuovo percorso di Universal Naming Convention (UNC), ad esempio `\\YourStorageAccountName.file.core.windows.net\ssdtexecution\YourChildPackage1.dtsx`

2. Sostituire il percorso del file dei pacchetti figlio nella gestione connessione file delle attività Esegui pacchetto con il nuovo percorso UNC
   - Se il computer locale che esegue SSDT non è in grado di accedere al nuovo percorso UNC, è possibile immetterlo nel pannello Proprietà di gestione connessione file
   - In alternativa, è possibile usare una variabile per il percorso del file per assegnare il valore corretto in fase di esecuzione

Se i pacchetti contengono attività Esegui pacchetto che fanno riferimento a pacchetti figlio nello stesso progetto, non è necessario eseguire ulteriori passaggi.

### <a name="switching-package-protection-level"></a>Cambio del livello di protezione del pacchetto

L'esecuzione di pacchetti SSIS in Azure non **EncryptSensitiveWithUserKey**supporta i / livelli di protezione**EncryptAllWithUserKey** di EncryptSensitiveWithUserKey. Di conseguenza, se i pacchetti sono configurati per usarli, verranno convertiti temporaneamente in usando i livelli di protezione EncryptAllWithPassword di **EncryptSensitiveWithPassword** / **EncryptAllWithPassword** , rispettivamente. Verranno anche generate in modo casuale le password di crittografia quando si caricano i pacchetti in File di Azure per le esecuzioni nel Azure-SSIS IR.

> [!NOTE]
> Se i pacchetti contengono attività Esegui pacchetto che fanno riferimento ai pacchetti figlio configurati per l'uso dei livelli di protezione EncryptAllWithUserKey di **EncryptSensitiveWithUserKey** / **EncryptAllWithUserKey** , è necessario riconfigurare manualmente i pacchetti figlio in modo che usino rispettivamente i livelli di protezione EncryptAllWithPassword di **EncryptSensitiveWithPassword** / **EncryptAllWithPassword** prima di eseguire i pacchetti.

Se i pacchetti sono già configurati per l'uso dei livelli di protezione EncryptAllWithPassword di **EncryptSensitiveWithPassword** / **EncryptAllWithPassword** , questi verranno mantenuti invariati. Verranno comunque generate in modo casuale le password di crittografia quando si caricano i pacchetti in File di Azure per le esecuzioni nel Azure-SSIS IR.

### <a name="switching-package-execution-environments"></a><a name="switchenvironment"></a> Cambio di ambienti di esecuzione del pacchetto

Se si parametrizzano il progetto o i pacchetti nel modello di distribuzione del progetto, è possibile creare più configurazioni di Visual Studio per cambiare gli ambienti di esecuzione dei pacchetti. In questo modo, è possibile assegnare valori specifici dell'ambiente ai parametri di progetto/pacchetto in fase di esecuzione. Si consiglia di disporre di almeno due diverse configurazioni di Visual Studio per le esecuzioni di pacchetti negli ambienti locali e cloud, in modo da consentire l'abilitazione di Azure per i progetti rispetto alla configurazione cloud. Ecco un esempio dettagliato di cambio di ambienti di esecuzione dei pacchetti tra il computer locale e Azure:

1. Supponiamo che il pacchetto contenga un'attività file System che imposta gli attributi di un file. Quando viene eseguito nel computer locale, vengono impostati gli attributi di un file archiviato nella file system locale. Quando si esegue la Azure-SSIS IR, è necessario impostare gli attributi di un file archiviato in File di Azure. Per prima cosa, creare un parametro del pacchetto di tipo stringa e denominarlo **filePath** per mantenere il valore del percorso del file di destinazione.

   ![Crea parametro pacchetto](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-define-parameters.png)

2. Quindi, nella pagina **generale** della finestra **Editor attività file System** , parametrizzare la proprietà **SourceVariable** nella sezione **connessione di origine** con il parametro del pacchetto **filePath** . 

   ![Imposta parametri per la connessione di origine](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-update-task-with-parameters.png)

3. Per impostazione predefinita, è presente una configurazione di Visual Studio per le esecuzioni di pacchetti nell'ambiente locale denominato **sviluppo**. Creare una nuova configurazione di Visual Studio per le esecuzioni di pacchetti nell'ambiente cloud denominato **Azure**, vedere [creazione di una nuova configurazione di Visual](https://docs.microsoft.com/visualstudio/ide/how-to-create-and-edit-configurations?view=vs-2019)studio, se non è già stato fatto.

4. Quando si visualizzano i parametri del pacchetto, selezionare il pulsante **Aggiungi parametri alle configurazioni** per aprire la finestra **Gestisci valori** di parametri per il pacchetto. Assegnare quindi valori diversi del percorso del file di destinazione al parametro del pacchetto **filePath** nelle configurazioni di **sviluppo** e **Azure** .

   ![Assegnare i valori dei parametri](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-override-parameter.png)

5. Azure: abilitare il progetto con la configurazione cloud, vedere Abilitazione di [progetti SSIS esistenti](#azureenableproject), se non è già stato fatto. Configurare quindi le impostazioni abilitate per Azure per consentire l'autenticazione di Windows per l'Azure-SSIS IR per accedere File di Azure, vedere [configurazione delle impostazioni abilitate per Azure](#azureenabledsettings), se non è già stato fatto.

6. Eseguire il pacchetto in Azure. È possibile riportare l'ambiente di esecuzione del pacchetto nel computer locale selezionando la configurazione di **sviluppo** .

   ![Passa alla configurazione di Visual Studio](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-switch-configurations.png)

### <a name="using-package-configuration-file"></a>Uso del file di configurazione del pacchetto

Se si usano i file di configurazione del pacchetto nel modello di distribuzione del pacchetto, è possibile assegnare valori specifici dell'ambiente alle proprietà del pacchetto in fase di esecuzione. I file verranno caricati automaticamente con i pacchetti in File di Azure per le esecuzioni nel Azure-SSIS IR.

### <a name="checking-package-execution-logs"></a>Verifica dei log di esecuzione dei pacchetti

Dopo l'avvio dell'esecuzione del pacchetto, i log verranno formattati e visualizzati nella finestra di **stato** di SSDT. Per un pacchetto con esecuzione prolungata, i log verranno periodicamente aggiornati in base ai minuti. È possibile annullare immediatamente l'esecuzione del pacchetto selezionando il pulsante **Interrompi** nella barra degli strumenti di SSDT. È anche possibile trovare temporaneamente i dati non elaborati dei log nel percorso UNC seguente: `\\<YourStorageAccountName>.file.core.windows.net\ssdtexecution\<YourProjectName-FirstConnectTime>\<YourPackageName-tmp-ExecutionTime>\logs` , ma verrà pulito dopo un giorno.

## <a name="current-limitations"></a>Limitazioni correnti

-  Il servizio SSDT abilitato per Azure supporta solo aree commerciali/globali del cloud e non supporta le aree del cloud governativo/nazionale per il momento.

## <a name="next-steps"></a>Passaggi successivi

Quando si è soddisfatti dell'esecuzione dei pacchetti in Azure da SSDT, è possibile distribuirli ed eseguirli come attività Esegui pacchetto SSIS nelle pipeline di ADF, vedere [esecuzione di pacchetti SSIS come attività Esegui pacchetto SSIS nelle pipeline di ADF](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).
