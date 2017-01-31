---
title: Modelli di Visual Studio per Azure Batch | Microsoft Docs
description: Informazioni su come questi modelli di progetto di Visual Studio consentono di implementare ed eseguire i carichi di lavoro a elevato utilizzo di calcolo in Azure Batch.
services: batch
documentationcenter: .net
author: fayora
manager: timlt
editor: 
ms.assetid: 5e041ae2-25af-4882-a79e-3aa63c4bfb20
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 01/05/2017
ms.author: tamram
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 84e7e6ddf03172e646c55e7e3bf45a32feb15b06


---
# <a name="visual-studio-project-templates-for-azure-batch"></a>Modelli di progetto di Visual Studio per Azure Batch
I modelli di Visual Studio **Gestore di processi** e **Task Processor** (Elaboratore di attività) per Batch forniscono il codice per implementare ed eseguire i carichi di lavoro a elevato utilizzo di calcolo in Batch con il minimo sforzo. Questo documento illustra tali modelli e fornisce le linee guida per usarli.

> [!IMPORTANT]
> Questo articolo illustra solo le informazioni applicabili a questi due modelli e presuppone che si abbia familiarità con il servizio Batch e con i concetti chiave correlati: pool, nodi di calcolo, processi e attività, attività del gestore di processi, variabili di ambiente e altre informazioni pertinenti. È possibile trovare altre informazioni in [Nozioni di base su Azure Batch](batch-technical-overview.md), [Panoramica delle funzionalità di Batch per sviluppatori](batch-api-basics.md) e [Introduzione alla libreria di Azure Batch per .NET](batch-dotnet-get-started.md).
> 
> 

## <a name="high-level-overview"></a>Panoramica generale
I modelli Job Manager (Gestore di processi) e Task Processor (Elaboratore di attività) possono essere usati per creare due utili componenti:

* Un'attività del gestore di processi che implementa un componente di suddivisione dei processi per suddividere un processo in più attività eseguibili in modo indipendente e parallelo.
* Un elaboratore di attività che può essere usato per eseguire la pre-elaborazione e la post-elaborazione in una riga di comando dell'applicazione.

Ad esempio, in uno scenario di rendering di un filmato, il componente di suddivisione dei processi convertirà un unico processo in centinaia o migliaia di attività distinte che elaboreranno i singoli fotogrammi separatamente. Di conseguenza, l'elaboratore di attività richiamerà l'applicazione di rendering e tutti i processi dipendenti necessari per eseguire il rendering di ogni fotogramma, oltre che per eseguire eventuali azioni aggiuntive, ad esempio la copia del fotogramma sottoposto a rendering in una posizione di archiviazione.

> [!NOTE]
> I modelli Job Manager (Gestore di processi) e Task Processor (Elaboratore di attività) sono indipendenti tra loro e quindi è possibile scegliere di usarli entrambi o solo uno, in base ai requisiti del processo di calcolo e alle preferenze.
> 
> 

Come illustrato nel diagramma seguente, un processo di calcolo che usa questi modelli prevede tre fasi:

1. Un codice client (ad esempio, un'applicazione, un servizio Web e così via) invia un processo al servizio Batch in Azure, specificando come attività del gestore di processi il programma del gestore di processi.
2. Il servizio Batch esegue l'attività del gestore di processi in un nodo di calcolo e il componente di suddivisione dei processi avvia il numero specificato di attività dell'elaboratore di attività, in tutti i nodi di calcolo necessari, in base ai parametri e alle specifiche del codice del componente di suddivisione dei processi.
3. Le attività dell'elaboratore di attività vengono eseguite in modo indipendente, in parallelo, per elaborare i dati di input e generare i dati di output.

![Diagramma che illustra come il codice client interagisce con il servizio Batch][diagram01]

## <a name="prerequisites"></a>Prerequisiti
Per usare i modelli di Batch, sarà necessario quanto segue:

* Un computer con Visual Studio 2015 o versione successiva già installato.
* I modelli di Batch, disponibili in [Visual Studio Gallery][vs_gallery] come estensioni di Visual Studio. È possibile ottenere i modelli in due modi:
  
  * Installare i modelli usando la finestra di dialogo **Estensioni e aggiornamenti** in Visual Studio. Per altre informazioni, vedere [Ricerca e uso delle estensioni di Visual Studio][vs_find_use_ext]. Nella finestra di dialogo **Estensioni e aggiornamenti** cercare e scaricare le due estensioni seguenti:
    
    * Azure Batch Job Manager with Job Splitter (Gestore di processi di Azure Batch con componente di suddivisione dei processi)
    * Azure Batch Task Processor (Elaboratore di attività di Azure Batch)
  * Scaricare i modelli dalla raccolta online per Visual Studio: [Microsoft Azure Batch Project Templates][vs_gallery_templates] (Modelli di progetto di Microsoft Azure Batch).
* Se si prevede di usare la funzionalità [Pacchetti dell'applicazione](batch-application-packages.md) per distribuire il gestore di processi e l'elaboratore di attività nei nodi di calcolo di Batch, è necessario collegare un account di archiviazione all'account Batch.

## <a name="preparation"></a>Operazioni preliminari
Si consiglia di creare una soluzione che possa contenere il gestore di processi oltre all'elaboratore di attività, perché può semplificare la condivisione del codice tra i programmi del gestore di processi e dell'elaboratore di attività. Per creare la soluzione, seguire questi passaggi:

1. Aprire Visual Studio 2015 e selezionare **File** > **Nuovo** > **Progetto**.
2. In **Modelli** espandere **Altri tipi di progetti**, fare clic su **Soluzioni di Visual Studio** e selezionare **Soluzione vuota**.
3. Digitare un nome che descriva l'applicazione e lo scopo di questa soluzione, ad esempio "ProgrammiAttivitàBatchLitware".
4. Per creare la nuova soluzione, fare clic su **OK**.

## <a name="job-manager-template"></a>Modello Job Manager (Gestore di processi)
Il modello Job Manager (Gestore di processi) consente di implementare un'attività del gestore di processi che può eseguire queste azioni:

* Dividere un processo in più attività.
* Inviare tali attività per l'esecuzione in Batch.

> [!NOTE]
> Per altre informazioni sulle attività del gestore di processi, vedere [Panoramica delle funzionalità di Batch per sviluppatori](batch-api-basics.md#job-manager-task).
> 
> 

### <a name="create-a-job-manager-using-the-template"></a>Creare un gestore di processi usando il modello
Per aggiungere un gestore di processi alla soluzione creata prima, seguire questi passaggi:

1. Aprire la soluzione esistente in Visual Studio 2015.
2. In Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione e selezionare **Aggiungi** > **Nuovo progetto**.
3. In **Visual C#** fare clic su **Cloud** e su **Azure Batch Job Manager with Job Splitter** (Gestore di processi di Azure Batch con componente di suddivisione dei processi).
4. Digitare un nome che descriva l'applicazione e identifichi questo progetto come gestore di processi, ad esempio "GestoreProcessiLitware".
5. Per creare il progetto, fare clic su **OK**.
6. Compilare infine il progetto per fare in modo che Visual Studio carichi tutti i pacchetti NuGet di riferimento e verificare che il progetto sia valido prima di iniziare a modificarlo.

### <a name="job-manager-template-files-and-their-purpose"></a>File del modello Job Manager (Gestore di processi) e relativo scopo
Quando si crea un progetto usando il modello Job Manager (Gestore di processi), questo genera tre gruppi di file di codice:

* Il file di programma principale (Program.cs), che contiene il punto di ingresso del programma e la gestione delle eccezioni di primo livello. In genere non è necessario modificarlo.
* La directory Framework, che contiene i file responsabili delle operazioni del "boilerplate" eseguite dal programma del gestore di processi, ad esempio decompressione dei parametri, aggiunta di attività al processo batch e così via. In genere non è necessario modificare questi file.
* Il file del componente di suddivisione dei processi (JobSplitter.cs), in cui si inserirà la logica specifica dell'applicazione per suddividere un processo in attività.

Ovviamente è possibile aggiungere altri file, se necessari per supportare il codice del componente di suddivisione dei processi, in base alla complessità della logica di suddivisione dei processi.

Il modello genera anche i file di progetto .NET standard, ad esempio un file CSPROJ, app.config, packages.config e così via.

La parte restante di questa sezione illustra i diversi file e la struttura del codice e spiega la funzione di ogni classe.

![Esplora soluzioni di Visual Studio che illustra la soluzione del modello Job Manager (Gestore di processi)][solution_explorer01]

**File di Framework**

* `Configuration.cs`: incapsula il caricamento dei dati di configurazione del processo, ad esempio i dettagli dell'account Batch, le credenziali dell'account di archiviazione collegato, le informazioni sul processo e sulle attività e i parametri del processo. Fornisce anche l'accesso alle variabili di ambiente definite da Batch (vedere Impostazioni di ambiente per le attività nella documentazione di Batch) tramite la classe Configuration.EnvironmentVariable.
* `IConfiguration.cs`: astrae l'implementazione della classe Configuration, per poter eseguire uno unit test del componente di suddivisione dei processi usando un oggetto di configurazione falso o fittizio.
* `JobManager.cs`: orchestra i componenti del programma del gestore di processi. È responsabile dell'inizializzazione e della chiamata del componente di suddivisione dei processi e dell'invio delle attività restituite dal componente di suddivisione dei processi al mittente delle attività.
* `JobManagerException.cs`: rappresenta un errore che obbliga a terminare il gestore di processi. JobManagerException viene usata per eseguire il wrapping degli errori "previsti" dove possono essere fornite informazioni di diagnostica specifiche nell'ambito della terminazione.
* `TaskSubmitter.cs`: questa classe è responsabile dell'aggiunta delle attività restituite dal componente di suddivisione dei processi al processo batch. La classe JobManager aggrega la sequenza di attività in batch per un'aggiunta efficiente, ma tempestiva, al processo, quindi chiama TaskSubmitter.SubmitTasks in un thread in background per ogni batch.

**Componente di suddivisione dei processi**

`JobSplitter.cs`: questa classe contiene la logica specifica dell'applicazione per la suddivisione del processo in attività. Il framework richiama il metodo JobSplitter.Split per ottenere una sequenza di attività che aggiunge al processo quando il metodo le restituisce. Questa è la classe in cui si inserirà la logica del processo. Implementare il metodo Split per restituire una sequenza di istanze di CloudTask che rappresentano le attività in cui si vuole partizionare il processo.

**File di progetto della riga di comando .NET standard**

* `App.config`: file di configurazione dell'applicazione .NET standard.
* `Packages.config`: file di dipendenza del pacchetto NuGet standard.
* `Program.cs`: contiene il punto di ingresso del programma e la gestione delle eccezioni di primo livello.

### <a name="implementing-the-job-splitter"></a>Implementazione del componente di suddivisione dei processi
Quando si apre il progetto del modello Job Manager (Gestore di processi), il progetto avrà il file JobSplitter.cs aperto per impostazione predefinita. È possibile implementare la logica di suddivisione per le attività del carico di lavoro usando il metodo Split() illustrato sotto:

```csharp
/// <summary>
/// Gets the tasks into which to split the job. This is where you inject
/// your application-specific logic for decomposing the job into tasks.
///
/// The job manager framework invokes the Split method for you; you need
/// only to implement it, not to call it yourself. Typically, your
/// implementation should return tasks lazily, for example using a C#
/// iterator and the "yield return" statement; this allows tasks to be added
/// and to start running while splitting is still in progress.
/// </summary>
/// <returns>The tasks to be added to the job. Tasks are added automatically
/// by the job manager framework as they are returned by this method.</returns>
public IEnumerable<CloudTask> Split()
{
    // Your code for the split logic goes here.
    int startFrame = Convert.ToInt32(_parameters["StartFrame"]);
    int endFrame = Convert.ToInt32(_parameters["EndFrame"]);

    for (int i = startFrame; i <= endFrame; i++)
    {
        yield return new CloudTask("myTask" + i, "cmd /c dir");
    }
}
```

> [!NOTE]
> La sezione con annotazioni nel metodo `Split()` è la sola del codice del modello Job Manager (Gestore di processi) che può essere modificata dall'utente aggiungendo la logica per suddividere i processi in attività diverse. Per modificare un'altra sezione del modello, assicurarsi di avere familiarità con il funzionamento di Batch e provare a usare alcuni [esempi di codice di Batch][github_samples].
> 
> 

L'implementazione di Split() ha accesso a:

* Parametri del processo, tramite il campo `_parameters` .
* Oggetto CloudJob che rappresenta il processo, tramite il campo `_job` .
* Oggetto CloudTask che rappresenta l'attività del gestore di processi, tramite il campo `_jobManagerTask` .

L'implementazione di `Split()` non richiede di aggiungere le attività direttamente al processo. Il codice deve invece restituire una sequenza di oggetti CloudTask che verranno aggiunti automaticamente al processo dalle classi del framework che richiamano il componente di suddivisione dei processi. Di solito si usa la funzionalità dell'iteratore di C# (`yield return`) per implementare i componenti di suddivisione dei processi per poter iniziare a eseguire le attività il prima possibile invece di attendere che tutte le attività vengano calcolate.

**Errore del componente di suddivisione dei processi**

Se il componente di suddivisione dei processi rileva un errore, deve eseguire una di queste due operazioni:

* Terminare la sequenza usando l'istruzione `yield break` C#, nel qual caso il gestore di processi verrà considerato correttamente eseguito.
* Generare un'eccezione, nel qual caso il gestore di processi verrà considerato non correttamente eseguito e si potrà riprovare a eseguirlo a seconda di come è stato configurato dal client.

In entrambi i casi, le attività già restituite dal componente di suddivisione dei processi e aggiunte al processo batch potranno essere eseguite. Per evitare che questo accada, è possibile:

* Terminare il processo prima della restituzione dal componente di suddivisione dei processi
* Fomulare l'intera raccolta di attività prima di restituirla, ovvero restituire `ICollection<CloudTask>` o `IList<CloudTask>` instead of implementing your job splitter using a C# iterato)
* Usare le relazioni tra attività per fare in modo che tutte le attività dipendano dal corretto completamento del gestore di processi

**Tentativi del gestore di processi**

Se il gestore di processi non riesce, è possibile riprovare con il servizio Batch in base alle impostazioni dei tentativi del client. Questo in genere è sicuro perché, quando il framework aggiunge le attività al processo, ignora quelle già esistenti. Se tuttavia il calcolo delle attività è costoso, è possibile che non si voglia sostenere i costi necessari per ricalcolare le attività già aggiunte al processo. Al contrario, se non esiste garanzia che la nuova esecuzione generi gli stessi ID attività, il comportamento che prevede di ignorare i duplicati non verrà applicato. In questi casi è consigliabile progettare il componente di suddivisione dei processi per rilevare le operazioni già eseguite e non ripeterle, ad esempio eseguendo CloudJob.ListTasks prima di iniziare a restituire le attività.

### <a name="exit-codes-and-exceptions-in-the-job-manager-template"></a>Codici di uscita ed eccezioni nel modello Job Manager (Gestione di processi)
I codici di uscita e le eccezioni forniscono un meccanismo per determinare il risultato dell'esecuzione di un programma e consentono di identificare eventuali problemi di esecuzione del programma. Il modello Job Manager (Gestore di processi) implementa i codici di uscita e le eccezioni descritte in questa sezione.

Un'attività del gestore di processi implementata con il modello Job Manager (Gestore di processi) può restituire tre possibili codici di uscita:

| Codice | Descrizione |
| --- | --- |
| 0 |Il gestore di processi è stato completato. Il codice del componente di suddivisione dei processi è stato eseguito fino al completamento e tutte le attività sono state aggiunte al processo. |
| 1 |L'attività del gestore di processi non è riuscita con un'eccezione in una parte "prevista" del programma. L'eccezione è stata convertita in JobManagerException con informazioni di diagnostica e, dove possibile, suggerimenti per la risoluzione dell'errore. |
| 2 |L'attività del gestore di processi non è riuscita con un'eccezione "non prevista". L'eccezione è stata registrata nell'output standard, ma il gestore di processi non è riuscito ad aggiungere altre informazioni di diagnostica o correzione. |

In caso di errore dell'attività del gestore di processi, alcune attività potrebbero essere state ugualmente aggiunte al servizio prima che si verificasse l'errore. Queste attività verranno eseguite normalmente. Per informazioni su questo percorso del codice, vedere sopra "Errore del componente di suddivisione dei processi".

Tutte le informazioni restituite dalle eccezioni vengono scritte nei file stdout.txt e stderr.txt. Per altre informazioni, vedere [Gestione degli errori](batch-api-basics.md#error-handling).

### <a name="client-considerations"></a>Considerazioni sul client
Questa sezione illustra alcuni requisiti dell'implementazione client quando si richiama un gestore di processi basato su questo modello. Per informazioni dettagliate sul passaggio dei parametri e delle impostazioni di ambiente, vedere [Passare i parametri e le variabili di ambiente dal codice client](#pass-environment-settings) .

**Credenziali obbligatorie**

Per aggiungere attività al processo di Azure Batch, l'attività del gestore di processi richiede l'URL e la chiave dell'account Azure Batch. È necessario passarli nelle variabili di ambiente denominate YOUR_BATCH_URL e YOUR_BATCH_KEY. È possibile impostarle nelle impostazioni di ambiente dell'attività del gestore di processi. Ad esempio, in un client C#:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    new EnvironmentSetting("YOUR_BATCH_URL", "https://account.region.batch.azure.com"),
    new EnvironmentSetting("YOUR_BATCH_KEY", "{your_base64_encoded_account_key}"),
};
```
**Credenziali di archiviazione**

In genere, non è necessario che il client fornisca all'attività del gestore di processi le credenziali dell'account di archiviazione collegato perché (a) la maggior parte dei gestori di processi non deve accedere in modo esplicito all'account di archiviazione collegato e (b) l'account di archiviazione collegato viene spesso fornito a tutte le attività come impostazione di ambiente comune per il processo. Se non si specifica l'account di archiviazione collegato tramite le impostazioni di ambiente comuni e il gestore di processi deve accedere alla risorsa di archiviazione collegata, è consigliabile fornire le credenziali di archiviazione collegate, come segue:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    /* other environment settings */
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

**Impostazioni dell'attività del gestore di processi**

Il client deve impostare il flag *killJobOnCompletion* del gestore di processi su **false**.

In genere per il client è sicuro impostare *runExclusive* su **false**.

Il client deve usare la raccolta *resourceFiles* o *applicationPackageReferences* per distribuire l'eseguibile del gestore di processi (e le DLL necessarie) nel nodo di calcolo.

Per impostazione predefinita, l'esecuzione del gestore di processi non verrà riprovata in caso di errore. A seconda della logica del gestore di processi, il client può consentire i tentativi tramite *constraints*/*maxTaskRetryCount*.

**Impostazioni del processo**

Se il componente di suddivisione dei processi crea attività con dipendenze, il client deve impostare usesTaskDependencies del processo su true.

Nel modello del componente di suddivisione dei processi in genere i client non aggiungono attività ai processi oltre a quelle create dal componente di suddivisione dei processi. In genere il client deve quindi impostare *onAllTasksComplete* del processo su **terminatejob**.

## <a name="task-processor-template"></a>Modello Task Processor (Elaboratore di attività)
Un modello Task Processor (Elaboratore di attività) consente di implementare un elaboratore di attività che può eseguire queste azioni:

* Configurare le informazioni necessarie per l'esecuzione di ogni attività batch.
* Eseguire tutte le azioni necessarie per ogni attività batch.
* Salvare l'output di un'attività in una risorsa di archiviazione permanente.

Anche se non è necessario un elaboratore di attività per eseguire le attività in Batch, il vantaggio principale di usare un elaboratore di attività è che fornisce un wrapper per implementare tutte le azioni di esecuzione di un'attività in un'unica posizione, ad esempio, se è necessario eseguire più applicazioni nel contesto di ogni attività o se è necessario copiare i dati in una risorsa di archiviazione permanente dopo avere completato ogni attività.

Le azioni eseguite dall'elaboratore di attività possono essere semplici o complesse e più o meno numerose a seconda del carico di lavoro. Implementando tutte le azioni di un'attività in un unico elaboratore di attività, è anche possibile aggiornare o aggiungere facilmente azioni in base alle modifiche apportate alle applicazioni o ai requisiti del carico di lavoro. In alcuni casi, tuttavia, è meglio non usare un elaboratore di attività per l'implementazione perché può creare inutili difficoltà, ad esempio quando si eseguono processi che possono essere avviati rapidamente da una semplice riga di comando.

### <a name="create-a-task-processor-using-the-template"></a>Creare un elaboratore delle attività usando il modello
Per aggiungere un elaboratore di attività alla soluzione creata prima, seguire questi passaggi:

1. Aprire la soluzione esistente in Visual Studio 2015.
2. In Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione scegliere **Aggiungi**, quindi fare clic su **Nuovo progetto**.
3. In **Visual C#** fare clic su **Cloud** e su **Azure Batch Task Processor** (Elaboratore di attività di Azure Batch).
4. Digitare un nome che descriva l'applicazione e identifichi questo progetto come elaboratore di attività, ad esempio "ElaboratoreAttivitàLitware".
5. Per creare il progetto, fare clic su **OK**.
6. Compilare infine il progetto per fare in modo che Visual Studio carichi tutti i pacchetti NuGet di riferimento e verificare che il progetto sia valido prima di iniziare a modificarlo.

### <a name="task-processor-template-files-and-their-purpose"></a>File del modello Task Processor (Elaboratore di attività) e relativo scopo
Quando si crea un progetto usando il modello di elaboratore di attività, questo genera tre gruppi di file di codice:

* Il file di programma principale (Program.cs), che contiene il punto di ingresso del programma e la gestione delle eccezioni di primo livello. In genere non è necessario modificarlo.
* La directory Framework, che contiene i file responsabili delle operazioni del "boilerplate" eseguite dal programma del gestore di processi, ad esempio decompressione dei parametri, aggiunta di attività al processo batch e così via. In genere non è necessario modificare questi file.
* Il file dell'elaboratore di attività (TaskProcessor.cs), in cui si inserirà la logica specifica dell'applicazione per l'esecuzione di un'attività, in genere chiamando un eseguibile esistente. Anche il codice di pre-elaborazione e post-elaborazione, ad esempio per il download di dati aggiuntivi o il caricamento dei file dei risultati, viene inserito qui.

Ovviamente è possibile aggiungere altri file, se necessari per supportare il codice dell'elaboratore di attività, in base alla complessità della logica di suddivisione dei processi.

Il modello genera anche i file di progetto .NET standard, ad esempio un file CSPROJ, app.config, packages.config e così via.

La parte restante di questa sezione illustra i diversi file e la struttura del codice e spiega la funzione di ogni classe.

![Esplora soluzioni di Visual Studio che illustra la soluzione del modello Task Processor (Elaboratore di attività)][solution_explorer02]

**File di Framework**

* `Configuration.cs`: incapsula il caricamento dei dati di configurazione del processo, ad esempio i dettagli dell'account Batch, le credenziali dell'account di archiviazione collegato, le informazioni sul processo e sulle attività e i parametri del processo. Fornisce anche l'accesso alle variabili di ambiente definite da Batch (vedere Impostazioni di ambiente per le attività nella documentazione di Batch) tramite la classe Configuration.EnvironmentVariable.
* `IConfiguration.cs`: astrae l'implementazione della classe Configuration, per poter eseguire uno unit test del componente di suddivisione dei processi usando un oggetto di configurazione falso o fittizio.
* `TaskProcessorException.cs`: rappresenta un errore che obbliga a terminare il gestore di processi. TaskProcessorException viene usata per eseguire il wrapping degli errori "previsti" dove possono essere fornite informazioni di diagnostica specifiche nell'ambito della terminazione.

**Elaboratore di attività**

* `TaskProcessor.cs`: esegue l'attività. Il framework chiama il metodo TaskProcessor.Run. Questa è la classe in cui si inserirà la logica dell'applicazione dell'attività. Implementare il metodo Run per:
  * Analizzare e convalidare i parametri delle attività
  * Scrivere la riga di comando per i programmi esterni che si vuole richiamare
  * Registrare le informazioni di diagnostica che potrebbero essere necessarie a scopo di debug
  * Avviare un processo usando tale riga di comando
  * Attendere la chiusura del processo
  * Acquisire il codice di uscita del processo per determinare se ha avuto esito positivo o negativo
  * Salvare i file di output che si vuole mantenere in una risorsa di archiviazione permanente

**File di progetto della riga di comando .NET standard**

* `App.config`: file di configurazione dell'applicazione .NET standard.
* `Packages.config`: file di dipendenza del pacchetto NuGet standard.
* `Program.cs`: contiene il punto di ingresso del programma e la gestione delle eccezioni di primo livello.

## <a name="implementing-the-task-processor"></a>Implementazione dell'elaboratore di attività
Quando si apre il progetto del modello Task Processor (Elaboratore di attività), il progetto avrà il file TaskProcessor.cs aperto per impostazione predefinita. È possibile implementare la logica di esecuzione per le attività del carico di lavoro usando il metodo Run() illustrato sotto:

```csharp
/// <summary>
/// Runs the task processing logic. This is where you inject
/// your application-specific logic for decomposing the job into tasks.
///
/// The task processor framework invokes the Run method for you; you need
/// only to implement it, not to call it yourself. Typically, your
/// implementation will execute an external program (from resource files or
/// an application package), check the exit code of that program and
/// save output files to persistent storage.
/// </summary>
public async Task<int> Run()

{
    try
    {
        //Your code for the task processor goes here.
        var command = $"compare {_parameters["Frame1"]} {_parameters["Frame2"]} compare.gif";
        using (var process = Process.Start($"cmd /c {command}"))
        {
            process.WaitForExit();
            var taskOutputStorage = new TaskOutputStorage(
            _configuration.StorageAccount,
            _configuration.JobId,
            _configuration.TaskId
            );
            await taskOutputStorage.SaveAsync(
            TaskOutputKind.TaskOutput,
            @"..\stdout.txt",
            @"stdout.txt"
            );
            return process.ExitCode;
        }
    }
    catch (Exception ex)
    {
        throw new TaskProcessorException(
        $"{ex.GetType().Name} exception in run task processor: {ex.Message}",
        ex
        );
    }
}
```
> [!NOTE]
> La sezione con annotazioni nel metodo Run() è la sola del codice del modello Task Processor (Elaboratore di attività) che può essere modificata dall'utente aggiungendo la logica di esecuzione per le attività nel carico di lavoro. Per modificare un'altra sezione del modello, leggere prima la documentazione di Batch e provare a usare alcuni esempi di codice di Batch per familiarizzare con il funzionamento di Batch.
> 
> 

Il metodo Run() è responsabile dell'avvio della riga di comando, dell'avvio di uno o più processi, dell'attesa del completamento di tutti i processi, del salvataggio dei risultati e infine delle restituzione di un codice di uscita. Nel metodo Run() si implementa la logica di elaborazione per le attività. Il framework dell'elaboratore di attività richiama il metodo Run() automaticamente. Non è necessario chiamarlo manualmente.

L'implementazione di Run() ha accesso a:

* Parametri dell'attività tramite il campo `_parameters`.
* ID processo e attività, tramite i campi `_jobId` e `_taskId`.
* Configurazione dell'attività, tramite il campo `_configuration` .

**Errore dell'attività**

In caso di errore, è possibile uscire dal metodo Run() generando un'eccezione, ma in questo modo il gestore di eccezioni di primo livello rimane sotto il controllo del codice di uscita dell'attività. Se è necessario controllare il codice di uscita per poter distinguere i diversi tipi di errore, ad esempio per finalità di diagnostica o perché alcune modalità di errore devono terminare il processo e altre no, è consigliabile uscire dal metodo Run() restituendo un codice di uscita diverso da zero, che diventa il codice di uscita dell'attività.

### <a name="exit-codes-and-exceptions-in-the-task-processor-template"></a>Codici di uscita ed eccezioni nel modello Task Processor (Elaboratore di attività)
I codici di uscita e le eccezioni forniscono un meccanismo per determinare il risultato dell'esecuzione di un programma e consentono di identificare eventuali problemi di esecuzione del programma. Il modello Task Processor (Elaboratore di attività) implementa i codici di uscita e le eccezioni descritte in questa sezione.

Un'attività dell'elaboratore di attività implementata con il modello Task Processor (Elaboratore di attività) può restituire tre possibili codici di uscita:

| Codice | Descrizione |
| --- | --- |
| [Process.ExitCode][process_exitcode] |L'elaboratore di attività è stato eseguito fino al completamento. Si noti che questo non significa che il programma richiamato ha avuto esito positivo, ma solo che l'elaboratore di attività lo ha richiamato correttamente e ha eseguito le operazioni di post-elaborazione senza eccezioni. Il significato del codice di uscita dipende dal programma richiamato: in genere il codice di uscita 0 indica che il programma ha avuto esito positivo, mentre gli altri codici di uscita indicano che il programma ha avuto esito negativo. |
| 1 |L'elaboratore di attività non è riuscito con un'eccezione in una parte "prevista" del programma. L'eccezione è stata convertita in `TaskProcessorException` con informazioni di diagnostica e, dove possibile, suggerimenti per la risoluzione dell'errore. |
| 2 |L'elaboratore di attività non è riuscito con un'eccezione "non prevista". L'eccezione è stata registrata nell'output standard, ma l'elaboratore di attività non è riuscito ad aggiungere altre informazioni di diagnostica o correzione. |

> [!NOTE]
> Se il programma richiamato usa i codici di uscita 1 e 2 per indicare specifiche modalità di errore, l'uso dei codici di uscita 1 e 2 per gli errori dell'elaboratore di attività è ambiguo. È possibile sostituire questi codici di errore dell'elaboratore di attività con codici di uscita distintivi modificando le lettere maiuscole/minuscole delle eccezioni nel file Program.cs.
> 
> 

Tutte le informazioni restituite dalle eccezioni vengono scritte nei file stdout.txt e stderr.txt. Per altre informazioni, vedere Gestione degli errori nella documentazione di Batch.

### <a name="client-considerations"></a>Considerazioni sul client
**Credenziali di archiviazione**

Se l'elaboratore di attività usa l'archivio BLOB di Azure per rendere persistenti gli output, ad esempio usando la libreria helper File Conventions, deve accedere *o* alle credenziali dell'account di archiviazione cloud *o* all'URL di un contenitore BLOB che include una firma di accesso condiviso. Il modello include il supporto per fornire le credenziali tramite le variabili di ambiente comuni. Il client può passare le credenziali di archiviazione come segue:

```csharp
job.CommonEnvironmentSettings = new [] {
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

L'account di archiviazione è quindi disponibile nella classe TaskProcessor tramite la proprietà `_configuration.StorageAccount` .

Se si preferisce usare l'URL di un contenitore con la firma di accesso condiviso, è anche possibile passarlo tramite un'impostazione di ambiente comune del processo, ma il modello di elaboratore di attività attualmente non include il supporto predefinito a questo scopo.

**Configurazione dell'archiviazione**

È consigliabile che il client o l'attività del gestore di processi crei i contenitori richiesti dalle attività prima di aggiungere le attività al processo. Questo è obbligatorio se si usa l'URL di un contenitore con la firma di accesso condiviso, perché un URL di questo tipo non include l'autorizzazione per creare il contenitore. È consigliabile anche se si passano le credenziali dell'account di archiviazione, perché salva ogni attività dovendo chiamare CloudBlobContainer.CreateIfNotExistsAsync sul contenitore.

## <a name="pass-parameters-and-environment-variables"></a>Passare i parametri e le variabili di ambiente
### <a name="pass-environment-settings"></a>Passare le impostazioni di ambiente
Un client può passare informazioni all'attività del gestore di processi sotto forma di impostazioni di ambiente. Queste informazioni possono quindi essere usate dall'attività del gestore di processi quando si generano le attività dell'elaboratore di attività che verranno eseguite durante il processo di calcolo. Alcuni esempi di informazioni che è possibile passare come impostazioni di ambiente sono:

* Nome dell'account di archiviazione e chiavi dell'account
* URL dell'account Batch
* Chiave dell'account Batch

Il servizio Batch ha un semplice meccanismo per passare le impostazioni di ambiente a un'attività del gestore di processi usando la proprietà `EnvironmentSettings` in [Microsoft.Azure.Batch.JobManagerTask][net_jobmanagertask].

Ad esempio, per ottenere l'istanza di `BatchClient` per un account Batch, è possibile passare come variabili di ambiente dal codice client l'URL e le credenziali con chiave condivisa per l'account Batch. Analogamente, per accedere all'account di archiviazione collegato all'account Batch, è possibile passare il nome dell'account di archiviazione e la chiave dell'account di archiviazione come variabili di ambiente.

### <a name="pass-parameters-to-the-job-manager-template"></a>Passare i parametri al modello Job Manager (Gestore di processi)
In molti casi, è utile passare i parametri per ogni processo all'attività del gestore di processi, per controllare la procedura di suddivisione del processo o per configurare le attività per il processo. A questo scopo, caricare un file JSON denominato parameters.json come file di risorse per l'attività del gestore di processi. I parametri possono quindi essere disponibili nel campo `JobSplitter._parameters` del modello Job Manager (Gestore di processi).

> [!NOTE]
> Il gestore di parametri predefinito supporta solo i dizionari da stringa a stringa. Per passare valori JSON complessi come valori di parametri, sarà necessario passarli come stringhe e quindi analizzarli nel componente di suddivisione dei processi o modificare il metodo `Configuration.GetJobParameters` del framework.
> 
> 

### <a name="pass-parameters-to-the-task-processor-template"></a>Passare i parametri al modello Task Processor (Elaboratore di attività)
È anche possibile passare i parametri alle singole attività implementate usando il modello Task Processor (Elaboratore di attività). Proprio come il modello di gestore di processi, il modello di elaboratore di attività cerca un file di risorse denominato

parameters.json e, se lo trova, lo carica come dizionario di parametri. Per passare i parametri alle attività dell'elaboratore di attività, esistono due opzioni:

* Usare di nuovo il file JSON dei parametri del processo. Questa opzione funziona bene se i soli parametri sono quelli a livello di processo, ad esempio un'altezza e una larghezza di rendering. A questo scopo, quando si crea CloudTask nel componente di suddivisione dei processi, aggiungere un riferimento all'oggetto file di risorse parameters.json da ResourceFiles (`JobSplitter._jobManagerTask.ResourceFiles`) dell'attività del gestore di processi alla raccolta ResourceFiles di CloudTask.
* Generare e caricare un documento parameters.json specifico dell'attività durante l'esecuzione del componente di suddivisione dei processi e fare riferimento a tale BLOB nella raccolta di file di risorse dell'attività. Ciò è necessario se i parametri sono diversi per ogni attività. Come esempio si pensi a uno scenario di rendering 3D dove l'indice dei fotogrammi viene passato all'attività come parametro.

> [!NOTE]
> Il gestore di parametri predefinito supporta solo i dizionari da stringa a stringa. Per passare valori JSON complessi come valori di parametri, sarà necessario passarli come stringhe e quindi analizzarli nell'elaboratore di attività o modificare il metodo `Configuration.GetTaskParameters` del framework.
> 
> 

## <a name="next-steps"></a>Passaggi successivi
### <a name="persist-job-and-task-output-to-azure-storage"></a>Rendere persistenti l'output di attività e processi in Archiviazione di Azure
Un altro strumento utile nello sviluppo di soluzioni Batch è [Azure Batch File Conventions][nuget_package]. Usare questa libreria di classi .NET (attualmente in anteprima) nelle applicazioni Batch .NET per archiviare e recuperare facilmente gli output delle attività in e da Archiviazione di Azure. [Salvare in modo permanente l'output dei processi e delle attività di Azure Batch](batch-task-output.md) contiene una descrizione completa della libreria e di come utilizzarla.

### <a name="batch-forum"></a>Forum di Batch
Il [forum di Azure Batch][forum] su MSDN consente di seguire discussioni su Batch e di inviare domande sul servizio. Leggere i post contrassegnati e inviare domande durante le procedure di sviluppo delle soluzioni Batch.

[forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azurebatch
[net_jobmanagertask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobmanagertask.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[process_exitcode]: https://msdn.microsoft.com/library/system.diagnostics.process.exitcode.aspx
[vs_gallery]: https://visualstudiogallery.msdn.microsoft.com/
[vs_gallery_templates]: https://go.microsoft.com/fwlink/?linkid=820714
[vs_find_use_ext]: https://msdn.microsoft.com/library/dd293638.aspx

[diagram01]: ./media/batch-visual-studio-templates/diagram01.png
[solution_explorer01]: ./media/batch-visual-studio-templates/solution_explorer01.png
[solution_explorer02]: ./media/batch-visual-studio-templates/solution_explorer02.png



<!--HONumber=Dec16_HO2-->


