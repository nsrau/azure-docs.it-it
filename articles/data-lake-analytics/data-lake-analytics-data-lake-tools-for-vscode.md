---
title: 'Strumenti Azure Data Lake: Usare Strumenti Azure Data Lake per Visual Studio Code | Microsoft Docs'
description: 'Informazioni su come usare gli Strumenti Azure Data Lake per Visual Studio Code per creare, testare ed eseguire gli script U-SQL. '
Keywords: VScode,Azure Data Lake Tools,Local run,Local debug,Local Debug,preview file,upload to storage path,download,upload
services: data-lake-analytics
documentationcenter: 
author: jejiang
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/10/2017
ms.author: jejiang
ms.openlocfilehash: 60307b8b16718fdc947bde7616532fa6a0920cf0
ms.sourcegitcommit: 21a58a43ceceaefb4cd46c29180a629429bfcf76
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2017
---
# <a name="use-azure-data-lake-tools-for-visual-studio-code"></a>Usare gli Strumenti Azure Data Lake per Visual Studio Code

Informazioni su Strumenti Azure Data Lake per Visual Studio Code (VS Code) per creare, testare ed eseguire gli script U-SQL. Le informazioni sono disponibili anche nel video seguente:

<a href="https://channel9.msdn.com/Series/AzureDataLake/Azure-Data-Lake-Tools-for-VSCode?term=ADL%20Tools%20for%20VSCode"><img src="./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png"></a>

## <a name="prerequisites"></a>Prerequisiti

Strumenti Azure Data Lake per Visual Studio Code supportano Windows, Linux e MacOS.  

- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx).

Per MacOS e Linux:
- [.NET Core SDK 2.0](https://www.microsoft.com/net/download/core). 
- [Mono 5.2.x](http://www.mono-project.com/download/).

## <a name="install-data-lake-tools"></a>Installare gli strumenti di Data Lake

Dopo aver installato i prerequisiti, è possibile installare gli strumenti di Data Lake per VSCode.

**Per installare gli strumenti di Data Lake**

1. Aprire Visual Studio Code.
2. Fare clic su **Estensioni** nel riquadro sinistro. Immettere **Azure Data Lake** nella casella di ricerca.
3. Fare clic su **Installa** accanto a **Strumenti di Azure Data Lake**. Dopo alcuni secondi, il pulsante **Installa** verrà sostituito dal pulsante **Ricarica**.
4. Fare clic su **Ricarica** per attivare l'estensione **Strumenti di Azure Data Lake**.
5. Fare clic su **Ricarica finestra** per confermare. È possibile visualizzare **Strumenti di Azure Data Lake** nel riquadro Estensioni.

    ![Riquadro Estensioni di Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

 
## <a name="activate-azure-data-lake-tools"></a>Attivare Strumenti Azure Data Lake
Creare un nuovo file .usql o aprire un file .usql esistente per attivare l'estensione. 

## <a name="open-the-sample-script"></a>Aprire lo script di esempio
Usare il riquadro comandi (CTRL+MAIUSC+P) e immettere **ADL: Open Sample Script** (ADL: Apri script di esempio). Viene aperta un'altra istanza di questo esempio. Anche in questa istanza è possibile modificare, configurare e inviare lo script.

## <a name="work-with-u-sql"></a>Uso di U-SQL

Per poter usare U-SQL è necessario aprire una cartella o un file U-SQL.

**Aprire una cartella per il progetto U-SQL**

1. Da Visual Studio Code selezionare il menu **File** e quindi selezionare **Apri cartella**.
2. Specificare una cartella e quindi scegliere **Seleziona cartella**.
3. Selezionare il menu **File** e quindi scegliere **Nuovo**. Un file Untilted-1 viene aggiunto al progetto.
4. Immettere il codice seguente nel file Untitled-1:

        @departments  = 
            SELECT * FROM 
                (VALUES
                    (31,    "Sales"),
                    (33,    "Engineering"), 
                    (34,    "Clerical"),
                    (35,    "Marketing")
                ) AS 
                      D( DepID, DepName );
         
        OUTPUT @departments
            TO "/Output/departments.csv"
        USING Outputters.Csv();

    Lo script crea un file departments.csv con alcuni dati inclusi nella cartella /output.

5. Salvare il file come **myUSQL.usql** nella cartella aperta. Alla cartella viene aggiunto anche un file di configurazione xxx_settings.json.
6. Aprire e configurare il file xxx_settings.json con le proprietà seguenti:

    - Account: account di Data Lake Analytics nella sottoscrizione di Azure, necessario per compilare ed eseguire processi U-SQL. È quindi necessario configurare l'account del computer prima di compilare ed eseguire i processi U-SQL.
    - Database: un database nel proprio account. Il database predefinito è **master**.
    - Schema: uno schema nel database. Lo schema è **dbo**.
    - Impostazioni facoltative:
        - Priorità: L'intervallo di priorità va da 1 a 1000 dove la priorità più alta corrisponde al valore 1. Il valore predefinito è **1000**.
        - Parallelismo: l'intervallo di parallelismo va da 1 a 150. Il valore predefinito è il parallelismo massimo consentito nell'account Azure Data Lake Analytics. 
        
        ![File di configurazione degli strumenti di Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-configuration-file.png)
      
        > [!NOTE] 
        > Dopo il salvataggio della configurazione, le informazioni relative ad account, database e schema vengono visualizzate sulla barra di stato nell'angolo inferiore sinistro del file con estensione USQL corrispondente.

**Compilare uno script U-SQL**

1. Premere CTRL+MAIUSC+P per aprire il riquadro comandi. 
2. Immettere **ADL: Compile Script**. I risultati di compilazione vengono visualizzati nella finestra **Output**. È anche possibile fare clic con il pulsante destro del mouse su un file di script e selezionare **ADL: Compile Script** (ADL: Compila script) per eseguire la compilazione di un processo U-SQL. Il risultato della compilazione viene visualizzato nel riquadro **Output**.
 

**Inviare uno script U-SQL**

1. Premere CTRL+MAIUSC+P per aprire il riquadro comandi. 
2. Immettere **ADL: Submit Job**.  È anche possibile fare clic con il pulsante destro del mouse su un file di script e selezionare **ADL: Invia processo**. 

Dopo aver inviato un processo U-SQL, i log di invio vengono visualizzati nella finestra **Output** in VS Code. Se l'invio ha esito positivo, viene visualizzato anche l'URL del processo. È possibile aprire l'URL del processo in un Web browser per monitorare lo stato del processo in tempo reale.

Per abilitare l'output dei dettagli del processo: impostare **jobInformationOutputPath** nel file **vs code for the u-sql_settings.json**.
 
## <a name="use-python-r-and-csharp-code-behind-file"></a>Usare il file code-behind Python, R e CSharp
Strumenti Azure Data Lake supporta diversi tipi di codice personalizzato. Per istruzioni, vedere [Eseguire lo sviluppo U-SQL con Python, R e CSharp per Azure Data Lake Analytics in VSCode](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md).

## <a name="use-assemblies"></a>Usare gli assembly

Per informazioni sullo sviluppo di assembly, vedere [Develop U-SQL assemblies for Azure Data Lake Analytics jobs](data-lake-analytics-u-sql-develop-assemblies.md) (Sviluppare assembly U-SQL per i processi di Azure Data Lake Analytics).

È possibile usare Strumenti Data Lake per registrare gli assembly di codice personalizzato nel catalogo di Data Lake Analytics.

**Registrare un assembly**

È possibile registrare l'assembly tramite i comandi **ADL: Register Assembly** (ADL: Registra assembly) o **ADL: Register Assembly through Configuration** (ADL: Regista assembly da configurazione).

**Per eseguire la registrazione mediante il comando ADL: Register Assembly** (ADL: Registra assembly)
1.  Premere CTRL+MAIUSC+P per aprire il riquadro comandi.
2.  Immettere **ADL: Register Assembly** (ADL: Registra assembly). 
3.  Specificare il percorso dell'assembly locale. 
4.  Selezionare un account di Data Lake Analytics.
5.  Selezionare un database.

Risultati: il portale viene aperto nel browser e visualizza il processo di registrazione dell'assembly.  

Un altro modo pratico per attivare il comando **ADL: Register Assembly** (ADL: Registra assembly) consiste nel fare clic con il pulsante destro del mouse sul file .dll in Esplora file. 

**Per eseguire la registrazione mediante il comando ADL: Register Assembly through Configuration** (ADL: Registra assembly da configurazione)
1.  Premere CTRL+MAIUSC+P per aprire il riquadro comandi.
2.  Immettere **ADL: Register Assembly through Configuration** (ADL: Registra assembly da configurazione). 
3.  Specificare il percorso dell'assembly locale. 
4.  Verrà visualizzato il file JSON. Esaminare e modificare le dipendenze dell'assembly e i parametri delle risorse, se necessario. Le istruzioni sono visualizzate nella finestra **Output**. Salvare (CTRL+S) il file JSON per procedere con la registrazione dell'assembly.

![Code-behind di Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)
>[!NOTE]
>- Dipendenze dell'assembly: Strumenti Azure Data Lake rileva automaticamente se la DLL include dipendenze. Le dipendenze vengono visualizzate nel file JSON dopo che sono state rilevate. 
>- Risorse: è possibile caricare le risorse DLL (ad esempio i file con estensione txt, png e csv) come parte della registrazione dell'assembly. 

Un altro modo pratico per attivare il comando **ADL: Register Assembly through Configuration** (ADL: Registra assembly da configurazione) consiste nel fare clic con il pulsante destro del mouse sul file .dll in Esplora file. 

Il codice U-SQL seguente illustra come chiamare un assembly. Nell'esempio il nome dell'assembly è *test*.

```
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
```

## <a name="connect-to-azure"></a>Connect to Azure

Prima di compilare ed eseguire gli script U-SQL in Data Lake Analytics, è necessario connettersi al proprio account di Azure.

**Connettersi ad Azure**

1.  Premere CTRL+MAIUSC+P per aprire il riquadro comandi. 
2.  Immettere **ADL: Login** (ADL: Accedi). Le informazioni di accesso sono presenti nel riquadro **Output**.

    ![Riquadro comandi di Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)
    ![Informazioni di accesso di Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)
3. Tenendo premuto CTRL fare clic sull'URL di accesso: https://aka.ms/devicelogin per aprire la pagina Web di accesso. Immettere il codice **G567LX42V** nella casella di testo e quindi selezionare **Continua**.

   ![Codice di accesso da incollare di Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png )   
4.  Seguire le istruzioni per accedere dalla pagina Web. Quando si è connessi viene visualizzato il nome di account di Azure sulla barra di stato nell'angolo inferiore sinistro della finestra **VS Code**. 

    > [!NOTE] 
    > Se l'account ha due fattori abilitati, è consigliabile usare l'autenticazione telefonica anziché un PIN.

Per disconnettersi immettere il comando **ADL: Logout** (ADL: Disconnetti).

## <a name="list-your-data-lake-analytics-accounts"></a>Elencare gli account di Data Lake Analytics personali

Per testare la connessione ottenere un elenco degli account di Data Lake Analytics.

**Elencare gli account di Data Lake Analytics sotto una sottoscrizione di Azure**

1. Premere CTRL+MAIUSC+P per aprire il riquadro comandi.
2. Immettere **ADL: List Accounts** (ADL: Elenca gli account). L'account viene visualizzato nel riquadro **Output**.


## <a name="access-the-data-lake-analytics-catalog"></a>Accedere al catalogo di Data Lake Analytics

Dopo essersi connessi ad Azure, è possibile seguire questa procedura per accedere al catalogo di U-SQL.

**Per accedere ai metadati di Azure Data Lake Analytics**

1.  Premere CTRL+MAIUSC+P e digitare **ADL: List Tables** (ADL: Elenca tabelle).
2.  Selezionare uno degli account di Data Lake Analytics.
3.  Selezionare uno dei database di Data Lake Analytics.
4.  Selezionare uno degli schemi. È possibile visualizzare l'elenco delle tabelle.

## <a name="view-data-lake-analytics-jobs"></a>Visualizzare i processi di Data Lake Analytics

**Per visualizzare i processi di Data Lake Analytics**
1.  Aprire il riquadro comandi (CTRL+MAIUSC+P) e selezionare **ADL: Show Job** (ADL: Mostra processo). 
2.  Selezionare un account di Data Lake Analytics o locale. 
3.  Attendere che venga visualizzato l'elenco dei processi per l'account.
4.  Selezionare un processo nell'elenco. Strumenti Data Lake apre i dettagli del processo nel portale e mostra il file JobInfo in VSCode.

    ![Tipi di oggetto IntelliSense degli strumenti di Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="azure-data-lake-storage-integration"></a>Integrazione di Azure Data Lake Store

È possibile usare i comandi correlati all'archiviazione di Azure Data Lake per:
 - Esplorare le risorse di archiviazione di Azure Data Lake. [Elencare il percorso di archiviazione](#list-the-storage-path). 
 - Visualizzare in anteprima il file di archiviazione di Azure Data Lake. [Visualizzare in anteprima il file di archiviazione](#preview-the-storage-file). 
 - Caricare il file direttamente nell'archiviazione di Azure Data Lake in VS Code. [Caricare un file o una cartella](#upload-file-or-folder).
 - Scaricare il file direttamente dall'archiviazione di Azure Data Lake in VS Code. [Scaricare il file](#download-file).

## <a name="list-the-storage-path"></a>Elencare il percorso di archiviazione 

**Per elencare il percorso di archiviazione tramite il riquadro comandi**

Fare clic con il pulsante destro del mouse sull'editor di script e scegliere **ADL: List Path** (ADL: Elenca percorso).

Scegliere la cartella nell'elenco o fare clic su **Enter Path** (Immetti percorso) o su **Browse from Root** (Sfoglia da radice). Nella figura seguente viene selezionata l'opzione "Enter a path" (Immetti percorso) a titolo di esempio. -> Selezionare il proprio **account ADLA**. -> Passare al percorso della cartella di archiviazione o immetterlo manualmente, ad esempio /output/. -> Il riquadro comandi elenca le informazioni sul percorso in base all'input.

![Risultato dell'inserimento del percorso di archiviazione di Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/list-storage-path.png)

Un modo più pratico per elencare il percorso relativo consiste nell'usare il menu di scelta rapida.

**Per elencare il percorso di archiviazione facendo clic con il pulsante destro del mouse**

Fare clic con il pulsante destro del mouse sulla stringa del percorso e scegliere **List Path** (Elenca percorso) per continuare.

![Menu di scelta rapida di Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)


## <a name="preview-the-storage-file"></a>Visualizzare in anteprima il file di archiviazione

Fare clic con il pulsante destro del mouse sull'editor di script e scegliere **ADL: Preview File** (ADL: Anteprima file).

Selezionare il proprio **account ADLA**. -> Immettere un percorso di file di archiviazione di Azure, ad esempio /output/SearchLog.txt. -> Risultato: il file viene aperto in VSCode.

   ![Risultato del file di anteprima in Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/preview-storage-file.png)

Un altro modo per visualizzare l'anteprima del file è fare clic con il pulsante destro del mouse sul percorso completo o sul percorso relativo del file nell'editor di script. 

## <a name="upload-file-or-folder"></a>Caricare un file o una cartella

1. Fare clic con il pulsante destro del mouse sull'editor di script e scegliere **Upload File** (Carica file) o **Upload Folder** (Carica cartella).

2. Scegliere uno o più file se è stata selezionata l'opzione per il caricamento dei file oppure scegliere l'intera cartella se è stata selezionata l'opzione di caricamento della cartella e quindi fare clic su **Upload** (Carica). -> Scegliere la cartella di archiviazione nell'elenco o fare clic su **Enter Path** (Immetti percorso) o su **Browse from Root** (Sfoglia da radice). Come esempio è stata selezionata l'opzione "Enter a path" (Immetti percorso). -> Selezionare il proprio **account ADLA**. -> Passare al percorso della cartella di archiviazione o immetterlo manualmente, ad esempio /output/. -> Fare clic su **Choose Current Folder** (Scegli cartella corrente) per specificare la destinazione di caricamento.

   ![Stato di caricamento in Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/upload-file.png)    


   Un altro modo per caricare file da archiviare è fare clic con il pulsante destro del mouse sul percorso completo del file o sul percorso relativo del file nell'editor di script.

È contemporaneamente possibile monitorare lo [stato di caricamento](#check-storage-tasks-status).


## <a name="download-file"></a>Scaricare il file 
È possibile scaricare i file immettendo i comandi **ADL: Download File** (ADL: Scarica file) o **ADL: Download file (Advanced)** (ADL: Scarica file - Opzioni avanzate).

**Per scaricare i file tramite il comando ADL: Download file (Advanced) (ADL: Scarica file - Opzioni avanzate)**
1. Fare clic con il pulsante destro del mouse sull'editor di script e scegliere **Download file (Advanced)** (Scarica file - Opzioni avanzate).
2. VS Code visualizza un file JSON. È possibile immettere i percorsi di file e scaricare più file contemporaneamente. Le istruzioni sono visualizzate nella finestra **Output**. Salvare (CTRL+S) il file JSON per procedere con lo scaricamento del file.

    ![Scaricamento di file in Strumenti Azure Data Lake per Visual Studio Code da configurazione](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-files.png)

3.  Risultati: la finestra **Output** mostra lo stato di caricamento del file.

    ![Risultato dello scaricamento di più file in Strumenti Azure Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-file-result.png)     

È contemporaneamente possibile monitorare lo [stato di scaricamento](#check-storage-tasks-status).

**Per scaricare i file tramite il comando ADL: Download file (ADL: Scarica file)**

1. Fare clic con il pulsante destro del mouse sull'editor di script, scegliere **Download File** (Scarica File) e quindi selezionare la cartella di destinazione nella finestra di dialogo **Select Folder** (Seleziona cartella).

2. Scegliere la cartella nell'elenco o fare clic su **Enter Path** (Immetti percorso) o su **Browse from Root** (Sfoglia da radice). Nella figura seguente viene selezionata l'opzione "Enter a path" (Immetti percorso) a titolo di esempio. -> Selezionare il proprio **account ADLA**. -> Passare al percorso della cartella di archiviazione o immetterlo manualmente, ad esempio /output/ -> Scegliere un file da scaricare.

   ![Stato di scaricamento in Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/download-file.png) 

   
   Un altro modo per scaricare file di archiviazione è fare clic con il pulsante destro del mouse sul percorso completo del file o sul percorso relativo del file nell'editor di script.

È contemporaneamente possibile monitorare lo [stato di scaricamento](#check-storage-tasks-status).

## <a name="check-storage-tasks-status"></a>Controllare lo stato delle attività di archiviazione
Lo stato viene visualizzato nella parte inferiore della barra di stato al termine delle operazioni di scaricamento e caricamento.
1. Fare clic sulla barra di stato per visualizzare lo stato di caricamento e scaricamento nel riquadro **OUTPUT**.

   ![Verifica dello stato di archiviazione in Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-status.png)

## <a name="vscode-explorer-integration-with-azure-data-lake"></a>Integrazione di VSCode Explorer con Azure Data Lake
1. Dopo l'accesso, tutti gli account di Azure sono elencati nel riquadro sinistro di **Data Lake Explorer**. Espandere un database per visualizzare **Schemi**, **Tabelle**, **Assembly** e così via nel nodo.

   ![Data Lake Explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer.png)

2. È possibile eseguire il comando **Registra assembly** facendo clic con il pulsante destro del mouse sul nodo **Assembly**.

    ![Data Lake Explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer-register-assembly.png)

3. Passare a **Account di archiviazione**. È possibile caricare o scaricare file facendo clic con il pulsante destro del mouse sulla cartella o sul file. Nel menu di scelta rapida sono disponibili anche i comandi **Anteprima**, **Scarica**, **Copia percorso relativo** e **Copia percorso completo** per un file.

   ![Data Lake Explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-download-preview-file.png)

## <a name="open-adl-storage-explorer-in-portal"></a>Aprire ADL Storage Explorer nel portale
1. Premere CTRL+MAIUSC+P per aprire il riquadro comandi.
2. Immettere **Open Web Azure Storage Explorer** (Apri Web Azure Storage Explorer) o fare clic con il pulsante destro del mouse su un percorso relativo o un percorso completo nell'editor di script e quindi scegliere **Open Web Azure Storage Explorer** (Apri Web Azure Storage Explorer).
3. Selezionare un account di Data Lake Analytics.

Strumenti Data Lake apre il percorso di archiviazione nel portale di Azure. È possibile trovare il percorso e visualizzare in anteprima il file dal Web.

## <a name="local-run-and-local-debug-for-windows-users"></a>Esecuzione e debug locale per utenti Windows
L'esecuzione locale di U-SQL verifica i dati locali e convalida lo script localmente prima che il codice venga pubblicato in Data Lake Analytics. La funzionalità di debug locale consente di completare le seguenti operazioni prima che il codice venga inviato a Data Lake Analytics: 
- Eseguire il debug del code-behind di C#. 
- Eseguire il codice passo per passo. 
- Convalidare lo script in locale.

Per istruzioni sull'esecuzione e il debug locali, vedere [Esecuzione locale e debug locale di U-SQL con Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md).

## <a name="additional-features"></a>Funzionalità aggiuntive

Strumenti Data Lake per VS Code supporta le funzionalità seguenti:

-   Completamento automatico di IntelliSense: vengono visualizzati suggerimenti in finestre popup intorno agli elementi, ad esempio parole chiave, metodi e variabili. Le icone differenti rappresentano tipi di oggetti diversi:

    - Tipo di dati Scala
    - Tipo di dati complesso
    - UDT integrati
    - Raccolta e classi .NET
    - Espressioni C#
    - UDF, UDO e UDAAG di C# integrati 
    - Funzioni di U-SQL
    - Funzione di windowing di U-SQL
 
    ![Tipi di oggetto IntelliSense degli strumenti di Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)
 
-   Completamento automatico IntelliSense per i metadati di Data Lake Analytics: Strumanti Data Lake scarica le informazioni di metadati di Data Lake Analytics localmente. La funzionalità IntelliSense popola automaticamente gli oggetti, tra cui database, schemi, tabelle, visualizzazione, funzione con valori di tabella, procedure e assembly C#, dai metadati di Data Lake Analytics.
 
    ![Metadati IntelliSense degli strumenti di Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

-   Indicatore di errore di IntelliSense: Strumenti Data Lake sottolinea gli errori di modifica per U-SQL e C#. 
-   Evidenziazione della sintassi: Strumenti Data Lake usa colori diversi per distinguere gli elementi, ad esempio variabili, parole chiave, tipo di dati e funzioni. 

    ![Sintassi in evidenza degli strumenti di Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

## <a name="next-steps"></a>Passaggi successivi
- [Eseguire lo sviluppo U-SQL con Python, R e CSharp per Azure Data Lake Analytics in VSCode](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [Esecuzione locale e debug locale di U-SQL per Windows con Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
- [Esercitazione: Introduzione ad Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md)
- [Esercitazione: Sviluppare script U-SQL tramite Strumenti Data Lake per Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
- [Sviluppare assembly U-SQL per processi di Azure Data Lake Analytics](data-lake-analytics-u-sql-develop-assemblies.md)




