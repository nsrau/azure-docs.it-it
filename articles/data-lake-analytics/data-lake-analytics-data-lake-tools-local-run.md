---
title: Eseguire test e debug di processi U-SQL tramite l&quot;esecuzione locale e l&quot;SDK U-SQL di Azure Data Lake | Documentazione Microsoft
description: Informazioni su come usare gli strumenti di Azure Data Lake per Visual Studio e l&quot;SDK U-SQL di Azure Data Lake per eseguire test e debug dei processi di U-SQL nella workstation locale.
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/15/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: c43fb7b513136bb3ea964419d68f58585bba48fd
ms.openlocfilehash: 348971e07169ae9c155753c7943124e96e6e9597


---
# <a name="test-and-debug-u-sql-jobs-by-using-local-run-and-the-azure-data-lake-u-sql-sdk"></a>Eseguire test e debug di processi U-SQL tramite l'esecuzione locale e l'SDK U-SQL di Azure Data Lake

È possibile usare Strumenti di Azure Data Lake per Visual Studio e l'SDK U-SQL di Azure Data Lake per eseguire i processi di U-SQL nella workstation, esattamente come nel servizio Azure Data Lake. Queste due funzionalità eseguite localmente permettono di risparmiare tempo per le operazioni di test e debug dei processi di U-SQL.

Prerequisiti:

- Un account di Azure Data Lake Analytics. Vedere [Introduzione ad Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md).
- Strumenti di Azure Data Lake per Visual Studio. Vedere [Sviluppare script U-SQL con gli strumenti di Data Lake per Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- L'esperienza di sviluppo degli script U-SQL. Vedere [Introduzione ad Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md).


## <a name="understand-the-data-root-folder-and-the-file-path"></a>Comprendere la cartella data-root e il percorso dei file

Sia l'SDK di U-SQL che l'esecuzione locale richiedono una cartella data-root. La cartella data-root è un "archivio locale" per l'account di calcolo locale, equivalente all'account di Azure Data Lake Store di un account Data Lake Analytics. Il passaggio a un'altra cartella data-root è identico al passaggio a un account archivio differente. Se si vuole accedere a dati comunemente condivisi con cartelle data-root diverse, è necessario usare percorsi assoluti negli script. In alternativa è possibile creare collegamenti simbolici del file system (ad esempio **mklink** in un sistema NTFS) nella cartella data-root che puntino ai dati condivisi.

La cartella data-root viene usata per:

- Archiviare i metadati, inclusi database, tabelle, funzione con valori di tabella e assembly.
- Cercare i percorsi di input e output che sono definiti come percorsi relativi in U-SQL. L'uso di percorsi relativi semplifica la distribuzione dei progetti di U-SQL in Azure.

Negli script U-SQL è possibile usare sia un percorso relativo sia un percorso assoluto locale. Il percorso relativo è relativo al percorso della cartella data-root specificato. È consigliabile usare "/" come separatore del percorso per rendere gli script compatibili con il lato server. Di seguito sono riportati alcuni esempi di percorsi relativi e dei loro percorsi assoluti equivalenti. In questi esempi la cartella data-root è C:\LocalRunDataRoot.

|Percorso relativo|Percorso assoluto|
|-------------|-------------|
|/abc/def/input.csv |C:\LocalRunDataRoot\abc\def\input.csv|
|abc/def/input.csv  |C:\LocalRunDataRoot\abc\def\input.csv|
|D:/abc/def/input.csv |D:\abc\def\input.csv|

## <a name="use-local-run-from-visual-studio"></a>Usare l'esecuzione locale per Visual Studio

Gli Strumenti di Data Lake per Visual Studio offrono un'esperienza di esecuzione locale di U-SQL in Visual Studio. Questa funzionalità permette di:

- Eseguire uno script U-SQL in locale, insieme agli assembly C#.
- Eseguire il debug di un assembly C# localmente.
- Creare, visualizzare ed eliminare i cataloghi di U-SQL (database locali, assembly, schemi e tabelle) da Esplora server. Il catalogo locale è disponibile anche in Esplora server.

    ![Catalogo locale dell'esecuzione locale degli strumenti di Data Lake per Visual Studio](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-local-catalog.png)

Il programma di installazione di Strumenti di Data Lake crea la cartella C:\LocalRunRoot che diventa la cartella data-root predefinita. Il parallelismo di esecuzione locale predefinito è 1.

### <a name="to-configure-local-run-in-visual-studio"></a>Configurare l'esecuzione locale in Visual Studio

1. Aprire Visual Studio.
2. Aprire **Esplora server**.
3. Espandere **Azure** > **Data Lake Analytics**.
4. Aprire il menu **Data Lake** e fare clic su **Opzioni e impostazioni**.
5. Nella struttura ad albero a sinistra espandere **Azure Data Lake** e **Generale**.

    ![Impostazioni di configurazione dell'esecuzione locale degli strumenti di Data Lake per Visual Studio](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-configure.png)

Per poter eseguire la funzionalità di esecuzione locale è richiesto un progetto U-SQL di Visual Studio. Questa parte è diversa rispetto all'esecuzione di script U-SQL da Azure.

### <a name="to-run-a-u-sql-script-locally"></a>Eseguire uno script U-SQL localmente
1. Aprire il progetto U-SQL in Visual Studio.   
2. In Esplora soluzioni fare clic con il pulsante destro del mouse su uno script U-SQL e selezionare **Invia script**.
3. Selezionare **(Locale)** come account di Analytics per eseguire lo script in locale.
È anche possibile fare clic sull'account **(Locale)** nella parte superiore della finestra dello script, quindi selezionare **Invia** (o usare la combinazione di tasti CTRL+F5).

    ![Processi di invio dell'esecuzione locale degli strumenti di Data Lake per Visual Studio](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-submit-job.png)



## <a name="use-local-run-from-the-data-lake-u-sql-sdk"></a>Usare l'esecuzione locale dall'SDK U-SQL di Data Lake

Oltre a eseguire gli script U-SQL localmente con Visual Studio, è possibile usare anche l'SDK U-SQL di Azure Data Lake per eseguire gli script U-SQL localmente con le interfacce della riga di comando e di programmazione. In questo modo è possibile scalare il test locale di U-SQL.

### <a name="install-the-sdk"></a>Installare l'SDK

L'SDK U-SQL di Data Lake richiede le dipendenze seguenti:

- [Microsoft .NET Framework 4.6 o versione successiva](https://www.microsoft.com/en-us/download/details.aspx?id=17851).
- Microsoft Visual C++ 14 e Windows SDK 10.0.10240.0 o versione successiva. È possibile procedere in due modi:

    - Installare [Visual Studio Community Edition](https://developer.microsoft.com/downloads/vs-thankyou). Sarà presente la cartella \Windows Kits\10 all'interno della cartella Programmi, ad esempio C:\Programmi (x86)\Windows Kits\10\. Sarà disponibile anche la versione Windows 10 SDK in \Windows Kits\10\Lib. Se queste cartelle non sono presenti, reinstallare Visual Studio e selezionare Windows 10 SDK durante l'installazione. Lo script di compilazione locale di U-SQL eseguirà il rilevamento automatico di queste dipendenze.

    ![Windows 10 SDK ad esecuzione locale degli strumenti di Data Lake per Visual Studio](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-windows-10-sdk.png)

    - Installare [Strumenti Data Lake per Visual Studio](http://aka.ms/adltoolsvs). I file di Windows SDK e Visual C++ preassemblati sono disponibili in C:\Programmi (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\ADL Tools\X.X.XXXX.X\CppSDK. In questo caso il compilazione locale di U-SQL non è in grado di trovare le dipendenze automaticamente. È necessario specificare il relativo percorso CppSDK. È possibile copiare i file in un altro percorso o usarli così come sono. Successivamente sarà possibile scegliere di impostare la variabile di ambiente **SCOPE_CPP_SDK** nella directory o di specificare l'argomento **-CppSDK** con questa directory nella riga di comando dell'applicazione helper eseguita localmente.

Dopo aver installato l'SDK, è necessario attenersi alla procedura di configurazione seguente:

- Impostare la variabile di ambiente **SCOPE_CPP_SDK**.

    Se si ottiene Microsoft Visual C++ e Windows SDK dall'installazione di Strumenti di Data Lake per Visual Studio, verificare di avere la cartella seguente:

        C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK

    Definire una nuova variabile di ambiente denominata **SCOPE_CPP_SDK** che punti a questa directory. In alternativa copiare la cartella in un'altra posizione e specificare **SCOPE_CPP_SDK**.

    Oltre a impostare la variabile di ambiente, è possibile specificare l'argomento **-CppSDK** quando si usa la riga di comando. Questo argomento sovrascrive la variabile di ambiente CppSDK predefinita.

- Impostare la variabile di ambiente **LOCALRUN_DATAROOT**.

    Definire una nuova variabile di ambiente denominata **LOCALRUN_DATAROOT** che punta alla cartella data-root.

    Oltre a impostare la variabile di ambiente, è possibile specificare l'argomento **-DataRoot** con il percorso di data-root quando si usa la riga di comando. Questo argomento sovrascrive la variabile di ambiente data-root predefinita. È necessario aggiungere questo argomento a ogni riga di comando eseguita, così da poter sovrascrivere la variabile di ambiente data-root predefinita per tutte le operazioni.

### <a name="use-the-sdk-from-the-command-line"></a>Usare l'SDK dalla riga di comando

#### <a name="command-line-interface-of-the-helper-application"></a>Interfaccia della riga di comando dell'applicazione helper

Nell'SDK, LocalRunHelper.exe è l'applicazione helper della riga di comando che offre interfacce alla maggior parte delle funzioni di esecuzione locale di uso comune. Entrambe le opzioni di comandi e argomenti fanno distinzione tra maiuscole e minuscole. Per richiamarle:

    LocalRunHelper.exe <command> <Required-Command-Arguments> [Optional-Command-Arguments]

Eseguire LocalRunHelper.exe senza argomenti o con l'interruttore **Guida** per mostrare le informazioni della Guida:

    > LocalRunHelper.exe help

        Command 'help' :  Show usage information
        Command 'compile' :  Compile the script
        Required Arguments :
            -Script param
                    Script File Path
        Optional Arguments :
            -Shallow [default value 'False']
                    Shallow compile

Nelle informazioni di aiuto:

-  **Comando** indica il nome del comando.  
-  **Argomento richiesto** elenca gli argomenti che devono essere forniti.  
-  **Argomento facoltativo** elenca gli argomenti facoltativi con i valori predefiniti.  Gli argomenti Boolean facoltativi non hanno parametri e la loro comparsa ha un significato negativo per il loro valore predefinito.

#### <a name="return-value-and-logging"></a>Valore restituito e registrazione

L'applicazione helper restituisce **0** in caso di successo e **-1** in caso di errore. Per impostazione predefinita, l'helper invia tutti i messaggi alla console corrente. Tuttavia, la maggior parte dei comandi supporta l'argomento facoltativo**-MessageOut path_to_log_file** che reindirizza gli output in un file di log.


### <a name="sdk-usage-samples"></a>Esempi d'uso dell'SDK

#### <a name="compile-and-run"></a>Compila ed esegui

Il comando **run** viene usato per compilare lo script ed eseguire i risultati compilati. Gli argomenti della riga di comando sono una combinazione di questi da **compile** e **run**.

    LocalRunHelper run -Script path_to_usql_script.usql [optional_arguments]

Ad esempio:

    LocalRunHelper run -Script d:\test\test1.usql -WorkDir d:\test\bin -CodeBehind -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB –Parallel 5 -Verbose

Oltre a combinare insieme **compile** e **run**, è possibile compilare ed eseguire separatamente i file eseguibili compilati.

#### <a name="compile-a-u-sql-script"></a>Compilare uno script U-SQL

Il comando **compile** viene usato per compilare uno script di U-SQL in file eseguibili.

    LocalRunHelper compile -Script path_to_usql_script.usql [optional_arguments]

Di seguito sono indicati gli argomenti facoltativi per la compilazione:

|Argomento|Descrizione|
|--------|-----------|
|-CppSDK [valore predefinito '']|Directory CppSDK.|
|-DataRoot [valore predefinito '']|Radice dei dati per i dati e i metadati. Il valore predefinito è la variabile di ambiente **LOCALRUN_DATAROOT**.|
|-MessageOut [valore predefinito '']|Esecuzione del dump dei messaggi della console in un file.|
|-Shallow [valore predefinito 'False']|Compilazione superficiale. Esegue solo una verifica della sintassi dello script, restituendo un valore.|
|-WorkDir [valore predefinito 'D:\localrun\t\ScopeWorkDir']|Directory per l'uso del compilatore e l'output. Per altre informazioni, vedere "Directory di lavoro" nell'appendice.|

Argomenti facoltativi per assembly e code-behind sono riportati di seguito:

|Argomento|Descrizione|
|--------|-----------|
|-CodeBehind [valore predefinito 'False']|Indica che lo script ha un code-behind con estensione .cs che verrà compilato e registrato automaticamente come UDO (oggetto definito dall'utente)|
|-References [valore predefinito '']|Elenco di percorsi ad assembly di riferimento aggiuntivi o a file di dati code-behind separati da ";"|
|-UseDatabase [valore predefinito 'master']|Database da usare per la registrazione di assembly temporanei code-behind|
|-UdoRedirect [valore predefinito 'False']|Configurazione di reindirizzamento dell'assembly UDO che comunica al runtime .NET di testare prima di tutto gli assembly dipendenti dalla directory di output compilata quando viene chiamato l'UDO|

Di seguito sono riportati alcuni esempi d'uso.

Compilare uno script U-SQL:

    LocalRunHelper compile -Script d:\test\test1.usql

Compilare uno script U-SQL e impostare la cartella data-root. Si noti la variabile di ambiente impostata verrà sovrascritta.

    LocalRunHelper compile -Script d:\test\test1.usql –DataRoot c:\DataRoot

Compilare uno script U-SQL e impostare la directory di lavoro, un assembly di riferimento e un database:

    LocalRunHelper compile -Script d:\test\test1.usql -WorkDir d:\test\bin -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB

#### <a name="execute-compiled-results"></a>Eseguire i risultati compilati

Il comando **execute** viene usato per eseguire i risultati compilati.   

    LocalRunHelper execute -Algebra path_to_compiled_algebra_file [optional_arguments]

Di seguito sono indicati gli argomenti facoltativi:

|Argomento|Descrizione|
|--------|-----------|
|-DataRoot [valore predefinito '']|Radice dei dati per l'esecuzione dei metadati. Il valore predefinito è la variabile di ambiente **LOCALRUN_DATAROOT**.|
|-MessageOut [valore predefinito '']|Esecuzione del dump dei messaggi della console in un file.|
|-Parallel [valore predefinito '1']|Indica di eseguire i passaggi di esecuzione locale generati con il livello di parallelismo specificato.|
|-Verbose [valore predefinito 'False']|Indica di visualizzare output dettagliati dal runtime.|

Ecco un esempio d'uso:

    LocalRunHelper execute -Algebra d:\test\workdir\C6A101DDCB470506\Script_66AE4909AA0ED06C\__script__.abr –DataRoot c:\DataRoot –Parallel 5

## <a name="use-the-sdk-with-programming-interfaces"></a>Uso dell'SDK con interfacce di programmazione

Le interfacce di programmazione si trovano tutte nell'assembly Microsoft.Analytics.LocalRun. È possibile usarle per integrare le funzionalità dell'SDK U-SQL e il framework di test di C# per scalare il test locale dello script U-SQL. Per altre informazioni sulle interfacce, vedere l'appendice.

## <a name="appendix"></a>Appendice

### <a name="working-directory"></a>Directory di lavoro

Quando si esegue lo script U-SQL localmente, durante la compilazione viene creata una directory di lavoro. Oltre agli output di compilazione, nella directory di lavoro verrà creata una copia shadow dei file di runtime necessari per l'esecuzione locale. Se l'argomento **-WorkDir** non viene fornito nella riga di comando, la directory di lavoro predefinita ScopeWorkDir verrà creata nella directory corrente. I file presenti nella directory di lavoro sono i seguenti:

|Directory/File|Definizione|Descrizione|
|--------------|----------|-----------|
|ScopeWorkDir|Directory di lavoro|Cartella radice|
|C6A101DDCB470506|Stringa di hash della versione di runtime|Copia shadow dei file di runtime necessari per l'esecuzione locale|
|\.\Script_66AE4909AA0ED06C|Nome dello script più stringa hash del percorso dello script|Output di compilazione e registrazione del passaggio di esecuzione|
|\.\.\\_\_script\_\_.abr|Output del compilatore|File algebra|
|\.\.\\_\_ScopeCodeGen\_\_.*|Output del compilatore|Codice gestito generato|
|\.\.\\_\_ScopeCodeGenEngine\_\_.*|Output del compilatore|Codice nativo generato|
|\.\.\referenced_assemblies|Riferimento assembly|File assembly di riferimento|
|\.\.\deployed_resources|Distribuzione risorse|File di distribuzione risorse|
|\.\.\xxxxxxxx.xxx[1..n]_*.*|Log di esecuzione|Log dei passaggi di esecuzione|

### <a name="programming-interfaces-for-the-azure-data-lake-u-sql-sdk"></a>Interfacce di programmazione per l'SDK U-SQL di Azure Data Lake

Le interfacce di programmazione si trovano tutte nell'assembly Microsoft.Analytics.LocalRun.

#### <a name="microsoftanalyticslocalrunconfiguration"></a>Microsoft.Analytics.LocalRun.Configuration
Microsoft.Analytics.LocalRun.Configuration è la classe di parametri di configurazione per la compilazione.

**Costruttore**

public Configuration(string rootPath)

|Parametro|Tipo|Descrizione|
|---------|----|-----------|
|rootPath|System.String|Percorso alla directory corrente del contesto di lavoro. Se il parametro WorkingDirectory non è impostato, la directory di lavoro predefinita sarà rootPath più ScopeWorkDir.|

**Proprietà**

|Nome|Descrizione|
|----|-----------|
|CppSDK|Posizione di CppSDK o la configurazione predefinita del sistema. |
|DataDirectory|Posizione in cui vengono salvati tabelle, assembly e dati di input/output. Il valore predefinito è ScopeWorkDir\DataDir. |
|GenerateUdoRedirect|Indica se si vuole generare una configurazione di override di reindirizzamento per il caricamento dell'assembly.|
|WorkingDirectory|Directory di lavoro del compilatore. Il valore predefinito è ScopeWorkDir.|


#### <a name="microsoftanalyticslocalrunlocalcompiler"></a>Microsoft.Analytics.LocalRun.LocalCompiler
Microsoft.Analytics.LocalRun.LocalCompiler è la classe del compilatore U-SQL locale.

**Costruttore**

public LocalCompiler(Configuration configuration)

|Parametro|Tipo|Descrizione|
|---------|----|-----------|
|configurazione|Microsoft.Analytics.LocalRun.Configuration||

**Metodo**

public bool Compile( string script, string filePath, bool shallow, out CommonCompileResult result )

|Parametro|Tipo|Descrizione|
|---------|----|-----------|
|script|System.String|Stringa dello script di input.|
|filePath|System.String|Percorso del file di script. È impostato su null per usare il valore predefinito.|
|shallow|System.Boolean|Compilazione superficiale (solo verifica della sintassi) o compilazione completa.|
|risultato|Microsoft.Cosmos.ClientTools.Shared.CommonCompileResult|Risultati di compilazione dettagliati.|
|Valore restituito|System.Boolean|True: nessun errore grave nella compilazione. <br>False: errore grave nella compilazione.|

#### <a name="microsoftanalyticslocalrunlocalrunner--idisposable"></a>Microsoft.Analytics.LocalRun.LocalRunner : IDisposable
Microsoft.Analytics.LocalRun.LocalRunner: IDisposable è la classe runner locale di U-SQL.

**Costruttore**

public LocalRunner( string algebraFilePath, string dataRoot, Action<string> postMessage = null )

|Parametro|Tipo|Descrizione|
|---------|----|-----------|
|algebraFilePath|System.String|Percorso al file Algebra|
|dataRoot|System.String|Percorso a DataRoot|
|postMessage (facoltativo)|System.Action<String>|Gestore delle registrazioni per l'avanzamento|

public LocalRunner( string algebraFilePath, string dataRoot, string cachePath, string runtimePath, string tempPath, string logPath, Action<Object, ExecutionStatusBase. ExecutionEventArgs> execEventHandler, Object eventContext, Action<string> postMessage = null )

|Parametro|Tipo|Descrizione|
|---------|----|-----------|
|algebraFilePath|System.String|Percorso del file algebra.|
|dataRoot|System.String|Percorso di DataRoot.|
|cachePath|System.String|Percorso della directory del risultato della compilazione. È impostato su null per usare il valore predefinito, ovvero la posizione del file algebra.|
|runtimePath|System.String|Percorso della directory del runtime con copia shadow. È impostato su null per usare il valore predefinito, ovvero la posizione della directory padre di cachePath.|
|tempPath|System.String|Percorso di archiviazione temporanea, solo per uso interno. È impostato su null.|
|logPath|System.String|Percorso in cui verranno scritti i log di esecuzione. È impostato su null per usare cachePath come valore predefinito.|
|execEventHandler|Sistema. Action<Object, ExecutionStatusBase.ExecutionEventArgs>|Gestore delle notifiche di eventi per il cambiamento dello stato di esecuzione.|
|eventContext|Sistema. Oggetto|Contesto per il gestore eventi.|
|postMessage (facoltativo)|System.Action<String>|Gestore delle registrazioni per l'avanzamento.|

**Proprietà**

|Nome|Descrizione|
|----|-----------|
|AlgebraPath|Percorso del file algebra.|
|CachePath|Percorso della cache dei risultati del compilatore in cui vengono archiviati i file binari generati.|
|CompletedSteps|Numero di passaggi completati.|
|DataRoot|DataRoot per i metadati.|
|LastErrorMessage|(ereditato da ExecutionStatusBase)|
|LogPath|Percorso di archiviazione del file di registrazione. Il metodo di impostazione creerà la directory se non esiste. Il percorso di log creato in precedenza non verrà eliminato.|
|OutputHeader|Intestazione dello schema di dump negli output testuali.|
|Parallelismo|Parallelismo. Il valore predefinito è processori per la logica pari a 1. La modifica di tale impostazione dopo l'avvio darà origine a un'eccezione.|
|Avanzamento|Avanzamento dell'esecuzione dallo 0% al 100%.|
|RuntimePath|Percorso dei file di runtime. Deve essere una directory di livello superiore rispetto a cachePath quando è la copia shadow del compilatore.|
|Stato|Stato di esecuzione. <br><br>enum ExecutionStatusBase.ExecutionStatus <br>{ <br>Inizializzato, // Inizializzato. <br>In esecuzione,     // È in esecuzione.  WaitOne controlla solo l'evento in questo stato. <br>Operazione eseguita,     // Operazione completata correttamente. <br>Errore,       // Operazione non riuscita. <br>}|
|TotalSteps|Numero totale di passaggi da eseguire. Un valore valido è disponibile solo dopo la creazione del DAG vertice.|
|Dettagliato|Informazioni dettagliate durante l'esecuzione.|

**Metodo**

|Metodo|Descrizione|
|------|-----------|
|Cancel()|Annulla l'algebra in esecuzione. <br><br>Il tipo di valore restituito è Boolean. <br><br>Valore restituito false: impossibile annullare a causa di un errore; controllare LastErrorMessage per i dettagli.|
|Start()|Avviare per eseguire l'algebra. <br><br>Il tipo di valore restituito è Boolean. <br><br>Valore restituito false: impossibile eseguire l'avvio a causa di un errore; controllare LastErrorMessage per i dettagli.|
|WaitOne() <br>WaitOne(Int32) <br>WaitOne(TimeSpan) <br>WaitOne(Int32, Boolean) <br>WaitOne(TimeSpan, Boolean)|Attesa del completamento. Fare riferimento a WaitHandle.WaitOne.|
|Dispose()||


## <a name="next-steps"></a>Passaggi successivi

* Per una panoramica su Data Lake Analytics, vedere [Panoramica di Azure Data Lake Analytics](data-lake-analytics-overview.md).
* Per iniziare a sviluppare applicazioni U-SQL, vedere [Sviluppare script U-SQL tramite Strumenti di Data Lake per Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* Per informazioni su U-SQL, vedere [Introduzione al linguaggio U-SQL di Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
* Per informazioni sulle attività di gestione, vedere [Gestire Azure Data Lake Analytics tramite il portale di Azure](data-lake-analytics-manage-use-portal.md).
* Per registrare informazioni di diagnostica, vedere [Accesso ai log di diagnostica per Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md).
* Per visualizzare una query più complessa, vedere [Analizzare i log dei siti Web con Analisi Azure Data Lake](data-lake-analytics-analyze-weblogs.md).
* Per visualizzare i dettagli del processo, vedere [Usare Job Browser e Job View (Visualizzazione processo) per i processi di Azure Data Lake Analytics](data-lake-analytics-data-lake-tools-view-jobs.md).
* Per usare la visualizzazione esecuzioni vertici, vedere [Usare la visualizzazione esecuzioni vertici in Azure Data Lake Tools per Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).



<!--HONumber=Dec16_HO1-->


