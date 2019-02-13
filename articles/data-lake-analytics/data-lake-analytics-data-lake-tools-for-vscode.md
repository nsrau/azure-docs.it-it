---
title: Usare gli Strumenti Azure Data Lake per Visual Studio Code
description: Informazioni su come usare gli Strumenti Azure Data Lake per Visual Studio Code per creare, testare ed eseguire gli script U-SQL.
services: data-lake-analytics
ms.service: data-lake-analytics
author: Jejiang
ms.author: jejiang
ms.reviewer: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.topic: conceptual
ms.date: 02/09/2018
ms.openlocfilehash: 5ebd543000c8927f714e5345dfd8eb6033c6301a
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55820369"
---
# <a name="use-azure-data-lake-tools-for-visual-studio-code"></a>Usare gli Strumenti Azure Data Lake per Visual Studio Code

In questo articolo si apprenderà come usare gli Strumenti Azure Data Lake per Visual Studio Code (VS Code) per creare, testare ed eseguire gli script U-SQL. Le informazioni sono disponibili anche nel video seguente:

<a href="https://channel9.msdn.com/Series/AzureDataLake/Azure-Data-Lake-Tools-for-VSCode?term=ADL%20Tools%20for%20VSCode"><img src="./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png"></a>

## <a name="prerequisites"></a>Prerequisiti

Gli strumenti Azure Data Lake per VS Code supportano Windows, Linux e MacOS. L'esecuzione e il debug locali di U-SQL funzionano solo in Windows.

- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx)

Per MacOS e Linux:
- [.NET Core SDK 2.0](https://www.microsoft.com/net/download/core)
- [Mono 5.2.x](http://www.mono-project.com/download/)

## <a name="install-azure-data-lake-tools"></a>Installare Azure Data Lake Tools

Dopo avere installato i prerequisiti, è possibile installare Strumenti Azure Data Lake per VS Code.

**Per installare Strumenti Azure Data Lake**

1. Aprire Visual Studio Code.
2. Selezionare **Estensioni** nel riquadro sinistro. Immettere **Strumenti Azure Data Lake** nella casella di ricerca.
3. Selezionare **Installa** accanto a **Strumenti Azure Data Lake**. 

   ![Selezioni per l'installazione degli Strumenti Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

   Dopo alcuni secondi il pulsante **Installa** diventa **Ricarica**.
4. Scegliere **Ricarica** per attivare l'estensione **Strumenti Azure Data Lake**.
5. Selezionare **Ricarica finestra** per confermare. La voce **Strumenti Azure Data Lake** sarà visualizzata nel riquadro **Estensioni**.

 
## <a name="activate-azure-data-lake-tools"></a>Attivare Strumenti Azure Data Lake
Creare un file con estensione usql o aprirne uno esistente per attivare l'estensione. 


## <a name="work-with-u-sql"></a>Uso di U-SQL

Per usare U-SQL, è necessario aprire una cartella o un file U-SQL.

**Per aprire lo script di esempio**

Aprire il riquadro comandi (CTRL+MAIUSC+P) e immettere **ADL: Open Sample Script** (ADL: Apri script di esempio). Viene aperta un'altra istanza di questo esempio. In questa istanza è anche possibile modificare, configurare e inviare uno script.

**Aprire una cartella per il progetto U-SQL**

1. Da Visual Studio Code selezionare il menu **File** e quindi selezionare **Apri cartella**.
2. Specificare una cartella e quindi scegliere **Seleziona cartella**.
3. Selezionare il menu **File** e quindi scegliere **Nuovo**. Un file Untilted-1 viene aggiunto al progetto.
4. Immettere il codice seguente nel file Untitled-1:

        @departments  = 
            SELECT * FROM 
                (VALUES
                    (31,    "Sales"),
                    (33,    "Engineering"), 
                    (34,    "Clerical"),
                    (35,    "Marketing")
                ) AS 
                      D( DepID, DepName );
         
        OUTPUT @departments
            TO "/Output/departments.csv"
        USING Outputters.Csv();

    Lo script crea un file departments.csv con alcuni dati inclusi nella cartella /output.

5. Salvare il file come **myUSQL.usql** nella cartella aperta.

**Compilare uno script U-SQL**

1. Premere CTRL+MAIUSC+P per aprire il riquadro comandi. 
2. Immettere **ADL: Compile Script** (ADL: Compila script). I risultati di compilazione vengono visualizzati nella finestra **Output**. È anche possibile fare clic con il pulsante destro del mouse su un file di script e selezionare **ADL: Compile script** (ADL: Compila script) per compilare un processo U-SQL. Il risultato della compilazione viene visualizzato nel riquadro **Output**.
 
**Inviare uno script U-SQL**

1. Premere CTRL+MAIUSC+P per aprire il riquadro comandi. 
2. Immettere **ADL: Submit Job** (ADL: Invia processo). È anche possibile fare clic con il pulsante destro del mouse su un file di script e selezionare **ADL: Submit Job** (ADL: Invia processo). 

Dopo aver inviato un processo U-SQL, i log di invio vengono visualizzati nella finestra **Output** in VS Code. La visualizzazione del processo appare nel riquadro di destra. Se l'invio ha esito positivo, viene visualizzato anche l'URL del processo. È possibile aprire l'URL del processo in un Web browser per monitorare lo stato del processo in tempo reale. 

Nella scheda **RIEPILOGO** della visualizzazione del processo è possibile vedere i dettagli del processo. Le funzioni principali includono il reinvio e la duplicazione di uno script e l'apertura nel portale. Nella scheda **DATI** della visualizzazione del processo è possibile fare riferimento a file di input, di output e di risorse. I file possono essere scaricati nel computer locale.

![Scheda Riepilogo nella visualizzazione del processo](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-summary.png)

![Scheda Dati nella visualizzazione del processo](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-data.png)

**Per impostare il contesto predefinito**

È possibile impostare il contesto predefinito per applicare questa impostazione a tutti i file di script, se non sono stati impostati parametri specifici per ogni file.

1. Premere CTRL+MAIUSC+P per aprire il riquadro comandi. 
2. Immettere **ADL: Set Default Context** (ADL: Imposta contesto predefinito). Fare clic con il pulsante destro del mouse sull'editor di script e scegliere **ADL: Set Default Context** (ADL: Imposta contesto predefinito).
3. Scegliere l'account, il database e lo schema desiderato. L'impostazione viene salvata nel file di configurazione xxx_settings.json.

   ![Account, database e schema impostati come contesto predefinito](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-sequence.png)

**Per impostare i parametri di script**

1. Premere CTRL+MAIUSC+P per aprire il riquadro comandi. 
2. Immettere **ADL: Set Script Parameters** (ADL: Imposta parametri di script).
3. Si apre il file xxx_settings.json con le proprietà seguenti:

   - **account**: un account di Azure Data Lake Analytics nella sottoscrizione di Azure necessario per compilare ed eseguire i processi U-SQL. È necessario configurare l'account del computer prima di compilare ed eseguire i processi di U-SQL.
   - **database**: un database nel proprio account. Il database predefinito è **master**.
   - **schema**: uno schema nel database. Lo schema è **dbo**.
   - **optionalSettings**:
        - **priority**: l'intervallo di priorità va da 1 a 1000 dove la priorità più alta corrisponde al valore 1. Il valore predefinito è **1000**.
        - **degreeOfParallelism**: l'intervallo di parallelismo va da 1 a 150. Il valore predefinito è il parallelismo massimo consentito nell'account Azure Data Lake Analytics. 

   ![Contenuto del file JSON](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-setting.png)
      
> [!NOTE] 
> Dopo il salvataggio della configurazione, se non è stato configurato un contesto predefinito, le informazioni relative ad account, database e schema vengono visualizzate sulla barra di stato nell'angolo inferiore sinistro del file con estensione usql corrispondente.

**Per impostare Git Ignore**

1. Premere CTRL+MAIUSC+P per aprire il riquadro comandi. 
2. Immettere **ADL: Set Git Ignore** (ADL: Imposta Git Ignore).

   - Se nella cartella di lavoro di VS Code non è presente un file con estensione **gitIgnore**, nella cartella viene creato un file denominato **.gitIgnore**. Per impostazione predefinita, nel file vengono aggiunti quattro elementi: **usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache** e **obj**. Se necessario, è possibile effettuare altri aggiornamenti.
   - Se nella cartella di lavoro di VS Code è già presente un file con estensione **gitIgnore** e nel file non sono presenti gli elementi **usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache** e **obj**, lo strumento li aggiunge al file con estensione **gitIgnore**.

   ![Elementi del file con estensione gitIgnore](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-gitignore.png)


## <a name="work-with-code-behind-files-c-sharp-python-and-r"></a>Utilizzare file code-behind: C Sharp, Python e R

Azure Data Lake Tools supporta più codici personalizzati. Per istruzioni, vedere [Eseguire lo sviluppo U-SQL con Python, R e C# per Azure Data Lake Analytics in Visual Studio Code](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md).

## <a name="work-with-assemblies"></a>Usare gli assembly

Per informazioni sullo sviluppo di assembly, vedere [Develop U-SQL assemblies for Azure Data Lake Analytics jobs](data-lake-analytics-u-sql-develop-assemblies.md) (Sviluppare assembly U-SQL per i processi di Azure Data Lake Analytics).

È possibile usare Strumenti Data Lake per registrare gli assembly di codice personalizzato nel catalogo di Data Lake Analytics.

**Registrare un assembly**

È possibile registrare l'assembly tramite **ADL: Register Assembly** (ADL: Registra assembly) o **ADL: Register Assembly (Advanced)** (ADL: Registra assembly (avanzato)).

**Per registrare tramite il comando ADL: Register Assembly** (ADL: Registra assembly)
1.  Premere CTRL+MAIUSC+P per aprire il riquadro comandi.
2.  Immettere **ADL: Register Assembly** (ADL: Registra assembly). 
3.  Specificare il percorso dell'assembly locale. 
4.  Selezionare un account di Data Lake Analytics.
5.  Selezionare un database.

Il portale si apre in un browser e visualizza il processo di registrazione dell'assembly.  

Un modo più pratico per attivare il comando **ADL: Register Assembly** (ADL: Registra assembly) consiste nel fare clic con il pulsante destro del mouse sul file con estensione dll in Esplora file. 

**Per registrare tramite il comando ADL: Register Assembly (Advanced)** (ADL: Registra assembly (avanzato))
1.  Premere CTRL+MAIUSC+P per aprire il riquadro comandi.
2.  Immettere **ADL: Register Assembly (Advanced)** (ADL: Registra assembly (avanzato)). 
3.  Specificare il percorso dell'assembly locale. 
4.  Verrà visualizzato il file JSON. Esaminare e modificare le dipendenze dell'assembly e i parametri delle risorse, se necessario. Le istruzioni sono visualizzate nella finestra **Output**. Salvare (CTRL+S) il file JSON per procedere con la registrazione dell'assembly.

    ![File JSON con dipendenze dell'assembly e i parametri delle risorse](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)
    
>[!NOTE]
>- Strumenti Azure Data Lake rileva automaticamente se la DLL include dipendenze dell'assembly. Le dipendenze vengono visualizzate nel file JSON dopo che sono state rilevate. 
>- È possibile caricare le risorse DLL (ad esempio file con estensione txt, png e csv) nel corso della registrazione dell'assembly. 

Un altro modo per attivare il comando **ADL: Register Assembly (Advanced)** (ADL: Registra assembly (avanzato)) consiste nel fare clic con il pulsante destro del mouse sul file con estensione dll in Esplora file. 

Il codice U-SQL seguente illustra come chiamare un assembly. Nell'esempio il nome dell'assembly è *test*.


        REFERENCE ASSEMBLY [test];

        @a = 
            EXTRACT 
                Iid int,
            Starts DateTime,
            Region string,
            Query string,
            DwellTime int,
            Results string,
            ClickedUrls string 
            FROM @"Sample/SearchLog.txt" 
            USING Extractors.Tsv();

        @d =
            SELECT DISTINCT Region 
            FROM @a;

        @d1 = 
            PROCESS @d
            PRODUCE 
                Region string,
            Mkt string
            USING new USQLApplication_codebehind.MyProcessor();

        OUTPUT @d1 
            TO @"Sample/SearchLogtest.txt" 
            USING Outputters.Tsv();


## <a name="use-u-sql-local-run-and-local-debug-for-windows-users"></a>Usare l'esecuzione e il debug locali di U-SQL per utenti Windows
L'esecuzione locale di U-SQL verifica i dati locali e convalida lo script localmente prima che il codice venga pubblicato in Data Lake Analytics. È possibile usare la funzionalità di debug locale per completare le attività seguenti prima che il codice venga inviato a Data Lake Analytics: 
- Eseguire il debug del code-behind di C#. 
- Eseguire il codice passo per passo. 
- Convalidare lo script in locale.

La funzionalità di esecuzione locale e debug locale funziona solo in ambienti Windows e non è supportata nei sistemi operativi basati su Linux e macOS.

Per istruzioni sull'esecuzione e il debug locali, vedere [Esecuzione locale e debug locale di U-SQL con Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md).


## <a name="connect-to-azure"></a>Connect to Azure

Prima di compilare ed eseguire gli script U-SQL in Data Lake Analytics, è necessario connettersi al proprio account di Azure.

<b id="sign-in-by-command">Per connettersi ad Azure tramite un comando</b>

1.  Premere CTRL+MAIUSC+P per aprire il riquadro comandi. 
2.  Immettere **ADL: Login**. Le informazioni di accesso vengono visualizzate in basso a destra.

    ![Immissione del comando di accesso](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)

    ![Notifica di accesso e di autenticazione](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)

3.  Fare clic su **Copy & Open** (Copia e apri) per aprire la [pagina Web di accesso](https://aka.ms/devicelogin). Incollare il codice nella casella e quindi selezionare **Continua**.

    ![Pagina Web di accesso](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png)  
     
4.  Seguire le istruzioni per accedere dalla pagina Web. Quando si è connessi viene visualizzato il nome di account di Azure sulla barra di stato nell'angolo inferiore sinistro della finestra VS Code. 

> [!NOTE] 
>- Se non si effettua la disconnessione, Data Lake Tools effettuerà automaticamente l'accesso la volta successiva.
>- Se l'account ha due fattori abilitati, è consigliabile usare l'autenticazione telefonica anziché un PIN.


Per disconnettersi, immettere il comando **ADL: Logout** (ADL: Disconnetti).

**Per connettersi ad Azure da Explorer**

Espandere **AZURE DATALAKE**, selezionare **Sign in to Azure** (Accedi ad Azure), quindi seguire i passaggi 3 e 4 della sezione [Per connettersi ad Azure tramite un comando](#sign-in-by-command).

![Selezione di "Accedi ad Azure" in Explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-sign-in-from-explorer.png )  

Non è possibile disconnettersi da Explorer. Per disconnettersi, vedere [Per connettersi ad Azure tramite un comando](#sign-in-by-command).


## <a name="create-an-extraction-script"></a>Creare uno script di estrazione 
È possibile creare uno script di estrazione per i file con estensione csv, tsv e txt tramite il comando **ADL: Create EXTRACT Script** (ADL: Crea script EXTRACT) o da Azure Data Lake Explorer.

**Per creare uno script di estrazione tramite un comando**

1. Premere CTRL+MAIUSC+P per aprire il riquadro comandi e immettere **ADL: Create EXTRACT Script** (ADL: Crea script EXTRACT).
2. Specificare il percorso completo di un file di Archiviazione di Azure e premere INVIO.
3. Selezionare un account.
4. Per il file con estensione txt, selezionare un delimitatore per estrarre il file. 

![Processo per la creazione di uno script di estrazione](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-process.png)

Lo script di estrazione viene generato in base ai valori immessi. Per uno script che non è in grado di rilevare le colonne, scegliere una tra le due opzioni. In caso contrario, verrà generato un solo script.

![Risultato della creazione di uno script di estrazione](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-result.png)

**Per creare uno script di estrazione da Explorer**

È possibile creare lo script di estrazione anche tramite il menu di scelta rapida che è possibile visualizzare facendo clic con il pulsante destro del mouse sul file con estensione csv, tsv o txt in Azure Data Lake Store o nell'archiviazione BLOB di Azure.

![Comando "Crea script EXTRACT" dal menu di scelta rapida](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu.png)

## <a name="integrate-with-azure-data-lake-analytics-through-a-command"></a>Integrazione con Azure Data Lake Analytics tramite un comando

È possibile accedere alle risorse di Azure Data Lake Analytics per elencare gli account, accedere ai metadati e visualizzare i processi di analisi. 

**Per elencare gli account Azure Data Lake Analytics della sottoscrizione di Azure**

1. Premere CTRL+MAIUSC+P per aprire il riquadro comandi.
2. Immettere **ADL: List Accounts** (ADL: Elenca account). L'account viene visualizzato nel riquadro **Output**.

**Per accedere ai metadati di Azure Data Lake Analytics**

1.  Premere CTRL+MAIUSC+P e immettere **ADL: List Tables** (ADL: Elenca tabelle).
2.  Selezionare uno degli account di Data Lake Analytics.
3.  Selezionare uno dei database di Data Lake Analytics.
4.  Selezionare uno degli schemi. È possibile visualizzare l'elenco delle tabelle.

**Per visualizzare i processi di Azure Data Lake Analytics**
1.  Aprire il riquadro comandi (CTRL+MAIUSC+P) e selezionare **ADL: Show Jobs** (ADL: Mostra processi). 
2.  Selezionare un account di Data Lake Analytics o locale. 
3.  Attendere che venga visualizzato l'elenco dei processi per l'account.
4.  Selezionare un processo nell'elenco dei processi. Strumenti Data Lake aprirà la visualizzazione del processo nel riquadro di destra e visualizzerà alcune informazioni nell'output VS Code.

    ![Elenco processi](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="integrate-with-azure-data-lake-store-through-a-command"></a>Integrazione con Azure Data Lake Store tramite un comando

È possibile usare i comandi correlati ad Azure Data Lake Store per:
 - [Esplorare le risorse di Azure Data Lake Store](#list-the-storage-path) 
 - [Visualizzare in anteprima il file di Azure Data Lake Store](#preview-the-storage-file) 
 - Caricare il file direttamente in Azure Data Lake Store in VS Code
 - Scaricare il file direttamente da Azure Data Lake Store in VS Code

### <a name="list-the-storage-path"></a>Elencare il percorso di archiviazione 

**Per elencare il percorso di archiviazione tramite il riquadro comandi**

1. Fare clic con il pulsante destro del mouse sull'editor di script e scegliere **ADL: List Path** (ADL: Elenca percorso).
2. Scegliere la cartella nell'elenco o fare clic su **Enter Path** (Immetti un percorso) o su **Browse from Root** (Sfoglia da radice). Questo passaggio usa **Enter a path** (Immetti un percorso) come esempio. 
3. Selezionare l'account di Data Lake Analytics.
4. Andare al percorso della cartella di archiviazione o immetterlo manualmente, ad esempio /output/.  

Il riquadro comandi elenca le informazioni sul percorso in base all'input.

![Risultati del percorso di archiviazione](./media/data-lake-analytics-data-lake-tools-for-vscode/list-storage-path.png)

Un modo più pratico per elencare il percorso relativo consiste nell'usare il menu di scelta rapida.

**Per elencare il percorso di archiviazione tramite il menu di scelta rapida**

Fare clic con il pulsante destro del mouse sulla stringa del percorso e scegliere **List Path** (Elenca percorso).

!["List Path" (Elenca percorso) nel menu di scelta rapida](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)


### <a name="preview-the-storage-file"></a>Visualizzare in anteprima il file di archiviazione

1. Fare clic con il pulsante destro del mouse sull'editor di script e scegliere **ADL: Preview File** (ADL: Anteprima file).
2. Selezionare l'account di Data Lake Analytics. 
3. Immettere un percorso di file di Archiviazione di Azure, ad esempio /output/SearchLog.txt. 

Il file si apre in VS Code.

![Passaggi e risultati per visualizzare in anteprima il file di archiviazione](./media/data-lake-analytics-data-lake-tools-for-vscode/preview-storage-file.png)

Un altro modo per visualizzare l'anteprima del file è attraverso il menu di scelta rapida sul percorso completo o sul percorso relativo del file nell'editor di script. 

### <a name="upload-a-file-or-folder"></a>Caricare un file o una cartella

1. Fare clic con il pulsante destro del mouse sull'editor di script e scegliere **Upload File** (Carica file) o **Upload Folder** (Carica cartella).
2. Scegliere uno o più file se è stata selezionata l'opzione **Carica file** oppure scegliere l'intera cartella se è stata selezionata l'opzione **Upload Folder** (Carica cartella). Selezionare quindi **Carica**. 
3. Scegliere la cartella di archiviazione nell'elenco o selezionare **Enter Path** (Immetti un percorso) o su **Browse from Root** (Sfoglia da radice). Questo passaggio usa **Enter a path** (Immetti un percorso) come esempio. 
4. Selezionare l'account di Data Lake Analytics. 
5. Andare al percorso della cartella di archiviazione o immetterlo manualmente, ad esempio /output/. 
6. Scegliere **Choose Current Folder** (Scegli cartella corrente) per specificare la destinazione di caricamento.

![Passaggi e risultati per il caricamento di un file o di una cartella](./media/data-lake-analytics-data-lake-tools-for-vscode/upload-file.png)    

Un altro modo per caricare file da archiviare è attraverso il menu di scelta rapida sul percorso completo o sul percorso relativo del file nell'editor di script.

È possibile [monitorare lo stato di caricamento](#check-storage-tasks-status).


### <a name="download-a-file"></a>Scaricare un file 
È possibile scaricare un file usando il comando **ADL: Download File** (ADL: Scarica file) o **ADL: Download File (Advanced)** (ADL: Scarica file (avanzato)).

**Per scaricare un file tramite il comando ADL: Download File (Advanced)** (ADL: Scarica file (avanzato))
1. Fare clic con il pulsante destro del mouse sull'editor di script e scegliere **Download file (Advanced)** (Scarica file - Opzioni avanzate).
2. VS Code visualizza un file JSON. È possibile immettere i percorsi di file e scaricare più file contemporaneamente. Le istruzioni sono visualizzate nella finestra **Output**. Salvare (CTRL+S) il file JSON per procedere con il download dei file.

    ![File JSON con i percorsi per il download dei file](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-files.png)

La finestra **Output** mostra lo stato del download dei file.

![Finestra di output con lo stato del download](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-file-result.png)     

È possibile [monitorare lo stato del download](#check-storage-tasks-status).

**Per scaricare un file tramite il comando ADL: Download File** (ADL: Scarica file)

1. Fare clic con il pulsante destro del mouse sull'editor di script, scegliere **Download File** (Scarica file) e quindi selezionare la cartella di destinazione nella finestra di dialogo **Select Folder** (Seleziona cartella).
2. Scegliere la cartella nell'elenco o fare clic su **Enter Path** (Immetti un percorso) o su **Browse from Root** (Sfoglia da radice). Questo passaggio usa **Enter a path** (Immetti un percorso) come esempio. 
3. Selezionare l'account di Data Lake Analytics. 
4. Andare al percorso della cartella di archiviazione o immetterlo manualmente, ad esempio /output/, e scegliere un file da scaricare.

![Passaggi e risultati per il download di un file](./media/data-lake-analytics-data-lake-tools-for-vscode/download-file.png) 

Un altro modo per scaricare file di archiviazione è attraverso il menu di scelta rapida sul percorso completo o sul percorso relativo del file nell'editor di script.

È possibile [monitorare lo stato del download](#check-storage-tasks-status).

### <a name="check-storage-tasks-status"></a>Controllare lo stato delle attività di archiviazione
Sulla barra di stato viene visualizzato lo stato del caricamento e del download. Selezionare la barra di stato: viene visualizzato lo stato nella scheda **OUTPUT**.

![Dettagli della barra di stato e di output](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-status.png)


## <a name="integrate-with-azure-data-lake-analytics-from-the-explorer"></a>Integrazione con Azure Data Lake Analytics da Explorer

Dopo l'accesso, tutte le sottoscrizioni dell'account Azure sono elencate nel riquadro sinistro di **AZURE DATALAKE**. 

![Data Lake Explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer.png)

### <a name="data-lake-analytics-metadata-navigation"></a>Navigazione nei metadati di Data Lake Analytics

Espandere la sottoscrizione di Azure. Sotto il nodo **Database U-SQL**, è possibile esplorare il database U-SQL e visualizzare cartelle come **Schemi**, **Credenziali**, **Assembly**, **Tabelle** e **Indice**.

### <a name="data-lake-analytics-metadata-entity-management"></a>Gestione delle entità dei metadati di Azure Data Lake Analytics

Espandere i **database U-SQL**. È possibile creare un database, uno schema, una tabella, un tipo di tabella, un indice o una statistica facendo clic sul nodo corrispondente e quindi selezionando **Script to Create** (Script per la creazione) nel menu di scelta rapida. Nella pagina dello script aperta modificare lo script in base alle esigenze. Quindi inviare il processo facendo clic con il pulsante destro del mouse su di esso e selezionando **ADL: Submit Job** (ADL: Invia processo). 

Al termine della creazione dell'elemento, fare clic con il pulsante destro del mouse sul nodo e quindi selezionare **Aggiorna** per visualizzare l'elemento. È anche possibile eliminare l'elemento facendo clic con il pulsante destro del mouse su di esso e scegliendo **Elimina**.

![Comando "Script to Create" (Script per la creazione) nel menu di scelta rapida in Data Lake Explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create.png)

![Pagina dello script per il nuovo elemento](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create-snippet.png)

### <a name="data-lake-analytics-assembly-registration"></a>Registrazione dell'assembly Data Lake Analytics

È possibile registrare un assembly nel database corrispondente facendo clic con il pulsante destro del mouse sul nodo **Assembly** e selezionando **Registra assembly**.

![Comando "Registra assembly" nel menu di scelta rapida del nodo di Assembly](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer-register-assembly.png)

## <a name="integrate-with-azure-data-lake-store-from-the-explorer"></a>Integrazione con Azure Data Lake Store da Explorer

Passare a **Data Lake Store**:

- Nel nodo della cartella è possibile fare clic con il pulsante destro del mouse e usare i comandi **Aggiorna**, **Elimina**, **Carica**, **Upload Folder** (Carica cartella), **Copia percorso relativo** e **Copia percorso completo** dal menu di scelta rapida.

   ![Comandi di menu di scelta rapida per un nodo di cartella in Data Lake Explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-folder-menu.png)

- È possibile fare clic con il pulsante destro del mouse sul nodo dei file e quindi usare i comandi **Anteprima**, **Scarica**, **Elimina**, **Crea script EXTRACT** (disponibile solo per file con estensione csv, tsv e txt), **Copia percorso relativo** e **Copia percorso completo** del menu di scelta rapida.

   ![Comandi di menu di scelta rapida per un nodo di file in Data Lake Explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-extract.png)

## <a name="integrate-with-azure-blob-storage-from-the-explorer"></a>Integrazione con archiviazione BLOB di Azure da Explorer

Andare all'archiviazione BLOB:

- È possibile fare doppio clic con il pulsante destro del mouse sul nodo contenitore BLOB e quindi usare i comandi **Aggiorna**, **Elimina contenitore BLOB** e **Carica BLOB** nel menu di scelta rapida.

   ![Comandi di menu di scelta rapida per un nodo contenitore BLOB nell'archiviazione BLOB](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-blob-container-node.png)

- È possibile fare clic con il pulsante destro del mouse sul nodo della cartella e quindi usare i comandi **Aggiorna** e **Carica BLOB** nel menu di scelta rapida.

   ![Comandi di menu di scelta rapida per un nodo della cartella nell'archiviazione BLOB](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-folder-node.png)

- È possibile fare clic con il pulsante destro del mouse sul nodo dei file e quindi usare i comandi **Preview/Edit** (Anteprima/Modifica), **Scarica**, **Elimina**, **Crea script EXTRACT** (disponibile solo per file con estensione csv, tsv e txt), **Copia percorso relativo** e **Copia percorso completo** del menu di scelta rapida.

    ![Comandi di menu di scelta rapida per un nodo di file nell'archiviazione BLOB](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu-2.png)

## <a name="open-the-data-lake-explorer-in-the-portal"></a>Aprire Esplora risorse di Data Lake Explorer
1. Premere CTRL+MAIUSC+P per aprire il riquadro comandi.
2. Immettere **Open Web Azure Storage Explorer** (Apri Web Azure Storage Explorer) o fare clic con il pulsante destro del mouse su un percorso relativo o un percorso completo nell'editor di script e quindi scegliere **Open Web Azure Storage Explorer** (Apri Web Azure Storage Explorer).
3. Selezionare un account di Data Lake Analytics.

Strumenti Data Lake apre il percorso di Archiviazione di Azure nel portale di Azure. È possibile trovare il percorso e visualizzare in anteprima il file dal Web.

## <a name="additional-features"></a>Funzionalità aggiuntive

Strumenti Data Lake per VS Code supporta le funzionalità seguenti:

-   **Completamento automatico IntelliSense**: vengono visualizzati suggerimenti in finestre popup intorno agli elementi, ad esempio parole chiave, metodi e variabili. Icone diverse rappresentano vari tipi di oggetti:

    - Tipo di dati Scala
    - Tipo di dati complesso
    - UDT integrati
    - Raccolta e classi .NET
    - Espressioni C#
    - UDF, UDO e UDAAG di C# integrati 
    - Funzioni di U-SQL
    - Funzioni di windowing di U-SQL
 
    ![Tipi di oggetti IntelliSense](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)
 
-   **Completamento automatico IntelliSense sui metadati di Data Lake Analytics**: gli strumenti di Data Lake scaricano le informazioni sui metadati di Data Lake Analytics in locale. La funzionalità IntelliSense consente di popolare automaticamente gli oggetti dai metadati di Data Lake Analytics. Questi oggetti includono il database, lo schema, la tabella, la vista, la funzione con valori di tabella, le procedure e gli assembly C#.
 
    ![Metadati di IntelliSense](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

-   **Indicatore di errore di IntelliSense**: gli strumenti di Data Lake rimarcano gli errori di modifica per U-SQL e C#. 
-   **Evidenziazione della sintassi**: gli strumenti di Data Lake usano colori diversi per distinguere gli elementi, ad esempio variabili, parole chiave, tipi di dati e funzioni. 

    ![Sintassi con diversi colori](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

> [!NOTE]
> Si consiglia di eseguire l'aggiornamento a Strumenti Azure Data Lake per Visual Studio versione 2.3.3000.4 o successiva. Le versioni precedenti non sono più disponibili per il download e sono deprecate.  
   
## <a name="next-steps"></a>Passaggi successivi
- [Eseguire lo sviluppo U-SQL con Python, R e CSharp per Azure Data Lake Analytics in VS Code](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [Esecuzione locale e debug locale di U-SQL per Windows con Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
- [Esercitazione: Introduzione ad Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md)
- [Esercitazione: Sviluppare script U-SQL tramite Strumenti Data Lake per Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
