---
title: Eseguire test e debug di processi U-SQL tramite l&quot;esecuzione locale e l&quot;SDK U-SQL di Azure Data Lake | Microsoft Docs
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
ms.sourcegitcommit: 9d088a3afd8830c80462099cc869f1493d09fb41
ms.openlocfilehash: e0385315434ac7a10e121f88469aaaec45160b86


---
# <a name="test-and-debug-u-sql-jobs-using-local-run-and-the-azure-data-lake-u-sql-sdk"></a>Eseguire test e debug di processi U-SQL tramite l'esecuzione locale e l'SDK U-SQL di Azure Data Lake

Informazioni su come usare gli strumenti di Azure Data Lake per Visual Studio e l'SDK U-SQL di Azure Data Lake per eseguire test e debug dei processi di U-SQL nella workstation locale.  Queste due funzionalità di esecuzione locale consentono di eseguire i processi di U-SQL nella workstation così come lo si fa nel servizio di Azure Data Lake. Queste funzionalità permettono di risparmiare tempo per le operazioni di test e debug dei processi di U-SQL.

Prerequisiti: 

- Un account di Azure Data Lake Analytics. Vedere [Introduzione ad Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md). 
- Gli strumenti di Azure Data Lake per Visual Studio.  Vedere [Sviluppare script U-SQL con gli strumenti di Data Lake per Visual Studio](data-lake-analytics-data-lake-tools-get-started.md). 
- L'esperienza di sviluppo degli script U-SQL. Vedere [Introduzione ad Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md). 


## <a name="understand-data-root-and-file-path"></a>Comprendere il percorso di file e data-root

Sia l'SDK di U-SQL che l'esecuzione locale richiedono una cartella data-root. Data-root è un "archivio locale" per l'account del computer locale, equivalente all'account di Data Lake Store di un account Data Lake Analytics in Azure. Il passaggio a un'altra cartella data-root è identico al passaggio a un account archivio differente. Se si vuole accedere ai dati condivisi comunemente con cartelle data-root differenti, è necessario usare i percorsi assoluti negli script oppure sidevono creare collegamenti simbolici del file system (ad esempio, mklink su NTFS) in questa cartella data-root che portano ai dati condivisi. 

La cartella data-root viene usata per:

- Archiviare i metadati inclusi database, tabelle, funzione con valori di tabella, assembly e così via.
- Cercare i percorsi di input e output che sono definiti come percorsi relativi in U-SQL. L'uso di percorsi relativi semplifica la distribuzione dei progetti di U-SQL in Azure.

È possibile usare sia i percorsi relativi che quelli assoluti locali negli script di U-SQL e il percorso relativo lo è in relazione al percorso della cartella data-root specificata. È consigliabile usare "/" come separatore di percorso per rendere gli script compatibili con il lato server. Di seguito sono riportati alcuni esempi di percorsi relativi e dei loro percorsi assoluti equivalenti. In questi esempi, la cartella data-root è "C:\LocalRunDataRoot":

|Percorso relativo|Percorso assoluto|
|-------------|-------------|
|/abc/def/input.csv |C:\LocalRunDataRoot\abc\def\input.csv|
|abc/def/input.csv  |C:\LocalRunDataRoot\abc\def\input.csv|
|D:/abc/def/input.csv |D:\abc\def\input.csv|

## <a name="use-local-run-from-visual-studio"></a>Usare l'esecuzione locale per Visual Studio

Gli strumenti di Data Lake per Visual Studio offrono un'esperienza di esecuzione locale di U-SQL in Visual Studio. Questa funzionalità permette di:

- Eseguire script U-SQL in locale, insieme agli assembly C#.
- Eseguire il debug di assembly C# localmente.
- Creare, visualizzare ed eliminare i cataloghi di U-SQL (database locali, assembly, schemi e tabelle) da Esplora server. Il catalogo locale è disponibile anche da Esplora server.

    ![Catalogo locale dell'esecuzione locale degli strumenti di Data Lake per Visual Studio](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-local-catalog.png)

Il programma di installazione degli strumenti di Data Lake crea la cartella "C:\LocalRunRoot" che diventa la cartella data-root predefinita. Il parallelismo di esecuzione locale predefinito è 1. 

### <a name="to-configure-local-run-in-visual-studio"></a>Configurare l'esecuzione locale in Visual Studio

1. Aprire Visual Studio.
2. Aprire **Esplora server**.
3. Espandere **Azure**, **Data Lake Analytics**.
4. Aprire il menu **Data Lake** e fare clic su **Opzioni e impostazioni**. 
5. Nella struttura ad albero a sinistra, espandere **Azure Data Lake** e **Generale**.

    ![Impostazioni di configurazione dell'esecuzione locale degli strumenti di Data Lake per Visual Studio](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-configure.png)

Per poter eseguire la funzionalità di esecuzione locale è richiesto un progetto U-SQL di Visual Studio. Questa parte è diversa rispetto all'esecuzione di script U-SQL da Azure.

### <a name="to-run-a-u-sql-script-locally"></a>Eseguire uno script U-SQL localmente
1. Aprire il progetto U-SQL in Visual Studio.   
2. In Esplora soluzioni fare clic con il pulsante destro del mouse su uno script U-SQL e selezionare **Invia script**. Selezionare (locale) come account di Analytics per eseguire lo script in locale.
3. È anche possibile fare clic su account (locale) nella parte superiore della finestra dello script, quindi selezionare **Invia** (o usare la combinazione di tasti **CTRL+F5**).

    ![Processi di invio dell'esecuzione locale degli strumenti di Data Lake per Visual Studio](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-submit-job.png)



## <a name="use-local-run-from-data-lake-u-sql-sdk"></a>Usare l'esecuzione locale dall'SDK U-SQL di Data Lake
 
Oltre a eseguire gli script U-SQL localmente con Visual Studio, è possibile usare anche l'SDK U-SQL di Azure Data Lake per eseguire gli script U-SQL localmente con le interfacce della riga di comando e di programmazione, che consentono di scalare il test locale di U-SQL.

### <a name="install-the-sdk"></a>Installare l'SDK

L'SDK U-SQL di Data Lake richiede le dipendenze seguenti:

- [Microsoft .NET Framework 4.6 o versione successiva](https://www.microsoft.com/en-us/download/details.aspx?id=17851).
- Microsoft Visual C++ 14 e Windows SDK 10.0.10240.0 o versione successiva. Per ottenerle è possibile ricorrere a uno dei due metodi disponibili:

    - Installare Visual Studio ([Visual Studio Community Edition](https://developer.microsoft.com/downloads/vs-thankyou)). È necessario che ci sia una cartella "\Windows Kits\10" all'interno della cartella Programmi, ad esempio, "C:\Programmi (x86)\Windows Kits\10\"; è necessario anche trovare la versione di Windows 10 SDK in "\Windows Kits\10\Lib". Se queste cartelle non sono presenti, reinstallare Visual Studio e assicurarsi di aver selezionato Windows 10 SDK durante l'installazione. Grazie a questo metodo, lo script di compilazione locale di U-SQL eseguirà il rilevamento automatico di queste dipendenze.

    ![Windows 10 SDK ad esecuzione locale degli strumenti di Data Lake per Visual Studio](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-windows-10-sdk.png)
 
    - In alternativa, Installare gli [strumenti di Data Lake per Visual Studio](http://aka.ms/adltoolsvs). I file di Windows SDK e VC ++ preassemblati sono disponibili in C:\Programmi (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\ADL Tools\X.X.XXXX.X\CppSDK. In questo caso, il compilatore locale di U-SQL non è in grado di trovare automaticamente le dipendenze ed è necessario specificare il percorso CppSDK per esso. È possibile copiare i file in un altro percorso o usarli così come sono. Successivamente, è possibile scegliere di impostare una variabile di ambiente "SCOPE_CPP_SDK" nella directory, o di specificare l'argomento "-CppSDK" con questa directory nella riga di comando dell'applicazione helper di esecuzione locale. 

Dopo aver installato l'SDK, è necessario attenersi alla procedura di configurazione seguente:

- Impostare la variabile di ambiente **SCOPE_CPP_SDK**

    Se si ottiene Microsoft Visual C++ e Windows SDK dall'installazione degli strumenti di Data Lake per Visual Studio, verificare di avere la cartella seguente:

        C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK

    Definire una nuova variabile di ambiente denominata "SCOPE_CPP_SDK" per rimandare a questa directory. In alternativa, copiare la cartella in un'altra posizione e specificare "SCOPE_CPP_SDK" come tale.

    Oltre a impostare la variabile di ambiente, è anche possibile specificare l'argomento "-CppSDK" quando si usa la riga di comando. Questo argomento sovrascrive la variabile di ambiente CppSDK predefinita. 

- Impostare la variabile di ambiente **LOCALRUN_DATAROOT**

    Definire una nuova variabile di ambiente denominata "LOCALRUN_DATAROOT" che rimanda alla cartella data-root. 

    Oltre a impostare la variabile di ambiente, è anche possibile specificare l'argomento "-DataRoot" con il percorso data-root quando si usa la riga di comando. Questo argomento sovrascrive la variabile di ambiente data-root predefinita. Ed è necessario aggiungere questo argomento a ogni riga di comando eseguita, così da poter usare la stessa nuova data-root di sovrascrittura per tutte le operazioni. 

### <a name="using-the-sdk-from-command-line"></a>Uso dell'SDK dalla riga di comando

#### <a name="the-command-line-interface-of-the-helper-application"></a>Interfaccia della riga di comando dell'applicazione helper

LocalRunHelper.exe dell'SDK è l'applicazione helper della riga di comando che offre interfacce alla maggior parte delle funzioni di esecuzione locale di uso comune. Tenere presente che entrambe le opzioni di comandi e argomenti fanno distinzione tra maiuscole e minuscole. Per richiamarle:

    LocalRunHelper.exe <command> <Required-Command-Arguments> [Optional-Command-Arguments]

Eseguire "LocalRunHelper.exe" senza argomenti o con l'interruttore **Guida** per mostrare le informazioni della Guida:

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
-  **Argomento facoltativo** elenca gli argomenti facoltativi con i valori predefiniti.  Gli argomenti bool facoltativi non hanno parametri e la loro comparsa ha un significato negativo per il loro valore predefinito.

#### <a name="return-value-and-logging"></a>Valore restituito e registrazione

l'applicazione helper restituisce il valore **0** nel caso di esito positivo e **-1** in caso di errore. Per impostazione predefinita, l'helper invierà tutti i messaggi alla console corrente.  Tuttavia, la maggior parte dei comandi supporta l'argomento facoltativo**-MessageOut path_to_log_file** che reindirizzerà gli output in un file di log.


### <a name="the-sdk-usage-samples"></a>Esempi d'uso dell'SDK

#### <a name="compile-and-run"></a>Compila ed esegui

Il comando "esegui" viene usato per compilare lo script ed eseguire i risultati compilati. Gli argomenti della riga di comando sono combinazioni di questi da "compila" ed "esegui".

    LocalRunHelper run -Script path_to_usql_script.usql [optional_arguments]

Di seguito è fornito un esempio:

    LocalRunHelper run -Script d:\test\test1.usql -WorkDir d:\test\bin -CodeBehind -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB –Parallel 5 -Verbose

Oltre a combinare insieme "compila" ed "esegui", è possibile compilare ed eseguire separatamente i file eseguibili compilati. 

#### <a name="compile-u-sql-script"></a>Compila script di U-SQL

Il comando "compila" viene usato per compilare uno script di U-SQL sui file eseguibili. 

    LocalRunHelper compile -Script path_to_usql_script.usql [optional_arguments]

Argomenti facoltativi per la compilazione, è possibile trovare altre informazioni sulla directory di lavoro (-WorkDir) nell'appendice.

|Argomento|Descrizione|
|--------|-----------|
|-CppSDK [valore predefinito '']|Directory CppSDK|
|-DataRoot [valore predefinito '']|DataRoot di dati e metadati, impostazione predefinita su variabile di ambiente 'LOCALRUN_DATAROOT'|
|-MessageOut [valore predefinito '']|Messaggi dump sulla console a un file|
|-Shallow [valore predefinito 'False']|Compilazione superficiale; esegue solo una verifica della sintassi dello script, restituendo un valore.|
|-WorkDir [valore predefinito 'D:\localrun\t\ScopeWorkDir']|Directory per l'uso e gli output del compilatore e output; altre informazioni disponibili nell'Appendice: Directory di lavoro.|

Argomenti facoltativi per assembly e code-behind:

|Argomento|Descrizione|
|--------|-----------|
|-CodeBehind [valore predefinito 'False']|Lo script ha un code-behind con estensione .cs che verrà compilato e registrato automaticamente come oggetto UDO|
|-References [valore predefinito '']|Elenco di percorsi agli assembly di riferimento aggiuntivi o a file di dati code-behind, separati da ";"|
|-UseDatabase [valore predefinito 'master']|Database da usare per la registrazione di assembly temporanei code-behind|
|-UdoRedirect [valore predefinito 'False']|Generare la configurazione di reindirizzamento dell'assembly UDO che comunica al runtime .Net di testare prima gli assembly dipendenti dalla directory di output compilata quando viene chiamato l'UDO|

Di seguito sono riportati alcuni esempi d'uso:

Compilare lo script di U-SQL:

    LocalRunHelper compile -Script d:\test\test1.usql

Compilare lo script di U-SQL e impostare la cartella data-root (tale operazione comporterà la sovrascrittura della variabile di ambiente impostata).

    LocalRunHelper compile -Script d:\test\test1.usql –DataRoot c:\DataRoot

Compilare lo script U-SQL e impostare la directory di lavoro, l'assembly di riferimento e il database.

    LocalRunHelper compile -Script d:\test\test1.usql -WorkDir d:\test\bin -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB

#### <a name="execute-compiled-result"></a>Eseguire il risultato compilato

Il comando "Esegui" viene usato per eseguire i risultati compilati.   

    LocalRunHelper execute -Algebra path_to_compiled_algebra_file [optional_arguments]

Argomenti facoltativi:

|Argomento|Descrizione|
|--------|-----------|
|-DataRoot [valore predefinito '']|DataRoot per l'esecuzione di metadati, impostazione predefinita su variabile di ambiente 'LOCALRUN_DATAROOT'|
|-MessageOut [valore predefinito '']|Messaggi dump sulla console a un file|
|-Parallel [valore predefinito '1']|Eseguire i passaggi di esecuzione locale generati con il livello di parallelismo specificato|
|-Verbose [valore predefinito 'False']|Mostrare gli output dettagliati dal runtime|

Ecco un esempio d'uso:

    LocalRunHelper execute -Algebra d:\test\workdir\C6A101DDCB470506\Script_66AE4909AA0ED06C\__script__.abr –DataRoot c:\DataRoot –Parallel 5

## <a name="using-the-sdk-with-programming-interface"></a>Uso dell'SDK con l'interfaccia di programmazione

L'interfaccia di programmazione si trova nell'assembly "Microsoft.Analytics.LocalRun", tramite cui è possibile integrare la funzionalità dell'SDK di U-SQL e il framework di test C# per scalare il test locale dello script di U-SQL (presto sarà disponibile un nuovo documento al riguardo). Per altre informazioni sulle interfacce, vedere l'appendice.

## <a name="appendix"></a>Appendice

### <a name="working-directory"></a>Directory di lavoro

Quando si avvia l'esecuzione locale dello script di U-SQL, durante la compilazione viene creata una directory di lavoro.  Oltre gli output di compilazione, nella directory di lavoro verrà creata una copia shadow dei file di runtime necessari per l'esecuzione locale.   Se l'argomento **-WorkDir** non viene concesso nella riga di comando, nella directory corrente verrà creata la directory di lavoro predefinita "ScopeWorkDir". I file presenti nella directory di lavoro vengono mostrati come indicato di seguito.

|Directory/File|Definizione|Descrizione|
|--------------|----------|-----------|
|ScopeWorkDir|La directory di lavoro|cartella radice|
|C6A101DDCB470506|Stringa di hash della versione di runtime|Copia shadow dei file di runtime necessari per l'esecuzione locale|
|\.\Script_66AE4909AA0ED06C|Nome di script + stringa hash del percorso dello script|Output di compilazione e registrazione del passaggio di esecuzione|
|\.\.\\_\_script\_\_.abr|Output del compilatore|File Algebra|
|\.\.\\_\_ScopeCodeGen\_\_.*|Output del compilatore|Codice gestito generato|
|\.\.\\_\_ScopeCodeGenEngine\_\_.*|Output del compilatore|Codice nativo generato|
|\.\.\referenced_assemblies|Riferimento ad assembly|File di ASSEMBLY DI RIFERIMENTO|
|\.\.\deployed_resources|Distribuzione risorse|File di DISTRIBUZIONE RISORSE|
|\.\.\xxxxxxxx.xxx[1..n]_*.*|Log di esecuzione|Log dei passaggi di esecuzione|

### <a name="programming-interfaces-for-azure-data-lake-u-sql-sdk"></a>Interfacce di programmazione per l'SDK U-SQL di Azure Data Lake

Le interfacce di programmazione si trovano nell'assembly "Microsoft.Analytics.LocalRun".

#### <a name="microsoftanalyticslocalrunconfiguration"></a>Microsoft.Analytics.LocalRun.Configuration
Classe di parametri della configurazione di compilazione

**Costruttore**

public Configuration(string rootPath)

|Parametro|Tipo|Descrizione|
|---------|----|-----------|
|rootPath|System.String|Percorso alla directory corrente del contesto di lavoro. Se il parametro WorkingDirectory non è impostato, la directory di lavoro predefinita sarà rootPath + "ScopeWorkDir".|

**Proprietà**

|Nome|Descrizione|
|----|-----------|
|CppSDK|Percorso in cui trovare l'SDK Cpp; per impostazione predefinita viene usata la configurazione predefinita di sistema |
|DataDirectory|Percorso in cui vengono assembly, tabelle e dati di input e outpu; per impostazione predefinita è ScopeWorkDir\DataDir |
|GenerateUdoRedirect|Se si vuole generare il reindirizzamento di caricamento dell'assembly eseguire l'override della configurazione|
|WorkingDirectory|Directory di lavoro del compilatore; per impostazione predefinita è ScopeWorkDir (se non impostata)|


#### <a name="microsoftanalyticslocalrunlocalcompiler"></a>Microsoft.Analytics.LocalRun.LocalCompiler
La classe del compilatore locale di U-SQL

**Costruttore**

public LocalCompiler(Configuration configuration)

|Parametro|Tipo|Descrizione|
|---------|----|-----------|
|configurazione|Microsoft.Analytics.LocalRun.Configuration||

**Metodo**

public bool Compile( string script, string filePath, bool shallow, out CommonCompileResult result )

|Parametro|Tipo|Descrizione|
|---------|----|-----------|
|script|System.String|stringa dello script di input|
|filePath|System.String|percorso del file di script; impostato su null per usare l'impostazione predefinita|
|shallow|System.Boolean|compilazione superficiale (solo verifica sintassi) o compilazione completa|
|risultato|Microsoft.Cosmos.ClientTools.Shared.CommonCompileResult|Risultati di compilazione dettagliati|
|Valore restituito|System.Boolean|true: nessun errore grave nella compilazione. <br>false: errore grave nella compilazione.|

#### <a name="microsoftanalyticslocalrunlocalrunner--idisposable"></a>Microsoft.Analytics.LocalRun.LocalRunner : IDisposable
La classe del runner locale di U-SQL

**Costruttore**

public LocalRunner( string algebraFilePath, string dataRoot, Action<string> postMessage = null )

|Parametro|Tipo|Descrizione|
|---------|----|-----------|
|algebraFilePath|System.String|Percorso al file Algebra|
|dataRoot|System.String|Percorso a DataRoot|
|postMessage (facoltativo)|System.Action<String>|gestore delle registrazioni per l'avanzamento|

public LocalRunner( string algebraFilePath, string dataRoot, string cachePath, string runtimePath, string tempPath, string logPath, Action<Object, ExecutionStatusBase. ExecutionEventArgs> execEventHandler, Object eventContext, Action<string> postMessage = null )

|Parametro|Tipo|Descrizione|
|---------|----|-----------|
|algebraFilePath|System.String|Percorso al file Algebra|
|dataRoot|System.String|Percorso a DataRoot|
|cachePath|System.String|Percorso alla directory dei risultati di compilazione; impostato su null per usare il percorso predefinito in cui è presente il file Algebra|
|runtimePath|System.String|Percorso alla directory della copia shadow del runtime; impostato su null per usare il percorso predefinito in cui è presente la directory padre di cachePath|
|tempPath|System.String|Percorso di archiviazione temporanea, solo per uso interno, impostato su null|
|logPath|System.String|Percorso in cui verranno scritti i log di esecuzione; impostato su null per usare il percorso predefinito come cachePath|
|execEventHandler|Sistema. Action<Object, ExecutionStatusBase.ExecutionEventArgs>|gestore delle notifiche di eventi di modifica dello stato di esecuzione|
|eventContext|Sistema. Oggetto|contesto per il gestore eventi|
|postMessage (facoltativo)|System.Action<String>|gestore delle registrazioni per l'avanzamento|

**Proprietà**

|Nome|Descrizione|
|----|-----------|
|AlgebraPath|Percorso al file Algebra|
|CachePath|Il percorso cache per i risultati del compilatore in cui vengono archiviati i file binari generati|
|CompletedSteps|Numero di passaggi completati|
|DataRoot|DataRoot per metadati|
|LastErrorMessage|(ereditato da ExecutionStatusBase)|
|LogPath|La registrazione del setter del percorso di archiviazione file creerà la directory se il percorso di log immanente creato in precedenza non verrà eliminato|
|OutputHeader|Intestazione dello schema Dump negli output testuali|
|Parallelismo|Parallelismo, per impostazione predefinita configurato sui processori per la logica - 1 La modifica di tale impostazione dopo l'avvio darà origine a un'eccezione|
|Avanzamento|Avanzamento dell'esecuzione dallo 0% al 100%|
|RuntimePath|Percorso di archiviazione dei file di runtime; deve essere una directory di livello superiore rispetto a cachePath quando è la copia shadow presso il compilatore|
|Stato|Stato di esecuzione <br>enum ExecutionStatusBase.ExecutionStatus <br>{ <br>Inizializzato, / / inizializza <br>In esecuzione, // è in esecuzione, WaitOne controlla solo l'evento in questo stato <br>Operazione eseguita, // operazione completata correttamente <br>Errore, / / operazione non riuscita <br>}|
|TotalSteps|Numero totale di passaggi da eseguire; valore valido disponibile solo dopo la creazione del vertice DAG|
|Dettagliato|Informazioni dettagliate durante l'esecuzione|

**Metodo**

|Metodo|Descrizione|
|------|-----------|
|Cancel()|Annulla l'algebra in esecuzione <br>Valore restituito <br>Tipo: booleano <br>false: impossibile annullare a causa di un errore; controllare LastErrorMessage per i dettagli|
|Start()|Avviare per eseguire l'algebra <br>Valore restituito <br>Tipo: booleano <br>false: impossibile avviare a causa di un errore; controllare LastErrorMessage per i dettagli|
|WaitOne() <br>WaitOne(Int32) <br>WaitOne(TimeSpan) <br>WaitOne(Int32, Boolean) <br>WaitOne(TimeSpan, Boolean)|Attendere il completamento. Fare riferimento a WaitHandle.WaitOne|
|Dispose()||


## <a name="see-also"></a>Vedere anche

* Per una panoramica su Analisi Data Lake, vedere [Panoramica di Analisi Data Lake di Azure](data-lake-analytics-overview.md).
* Per iniziare a sviluppare applicazioni U-SQL, vedere [Sviluppare script U-SQL tramite Strumenti di Data Lake per Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* Per informazioni su U-SQL, vedere [Introduzione al linguaggio U-SQL di Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
* Per informazioni sulle attività di gestione, vedere [Gestire Azure Data Lake Analytics tramite il portale di Azure](data-lake-analytics-manage-use-portal.md).
* Per registrare informazioni di diagnostica, vedere [Accessing diagnostics logs for Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md)
* Per visualizzare una query più complessa, vedere [Analizzare i log del sito Web mediante Analisi Data Lake di Azure](data-lake-analytics-analyze-weblogs.md).
* Per visualizzare i dettagli del processo, vedere [Usare Job Browser e Job View (Visualizzazione processo) per i processi di Azure Data Lake Analytics](data-lake-analytics-data-lake-tools-view-jobs.md)
* Per usare la visualizzazione esecuzioni vertici, vedere [Usare la visualizzazione esecuzioni vertici in Azure Data Lake Tools per Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)


<!--HONumber=Nov16_HO4-->


