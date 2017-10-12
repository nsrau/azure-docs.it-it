---
title: 'Strumenti Azure Data Lake: Usare Strumenti Azure Data Lake per Visual Studio Code | Microsoft Docs'
description: 'Informazioni su come usare gli Strumenti Azure Data Lake per Visual Studio Code per creare, testare ed eseguire gli script U-SQL. '
Keywords: VScode, Strumenti Azure Data Lake, Esecuzione locale, debug locale, Debug Locale, anteprima file di archiviazione, caricamento nel percorso di archiviazione
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
ms.date: 07/14/2017
ms.author: jejiang
ms.openlocfilehash: 4be6e2ef1cfba31dd3cf06f44e6a71ffd5900856
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-data-lake-tools-for-visual-studio-code"></a>Usare gli Strumenti Azure Data Lake per Visual Studio Code

Informazioni su come usare gli Strumenti Azure Data Lake per Visual Studio Code (VS Code) per creare, testare ed eseguire gli script U-SQL. Le informazioni sono disponibili anche nel video seguente:

<a href="https://www.youtube.com/watch?v=J_gWuyFnaGA&feature=youtu.be"><img src="./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png"></a>

## <a name="prerequisites"></a>Prerequisiti

È possibile installare Strumenti Data Lake con le piattaforme supportate da VS Code. Le piattaforme supportate includono Windows, Linux e MacOS. Le diverse piattaforme hanno i seguenti prerequisiti:

- Windows

    - [Visual Studio Code]( https://www.visualstudio.com/products/code-vs.aspx).
    - [Aggiornamento 77 o successivo di Java SE Runtime Environment, versione 8](https://java.com/download/manual.jsp). Aggiungere il percorso java.exe al percorso della variabile di ambiente di sistema. Per le istruzioni di configurazione vedere [Come impostare o modificare la variabile di sistema Path]( https://www.java.com/download/help/path.xml). Il percorso è simile a c:\Programmi\Java\jdk1.8.0_77\jre\bin.
    - [Runtime di .NET Core SDK 1.0.3 o .NET Core 1.1](https://www.microsoft.com/net/download).
    
- Linux (è consigliabile scegliere Ubuntu 14.04 LTS)

    - [Visual Studio Code]( https://www.visualstudio.com/products/code-vs.aspx). Per installare il codice immettere il comando seguente:

              sudo dpkg -i code_<version_number>_amd64.deb

    - [Mono 4.2.x](http://www.mono-project.com/docs/getting-started/install/linux/). 

        - Per aggiornare l'origine pacchetto deb immettere i comandi seguenti:

                sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 3FA7E0328081BFF6A14DA29AA6A19B38D3D831EF
                echo "deb http://download.mono-project.com/repo/debian wheezy/snapshots 4.2.4.4/main" | sudo tee /etc/apt/sources.list.d/mono-xamarin.list
                sudo apt-get update

        - Per installare Mono immettere il comando seguente:

                sudo apt-get install mono-complete

            > [!NOTE] 
            > Mono 4.6 non è supportato. Disinstallare completamente la versione 4.6 prima di installare la versione 4.2.x.  

        - [Aggiornamento 77 o successivo di Java SE Runtime Environment, versione 8](https://java.com/download/manual.jsp). Per istruzioni sull'installazione, vedere la pagina con le [istruzioni di installazione di Linux a 64 bit per Java]( https://java.com/en/download/help/linux_x64_install.xml).
        - [Runtime di .NET Core SDK 1.0.3 o .NET Core 1.1](https://www.microsoft.com/net/download).
- MacOS

    - [Visual Studio Code]( https://www.visualstudio.com/products/code-vs.aspx).
    - [Mono 4.2.4](http://download.mono-project.com/archive/4.2.4/macos-10-x86/). 
    - [Aggiornamento 77 o successivo di Java SE Runtime Environment, versione 8](https://java.com/download/manual.jsp). Per istruzioni sull'installazione, vedere la pagina con le [istruzioni di installazione di Linux a 64 bit per Java](https://java.com/en/download/help/mac_install.xml).
    - [Runtime di .NET Core SDK 1.0.3 o .NET Core 1.1](https://www.microsoft.com/net/download).

## <a name="install-data-lake-tools"></a>Installare gli strumenti di Data Lake

Dopo aver installato i prerequisiti, è possibile installare gli strumenti di Data Lake per VSCode.

**Per installare gli strumenti di Data Lake**

1. Aprire Visual Studio Code.
2. Selezionare CTRL+P e quindi immettere il comando seguente:
```
ext install usql-vscode-ext
```
È possibile visualizzare un elenco delle estensioni di codice di Visual Studio, tra cui Una di queste è costituita da **Strumenti Azure Data Lake**.

3. Selezionare **Installa** accanto a **Strumenti Azure Data Lake**. Dopo alcuni secondi il pulsante **Installa** diventa **Ricarica**.
4. Fare clic su **Ricarica** per attivare l'estensione.
5. Selezionare **OK** per confermare. È possibile visualizzare gli Strumenti Azure Data Lake nel riquadro **Estensioni**.
    ![Riquadro Estensioni di Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

## <a name="activate-azure-data-lake-tools"></a>Attivare Strumenti Azure Data Lake
Creare un nuovo file .usql o aprire un file .usql esistente per attivare l'estensione. 

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

## <a name="open-the-sample-script"></a>Aprire lo script di esempio
Usare il riquadro comandi (CTRL+MAIUSC+P) e immettere **ADL: Open Sample Script** (ADL: Apri script di esempio). Verrà quindi aperta un'altra istanza di questo esempio. Anche in questa istanza è possibile modificare, configurare e inviare lo script.

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

5. Salvare il file come **myUSQL.usql** nella cartella aperta. Al progetto viene inoltre aggiunto il file di configurazione adltools_settings.json.
4. Aprire e configurare il file adltools_settings.json con le proprietà seguenti:

    - Account: un account di Data Lake Analytics sotto la sottoscrizione di Azure.
    - Database: un database nel proprio account. Il database predefinito è **master**.
    - Schema: uno schema nel database. Lo schema è **dbo**.
    - Impostazioni facoltative:
        - Priorità: L'intervallo di priorità va da 1 a 1000 dove la priorità più alta corrisponde al valore 1. Il valore predefinito è **1000**.
        - Parallelismo: l'intervallo di parallelismo va da 1 a 150. Il valore predefinito è il parallelismo massimo consentito nell'account Azure Data Lake Analytics. 
        
        > [!NOTE] 
        > Se le impostazioni non sono valide, vengono applicati i valori predefiniti.

    ![File di configurazione degli strumenti di Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-configuration-file.png)

    È necessario un account di calcolo di Data Lake Analytics per poter compilare ed eseguire i processi U-SQL. È necessario configurare l'account del computer prima di compilare ed eseguire i processi di U-SQL.
    
Dopo il salvataggio della configurazione, le informazioni di account, database e schema vengono visualizzate sulla barra di stato nella parte inferiore sinistra del file .usql corrispondente. 
 
 
Rispetto all'apertura di un file, quando si apre una cartella è possibile:

- Usare un file code-behind. Code-behind non è supportato in modalità file unico.
- Usare un file di configurazione. Quando si apre una cartella, gli script nella cartella di lavoro condividono un singolo file di configurazione.


La compilazione dello script U-SQL viene eseguita da remoto attraverso il servizio Data Lake Analytics. Quando si esegue il comando **compile**, lo script U-SQL viene inviato all'account di Data Lake Analytics. In seguito Visual Studio Code riceve il risultato della compilazione. Per via della compilazione remota, Visual Studio Code richiede che vengano elencate le informazioni per connettersi all'account di Data Lake Analytics nel file di configurazione.

**Compilare uno script U-SQL**

1. Premere CTRL+MAIUSC+P per aprire il riquadro comandi. 
2. Immettere **ADL: Compile Script**. I risultati di compilazione vengono visualizzati nella finestra **Output**. È anche possibile fare clic con il pulsante destro del mouse su un file di script e selezionare **ADL: Compile Script** (ADL: Compila script) per eseguire la compilazione di un processo U-SQL. Il risultato della compilazione viene visualizzato nel riquadro **Output**.
 

**Inviare uno script U-SQL**

1. Premere CTRL+MAIUSC+P per aprire il riquadro comandi. 
2. Immettere **ADL: Submit Job**.  È anche possibile fare clic con il pulsante destro del mouse su un file di script e selezionare **ADL: Invia processo**. 

Dopo aver inviato un processo U-SQL, i log di invio vengono visualizzati nella finestra **Output** in VS Code. Se l'invio ha esito positivo, viene visualizzato anche l'URL del processo. È possibile aprire l'URL del processo in un Web browser per monitorare lo stato del processo in tempo reale.

Per abilitare l'output dei dettagli del processo: impostare **jobInformationOutputPath** nel file **vs code for the u-sql_settings.json**.
 
## <a name="use-a-code-behind-file"></a>Usare un file code-behind

Un file code-behind è un file C# associato a uno script U-SQL. È possibile definire uno script dedicato a UDO, UDA, UDT e UDFF nel file code-behind. L'opzione UDO, UDA, UDT e UDF può essere usata direttamente nello script senza dover prima registrare l'assembly. Il file code-behind viene inserito nella stessa cartella del suo file di script U-SQL associato. Se lo script viene denominato xxx.usql, code-behind assume il nome xxx.usql.cs. Se si elimina manualmente il file code-behind, viene disabilitata la funzionalità code-behind per lo script U-SQL associato. Per altre informazioni sulla scrittura del codice cliente per lo script U-SQL, vedere [Scrivere e usare il codice personalizzato in U-SQL: funzioni definite dall'utente]( https://blogs.msdn.microsoft.com/visualstudio/2015/10/28/writing-and-using-custom-code-in-u-sql-user-defined-functions/).

Per supportare code-behind, è necessario aprire una cartella di lavoro. 

**Generare un file code-behind**

1. Aprire un file di origine. 
2. Premere CTRL+MAIUSC+P per aprire il riquadro comandi.
3. Immettere **ADL: Generate Code Behind**. Viene creato un file code-behind nella stessa cartella. 

È anche possibile fare clic con il pulsante destro del mouse su un file di script e selezionare **ADL: Generate Code Behind** (ADL: Genera code-behind). 

La procedura di compilazione e invio di uno script U-SQL con code-behind è la stessa utilizzata per il file script U-SQL indipendente.

Le due schermate seguenti mostrano un file code-behind e il suo file di script U-SQL associato:
 
![Code-behind di Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-behind.png)

![File di script code-behind di Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-behind-call.png) 

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
>- Risorse: è possibile caricare le risorse DLL (ad esempio i file .txt, .png e .csv) come parte della registrazione dell'assembly. 

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
 - Esplorare le risorse di archiviazione di Azure Data Lake. 
 - Visualizzare in anteprima il file di archiviazione di Azure Data Lake.  
 - Caricare il file direttamente nell'archiviazione di Azure Data Lake in VS Code. 

### <a name="list-the-storage-path"></a>Elencare il percorso di archiviazione 
È possibile elencare il percorso di archiviazione tramite il riquadro comandi o facendo clic con il pulsante destro del mouse.

**Per elencare il percorso di archiviazione tramite il riquadro comandi**

1.  Usare il riquadro comandi (CTRL+MAIUSC+P) e scegliere **ADL: List Storage Path** (ADL: Elenca percorso di archiviazione).

    ![Elencare il percorso di archiviazione in Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-storage.png)

2.  Selezionare il modo preferito per elencare il percorso di archiviazione. Questo passaggio usa **Enter a path** (Immetti un percorso) come esempio.

    ![Un modo per elencare il percorso di archiviazione in Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account-selectoneway.png)

    > [!NOTE]
    >- VS Code mantiene l'ultimo percorso visitato in ogni account di Data Lake Analytics. Ad esempio /tt/ss.
    >- Sfogliare dal percorso radice: il percorso radice dell'elenco dall'account di Data Lake Analytics selezionato o un percorso locale.
    >- Immettere un percorso: elencare un percorso specificato dall'account di Data Lake Analytics selezionato o un percorso locale.
    
3. Selezionare un account dal percorso locale o un account di Data Lake Analytics.

    ![Selezionare altro in Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account.png)

4. Selezionare **more** (altro) per elencare altri account di Data Lake Analytics e quindi selezionare un account di Data Lake Analytics.

    ![Selezionare un account in Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-select-adla-account.png)

5.  Immettere un percorso di archiviazione di Azure. Ad esempio /output.

    ![Immettere un percorso di archiviazione in Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-input-path.png)

6.  Risultati: il riquadro comandi elenca le informazioni sul percorso in base all'input.

    ![Risultato dell'inserimento del percorso di archiviazione di Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-path.png)

Un modo più pratico per elencare il percorso relativo consiste nell'usare il menu di scelta rapida.

**Per elencare il percorso di archiviazione facendo clic con il pulsante destro del mouse**

1.  Fare clic con il pulsante destro del mouse sulla stringa del percorso e scegliere **List Storage Path** (Elenca percorso di archiviazione).

       ![Menu di scelta rapida di Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)

2. Il percorso relativo selezionato viene visualizzato nel riquadro comandi.

   ![Percorso relativo selezionato in Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-relative-path.png)

3.  Selezionare un account dal percorso locale o un account di Data Lake Analytics.

       ![Selezionare un account in Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account.png)

4.  Risultati: il riquadro comandi elenca le cartelle e i file per il percorso corrente.

       ![Elenco del percorso corrente in Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-current.png)

### <a name="preview-the-storage-file"></a>Visualizzare in anteprima il file di archiviazione
È possibile visualizzare in anteprima il file di archiviazione tramite il riquadro comandi o facendo clic con il pulsante destro del mouse.

**Per visualizzare in anteprima il file di archiviazione tramite il riquadro comandi**

1.  Usare il riquadro comandi (CTRL+MAIUSC+P) e immettere **ADL: Preview Storage File** (ADL: anteprima file di archiviazione).

       ![Anteprima dei file di archiviazione in Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-preview.png)

2.  Selezionare un account dal percorso locale o un account di Data Lake Analytics.

       ![Elencare l'account in Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account.png)

3.  Selezionare **more** (altro) per elencare altri account di Data Lake Analytics e quindi selezionare un account di Data Lake Analytics.

       ![Selezionare un account in Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-select-adla-account.png)

4.  Immettere un file o un percorso di archiviazione di Azure. Ad esempio /output/SearchLog.txt.

       ![Immissione di un file o percorso di archiviazione in Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-input-preview-file.png)

5.  Risultati: il riquadro comandi elenca le informazioni sul percorso in base all'input.

       ![Risultato del file di anteprima in Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-preview-results.png)

**Per elencare il percorso di archiviazione facendo clic con il pulsante destro del mouse**

1.  Per visualizzare in anteprima un file, fare clic con il pulsante destro del mouse sul percorso del file.

   ![Menu di scelta rapida di Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-preview.png) 

2.  Selezionare un account dal percorso locale o un account di Data Lake Analytics.

       ![Selezionare un account in Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account.png)

3.  Risultati: VS Code mostra il risultato dell'anteprima del file.

       ![Risultato del file di anteprima in Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-preview-results.png)

### <a name="upload-a-file"></a>Caricare un file 

È possibile caricare i file immettendo i comandi **ADL: Carica file** o **ADL: Upload File through Configuration** (ADL: Carica file da configurazione).

**Per caricare i file mediante il comando ADL: Carica file**
1. Premere CTRL+MAIUSC+P per aprire il riquadro comandi o fare clic con il pulsante destro del mouse sull'editor di script e immettere **Carica file**.
2.  Per caricare il file immettere un percorso locale.

    ![Inserimento di un percorso locale in Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-input-local-path.png)

3. Selezionare uno dei modi per elencare il percorso di archiviazione. Questo passaggio usa **Enter a path** (Immetti un percorso) come esempio.

    ![Elencare il percorso di archiviazione in Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account-selectoneway.png)
    >[!NOTE]
    >- VS Code mantiene l'ultimo percorso visitato in ogni account di Data Lake Analytics. Ad esempio /tt/ss.
    >- Sfogliare dal percorso radice: il percorso radice dell'elenco dall'account di Data Lake Analytics selezionato o un percorso locale.
    >- Immettere un percorso: elencare un percorso specificato dall'account di Data Lake Analytics selezionato o un percorso locale.

4. Selezionare un account dal percorso locale o un account di Data Lake Analytics.

    ![Archiviazione dal menu di scelta rapida in Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account.png)

5. Immettere un percorso di archiviazione di Azure. Ad esempio /output.

       ![Data Lake Tools for Visual Studio Code enter storage path](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-input-preview-file.png)

6. Individuare il percorso di archiviazione di Azure. Selezionare **Choose current folder** (Scegli cartella corrente).

    ![Selezionare una cartella in Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-choose-current-folder.png)

7.  Risultati: la finestra **Output** mostra lo stato di caricamento del file.

       ![Stato di caricamento in Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-upload-status.png)    

**Per caricare i file mediante il comando ADL: Upload File through Configuration (ADL: Carica file da configurazione)**
1.  Premere CTRL+MAIUSC+P per aprire il riquadro comandi o fare clic con il pulsante destro del mouse sull'editor di script, quindi immettere **Upload File through Configuration** (Carica file da configurazione).
2.  VS Code visualizza un file JSON. È possibile immettere i percorsi di file e caricare più file contemporaneamente. Le istruzioni sono visualizzate nella finestra **Output**. Salvare (CTRL+S) il file JSON per procedere con il caricamento del file.

       ![Percorso del file in Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-upload-file.png)

3.  Risultati: la finestra **Output** mostra lo stato di caricamento del file.

       ![Stato di caricamento in Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-upload-status.png)     

Un altro modo per caricare file da archiviare è fare clic con il pulsante destro del mouse sul percorso completo del file o sul percorso relativo del file nell'editor di script. Immettere il percorso del file locale e quindi selezionare l'account. La finestra **Output** mostra lo stato di caricamento del file. 

### <a name="open-azure-storage-explorer"></a>Aprire Azure Storage Explorer
È possibile aprire **Azure Storage Explorer** immettendo il comando **ADL: Open Web Azure Storage Explorer** (ADL: Apri Web Azure Storage Explorer) o selezionando la voce dal menu di scelta rapida visualizzato facendo clic con il pulsante destro del mouse.

**Per aprire Azure Storage Explorer**

1. Premere CTRL+MAIUSC+P per aprire il riquadro comandi.
2. Immettere **Open Web Azure Storage Explorer** (Apri Web Azure Storage Explorer) o fare clic con il pulsante destro del mouse su un percorso relativo o un percorso completo nell'editor di script e quindi scegliere **Open Web Azure Storage Explorer** (Apri Web Azure Storage Explorer).
3. Selezionare un account di Data Lake Analytics.

Strumenti Data Lake apre il percorso di archiviazione nel portale di Azure. È possibile trovare il percorso e visualizzare in anteprima il file dal Web.

### <a name="local-run-and-local-debug-for-windows-users"></a>Esecuzione e debug locale per utenti Windows
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

- Per l'esecuzione e il debug locali di U-SQL con Visual Studio Code, vedere [Esecuzione locale e debug locale di U-SQL con Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md).
- Per informazioni introduttive su Data Lake Analytics, vedere [Esercitazione: Introduzione a Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md).
- Per informazioni sull'uso degli strumenti di Data Lake per Visual Studio, vedere [Esercitazione: Sviluppare script U-SQL tramite Strumenti di Data Lake per Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Per informazioni sullo sviluppo di assembly, vedere [Develop U-SQL assemblies for Azure Data Lake Analytics jobs](data-lake-analytics-u-sql-develop-assemblies.md) (Sviluppare assembly U-SQL per i processi di Azure Data Lake Analytics).



