---
title: Sviluppare funzioni di .NET Standard per i processi di analisi di flusso di Azure (anteprima)
description: Informazioni su come scrivere funzioni c# definite dall'utente per i processi di analisi di flusso.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/28/2019
ms.custom: seodec18
ms.openlocfilehash: 4f9d117ccc763744411bfe24163ed955532e8e56
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85921862"
---
# <a name="develop-net-standard-user-defined-functions-for-azure-stream-analytics-jobs-preview"></a>Sviluppare .NET Standard funzioni definite dall'utente per i processi di analisi di flusso di Azure (anteprima)

L'Analisi di flusso di Azure offre un linguaggio di query simile a SQL per eseguire trasformazioni e calcoli sui flussi di dati degli eventi. Sono disponibili molte funzioni predefinite, ma alcuni scenari complessi richiedono una maggiore flessibilità. Con le funzioni .NET Standard definite dall'utente è possibile richiamare funzioni personalizzate scritte in qualsiasi linguaggio di programmazione .NET Standard (C#, F# e così via) per estendere il linguaggio di query dell'Analisi di flusso di Azure. Le funzioni definite dall'utente consentono di eseguire calcoli matematici complessi, di importare i modelli di Machine Learning personalizzati con ML.NET e di usare la logica di imputazione personalizzata per i dati mancanti. La funzionalità per la creazione di funzioni definite dall'utente per i processi di Analisi di flusso di Azure è attualmente in anteprima e non deve essere usata nei carichi di lavoro di produzione.

La funzione definita dall'utente .NET per i processi cloud è disponibile in:
* Stati Uniti centro-occidentali
* Europa settentrionale
* Stati Uniti orientali
* Stati Uniti occidentali
* Stati Uniti orientali 2
* Europa occidentale

Se si è interessati all'uso di questa funzionalità in un'altra area, è possibile [richiedere l'accesso](https://aka.ms/ccodereqregion).

## <a name="overview"></a>Panoramica
Gli strumenti di Visual Studio per l'Analisi di flusso di Azure semplificano la scrittura di funzioni definite dall'utente, l'esecuzione di test sui processi in locale (anche offline) e la pubblicazione del processo di Analisi di flusso in Azure. Dopo la pubblicazione in Azure è possibile distribuire il processo nei dispositivi IoT con l'hub IoT.

Vi sono tre modi per implementare le funzioni definite dall'utente:

* File CodeBehind in un progetto ASA
* Funzione definita dall'utente da un progetto locale
* Un pacchetto esistente da un account di archiviazione di Azure

## <a name="package-path"></a>Percorso del pacchetto

Il formato del pacchetto di una qualsiasi funzione definita dall'utente presenta il percorso `/UserCustomCode/CLR/*`. Le librerie di collegamento dinamico (DLL) e le risorse vengono copiate nella cartella `/UserCustomCode/CLR/*`, che consente di isolare le DLL dell'utente dal sistema e dalle DLL di Analisi di flusso di Azure. Questo percorso del pacchetto viene usato per tutte le funzioni, indipendentemente dal metodo usato per queste.

## <a name="supported-types-and-mapping"></a>Mapping e tipi supportati
Per usare i valori di analisi di flusso di Azure in C#, è necessario eseguirne il marshalling da un ambiente all'altro. Il marshalling si verifica per tutti i parametri di input di una funzione definita dall'utente. Ogni tipo di analisi di flusso di Azure ha un tipo corrispondente in C# indicato nella tabella seguente:

|**Tipo di Analisi di flusso di Azure** |**Tipo C#** |
|---------|---------|
|bigint | long |
|float | double |
|nvarchar(max) | string |
|Datetime | Datetime |
|Record | Dizionario\<string, object> |
|Array | Oggetto [] |

Lo stesso vale quando è necessario effettuare il marshalling dei dati da C# ad analisi di flusso di Azure, che si verifica sul valore di output di una funzione definita dall'utente. La tabella seguente illustra i tipi supportati:

|**Tipo C#**  |**Tipo di Analisi di flusso di Azure**  |
|---------|---------|
|long  |  bigint   |
|double  |  float   |
|string  |  nvarchar(max)   |
|Datetime  |  dateTime   |
|struct  |  Record   |
|object  |  Record   |
|Oggetto []  |  Array   |
|Dizionario\<string, object>  |  Record   |

## <a name="codebehind"></a>CodeBehind
È possibile scrivere funzioni definite dall'utente nel CodeBehind **Script.asql**. Gli strumenti di Visual Studio compileranno automaticamente il file CodeBehind in un file di assembly. Gli assembly sono inclusi in un pacchetto come file ZIP e caricati nell'account di archiviazione quando si invia il processo ad Azure. È possibile imparare a scrivere una funzione C# definita dall'utente con CodeBehind seguendo l'esercitazione [Funzione C# definita dall'utente per i processi di Analisi di flusso di Azure in IoT Edge](stream-analytics-edge-csharp-udf.md). 

## <a name="local-project"></a>Progetto locale
Le funzioni definite dall'utente possono essere scritte in un assembly a cui in seguito fa riferimento una query di Analisi di flusso di Azure. Si tratta dell'opzione consigliata per le funzioni complesse che richiedono la potenza completa di un linguaggio di programmazione .NET Standard oltre al linguaggio delle espressioni, ad esempio una logica procedurale o la ricorsione. Le funzioni definite dall'utente da un progetto locale possono anche essere usate quando è necessario condividere la logica della funzione in diverse query di Analisi di flusso di Azure. L'aggiunta di funzioni definite dall'utente a un progetto locale offre la possibilità di eseguire il debug e il test delle funzioni in locale da Visual Studio.

Per fare riferimento a un progetto locale:

1. Creare una nuova libreria di classi nella soluzione.
2. Scrivere il codice nella classe. Tenere presente che le classi devono essere definite come *public* e gli oggetti devono essere definiti come *static public*. 
3. Compilare il progetto. Gli strumenti creeranno un pacchetto di tutti gli artefatti presenti nella cartella bin inserendoli in un file con estensione zip che verrà caricato nell'account di archiviazione. Per i riferimenti esterni, usare un riferimento assembly anziché il pacchetto NuGet.
4. Fare riferimento alla nuova classe nel progetto di Analisi di flusso di Azure.
5. Aggiungere una nuova funzione nel progetto di Analisi di flusso di Azure.
6. Configurare il percorso dell'assembly nel file di configurazione del processo `JobConfig.json`. Impostare il percorso dell'assembly come **riferimento al progetto locale o CodeBehind**.
7. Ricompilare sia il progetto di funzione sia il progetto di Analisi di flusso di Azure.  

### <a name="example"></a>Esempio

In questo esempio, **UDFTest** è un progetto di libreria di classi C# e **ASAUDFDemo** è il progetto di analisi di flusso di Azure, che fa riferimento a **UDFTest**.

![Progetto di Analisi di flusso di Azure in IoT Edge in Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-demo.png)

1. Compilare il progetto C# che permetterà di aggiungere un riferimento alla funzione C# definita dall'utente dalla query di Analisi di flusso di Azure.
    
   ![Compilare un progetto di Analisi di flusso di Azure in IoT Edge in Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-build-project.png)

2. Aggiungere il riferimento al progetto C# nel progetto ASA. Fare clic con il pulsante destro del mouse sul nodo Riferimenti e scegliere Aggiungi riferimento.

   ![Aggiungere un riferimento a un progetto C# in Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-reference.png)

3. Scegliere il nome del progetto C# dall'elenco. 
    
   ![Scegliere il nome del progetto C# dall'elenco dei riferimenti](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-choose-project-name.png)

4. **UDFTest** verrà visualizzato nell'elenco **Riferimenti** in **Esplora soluzioni**.

   ![Visualizzare il riferimento della funzione definita dall'utente in Esplora soluzioni](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-added-reference.png)

5. Fare clic con il pulsante destro del mouse sulla cartella **Funzioni** e scegliere **Nuovo elemento**.

   ![Aggiungere il nuovo elemento alle funzioni nella soluzione Edge di Analisi di flusso di Azure](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function.png)

6. Aggiungere una funzione C# **SquareFunction.json** al progetto di Analisi di flusso di Azure.

   ![Selezionare la funzione CSharp dagli elementi Edge di Analisi di flusso di Azure in Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function-2.png)

7. Per aprire la finestra di dialogo di configurazione, fare doppio clic sulla funzione in **Esplora soluzioni**.

   ![Configurazione di una funzione C sharp in Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-csharp-function-config.png)

8. Nella configurazione della funzione C# scegliere **Carica dal riferimento al progetto ASA** e i nomi di assembly, classe e metodo correlati dall'elenco a discesa. Per fare riferimento ai metodi, ai tipi e alle funzioni nella query di analisi di flusso, le classi devono essere definite come *pubbliche* e gli oggetti devono essere definiti come *pubblici statici*.

   ![Configurazione della funzione C sharp dell'Analisi di flusso di Azure](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-asa-csharp-function-config.png)

## <a name="existing-packages"></a>Pacchetti esistenti

È possibile creare funzioni .NET Standard definite dall'utente in qualsiasi ambiente di sviluppo integrato desiderato e richiamarle dalla query di Analisi di flusso di Azure. Compilare innanzitutto il codice e includere in un pacchetto tutte le DLL. Il formato del pacchetto presenta il percorso `/UserCustomCode/CLR/*`. Caricare quindi `UserCustomCode.zip` nella radice del contenitore nell'account di archiviazione di Azure.

Dopo aver caricato i pacchetti ZIP di assembly nel proprio account di archiviazione di Azure, è possibile usare le funzioni nelle query di Analisi di flusso di Azure. È sufficiente includere le informazioni di archiviazione nella configurazione del processo di analisi di flusso. Con questa opzione non è possibile effettuare test in locale per la funzione, in quanto gli strumenti di Visual Studio non scaricheranno il pacchetto. Il percorso del pacchetto viene analizzato direttamente nel servizio. 

Per configurare il percorso dell'assembly nel file di configurazione del processo, `JobConfig.json`:

Espandere la sezione **Configurazione di codice definito dall'utente** e compilare la configurazione con i seguenti valori suggeriti:

   |**Impostazione**|**Valore consigliato**|
   |-------|---------------|
   |Global Storage Settings Resource (Risorsa impostazioni di archiviazione globali)|Scegliere l'origine dati dall'account corrente|
   |Global Storage Settings Subscription (Sottoscrizione impostazioni di archiviazione globali)| < sottoscrizione >|
   |Global Storage Settings Storage Account (Account di archiviazione impostazioni di archiviazione globali)| < account di archiviazione >|
   |Custom Code Storage Settings Resource (Risorsa impostazioni di archiviazione codice personalizzato)|Scegliere l'origine dati dall'account corrente|
   |Custom Code Storage Settings Storage Account (Account di archiviazione impostazioni di archiviazione codice personalizzato)|< account di archiviazione >|
   |Custom Code Storage Settings Container (Contenitore impostazioni di archiviazione codice personalizzato)|< contenitore di archiviazione >|
   |Origine assembly di codice personalizzato|Pacchetti di assembly esistenti dal cloud|
   |Origine assembly di codice personalizzato|UserCustomCode.zip|

## <a name="user-logging"></a>Registrazione utente
Il meccanismo di registrazione consente di acquisire informazioni personalizzate mentre un processo è in esecuzione. È possibile utilizzare i dati di log per eseguire il debug o valutare la correttezza del codice personalizzato in tempo reale.

La `StreamingContext` classe consente di pubblicare informazioni di diagnostica utilizzando la `StreamingDiagnostics.WriteError` funzione. Il codice seguente illustra l'interfaccia esposta da analisi di flusso di Azure.

```csharp
public abstract class StreamingContext
{
    public abstract StreamingDiagnostics Diagnostics { get; }
}

public abstract class StreamingDiagnostics
{
    public abstract void WriteError(string briefMessage, string detailedMessage);
}
```

`StreamingContext`viene passato come parametro di input al metodo UDF e può essere usato all'interno della funzione definita dall'utente per pubblicare informazioni di log personalizzate. Nell'esempio seguente, `MyUdfMethod` definisce un input di **dati** , fornito dalla query, e un input di **contesto** come `StreamingContext` , fornito dal motore di Runtime. 

```csharp
public static long MyUdfMethod(long data, StreamingContext context)
{
    // write log
    context.Diagnostics.WriteError("User Log", "This is a log message");
    
    return data;
}
```

Il `StreamingContext` valore non deve essere passato dalla query SQL. Analisi di flusso di Azure fornisce automaticamente un oggetto di contesto se è presente un parametro di input. L'utilizzo di `MyUdfMethod` non cambia, come illustrato nella query seguente:

```sql
SELECT udf.MyUdfMethod(input.value) as udfValue FROM input
```

È possibile accedere ai messaggi di log tramite i [log di diagnostica](data-errors.md).

## <a name="limitations"></a>Limitazioni
L'anteprima della funzione definita dall'utente attualmente presenta le limitazioni seguenti:

* Le funzioni .NET Standard definite dall'utente possono solo essere create in Visual Studio e pubblicate in Azure. Le versioni di sola lettura delle funzioni .NET Standard definite dall'utente possono essere visualizzate nella sezione **Funzioni** del portale di Azure. La creazione delle funzioni .NET Standard non è supportata nel portale di Azure.

* L'editor di query del portale di Azure mostra un errore quando nel portale si fa uso della funzione .NET Standard definita dall'utente. 

* Poiché il codice personalizzato condivide il contesto con il motore di Analisi di flusso di Azure, il codice personalizzato non può riferirsi a nessun elemento con uno spazio dei nomi/DLL_name in conflitto con il codice di Analisi di flusso di Azure. Ad esempio, non è possibile fare riferimento al *Json di Newtonsoft*.

* I file di supporto inclusi nel progetto vengono copiati nel file zip del codice personalizzato dell'utente usato quando si pubblica il processo nel cloud. Tutti i file nelle sottocartelle vengono copiati direttamente nella radice della cartella del codice personalizzato dell'utente nel cloud quando vengono decompressi. Il file zip è "flat" quando viene decompresso.

* Il codice personalizzato dell'utente non supporta le cartelle vuote. Non aggiungere cartelle vuote ai file di supporto nel progetto.

## <a name="next-steps"></a>Passaggi successivi

* [Esercitazione: scrivere una funzione C# definita dall'utente per un processo di analisi di flusso di Azure (anteprima)](stream-analytics-edge-csharp-udf.md)
* [Esercitazione: Funzioni JavaScript definite dall'utente in Analisi di flusso di Azure](stream-analytics-javascript-user-defined-functions.md)
* [Usare Visual Studio per visualizzare i processi di Analisi di flusso di Azure](stream-analytics-vs-tools.md)
