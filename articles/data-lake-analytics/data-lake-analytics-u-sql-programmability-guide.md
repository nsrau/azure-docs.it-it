---
title: "Guida alla programmabilità di U-SQL per Azure Data Lake Analytics | Microsoft Docs"
description: "Guida di programmabilità di U-SQL"
services: data-lake-analytics
documentationcenter: 
author: MikeRys
manager: arindamc
ms.assetid: 63be271e-7c44-4d19-9897-c2913ee9599d
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/15/2016
ms.author: mrys
translationtype: Human Translation
ms.sourcegitcommit: cd2aafd80db337cadaa2217a6638d93186975b68
ms.openlocfilehash: 563a6821b4a3736ef1233aa67d86b9ba06565788
ms.lasthandoff: 02/09/2017


---
# <a name="u-sql-programmability-guide"></a>Guida di programmabilità di U-SQL
## <a name="azure-data-lake"></a>Azure Data Lake
Azure Data Lake include tutte le funzionalità necessarie a sviluppatori, data scientist e analisti per archiviare più facilmente dati di tutte le dimensioni, forme e velocità e svolgere qualsiasi tipo di elaborazione e analisi su più piattaforme e linguaggi. Il servizio elimina la complessità correlata all'inserimento e all'archiviazione di tutti i dati, semplificando la preparazione e l'esecuzione con analisi in batch, di streaming e interattive.

Azure Data Lake è un set di servizi che interagiscono per comporre una piattaforma Big Data basata sul cloud:

- HDInsight
- Archivio Azure Data Lake
- Azure Data Lake Analytics.

U-SQL è un linguaggio di query appositamente progettato per i carichi di lavoro Big Data. Una delle caratteristiche esclusive di U-SQL è la combinazione tra il linguaggio dichiarativo di SQL e gli aspetti di estendibilità e programmabilità del linguaggio C#. U-SQL consente inoltre di accedere e modificare i metadati dello schema, nonché di creare componenti personalizzati, come riduttori ed elaboratori di dati.

Questa guida tratta gli aspetti di estendibilità e programmabilità del linguaggio U-SQL resi possibili da C#.

## <a name="requirements"></a>Requisiti
Per iniziare a sviluppare con Azure Data Lake (ADL), è anzitutto necessario scaricare e installare [Azure Data Lake Tools per Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

## <a name="starting-with-u-sql"></a>Primi passi con U-SQL  
La descrizione del linguaggio U-SQL non è oggetto di questo documento, che tuttavia descrive un costrutto U-SQL di base per presentare gradualmente le caratteristiche di programmabilità di U-SQL. Per altre informazioni, vedere la guida di [riferimento al linguaggio U-SQL](http://aka.ms/usql_reference).

Si veda l'esempio seguente:

```sql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid Guid,
        dt DateTime,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT
        MAX(guid) AS start_id,
        MIN(dt) AS start_time,
        user,
        des
    FROM @rs0
    GROUP BY user, des;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

Nell'esempio precedente è presente un **file di ingresso** (input_file.tsv), definito dalla **variabile locale** @input_file.

L'esecuzione dello script U-SQL qui sopra ha come risultato le seguenti azioni:

* L'istruzione **EXTRACT** iniziale carica i dati in memoria convertendo il file di input nel **set di righe della memoria**.
* L'istruzione **SELECT** agisce sui set di righe dei dati per aggregare questi ultimi e predisporre l'esportazione.
* Il comando **OUTPUT** esporta i set di righe dei dati nel **file di output** esterno.

Esaminiamo anzitutto alcune opzioni per usare un'espressione C# direttamente in uno script U-SQL.

## <a name="c-types-and-expressions-in-u-sql-script"></a>Tipi ed espressioni C# negli script U-SQL
Un po' come nel linguaggio C# generale, un'espressione C# in U-SQL è una sequenza di uno o più operatori che può restituire un singolo valore, oggetto, metodo o spazio dei nomi. Le espressioni possono corrispondere a un valore letterale, una chiamata al metodo, un operatore o un nome semplice. Un nome semplice può essere il nome di una variabile, il membro di un tipo,il parametro di un metodo, uno spazio dei nomi o un tipo.

Quando si parla delle espressioni C# in U-SQL, ci si riferisce nello specifico alle espressioni C# dello script U-SQL di base. Espressioni C# come normali elementi di codice C# possono essere presenti anche nella sezione code-behind di C#, illustrata più avanti.

Le espressioni possono usare operatori che usano altre espressioni come parametri oppure chiamate a metodi i cui parametri sono a loro volta altre chiamate a metodi. Esempio di un'espressione:  

```c#
    Convert.ToDateTime(Convert.ToDateTime(dt).ToString("yyyy-MM-dd"))
```

Il linguaggio U-SQL consente di usare espressioni C# standard da spazi dei nomi predefiniti.  
 
```c#
    Microsoft.Analytics.Interfaces;  
    Microsoft.Analytics.Types.Sql;  
    System;  
    System.Collections.Generic;  
    System.Linq;  
    System.Text;  
```

Le espressioni C# generali possono essere usate nelle istruzioni SELECT ed EXTRACT di U-SQL,

oltre che nelle istruzioni DECLARE o IF. Un esempio di tali espressioni è:   

```c#
    DateTime.Today.Day   
    Convert.ToDateTime
```

Esempio di script U-SQL di base:  

```sql
    DECLARE @default_dt DateTime = Convert.ToDateTime("06/01/2016");
```

Le espressioni C# possono estendere le funzionalità di modifica quando si usano le colonne come parte del set di righe. Ad esempio, se si desidera convertire una colonna DateTime a una data con ore zero, è possibile usare la seguente parte SELECT dello script U-SQL di base:

```sql
@rs1 =
    SELECT
        MAX(guid) AS start_id,
     MIN(dt) AS start_time,
        MIN(Convert.ToDateTime(Convert.ToDateTime(dt).ToString("yyyy-MM-dd")))
AS start_zero_time,
        user,
        des
    FROM @rs0
    GROUP BY user, des;
```

Come si può vedere, per eseguire la conversione si usa il metodo `System.Convert.ToDateTime`.

Lo scenario d'uso un po' più complicato illustrato di seguito dimostra l'uso di alcuni operatori C# di base. Esaminare l'espressione seguente.

```sql
@rs1 =
    SELECT
        MAX(guid) AS start_id,
        MIN(dt) AS start_time,
          MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
        user,
        des
    FROM @rs0
    GROUP BY user, des;
```

Questo è un esempio di espressione con operatore C# condizionale.

Negli esempi precedenti è illustrato l'uso di espressioni C# nello script U-SQL di base. Tuttavia, U-SQL porta con sé caratteristiche di programmabilità più estendibili, trattate più avanti in questo documento.  

Script completo:

```sql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid Guid,
        dt DateTime,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

DECLARE @default_dt DateTime = Convert.ToDateTime("06/01/2016");

@rs1 =
    SELECT
        MAX(guid) AS start_id,
    MIN(dt) AS start_time,
        MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
        user,
        des
    FROM @rs0
    GROUP BY user, des;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

### <a name="using-c-expressions-for-data-type-conversions"></a>Uso di espressioni C# per la conversione di tipi di dati
Partendo dal frammento precedente, l'esempio che segue illustra la tecnica di conversione dei dati datetime tramite espressioni C#. In questo particolare scenario, i dati della stringa datetime vengono convertiti in datetime standard con la notazione dell'ora 00:00:00.

```sql
DECLARE @dt String = "2016-07-06 10:23:15";
@rs1 =
    SELECT Convert.ToDateTime(Convert.ToDateTime(@dt).ToString("yyyy-MM-dd")) AS dt,
           dt AS olddt
    FROM @rs0;
OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

### <a name="using-c-expressions-for-todays-date"></a>Uso delle espressioni C# per la data corrente
Per eseguire il pulling della data corrente, è possibile usare l'espressione C# seguente

```c#
DateTime.Now.ToString("M/d/yyyy")
```

In uno script

```sql
@rs1 =
    SELECT
        MAX(guid) AS start_id,
        MIN(dt) AS start_time,
        MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
        MIN(USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)) AS start_fiscalperiod,
        DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
        user,
        des
    FROM @rs0
    GROUP BY user, des;
```

## <a name="in-line-c-function-expressions"></a>Espressioni di funzione C# inline
U-SQL consente di usare la definizione di espressioni di funzione nelle espressioni C# inline. Ciò consente ulteriori possibilità di usare le funzioni C# con i parametri di riferimento di output.

Definizione di espressione di funzione inline generale

```c#
    (Func<type of param1, type of param2>)
    (param1 =>
         { … return param2}
    ) (Rowset Column)
```

Esempio:

```c#
    (Func<string, DateTime?>)
    (input_p =>
         {
            DateTime dt_result;
            return DateTime.TryParse(input_p, out dt_result)
                   ? (DateTime?) dt_result : (DateTime?) null;
         }
    )
    ) (dt)
```

In questo esempio è definita la funzione inline con parametro di input di stringa input_p. All'interno di questa funzione si verifica se la stringa di input corrisponda a un valore datetime valido. In caso affermativo, restituirlo, altrimenti restituire null.

La funzione inline è necessaria in questo scenario in quanto la funzione DateTime.TryParse contiene il parametro di output `out dt_result`, definito come `DateTime dt_result`;


## <a name="verifying-data-type-values"></a>Verifica dei valori del tipo di dati
Alcune funzioni C# non possono essere usate direttamente nello script U-SQL di base come espressione C#. In particolare, si tratta delle funzioni che richiedono un parametro di riferimento di output. Queste funzioni possono tuttavia essere definite e usate nell'espressione di funzione inline come illustrato in precedenza.

### <a name="using-inline-function-expressions"></a>Uso di espressioni di funzione inline
Per controllare la validità del valore DateTime, è possibile usare `DateTime.TryParse`.

Quello che segue è un esempio completo di come usare un'espressione di funzione inline per verificare il valore del tipo di dati con `DateTime.TryParse`.

In questo scenario si verifica il valore del tipo di dati DateTime

```sql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid Guid,
        dt string,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT
        MAX(guid) AS start_id,
        MIN((
             (Func<string, DateTime?>)
             (input_parameter =>
                { DateTime dt_result;
                  return
                     DateTime.TryParse(input_parameter, out dt_result)
                       ?(DateTime?) dt_result : (DateTime?) null;
                }
             )
             ) (dt)) AS start_time,
        DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
        user,
        des
    FROM @rs0
    GROUP BY user, des;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

### <a name="using-code-behind"></a>Uso di code-behind
Per usare la stessa funzionalità nella sezione code-behind del programma U-SQL, viene definita la funzione C# ToDateTime.

Di seguito è riportata la sezione dello script U-SQL di base, in cui sono state apportate le modifiche necessarie:

```sql
     @rs1 =
        SELECT
          MAX(guid) AS start_id,
          MIN(USQL_Programmability.CustomFunctions.ToDateTime(dt)) AS start_time,
          DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
          user,
          des
          FROM @rs0
        GROUP BY user, des;
```

insieme alla funzione code-behind

```c#
        static public DateTime? ToDateTime(string dt)
        {
            DateTime dtValue;

            if (!DateTime.TryParse(dt, out dtValue))
                return Convert.ToDateTime(dt);
            else
                return null;
        }
```

## <a name="using-code-behind"></a>Uso di code-behind
Quella di code-behind è una sezione di programmabilità C# del progetto U-SQL. Concettualmente, code-behind è un assembly compilato (DLL) a cui si fa riferimento nello script U-SQL. Visual Studio Tools consente di gestire ed eseguire il debug di una sezione di programmabilità C# nel progetto U-SQL stesso.

Un normale progetto U-SQL creato in Visual Studio si compone di due parti. Lo script di base: un file con estensione **.usql**.

![a](./media/data-lake-analytics-u-sql-programmability-guide/base-script-file.png)


Un tipico progetto di soluzione   
![typical-solution-project](./media/data-lake-analytics-u-sql-programmability-guide/typical-solution-project.png)


Nella seconda parte del progetto viene chiamato un code-behind: il file Script.usql.cs  
![code-behind](./media/data-lake-analytics-u-sql-programmability-guide/code-behind.png)

Questo file contiene una definizione dello spazio dei nomi predefinito per gli oggetti di programmabilità.

```c#
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace USQL_Programmability
{

}
```

Il modello code-behind qui sopra è quello generato automaticamente. Questo file contiene una definizione dello spazio dei nomi predefinito per gli oggetti di programmabilità. Durante l'esecuzione del progetto, questo viene compilato e vi si fa riferimento nello script U-SQL di base.

Per iniziare a sviluppare oggetti di programmabilità, è necessario creare una classe **pubblica**.

```c#
namespace USQL_Programmability
{
    public class MyClass
    {
        public static string MyFunction(string param1)
        {
            return "my result";
        }

    }

}
```

Gli oggetti di programmabilità possono essere funzioni definite dall'utente (**UDF**), tipi definiti dall'utente (**UDT, PROCESS, REDUCER**) e così via.

## <a name="registering-u-sql-assemblies"></a>Registrazione di assembly U-SQL
Il modello di estendibilità U-SQL dipende molto dalla possibilità di aggiungere codice personalizzato. Attualmente, U-SQL offre semplici metodi per aggiungere codice basato su .NET personalizzato, in particolare in C#, ma è anche possibile aggiungere codice personalizzato scritto in altri linguaggi .NET, come VB.Net o F#. È anche possibile distribuire il proprio Runtime per altre lingue, anche se è necessario provvedere autonomamente all'interoperabilità tramite un livello .NET. Se si desidera supportare un linguaggio specifico, registrare la richiesta per una funzionalità e/o lasciare un commento all'indirizzo http://aka.ms/adlfeedback

### <a name="difference-between-code-behind-and-assembly-registration-through-adl-tools-in-visual-studio"></a>Differenza tra code-behind e la registrazione di assembly tramite ADL Tools in Visual Studio
Il modo più semplice di usare il codice personalizzato consiste nell'usare le funzionalità code-behind di ADL Tools per Visual Studio.

Come accennato in precedenza, si compila il codice personalizzato dello script (ad esempio, Script.usql) nel relativo file code-behind (ad esempio, Script.usql.cs).

![code-behind-example](./media/data-lake-analytics-u-sql-programmability-guide/code-behind-example.jpg)
**Figura 1**: esempio di code-behind in ADL Tools in VS (fare clic per ingrandire l'immagine, il codice di esempio è disponibile [qui](https://github.com/Azure/usql/tree/master/Examples/TweetAnalysis))
<br />

Il vantaggio di code-behind è che gli strumenti gestiscono automaticamente i passaggi seguenti all'invio dello script:  

1. Viene compilato l'assembly relativo al file code-behind  

1. Viene aggiunto un prologo allo script che usa l'istruzione [CREATE ASSEMBLY](https://msdn.microsoft.com/library/azure/mt763293.aspx) per registrare il file di assembly e viene usato [REFERENCE ASSEMBLY] (https://msdn.microsoft.com/library/azure/mt763294.aspx) per caricare l'assembly nel contesto dello script.

1. Viene aggiunto un epilogo allo script che usa [DROP ASSEMBLY](https://msdn.microsoft.com/library/azure/mt763295.aspx) per tornare a rimuovere l'assembly registrato temporaneamente.

Il prologo e l'epilogo generati sono visibili quando si apre lo script:

![generated-prologue](./media/data-lake-analytics-u-sql-programmability-guide/generated-prologue.png)

**Figura 2**: prologo ed epilogo generati automaticamente per il code-behind
<br />

Di seguito sono descritti alcuni svantaggi di code-behind.

* Il codice viene caricato per ogni invio di script.
* La funzionalità non può essere condivisa con altri utenti.

Pertanto, è possibile aggiungere alla propria soluzione una libreria di classi C# distinta per U-SQL (vedere la Figura 3), sviluppare il codice o copiare il code-behind esistente (senza modificare il codice C# necessario, vedere la Figura 4), quindi registrare l'assembly usando l'opzione di menu Registra assembly del progetto (vedere il Passaggio 1 nella Figura 5).

![creating-project](./media/data-lake-analytics-u-sql-programmability-guide/creating-project.png)
**Figura 3**: creazione di un progetto di codice C# U-SQL.  
<br />

![class-library](./media/data-lake-analytics-u-sql-programmability-guide/class-library.png)
**Figura 4**: libreria di classi C# U-SQL accanto al file code-behind.  
<br />

![register-code](./media/data-lake-analytics-u-sql-programmability-guide/register-code.png)
**Figura 5**: come registrare il progetto codice C# U-SQL
<br />

Nella finestra di dialogo relativa alla registrazione (vedere il Passaggio 2 nella Figura 5) è possibile scegliere dove registrare l'assembly (l'account Data Lake Analytics e il database) e come denominarlo (il percorso dell'assembly locale viene compilato dallo strumento). È inoltre disponibile un'opzione per registrare nuovamente un assembly già registrato, con la possibilità di aggiungere ulteriori dipendenze scegliendo fra due opzioni :

* *Dipendenze gestite*: mostra gli assembly gestiti altrettanto necessari. Ogni assembly selezionato viene registrato singolarmente e diventa possibile farvi riferimento negli script. Questa opzione viene usata per gli assembly .NET

* *File aggiuntivi*: consente di aggiungere ulteriori file di risorse necessari per l'assembly. Questi vengono registrati insieme all'assembly e caricati automaticamente quando viene fatto riferimento all'assembly. Questa opzione viene usata per i file di configurazione, gli assembly nativi, i runtime di altri linguaggi e relative risorse e così via.

Negli esempi seguenti vengono usate entrambe le opzioni. Il [recente post di blog sull'elaborazione delle immagini](https://blogs.msdn.microsoft.com/azuredatalake/2016/08/18/introducing-image-processing-in-u-sql/) è un altro esempio che illustra l'uso di un assembly predefinito che può usare queste opzioni per la registrazione.

A questo punto è possibile fare riferimento agli assembly registrati da qualunque script U-SQL che dispone delle autorizzazioni per il database degli assembly registrati (vedere il codice nello script U-SQL nella Figura 4). È necessario aggiungere un riferimento per ogni assembly registrato separatamente. I file di risorse aggiuntivi verranno distribuiti automaticamente. Lo script non deve più disporre di un file code-behind per il codice negli assembly di riferimento, ma il file code-behind può comunque fornire altro codice.

### <a name="registering-assemblies-via-adl-tools-in-visual-studio-and-in-u-sql-scripts"></a>Registrazione di assembly tramite ADL Tools in Visual Studio e negli script U-SQL
Registrare un assembly con ADL Tools in Visual Studio è facile, ma è possibile farlo anche con uno script (in alternativa alla procedura automatica), ad esempio se si sviluppa su una piattaforma diversa e sono già stati compilati assembly da caricare e registrare. Per effettuare questa operazione, eseguire i passaggi seguenti:

1. Caricare la DLL dell'assembly e tutti i necessari file di risorse e DLL non di sistema in una posizione a scelta dell'account di archiviazione di Azure Data Lake oppure di un account di archiviazione BLOB di Microsoft Azure collegato al proprio account Azure Data Lake. È possibile usare uno qualsiasi dei numerosi strumenti di caricamento disponibili (ad esempio, i comandi PowerShell, ADL Tool Data Lake Explorer di Visual Studio, il comando di caricamento dell'SDK che si preferisce oppure il Portale di Azure).

1. Una volta caricate la DLL, usare le istruzioni [CREATE ASSEMBLY](https://msdn.microsoft.com/library/azure/mt763293.aspx) per registrarle.

Nell'esempio che segue viene usato questo approccio.

### <a name="registering-assemblies-that-use-other-net-assemblies-based-on-the-json-and-xml-sample-library"></a>Registrazione di assembly che usano altri assembly . NET (basati sulla libreria JSON e XML di esempio)
Sul [sito Github U-SQL](https://github.com/Azure/usql/) è disponibile un set di assembly di esempio condivisi. Uno degli assembly, denominato [Microsoft.Analytics.Samples.Formats](https://github.com/Azure/usql/tree/master/Examples/DataFormats), fornisce estrattori, funzioni e outputter per gestire i documenti JSON e XML. L'assembly Microsoft.Analytics.Samples.Formats dipende da due assembly esistenti specifici di un dominio per eseguire l'elaborazione di JSON e XML rispettivamente. Questo assembly usa la libreria [Newtonsoft Json.Net](http://www.newtonsoft.com/) per elaborare documenti JSON e l'assembly [System](https://msdn.microsoft.com/data/bb291078.aspx) per elaborare contenuti XML. Possiamo usarlo per spiegare come registrare e usare gli assembly negli script.

Innanzitutto si scarica il [progetto di Visual Studio](https://github.com/Azure/usql/tree/master/Examples/DataFormats) nell'ambiente di sviluppo locale (ad esempio, realizzando una copia locale con lo strumento GitHub per Windows). Dopodiché, si apre la soluzione in Visual Studio e si fa clic sul progetto con il pulsante destro del mouse, come descritto in precedenza, per registrare l'assembly. Anche se questo assembly presenta due dipendenze, è necessario includere solo la dipendenza Newtonsoft, poiché System.Xml è già disponibile nella finestra di Azure Data Lake (anche se occorre farvi riferimento in modo esplicito). Nella Figura 6 viene denominato l'assembly (è anche possibile scegliere altro un nome senza punti) e viene aggiunta anche la DLL Newtonsoft. Ognuno dei due assembly verrà ora registrato singolarmente nel database specificato (ad esempio, JSONBlog).

![register-assembly](./media/data-lake-analytics-u-sql-programmability-guide/register-assembly.png)

**Figura 6**: come registrare l'assembly Microsoft.Analytics.Samples.Formats da Visual Studio
<br />

Se gli utenti con cui sono stati condivisi gli assembly registrati e che possono accedere al database in lettura desiderano usare la funzionalità JSON nei propri script personalizzati, dovranno aggiungere allo script i seguenti due riferimenti:

```
REFERENCE ASSEMBLY JSONBlog.[NewtonSoft.Json];
REFERENCE ASSEMBLY JSONBlog.[Microsoft.Analytics.Samples.Formats];
```

Se si intende usare la funzionalità XML, si aggiungono anche un riferimento all'assembly di sistema e un riferimento all'assembly registrato:

```
REFERENCE SYSTEM ASSEMBLY [System.Xml];
REFERENCE ASSEMBLY JSONBlog.[Microsoft.Analytics.Samples.Formats];
```

Per altre informazioni su come usare la funzionalità JSON, vedere [questo post di blog](https://blogs.msdn.microsoft.com/mrys/?p=755).

### <a name="registering-assemblies-that-use-native-c-assemblies-using-the-sql-server-2016-spatial-type-assembly-from-the-feature-pack"></a>Registrare assembly che usano assembly C++ nativi (usando l'assembly di tipo spaziale di SQL Server 2016 contenuto nel feature pack)
Ora esaminiamo uno scenario leggermente diverso. Si supponga che l'assembly che vogliamo usare presenti una dipendenza da codice non basato su .NET e, in particolare, da un assembly C++ nativo. Un esempio di tale assembly è l'assembly del tipo SQL Server [Microsoft.SqlServer.Types.dll](https://www.microsoft.com/download/details.aspx?id=52676) che fornisce le implementazioni basate su .NET dei tipi hierarchyID, geometry e geography di SQL Server da usare tramite le applicazioni SQL Server lato client per gestire i tipi di SQL Server (originariamente, prima del rilascio di SQL Server 2016, si trattava dell'assembly che fornisce l'implementazione per i tipi spaziali di SQL Server).

Ora vediamo come registrare l'assembly in U-SQL.

Innanzitutto, occorre scaricare e installare l'assembly dal [feature pack di SQL Server 2016](https://www.microsoft.com/download/details.aspx?id=52676). Selezionare la versione a 64 bit del programma di installazione (ENU\x64\SQLSysClrTypes.msi), per accertarsi di avere la versione a 64 bit delle librerie.

Il programma di installazione installa l'assembly gestito Microsoft.SqlServer.Types.dll in C:\Program Files (x86)\Microsoft SQL Server\130\SDK\Assemblies e l'assembly nativo SqlServerSpatial130.dll in \Windows\System32\.. A questo punto, si caricano gli assembly in Azure Data Lake Store (ad esempio, in una cartella denominata /upload/asm/spatial).

Poiché la libreria nativa viene installata automaticamente in C:\Windows\System32, prima di caricarla è necessario copiare SqlServerSpatial130.dll da questa cartella oppure verificare che lo strumento usato non esegua il [reindirizzamento del file system](https://msdn.microsoft.com/library/windows/desktop/aa384187(v=vs.85).aspx) delle cartelle di sistema. Ad esempio, se si desidera caricare la cartella con le risorse di Esplora file di ADL di Visual Studio, per prima cosa è necessario copiare il file in un'altra directory, altrimenti (alla stesura di questo blog) viene caricata la versione a 32 bit (poiché Visual Studio è un'applicazione a 32 bit, che esegue il reindirizzamento del file system nella relativa finestra ADL per il caricamento dei file selezionati) e, quando si esegue uno script U-SQL che chiama l'assembly nativo, si verifica il seguente errore (interno) in fase di runtime:

*Eccezione interna dall'espressione dell'utente: Tentativo di caricare un programma con un formato non corretto. (Eccezione da HRESULT: 0x8007000B)*

Dopo aver caricato i due file assembly, si procede a registrarli in un database SQLSpatial con lo script seguente:

```sql
DECLARE @ASSEMBLY_PATH string = "/upload/asm/spatial/";
DECLARE @SPATIAL_ASM string = @ASSEMBLY_PATH+"Microsoft.SqlServer.Types.dll";
DECLARE @SPATIAL_NATIVEDLL string = @ASSEMBLY_PATH+"SqlServerSpatial130.dll";

CREATE DATABASE IF NOT EXISTS SQLSpatial;
USE DATABASE SQLSpatial;

DROP ASSEMBLY IF EXISTS SqlSpatial;
CREATE ASSEMBLY SqlSpatial
FROM @SPATIAL_ASM
WITH ADDITIONAL_FILES =
     (
         @SPATIAL_NATIVEDLL
     );
```

In questo caso, occorre registrare solo un assembly U-SQL e includervi l'assembly nativo come dipendenza di stringa. Per usare gli assembly spaziali è sufficiente fare riferimento all'assembly U-SQL e il file aggiuntivo verrà automaticamente reso disponibile per l'assembly. Quello che segue è uno script semplice di esempio che usa l'assembly spaziale:

```sql
REFERENCE SYSTEM ASSEMBLY [System.Xml];
REFERENCE ASSEMBLY SQLSpatial.SqlSpatial;

USING Geometry = Microsoft.SqlServer.Types.SqlGeometry;
USING Geography = Microsoft.SqlServer.Types.SqlGeography;
USING SqlChars = System.Data.SqlTypes.SqlChars;

@spatial =
    SELECT * FROM (VALUES
                   // The following expression is not using the native DDL
                   ( Geometry.Point(1.0,1.0,0).ToString()),    
                   // The following expression is using the native DDL
                   ( Geometry.STGeomFromText(new SqlChars("LINESTRING (100 100, 20 180, 180 180)"), 0).ToString())
                  ) AS T(geom);

OUTPUT @spatial
TO "/output/spatial.csv"
USING Outputters.Csv();
```

La libreria di tipi SQL presenta una dipendenza dall'assembly System. XML, pertanto è necessario farvi riferimento. Inoltre, alcuni dei metodi usano i tipi di System.Data.SqlTypes, anziché i tipi C# incorporati. Poiché System.Data è già incluso per impostazione predefinita, è possibile fare riferimento solo al tipo SQL necessario. Il codice qui sopra è disponibile sul [sito Github](https://github.com/Azure/usql/tree/master/Examples/SQLSpatialExample).


### <a name="assembly-versioning-and-other-points"></a>Controllo delle versioni degli assembly e altri aspetti
Attualmente, U-SQL usa .NET Framework versione 4.5. Pertanto, assicurarsi che i propri assembly siano compatibili con tale versione di runtime.

Come già detto, U-SQL esegue il codice in un formato a 64 bit (x64), pertanto il codice deve essere compilato per l'esecuzione su sistemi x64. In caso contrario, viene visualizzato l'errore di formato non corretto di cui sopra.

Ogni DLL dell'assembly caricata, file di risorse come un runtime diverso, un assembly nativo o un file di configurazione e così via possono avere un massimo di 400 MB di dimensioni totali delle risorse distribuite tramite DEPLOY RESOURCE o tramite gli assembly di riferimento e i relativi file aggiuntivi non possono superare i 3 GB.

Infine, tenere presente che ogni database U-SQL può contenere solo una versione dell'assembly specificato. Ad esempio, se sono necessarie entrambe le versioni 7 e 8 della libreria NewtonSoft Json.Net, è necessario registrarli in due database diversi. Inoltre, ogni script può fare riferimento a una sola versione di una DLL di assembly. A tale riguardo, U-SQL segue la semantica di gestione e controllo delle versioni degli assembly di C#.


## <a name="user-defined-functions---udf"></a>Funzioni definite dall'utente (UDF)
Le funzioni definite dall'utente (o UDF) di U-SQL sono routine di programmazione che accettano parametri, eseguono un'azione, ad esempio un calcolo complesso, e restituiscono il risultato di tale azione come valore. Il valore restituito della funzione UDF può essere solo un valore scalare singolo. Una funzione UDF di U-SQL può essere chiamata nello script di base di U-SQL come qualsiasi altra funzione scalare di C#.

Le funzioni definite dall'utente U-SQL devono essere inizializzato come pubblico e **statico**.

```c#
        public static string MyFunction(string param1)
        {
            return "my result";
        }
```

Per prima cosa, si veda questo semplice esempio di creazione di una funzione UDF.

In questo scenario, è necessario determinare il periodo fiscale, ovvero il trimestre e il mese fiscale del primo accesso dell'utente in questione. In questo scenario il primo mese dell'anno fiscale è giugno.

Per calcolare il periodo di imposta, viene introdotta la funzione C# seguente

```c#
        public static string GetFiscalPeriod(DateTime dt)
        {
            int FiscalMonth=0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter=0;
            if (FiscalMonth >=1 && FiscalMonth<=3)
            {
                FiscalQuarter = 1;
            }
            if (FiscalMonth >= 4 && FiscalMonth <= 6)
            {
                FiscalQuarter = 2;
            }
            if (FiscalMonth >= 7 && FiscalMonth <= 9)
            {
                FiscalQuarter = 3;
            }
            if (FiscalMonth >= 10 && FiscalMonth <= 12)
            {
                FiscalQuarter = 4;
            }

            return "Q" + FiscalQuarter.ToString() + ":P" + FiscalMonth.ToString();
        }
```

In questo modo vengono semplicemente calcolati il mese e il trimestre fiscali e viene restituito un valore stringa. Per giugno (primo mese del trimestre fiscale) "Q1:P1", per luglio "Q1:P2" e così via.

Questa normale funzione C# verrà usata per tutto il progetto U-SQL.

Di seguito è illustrato l'aspetto della sezione code-behind in questo scenario

```c#
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace USQL_Programmability
{
    public class CustomFunctions
    {
        public static string GetFiscalPeriod(DateTime dt)
        {
            int FiscalMonth=0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter=0;
            if (FiscalMonth >=1 && FiscalMonth<=3)
            {
                FiscalQuarter = 1;
            }
            if (FiscalMonth >= 4 && FiscalMonth <= 6)
            {
                FiscalQuarter = 2;
            }
            if (FiscalMonth >= 7 && FiscalMonth <= 9)
            {
                FiscalQuarter = 3;
            }
            if (FiscalMonth >= 10 && FiscalMonth <= 12)
            {
                FiscalQuarter = 4;
            }

            return "Q" + FiscalQuarter.ToString() + ":" + FiscalMonth.ToString();
        }

    }

}
```

Ora si procede a chiamare la funzione dallo script U-SQL di base. A tale scopo, è necessario fornire un nome completo della funzione, incluso lo spazio dei nomi: SpazioDeiNomi.Classe.Funzione(parametro)

```sql
    USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)
```

Questo è l'effettivo script di base di U-SQL

```sql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid Guid,
        dt DateTime,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

DECLARE @default_dt DateTime = Convert.ToDateTime("06/01/2016");

@rs1 =
    SELECT
        MAX(guid) AS start_id,
        MIN(dt) AS start_time,
        MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
        MIN(USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)) AS start_fiscalperiod,
        user,
        des
    FROM @rs0
    GROUP BY user, des;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

Questo è il file di output che risulta dall'esecuzione dello script

```
0d8b9630-d5ca-11e5-8329-251efa3a2941,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User1",""

20843640-d771-11e5-b87b-8b7265c75a44,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User2",""

301f23d2-d690-11e5-9a98-4b4f60a1836f,2016-02-11T09:01:33.9720000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User3",""
```

In questo esempio viene illustrato un uso semplificato della funzione UDF inline in U-SQL.

### <a name="keeping-state-between-udf-invocations"></a>Mantenere lo stato tra le chiamate alla funzione UDF
Gli oggetti di programmazione C# di U-SQL possono essere resi più sofisticati introducendo l'interattività tramite variabili code-behind globali. Ora vediamo lo scenario di caso d'uso aziendale:

Nelle grandi organizzazioni gli utenti possono accedere a svariate applicazioni interne, ad esempio Microsoft Dynamics CRM, Power BI e così via. Il cliente desidera applicare un'analisi dei dati di telemetria su come gli utenti si spostano da un'applicazione all'altra, delle tendenze d'uso e così via. L'obiettivo finale per l'azienda è ottimizzare l'uso delle applicazioni, magari combinando diverse applicazioni o specifiche routine di accesso.

Per farlo, è necessario determinare l'ID delle varie sessioni e il tempo trascorso fra le ultime sessioni eseguite.

È necessario trovare un accesso precedente e quindi assegnarlo a tutte le sessioni generate per la stessa applicazione. Il primo problema è che lo script U-SQL di base non consente di applicare i calcoli sulla colonna già calcolata con la funzione LAG. Il secondo problema è che è necessario mantenere la sessione specifica per tutte le sessioni incluse nello stesso periodo di tempo.

Per risolvere questo problema, verrà usata una variabile globale all'interno della sezione code-behind: `static public string globalSession;`.

Questa variabile globale viene applicata all'intero set di righe durante l'esecuzione dello script.

Ecco la sezione code-behind del programma U-SQL

```c#
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace USQLApplication21
{
    public class UserSession
    {
        static public string globalSession;
        static public string StampUserSession(string eventTime, string PreviousRow, string Session)
        {

            if (!string.IsNullOrEmpty(PreviousRow))
            {
                double timeGap = Convert.ToDateTime(eventTime).Subtract(Convert.ToDateTime(PreviousRow)).TotalMinutes;
                if (timeGap <= 60)
                    return Session;
                else
                    return Guid.NewGuid().ToString();
            }
            else
                return Guid.NewGuid().ToString();

        }

        static public string getStampUserSession(string Session)
        {
            if (Session != globalSession && !string.IsNullOrEmpty(Session))
            {
                globalSession = Session;
            }
            return globalSession;
        }

    }
}
```

La variabile globale `static public string globalSession;` usata nella funzione `getStampUserSession` viene reinizializzata a ogni volta modifica del parametro di sessione.

Script U-SQL di base

```sql
DECLARE @in string = @"\UserSession\test1.tsv";
DECLARE @out1 string = @"\UserSession\Out1.csv";
DECLARE @out2 string = @"\UserSession\Out2.csv";
DECLARE @out3 string = @"\UserSession\Out3.csv";

@records =
    EXTRACT DataId string,
            EventDateTime string,           
            UserName string,
            UserSessionTimestamp string

    FROM @in
    USING Extractors.Tsv();

@rs1 =
    SELECT EventDateTime,
           UserName,
LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC) AS prevDateTime,          
           string.IsNullOrEmpty(LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)) AS Flag,           
           USQLApplication21.UserSession.StampUserSession
           (
               EventDateTime,
               LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC),
               LAG(UserSessionTimestamp, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)
           )
           AS UserSessionTimestamp
    FROM @records;

@rs2 =
    SELECT EventDateTime,
           UserName,
           LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC) AS prevDateTime,
           string.IsNullOrEmpty(LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)) AS Flag,
           USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp) AS UserSessionTimestamp
    FROM @rs1
    WHERE UserName != "UserName";

OUTPUT @rs2
TO @out2
ORDER BY UserName, EventDateTime ASC
USING Outputters.Csv();
```

Qui vene chiamata la funzione `USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp)` durante il secondo calcolo del set di righe della memoria. Questa funzione passa la colonna `UserSessionTimestamp` e restituisce il valore finché non viene modificato `UserSessionTimestamp`.

Il file di output

```
"2016-02-19T07:32:36.8420000-08:00","User1",,True,"72a0660e-22df-428e-b672-e0977007177f"
"2016-02-17T11:52:43.6350000-08:00","User2",,True,"4a0cd19a-6e67-4d95-a119-4eda590226ba"
"2016-02-17T11:59:08.8320000-08:00","User2","2016-02-17T11:52:43.6350000-08:00",False,"4a0cd19a-6e67-4d95-a119-4eda590226ba"
"2016-02-11T07:04:17.2630000-08:00","User3",,True,"51860a7a-1610-4f74-a9ea-69d5eef7cd9c"
"2016-02-11T07:10:33.9720000-08:00","User3","2016-02-11T07:04:17.2630000-08:00",False,"51860a7a-1610-4f74-a9ea-69d5eef7cd9c"
"2016-02-15T21:27:41.8210000-08:00","User3","2016-02-11T07:10:33.9720000-08:00",False,"4d2bc48d-bdf3-4591-a9c1-7b15ceb8e074"
"2016-02-16T05:48:49.6360000-08:00","User3","2016-02-15T21:27:41.8210000-08:00",False,"dd3006d0-2dcd-42d0-b3a2-bc03dd77c8b9"
"2016-02-16T06:22:43.6390000-08:00","User3","2016-02-16T05:48:49.6360000-08:00",False,"dd3006d0-2dcd-42d0-b3a2-bc03dd77c8b9"
"2016-02-17T16:29:53.2280000-08:00","User3","2016-02-16T06:22:43.6390000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-17T16:39:07.2430000-08:00","User3","2016-02-17T16:29:53.2280000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-17T17:20:39.3220000-08:00","User3","2016-02-17T16:39:07.2430000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-19T05:23:54.5710000-08:00","User3","2016-02-17T17:20:39.3220000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T05:48:37.7510000-08:00","User3","2016-02-19T05:23:54.5710000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T06:40:27.4830000-08:00","User3","2016-02-19T05:48:37.7510000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T07:27:37.7550000-08:00","User3","2016-02-19T06:40:27.4830000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T19:35:40.9450000-08:00","User3","2016-02-19T07:27:37.7550000-08:00",False,"3f385f0b-3e68-4456-ac74-ff6cef093674"
"2016-02-20T00:07:37.8250000-08:00","User3","2016-02-19T19:35:40.9450000-08:00",False,"685f76d5-ca48-4c58-b77d-bd3a9ddb33da"
"2016-02-11T09:01:33.9720000-08:00","User4",,True,"9f0cf696-c8ba-449a-8d5f-1ca6ed8f2ee8"
"2016-02-17T06:30:38.6210000-08:00","User4","2016-02-11T09:01:33.9720000-08:00",False,"8b11fd2a-01bf-4a5e-a9af-3c92c4e4382a"
"2016-02-17T22:15:26.4020000-08:00","User4","2016-02-17T06:30:38.6210000-08:00",False,"4e1cb707-3b5f-49c1-90c7-9b33b86ca1f4"
"2016-02-18T14:37:27.6560000-08:00","User4","2016-02-17T22:15:26.4020000-08:00",False,"f4e44400-e837-40ed-8dfd-2ea264d4e338"
"2016-02-19T01:20:31.4800000-08:00","User4","2016-02-18T14:37:27.6560000-08:00",False,"2136f4cf-7c7d-43c1-8ae2-08f4ad6a6e08"
```

In questo esempio viene illustrato uno scenario d'uso più complesso quando si usa una variabile globale nella sezione code-behind applicata all'intero set di righe della memoria.

## <a name="using-user-defined-types---udt"></a>Uso dei tipi definiti dall'utente (UDT)
I tipi definiti dall'utente (o UDT) è un'altra caratteristica di programmabilità di U-SQL. L'UDT U-SQL funziona come un normale tipo definito dall'utente C#. C# è un linguaggio fortemente tipizzato che consente l'uso di tipi incorporati e personalizzati definiti dall'utente.

U-SQL non può serializzare/deserializzare implicitamente i tipi definiti dall'utente in modo arbitrario mentre il tipo definito dall'utente viene passato dai vertici nei set di righe. In questo modo l'utente deve fornire un formattatore esplicito tramite l'interfaccia iFormatter. Questo fornirà a U-SQL i metodi di serializzazione e deserializzazione per il tipo definito dall'utente. 

> [!NOTE]
> Outputter ed estrattori incorporati di U-SQL non possono attualmente serializzare/deserializzare i dati UDT ai/dai file anche con il set iFormatter.  Pertanto, quando si esegue la scrittura dei dati del tipo definito dall'utente in un file con l'istruzione OUTPUT, o la relativa lettura con un estrattore, l'utente deve passarla come stringa o matrice di byte e chiamare in modo esplicito il codice di serializzazione e deserializzazione (ad esempio, il metodo ToString() del tipo definito dall'utente). Gli outputter e gli estrattori definiti dall'utente, dall'altra parte, possono leggere e scrivere tipi definiti dall'utente.

Se si tenta di usare UDT in EXTRACTOR o OUTPUTTER (fuori dall'istruzione SELECT precedente)

```sql
@rs1 =
    SELECT MyNameSpace.Myfunction_Returning_UDT(filed1) AS myfield
    FROM @rs0;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

viene visualizzato l'errore seguente

```
    Error    1    E_CSC_USER_INVALIDTYPEINOUTPUTTER: Outputters.Text was used to output column myfield of type
    MyNameSpace.Myfunction_Returning_UDT.

    Description:

    Outputters.Text only supports built-in types.

    Resolution:

    Implement a custom outputter that knows how to serialize this type or call a serialization method on the type in
    the preceding SELECT.    C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\
    USQL-Programmability\Types.usql    52    1    USQL-Programmability
```

Per lavorare con l'UDT in OUTPUTTER, è necessario serializzarlo nella stringa con il metodo ToString() e creare un OUTPUTTER personalizzato.

Al momento non è possibile usare UDT in GROUP BY. Se si usa l'UDT in GROUP BY, viene generato l'errore seguente:

```
    Error    1    E_CSC_USER_INVALIDTYPEINCLAUSE: GROUP BY doesn't support type MyNameSpace.Myfunction_Returning_UDT
    for column myfield

    Description:

    GROUP BY doesn't support UDT or Complex types.

    Resolution:

    Add a SELECT statement where you can project a scalar column that you want to use with GROUP BY.
    C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\USQL-Programmability\Types.usql
    62    5    USQL-Programmability
```

Per definire un UDT, è necessario:

* Aggiungere gli spazi dei nomi seguenti

```c#
    using Microsoft.Analytics.Interfaces
    using System.IO;
```

* `Microsoft.Analytics.Interfaces` è necessario per le interfacce UDT e `System.IO` può essere necessario per definire l'interfaccia IFormatter

* Definire l'UDT con l'attributo SqlUserDefinedType

**SqlUserDefinedType** è usato per contrassegnare la definizione di un tipo in un assembly come tipo definito dall'utente (UDT) in U-SQL. Le proprietà dell'attributo corrispondono alle caratteristiche fisiche dell'UDT. Questa classe non può essere ereditata.

SqlUserDefinedType è un attributo obbligatorio per la definizione dell'UDT.

Il costruttore della classe  

* SqlUserDefinedTypeAttribute(formattatore Type)

* Formattatore Type: parametro obbligatorio per definire un formattatore UDT. Nello specifico, qui è necessario passare il tipo dell'interfaccia `IFormatter`.

```c#
    [SqlUserDefinedType(typeof(MyTypeFormatter))]
      public class MyType
              {
             …
           }
```

* L'UDT tipico richiederà la definizione dell'interfaccia IFormatter

```c#
       public class MyTypeFormatter : IFormatter<MyType>
        {
            public void Serialize(MyType instance, IColumnWriter writer,
                           ISerializationContext context)
            {
        …
            }

            public MyType Deserialize(IColumnReader reader,
                                  ISerializationContext context)
            {
        …
            }
        }
```

Interfaccia `IFormatter` per serializzare e deserializzare un oggetto grafico con il tipo radice di \<typeparamref name="T">.

\<typeparam name="T"> il tipo radice per l'oggetto grafico da serializzare e deserializzare.

* Deserialize: consente di deserializzare i dati nel flusso fornito e di ricostruire il grafico degli oggetti.

* Serialize: consente di serializzare un oggetto o un grafico di oggetti con la radice specificata nel flusso fornito.

Istanza `MyType`: istanza del tipo  
Writer `IColumnWriter`/Reader `IColumnReader`: il flusso di colonna sottostante.  
Contesto `ISerializationContext`: enumerazione che definisce un set di contrassegni che specifica il contesto di origine o di destinazione relativo al flusso in fase di serializzazione. 
 
   * *Intermediate*: specifica che il contesto di origine o di destinazione non è un archivio permanente

   * *Persistence*: specifica che il contesto di origine o di destinazione è un archivio permanente

Come un normale tipo C#, la definizione UDT di U-SQL può includere gli override di operatori come +/==/!= e così via. Può includere metodi statici e via dicendo. Ad esempio, se si intende usare questo UDT come parametro nella funzione di aggregazione MIN U-SQL, è necessario definire l'override dell'operatore <.

In precedenza in questa guida è stato illustrato un esempio di identificazione del periodo fiscale dalla data specifica in formato Qn:Pn (Q1:P10). Nell'esempio seguente viene illustrato come definire un tipo personalizzato per i valori del periodo fiscale.

Apposita sezione code-behind con UDT e interfaccia IFormatter personalizzati:

```c#
        [SqlUserDefinedType(typeof(FiscalPeriodFormatter))]
        public struct FiscalPeriod
        {
            public int Quarter { get; private set; }

            public int Month { get; private set; }

            public FiscalPeriod(int quarter, int month):this()
            {
                this.Quarter = quarter;
                this.Month = month;
            }

            public override bool Equals(object obj)
            {
                if (ReferenceEquals(null, obj))
                {
                    return false;
                }

                return obj is FiscalPeriod && Equals((FiscalPeriod)obj);
            }

            public bool Equals(FiscalPeriod other)
            {
return this.Quarter.Equals(other.Quarter) && this.Month.Equals(other.Month);
            }

            public bool GreaterThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) > 0 || this.Month.CompareTo(other.Month) > 0;
            }

            public bool LessThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) < 0 || this.Month.CompareTo(other.Month) < 0;
            }

            public override int GetHashCode()
            {
                unchecked
                {
                    return (this.Quarter.GetHashCode() * 397) ^ this.Month.GetHashCode();
                }
            }

            public static FiscalPeriod operator +(FiscalPeriod c1, FiscalPeriod c2)
            {
return new FiscalPeriod((c1.Quarter + c2.Quarter) > 4 ? (c1.Quarter + c2.Quarter)-4 : (c1.Quarter + c2.Quarter), (c1.Month + c2.Month) > 12 ? (c1.Month + c2.Month) - 12 : (c1.Month + c2.Month));
            }

            public static bool operator ==(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.Equals(c2);
            }

            public static bool operator !=(FiscalPeriod c1, FiscalPeriod c2)
            {
                return !c1.Equals(c2);
            }
            public static bool operator >(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.GreaterThan(c2);
            }
            public static bool operator <(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.LessThan(c2);
            }
            public override string ToString()
            {
                return (String.Format("Q{0}:P{1}", this.Quarter, this.Month));
            }

        }

        public class FiscalPeriodFormatter : IFormatter<FiscalPeriod>
        {
public void Serialize(FiscalPeriod instance, IColumnWriter writer, ISerializationContext context)
            {
                using (var binaryWriter = new BinaryWriter(writer.BaseStream))
                {
                    binaryWriter.Write(instance.Quarter);
                    binaryWriter.Write(instance.Month);
                    binaryWriter.Flush();
                }
            }

public FiscalPeriod Deserialize(IColumnReader reader, ISerializationContext context)
            {
                using (var binaryReader = new BinaryReader(reader.BaseStream))
                {
var result = new FiscalPeriod(binaryReader.ReadInt16(), binaryReader.ReadInt16());
                    return result;
                }
            }
        }
```

Il tipo definito include due numeri, corrispondenti a trimestre e mese. Qui sono definiti gli operatori ==/!=/>/< e il metodo statico ToString ().

Come accennato in precedenza, l'UDT può essere usato nell'espressione SELECT, ma non in OUTPUTTER/EXTRACTOR senza serializzazione personalizzata. L'UDT deve essere serializzato come una stringa con ToString () oppure usato con i valori OUTPUTTER/EXTRACTOR personalizzati.

Ora esaminiamo l'uso dell'UDT. Nella sezione code-behind la funzione GetFiscalPeriod è stata modificata in

```c#
        public static FiscalPeriod GetFiscalPeriodWithCustomType(DateTime dt)
        {
            int FiscalMonth = 0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter = 0;
            if (FiscalMonth >= 1 && FiscalMonth <= 3)
            {
                FiscalQuarter = 1;
            }
            if (FiscalMonth >= 4 && FiscalMonth <= 6)
            {
                FiscalQuarter = 2;
            }
            if (FiscalMonth >= 7 && FiscalMonth <= 9)
            {
                FiscalQuarter = 3;
            }
            if (FiscalMonth >= 10 && FiscalMonth <= 12)
            {
                FiscalQuarter = 4;
            }

            return new FiscalPeriod(FiscalQuarter, FiscalMonth);
        }
```

Come si può notare, viene restituito il valore del tipo FiscalPeriod.

Ecco un esempio dell'uso ulteriore nello script U-SQL di base. In questo esempio vengono illustrati diversi metodi per chiamare l'UDT dallo script U-SQL.

```sql
DECLARE @input_file string = @"c:\work\cosmos\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"c:\work\cosmos\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid string,
        dt DateTime,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT guid AS start_id,
           dt,
           DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).Quarter AS fiscalquarter,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).Month AS fiscalmonth,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt) + new USQL_Programmability.CustomFunctions.FiscalPeriod(1,7) AS fiscalperiod_adjusted,
           user,
           des
    FROM @rs0;

@rs2 =
    SELECT start_id,
           dt,
           DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
           fiscalquarter,
           fiscalmonth,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).ToString() AS fiscalperiod,
       
       // This user-defined type was created in the prior SELECT.  Passing the UDT to this subsequent SELECT would have failed if the UDT was not annotated with an IFormatter.
           fiscalperiod_adjusted.ToString() AS fiscalperiod_adjusted,
           user,
           des
    FROM @rs1;

OUTPUT @rs2 TO @output_file USING Outputters.Text();
```

Sezione code-behind completo:

```c#
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.IO;

namespace USQL_Programmability
{
    public class CustomFunctions
    {
        static public DateTime? ToDateTime(string dt)
        {
            DateTime dtValue;

            if (!DateTime.TryParse(dt, out dtValue))
                return Convert.ToDateTime(dt);
            else
                return null;
        }

        public static FiscalPeriod GetFiscalPeriodWithCustomType(DateTime dt)
        {
            int FiscalMonth = 0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter = 0;
            if (FiscalMonth >= 1 && FiscalMonth <= 3)
            {
                FiscalQuarter = 1;
            }
            if (FiscalMonth >= 4 && FiscalMonth <= 6)
            {
                FiscalQuarter = 2;
            }
            if (FiscalMonth >= 7 && FiscalMonth <= 9)
            {
                FiscalQuarter = 3;
            }
            if (FiscalMonth >= 10 && FiscalMonth <= 12)
            {
                FiscalQuarter = 4;
            }

            return new FiscalPeriod(FiscalQuarter, FiscalMonth);
        }



        [SqlUserDefinedType(typeof(FiscalPeriodFormatter))]
        public struct FiscalPeriod
        {
            public int Quarter { get; private set; }

            public int Month { get; private set; }

            public FiscalPeriod(int quarter, int month):this()
            {
                this.Quarter = quarter;
                this.Month = month;
            }

            public override bool Equals(object obj)
            {
                if (ReferenceEquals(null, obj))
                {
                    return false;
                }

                return obj is FiscalPeriod && Equals((FiscalPeriod)obj);
            }

            public bool Equals(FiscalPeriod other)
            {
return this.Quarter.Equals(other.Quarter) &&    this.Month.Equals(other.Month);
            }

            public bool GreaterThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) > 0 || this.Month.CompareTo(other.Month) > 0;
            }

            public bool LessThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) < 0 || this.Month.CompareTo(other.Month) < 0;
            }

            public override int GetHashCode()
            {
                unchecked
                {
                    return (this.Quarter.GetHashCode() * 397) ^ this.Month.GetHashCode();
                }
            }

            public static FiscalPeriod operator +(FiscalPeriod c1, FiscalPeriod c2)
            {
return new FiscalPeriod((c1.Quarter + c2.Quarter) > 4 ? (c1.Quarter + c2.Quarter)-4 : (c1.Quarter + c2.Quarter), (c1.Month + c2.Month) > 12 ? (c1.Month + c2.Month) - 12 : (c1.Month + c2.Month));
            }

            public static bool operator ==(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.Equals(c2);
            }

            public static bool operator !=(FiscalPeriod c1, FiscalPeriod c2)
            {
                return !c1.Equals(c2);
            }
            public static bool operator >(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.GreaterThan(c2);
            }
            public static bool operator <(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.LessThan(c2);
            }
            public override string ToString()
            {
                return (String.Format("Q{0}:P{1}", this.Quarter, this.Month));
            }

        }

        public class FiscalPeriodFormatter : IFormatter<FiscalPeriod>
        {
public void Serialize(FiscalPeriod instance, IColumnWriter writer, ISerializationContext context)
            {
                using (var binaryWriter = new BinaryWriter(writer.BaseStream))
                {
                    binaryWriter.Write(instance.Quarter);
                    binaryWriter.Write(instance.Month);
                    binaryWriter.Flush();
                }
            }

public FiscalPeriod Deserialize(IColumnReader reader, ISerializationContext context)
            {
                using (var binaryReader = new BinaryReader(reader.BaseStream))
                {
var result = new FiscalPeriod(binaryReader.ReadInt16(), binaryReader.ReadInt16());
                    return result;
                }
            }
        }

    }
}
```

## <a name="user-defined-aggregates--udagg"></a>Aggregazioni definite dall'utente: UDAGG
Si definiscono "aggregazioni definite dall'utente" le funzioni correlate all'aggregazione che non sono già incluse in U-SQL. L'esempio può essere una funzione di aggregazione che esegue un calcolo matematico personalizzato, concatenazioni di stringa o modifiche nelle stringhe e così via.

La definizione di classe aggregata di base definita dall'utente è

```c#
    [SqlUserDefinedAggregate]
    public abstract class IAggregate<T1, T2, TResult> : IAggregate
    {
        protected IAggregate();

        public abstract void Accumulate(T1 t1, T2 t2);
        public abstract void Init();
        public abstract TResult Terminate();
    }
```

**SqlUserDefinedAggregate** indica che il tipo deve essere registrato come aggregazione definita dall'utente. Questa classe non può essere ereditata.

L'attributo SqlUserDefinedType è **facoltativo** per la definizione UDAGG


La classe di base consente di passare tre parametri astratti: due sono di input e uno è il risultato. I tipi di dati sono variabili e devono essere definiti quando viene ereditata la classe.

```c#
    public class GuidAggregate : IAggregate<string, string, string>
    {
        string guid_agg;

        public override void Init()
        {
        …
        }

        public override void Accumulate(string guid, string user)
        {
        …
        }

        public override string Terminate()
        {
        …
        }

    }
```

* Init fornisce la routine di inizializzazione per ogni gruppo di aggregazione. Durante l'elaborazione, la chiamata viene eseguita una sola volta per ogni gruppo.
* Accumulate: viene eseguito una volta per ogni valore. Fornisce la funzionalità principale per l'algoritmo di aggregazione. Consente di aggregare i valori con vari tipi di dati definiti quando viene ereditata la classe. Può accettare due parametri dei tipi di dati della variabile.
* Terminate: viene eseguito una volta per ogni gruppo di aggregazione al termine dell'elaborazione per restituire il risultato relativo a ogni gruppo

Per dichiarare tipi di dati di input e output corretti, usare la definizione di classe

```c#
    public abstract class IAggregate<T1, T2, TResult> : IAggregate
```

* T1: primo parametro in Accumulate
* T2: primo parametro in Accumulate
* TResult: il tipo restituito di Terminate

ad esempio

```c#
    public class GuidAggregate : IAggregate<string, int, int>
```

oppure

```c#
    public class GuidAggregate : IAggregate<string, string, string>
```

### <a name="using-udagg-in-u-sql"></a>Uso di UDAGG in U-SQL
Per usare UDAGG, per prima cosa occorre definirlo in code-behind oppure farvi riferimento dalla DLL di programmabilità esistente, come descritto in precedenza.

Usare quindi la sintassi seguente

```c#
    AGG<UDAGG_functionname>(param1,param2)
```

Quello che segue è un esempio di UDAGG

```c#
    public class GuidAggregate : IAggregate<string, string, string>
    {
        string guid_agg;

        public override void Init()
        {
            guid_agg = "";
        }

        public override void Accumulate(string guid, string user)
        {
            if (user.ToUpper()== "USER1")
            {
                guid_agg += "{" + guid + "}";
            }
        }

        public override string Terminate()
        {
            return guid_agg;
        }

    }
```

e lo script U-SQL

```sql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @" \usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid string,
        dt DateTime,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT
        user,
        AGG<USQL_Programmability.GuidAggregate>(guid,user) AS guid_list
    FROM @rs0
    GROUP BY user;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

In questo scenario di caso d'uso, vengono concatenati i GUID delle classi per gli utenti specifici.

## <a name="user-defined-objects--udo"></a>Oggetti definiti dall'utente: UDO
U-SQL consente di definire oggetti di programmabilità personalizzati, denominati oggetti definiti dall'utente o UDO.

L'elenco seguente mostra gli oggetti UDO in U-SQL

* Estrattori definiti dall'utente
    * Estrazione riga per riga
    * Viene usato per implementare l'estrazione di dati da file strutturati personalizzati

* Outputter definiti dall'utente
    * Output riga per riga
    * Viene usato per i tipi di dati di output o per il formato di file personalizzato

* Processori definiti dall'utente
    * Viene usato per richiedere una riga e produrre una riga
    * Serve a ridurre il numero di colonne o produrre una nuova colonna con i valori derivati da set di colonne esistente

* Oggetti di applicazione definiti dall'utente
    * Serve a richiedere una riga e produrre da 0 a n righe
    * Viene usato con OUTER/CROSS APPLY

* Combinatori definiti dall'utente
    * Vengono combinati i set di righe: JOIN definiti dall'utente

* Riduttori definiti dall'utente
    * Serve a richiedere n righe e produrre una riga
    * Viene usato per ridurre il numero di righe

Oggetto UDO generalmente chiamato in modo esplicito nello script U-SQL delle istruzioni U-SQL seguenti:

* EXTRACT
* OUTPUT
* PROCESS
* COMBINE
* REDUCE

## <a name="user-defined-extractor"></a>Estrattore definito dall'utente
U-SQL consente di importare i dati esterni tramite l'istruzione EXTRACT. L'istruzione EXTRACT consente di usare estrattori UDO incorporati  

* *Extractors.Text()*: consente di estrarre da file di testo delimitati di varie codifiche.

* *Extractors.Csv()*: consente di estrarre da file di testo delimitati da virgole (CSV) di varie codifiche.

* *Extractors.Tsv()*: consente di estrarre da file di testo delimitati da tabulazioni (TSV) di varie codifiche.

Può essere utile per sviluppare un estrattore personalizzato, che può servire durante un'eventuale importazione di dati

* Modificare i dati di input suddividendo le colonne e modificando i singoli valori. La combinazione delle colonne riesce meglio usando la funzionalità PROCESSOR.
* Analizzare dati non strutturati, ad esempio pagine Web, messaggi e-mail o dati in parte non strutturati, come XML/JSON.
* Analizzare i dati in una codifica non supportata

Per definire un estrattore definito dall'utente, o UDE, occorre creare un'interfaccia `IExtractor`. Tutti i parametri di input inviati all'estrattore, come i delimitatori di riga/colonna, la codifica e così via, devono essere definiti nel costruttore della classe. L'interfaccia `IExtractor` deve inoltre contenere la definizione dell'override `IEnumerable<IRow>`

```c#
     [SqlUserDefinedExtractor]
     public class SampleExtractor : IExtractor
     {
         public SampleExtractor(string row_delimiter, char col_delimiter)
         {
            …

         }

         public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
         {
             …
         }
     }
```

L'attributo **SqlUserDefinedExtractor** indica che il tipo deve essere registrato come estrattore definito dall'utente. Questa classe non può essere ereditata.

SqlUserDefinedExtractor è un attributo facoltativo per la definizione di UDE, che consente di definire la proprietà AtomicFileProcessing dell'oggetto UDE.

* bool     AtomicFileProcessing   

* **true** = indica che questo estrattore richiede file di input atomici (JSON, XML, ...)
* **false** = indica che questo estrattore può gestire file suddivisi/distribuiti (CSV, SEQ, ...)

I principali oggetti di programmabilità UDE sono *input* e *output*. L'oggetto di input viene usato per enumerare i dati di input come `IUnstructuredReader` e l'*output*, per impostare i dati di output come risultato dell'attività di estrazione.

I dati di input sono accessibili tramite `System.IO.Stream` e `System.IO.StreamReader`.

Per l'enumerazione delle colonne di input, innanzitutto si procede a suddividere il flusso di input usando il delimitatore di riga

```c#
    foreach (Stream current in input.Split(my_row_delimiter))
    {
    …
    }
```

Quindi, la riga di input viene ulteriormente suddivisa nelle parti di una colonna.

```c#
    foreach (Stream current in input.Split(my_row_delimiter))
    {
    …
        string[] parts = line.Split(my_column_delimiter);
               foreach (string part in parts)
        {
        …
        }
    }
```

Per impostare dati di output, viene usato il metodo `output.Set`

È importante tenere presente che l'estrattore personalizzato restituirà come output solo le colonne o i valori definiti con il metodo di chiamata output.Set.

```c#
    output.Set<string>(count, part);
```

L'output di estrazione effettivo viene attivato con una chiamata a `yield return output.AsReadOnly();`.

Di seguito è riportato l'esempio di estrattore

```c#
    [SqlUserDefinedExtractor(AtomicFileProcessing = true)]
    public class FullDescriptionExtractor : IExtractor
    {
         private Encoding _encoding;
         private byte[] _row_delim;
         private char _col_delim;

        public FullDescriptionExtractor(Encoding encoding, string row_delim = "\r\n", char col_delim = '\t')
        {
             this._encoding = ((encoding == null) ? Encoding.UTF8 : encoding);
             this._row_delim = this._encoding.GetBytes(row_delim);
             this._col_delim = col_delim;

        }

        public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
        {
             string line;
             //Read the input line by line
             foreach (Stream current in input.Split(_encoding.GetBytes("\r\n")))
             {
                using (System.IO.StreamReader streamReader = new StreamReader(current, this._encoding))
                 {
                     line = streamReader.ReadToEnd().Trim();
                     //Split the input by the column delimiter
                     string[] parts = line.Split(this._col_delim);
                     int count = 0; // start with first column
                     foreach (string part in parts)
                     {
    if (count == 0)
                         {  // for column “guid”, re-generated guid
                             Guid new_guid = Guid.NewGuid();
                             output.Set<Guid>(count, new_guid);
                         }
                         else if (count == 2)
                         {
                             // for column “user”, convert to UPPER case
                             output.Set<string>(count, part.ToUpper());

                         }
                         else
                         {
                             // keep the rest of the columns as-is
                             output.Set<string>(count, part);
                         }
                         count += 1;
                     }

                 }
                 yield return output.AsReadOnly();
             }
             yield break;
         }
     }
```

In questo scenario, l'estrattore rigenera il GUID per la colonna "guid" e converte i valori della colonna "user" in lettere MAIUSCOLE. Gli estrattori personalizzati possono produrre risultati più complessi analizzando e modificando i dati di input.

Script U-SQL di base che usa un programma di estrazione personalizzato

```sql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid Guid,
        dt String,
        user String,
        des String
    FROM @input_file
    USING new USQL_Programmability.FullDescriptionExtractor(Encoding.UTF8);

OUTPUT @rs0 TO @output_file USING Outputters.Text();
```

## <a name="user-defined-outputter"></a>Outputter definito dall'utente
L'outputter definito dall'utente è un altro UDO di U-SQL che consente di estendere una funzionalità di U-SQL integrata. Come per l'estrattore, esistono diversi outputter integrati.

* *Outputters.Text()*: scrive i dati in file di testo delimitati di codifiche diverse.
* *Outputters.Csv()*: scrive i dati in file di testo delimitati da virgole (CSV) di codifiche diverse.
* *Outputters.Tsv()*: scrive i dati in file di testo delimitati da tabulazioni (TSV) di codifiche diverse.

L'outputter personalizzato consente di scrivere i dati in un formato definito personalizzato. Ciò può essere utile per

* Scrivere i dati in file non strutturati o semistrutturati
* Scrivere i dati in codifiche non supportate
* Modificare i dati di output o aggiungere attributi personalizzati

Per definire outputter definiti dall'utente, è necessario creare un'interfaccia `IOutputter`

Implementazione della classe `IOutputter` di base

```c#
    public abstract class IOutputter : IUserDefinedOperator
    {
        protected IOutputter();

        public virtual void Close();
        public abstract void Output(IRow input, IUnstructuredWriter output);
    }
```

Tutti i parametri di input diretti all'outputter, come i delimitatori di riga/colonna, la codifica e così via, devono essere definiti nel costruttore della classe. L'interfaccia `IOutputter` deve inoltre contenere la definizione dell'override `void Output`. Facoltativamente, è possibile impostare l'attributo `[SqlUserDefinedOutputter(AtomicFileProcessing = true)` per l'elaborazione atomica di file; vedere di seguito per i dettagli.

```c#
        [SqlUserDefinedOutputter(AtomicFileProcessing = true)]
        public class MyOutputter : IOutputter
        {

            public MyOutputter(myparam1, myparam2)
            {
              …
            }

            public override void Close()
            {
              …
            }

            public override void Output(IRow row, IUnstructuredWriter output)
            {
              …
            }
        }
```

* `Output` chiamato per ogni riga di input. Restituisce il set di righe `IUnstructuredWriter output`
* La classe del costruttore viene usata per passare parametri all'outputter definito dall'utente
* `Close` eseguire l'override per sapere quando è stata scritta l'ultima riga o costoso dello stato di rilascio

L'attributo **SqlUserDefinedOutputter** indica che il tipo deve essere registrato come outputter definito dall'utente. Questa classe non può essere ereditata.

SqlUserDefinedOutputter è un attributo facoltativo per la definizione dell'outputter definito dall'utente. Consente di definire la proprietà AtomicFileProcessing.

* bool     AtomicFileProcessing   

* **true** = indica che questo outputter richiede i file di output atomico (JSON, XML, ...)
* **false** = indica che questo outputter può gestire i file suddivisi/distribuiti (CSV, SEQ, ...)

I principali oggetti di programmabilità sono *row* e *output*. L'oggetto *row* viene usato per enumerare i dati di output come interfaccia `IRow`, mentre *output* viene usato per impostare i dati di output nel file di destinazione.

I dati di output sono accessibili tramite l'interfaccia `IRow`. I dati di output vengono trasmessi una riga alla volta.

I singoli valori sono elencati chiamando il metodo Get dell'interfaccia IRow.

```c#
    row.Get<string>("column_name")
```

I nomi delle singole colonne possono essere determinati chiamando `row.Schema`

```c#
    ISchema schema = row.Schema;
    var col = schema[i];
    string val = row.Get<string>(col.Name)
```

Questo approccio consente di compilare un outputter flessibile per qualsiasi schema di metadati.

I dati di output sono scritti in un file usando `System.IO.StreamWriter` con il parametro di flusso impostato su `output.BaseStrea` come parte di `IUnstructuredWriter output`

La nota importante: per eseguire il flushing del buffer di dati in un file dopo ogni iterazione di riga, l'oggetto `StreamWriter` deve essere usato con l'attributo Disposable abilitato (impostazione predefinita) e con l'*uso* di una parola chiave

```c#
    using (StreamWriter streamWriter = new StreamWriter(output.BaseStream, this._encoding))
            {
        …
            }
```

In caso contrario, chiamare il metodo Flush() in modo esplicito dopo ogni iterazione, come illustrato più avanti in questo esempio:

### <a name="setting-header-and-footer-for-user-defined-outputter"></a>Impostazione di intestazione e piè di pagina per l'outputter definito dall'utente
Per impostare un'intestazione, usare il flusso di esecuzione dell'iterazione singola.

```c#
            public override void Output(IRow row, IUnstructuredWriter output)
            {
             …
                if (isHeaderRow)
                {
                    …                
                }

             …
                if (isHeaderRow)
                {
                    isHeaderRow = false;
                }
             …
            }
        }
```

Il codice nel primo blocco `if (isHeaderRow)` viene eseguito una sola volta.

Per il piè di pagina, usare il riferimento all'istanza dell'oggetto `System.IO.Stream` (`output.BaseStream`) e scrivere il piè di pagina nel metodo Close() dell'interfaccia `IOutputter` (vedere l'esempio di seguito).

Quello che segue è un esempio di outputter definito dall'utente

```c#
        [SqlUserDefinedOutputter(AtomicFileProcessing = true)]
        public class HTMLOutputter : IOutputter
        {
            // Local variables initialization
            private string row_delimiter;
            private char col_delimiter;
            private bool isHeaderRow;
            private Encoding encoding;
            private bool IsTableHeader = true;
            private Stream g_writer;

            // Parameters definition            
            public HTMLOutputter(bool isHeader = false, Encoding encoding = null)
            {
                this.isHeaderRow = isHeader;
                this.encoding = ((encoding == null) ? Encoding.UTF8 : encoding);
            }

            // Close method is used to write footer to file - executed only once after all rows
            public override void Close()
            {
                //Reference to IO.Stream object - g_writer
                StreamWriter streamWriter = new StreamWriter(g_writer, this.encoding);
                streamWriter.Write("</table>");
                streamWriter.Flush();
                streamWriter.Close();
            }

            public override void Output(IRow row, IUnstructuredWriter output)
            {
                System.IO.StreamWriter streamWriter = new StreamWriter(output.BaseStream, this.encoding);

                // Metadata schema initialization to enumerate column names
                ISchema schema = row.Schema;

                // This is data independent header - HTML table definition
                if (IsTableHeader)
                {
                    streamWriter.Write("<table border=1>");
                    IsTableHeader = false;
                }

                // HTML table attributes
                string header_wrapper_on = "<th>";
                string header_wrapper_off = "</th>";
                string data_wrapper_on = "<td>";
                string data_wrapper_off = "</td>";

                // Header row output - runs only once
                if (isHeaderRow)
                {
                    streamWriter.Write("<tr>");
                    for (int i = 0; i < schema.Count(); i++)
                    {
                        var col = schema[i];
                        streamWriter.Write(header_wrapper_on + col.Name + header_wrapper_off);
                    }
                    streamWriter.Write("</tr>");
                }

                // Data row output
                streamWriter.Write("<tr>");                
                for (int i = 0; i < schema.Count(); i++)
                {
                    var col = schema[i];
                    string val = "";
                    try
                    {
                        // Data type enumeration - require to match the distinct list of types form OUTPUT statement
                        switch (col.Type.Name.ToString().ToLower())
                        {
                            case "string": val = row.Get<string>(col.Name).ToString(); break;
                            case "guid": val = row.Get<Guid>(col.Name).ToString(); break;
                            default: break;
                        }
                    }
                    // Hnadling NULL values - keeping them empty
                    catch (System.NullReferenceException)
                    {
                    }
                    streamWriter.Write(data_wrapper_on + val + data_wrapper_off);
                }
                streamWriter.Write("</tr>");

                if (isHeaderRow)
                {
                    isHeaderRow = false;
                }
                // Reference to the instance of the IO.Stream object for footer generation
                g_writer = output.BaseStream;
                streamWriter.Flush();
            }
        }

        // Define the factory classes
        public static class Factory
        {
            public static HTMLOutputter HTMLOutputter(bool isHeader = false, Encoding encoding = null)
            {
                return new HTMLOutputter(isHeader, encoding);
            }
        }
```

e questo è uno script U-SQL di base

```sql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.html";

@rs0 =
    EXTRACT
        guid Guid,
        dt String,
        user String,
        des String
    FROM @input_file
    USING new USQL_Programmability.FullDescriptionExtractor(Encoding.UTF8);

OUTPUT @rs0 TO @output_file USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

Questo outputter HTML crea un file HTML con i dati della tabella.

### <a name="calling-outputter-from-u-sql-base-script"></a>Chiamare l'outputter dallo script U-SQL di base
Per chiamare un outputter personalizzato dallo script U-SQL di base, è necessario creare la nuova istanza dell'oggetto outputter.

```sql
OUTPUT @rs0 TO @output_file USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

Per evitare di creare un'istanza dell'oggetto nello script di base, è possibile creare un wrapper di funzione. Dall'esempio precedente

```c#
        // Define the factory classes
        public static class Factory
        {
            public static HTMLOutputter HTMLOutputter(bool isHeader = false, Encoding encoding = null)
            {
                return new HTMLOutputter(isHeader, encoding);
            }
        }
```

In questo caso, la chiamata originale ha il seguente aspetto

```sql
OUTPUT @rs0 TO @output_file USING USQL_Programmability.Factory.HTMLOutputter(isHeader: true);
```

## <a name="user-defined-processor"></a>Processore definito dall'utente
Si definisce "processore definito dall'utente" o UDP un tipo di UDO di U-SQL che consente di elaborare le righe in ingresso applicando funzionalità di programmabilità. UDP consente di combinare colonne, modificare valori e aggiungere nuove colonne, se necessario. Essenzialmente, consente di elaborare un set di righe per produrre gli elementi di dati necessari.

Per definire un UDP, è necessario creare un'interfaccia `IProcessor` con l'attributo `SqlUserDefinedProcessor`, che per gli UDP è facoltativo.

Questa interfaccia deve contenere la definizione per l'override di set di righe dell'interfaccia `IRow`

```c#
    [SqlUserDefinedProcessor]
     public class MyProcessor: IProcessor
     {
        public override IRow Process(IRow input, IUpdatableRow output)
         {
            …
         }
     }
```

**SqlUserDefinedProcessor** indica che il tipo deve essere registrato come Processor definito dall'utente. Questa classe non può essere ereditata.

Per la definizione dell'UDP, l'attributo SqlUserDefinedProcessor è **facoltativo**

I principali oggetti di programmabilità sono *input* e *output*. L'oggetto di input serve a enumerare le colonne di input e l'output, per impostare i dati come risultato dell'attività Processor.

Per l'enumerazione delle colonne di input, viene usato il metodo `input.Get`

```c#
    string column_name = input.Get<string>("column_name");
```

Il parametro del metodo `input.Get` è una colonna passata come parte della clausola `PRODUCE` dell'istruzione ` PROCESS` dello script U-SQL di base. In questo caso è necessario usare il tipo di dati corretto.

Per l'output: metodo output.Set

È importante tenere presente che il producer personalizzato restituirà come valore solo le colonne o i valori definiti con la chiamata al metodo output.Set.

```c#
    output.Set<string>("mycolumn", mycolumn);
```

L'effettivo output di Processor viene attivato chiamando `return output.AsReadOnly()`.

Quello che segue è un esempio di Processor.

```c#
     [SqlUserDefinedProcessor]
     public class FullDescriptionProcessor : IProcessor
     {
        public override IRow Process(IRow input, IUpdatableRow output)
         {
             string user = input.Get<string>("user");
             string des = input.Get<string>("des");
             string full_description = user.ToUpper() + "=>" + des;
             output.Set<string>("dt", input.Get<string>("dt"));
             output.Set<string>("full_description", full_description);
             output.Set<Guid>("new_guid", Guid.NewGuid());
             output.Set<Guid>("guid", input.Get<Guid>("guid"));
             return output.AsReadOnly();
         }
     }
```

In questo scenario di caso d'uso il processore genera la nuova colonna "full_description", combinando quelle esistenti: "user" in lettere maiuscole e "des". Viene inoltre generato un GUID e vengono restituiti i valori GUID nuovo e originale.

Come si vede qui sopra, è possibile chiamare i metodi C# durante la chiamata output.Set.

Script U-SQL di base che usa un processore personalizzato

```sql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid Guid,
        dt String,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
     PROCESS @rs0
     PRODUCE dt String,
             full_description String,
             guid Guid,
             new_guid Guid
     USING new USQL_Programmability.FullDescriptionProcessor();

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

## <a name="user-defined-applier"></a>Oggetto di applicazione definito dall'utente
L'oggetto di applicazione U-SQL definito dall'utente consente di richiamare una funzione C# personalizzata per ogni riga restituita dall'espressione di tabella esterna di una query. L'input di destra viene valutato per ogni riga dell'input di sinistra e le righe prodotte vengono combinate per l'output finale. L'elenco di colonne prodotte dall'operatore APPLY è la combinazione del set di colonne di input di destra e di sinistra.

L'oggetto di applicazione definito dall'utente viene richiamato come parte dell'espressione U-SQL SELECT.

La chiamata all'oggetto di applicazione definito dall'utente è generalmente simile alla seguente

```sql
    SELECT …
    FROM …
    CROSS APPLY
    new MyScript.MyApplier(param1, param2) AS alias(output_param1 string, …);
```

Per altre informazioni sull'uso di applier nell'espressione SELECT, vedere [U-SQL SELECT Selecting from CROSS APPLY and OUTER APPLY](https://msdn.microsoft.com/library/azure/mt621307.aspx) (Selezionare da CROSS APPLY e OUTER APPLY tramite U-SQL SELECT).

La definizione di classe dell'oggetto di applicazione di base definita dall'utente è

```c#
    public abstract class IApplier : IUserDefinedOperator
    {
        protected IApplier();

        public abstract IEnumerable<IRow> Apply(IRow input, IUpdatableRow output);
    }
```

Per definire un oggetto di applicazione definito dall'utente, è necessario creare un'interfaccia `IApplier` con attributo [`SqlUserDefinedCombiner`], facoltativo per la definizione dell'oggetto di applicazione definito dall'utente.

```c#
    [SqlUserDefinedApplier]
    public class ParserApplier : IApplier
    {

        public ParserApplier()
        {
        …
        }

        public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
        {
        …
        }
    }
```

* Apply viene chiamato per ogni riga della tabella esterna. Restituisce il set di righe di output `IUpdatableRow`
* La classe del costruttore viene usata per passare i parametri all'oggetto di applicazione definito dall'utente

**SqlUserDefinedApplier** indica che il tipo deve essere registrato come oggetto di applicazione definito dall'utente. Questa classe non può essere ereditata.

L'attributo SqlUserDefinedApplier è **facoltativo** per la definizione dell'oggetto di applicazione definito dall'utente


I principali oggetti di programmazione sono

```c#
        public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
```

I set di righe di input vengono passati come input `IRow`. Le righe di output vengono generate come interfaccia output `IUpdatableRow`

I nomi delle singole colonne possono essere determinati chiamando il metodo dello schema `IRow`.

```c#
    ISchema schema = row.Schema;
    var col = schema[i];
    string val = row.Get<string>(col.Name)
```

Per ottenere i valori effettivi dei dati dal valore `IRow` in ingresso, viene usato il metodo Get() dell'interfaccia `IRow`

```c#
    mycolumn = row.Get<int>("mycolumn")
```

oppure il nome di colonna dello schema

```c#
    row.Get<int>(row.Schema[0].Name)
```

I valori di output devono essere impostati con output `IUpdatableRow`

```c#
    output.Set<int>("mycolumn", mycolumn)
```

È importante tenere presente che l'oggetto di applicazione personalizzato restituirà come valore solo le colonne o i valori definiti con la chiamata al metodo output.Set.

L'effettivo output viene attivato chiamando `yield return output.AsReadOnly()`.

I parametri dell'oggetto di applicazione definito dall'utente possono essere passati al costruttore. L'oggetto di applicazione può restituire un numero variabile di colonne da definire durante la chiamata all'oggetto di applicazione nello script U-SQL di base.

```c#
  new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);
```

Quello che segue è un esempio di oggetto di applicazione definito dall'utente:

```c#
   [SqlUserDefinedApplier]
    public class ParserApplier : IApplier
    {
        private string parsingPart;

        public ParserApplier(string parsingPart)
        {
            if (parsingPart.ToUpper().Contains("ALL")
                || parsingPart.ToUpper().Contains("MAKE")
                || parsingPart.ToUpper().Contains("MODEL")
                || parsingPart.ToUpper().Contains("YEAR")
                || parsingPart.ToUpper().Contains("TYPE")
                || parsingPart.ToUpper().Contains("MILLAGE")
                )
            {
                this.parsingPart = parsingPart;
            }
            else
            {
                throw new ArgumentException("Incorrect parameter. Please use: 'ALL[MAKE|MODEL|TYPE|MILLAGE]'");
            }
        }

        public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
        {

            string[] properties = input.Get<string>("properties").Split(',');

            //  only process with correct number of properties
            if (properties.Count() == 5)
            {

                string make = properties[0];
                string model = properties[1];
                string year = properties[2];
                string type = properties[3];
                int millage = -1;

                // Only return millage if it is number, otherwise, -1
                if (!int.TryParse(properties[4], out millage))
                {
                    millage = -1;
                }

                if (parsingPart.ToUpper().Contains("MAKE") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("make", make);
                if (parsingPart.ToUpper().Contains("MODEL") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("model", model);
                if (parsingPart.ToUpper().Contains("YEAR") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("year", year);
                if (parsingPart.ToUpper().Contains("TYPE") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("type", type);
                if (parsingPart.ToUpper().Contains("MILLAGE") || parsingPart.ToUpper().Contains("ALL")) output.Set<int>("millage", millage);
            }
            yield return output.AsReadOnly();            
        }
    }
```

Di seguito è invece illustrato lo script U-SQL di base per questo oggetto di applicazione definito dall'utente

```sql
DECLARE @input_file string = @"c:\usql-programmability\car_fleet.tsv";
DECLARE @output_file string = @"c:\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        stocknumber int,
        vin String,
        properties String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT
        r.stocknumber,
        r.vin,
        properties.make,
        properties.model,
        properties.year,
        properties.type,
        properties.millage
    FROM @rs0 AS r
    CROSS APPLY
    new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

In questo scenario d'uso l'oggetto di applicazione definito dall'utente funge da parser di valori delimitati da virgole per le proprietà del parco auto. Le righe del file di input si presentano così:

```
103    Z1AB2CD123XY45889    Ford,Explorer,2005,SUV,152345
303    Y0AB2CD34XY458890    Shevrolet,Cruise,2010,4Dr,32455
210    X5AB2CD45XY458893    Nissan,Altima,2011,4Dr,74000
```

Si tratta di un normale file TSV delimitato da tabulazioni, la cui colonna delle proprietà contiene le proprietà delle automobili, come la marca, il modello e così via. Tali proprietà devono essere analizzate rispetto alle colonne della tabella. L'oggetto di applicazione fornito consente inoltre di generare un numero dinamico di proprietà nel set di righe di risultati, in base al parametro passato (per tutti o solo per specifici set di proprietà).

    …USQL_Programmability.ParserApplier ("all")
    …USQL_Programmability.ParserApplier ("make")
    …USQL_Programmability.ParserApplier ("make&model")

L'oggetto di applicazione definito dall'utente può essere chiamato come una nuova istanza dell'oggetto di applicazione

```c#
    CROSS APPLY new MyNameSpace.MyApplier (parameter: “value”) AS alias([columns types]…);
```

o con la chiamata a un metodo factory wrapper

```c#
    CROSS APPLY MyNameSpace.MyApplier (parameter: “value”) AS alias([columns types]…);
```

## <a name="user-defined-combiner"></a>Combinatore definito dall'utente
Il combinatore definito dall'utente o UDC consente di combinare le righe dei set di sinistra e destra, in base a una logica personalizzata. Il combinatore definito dall'utente viene usato con l'espressione COMBINE.

Con l'espressione COMBINE viene richiamato un combinatore che fornisce le informazioni necessarie sul set di righe di input, le colonne di raggruppamento, lo schema dei risultati previsto e altre informazioni aggiuntive.

Per chiamare una funzione di combinazione in uno script U-SQL di base, viene usata la sintassi seguente

```sql
Combine_Expression :=
    'COMBINE' Combine_Input
    'WITH' Combine_Input
    Join_On_Clause
    Produce_Clause
    [Readonly_Clause]
    [Required_Clause]
    USING_Clause.
```

Per altre informazioni, vedere [COMBINE Expression (U-SQL)](https://msdn.microsoft.com/library/azure/mt621339.aspx) (Espressione COMBINE (U-SQL)).

Per definire un combinatore definito dall'utente, è necessario creare un'interfaccia `ICombiner` con attributo [`SqlUserDefinedCombiner`], facoltativo per la definizione del combinatore definito dall'utente.

Definizione della classe `ICombiner` di base

```c#
    public abstract class ICombiner : IUserDefinedOperator
    {
        protected ICombiner();
        public virtual IEnumerable<IRow> Combine(List<IRowset> inputs,
               IUpdatableRow output);
        public abstract IEnumerable<IRow> Combine(IRowset left, IRowset right,
               IUpdatableRow output);
    }
```

L'implementazione personalizzata dell'interfaccia `ICombiner` deve contenere una definizione dell'override COMBINE `IEnumerable<IRow>`.

```c#
    [SqlUserDefinedCombiner]
    public class MyCombiner : ICombiner
    {

        public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
        IUpdatableRow output)
        {
        …
        }
    }
```

L'attributo **SqlUserDefinedCombiner** indica che il tipo deve essere registrato come combinatore definito dall'utente. Questa classe non può essere ereditata.

SqlUserDefinedCombiner è un attributo facoltativo per la definizione del combinatore definito dall'utente. Consente di definire la proprietà della modalità di combinazione.

Modalità     CombinerMode

L'enumerazione CombinerMode può accettare i valori seguenti:

* Full   (0)    : ogni riga di output dipende potenzialmente da tutte le righe di input a sinistra e a destra     con lo stesso valore chiave

* Left  (1):    ogni riga di output dipende da una singola riga di input a sinistra (e potenzialmente da tutte le righe     a destra con lo stesso valore chiave)

* Right (2):     ogni riga di output dipende da una singola riga di input a destra (e potenzialmente da tutte le righe     a sinistra con lo stesso valore chiave)

* Inner (3):    ogni riga di output dipende da una sola riga di input a sinistra e a destra con lo stesso valore

Esempio:     [`SqlUserDefinedCombiner(Mode=CombinerMode.Left)`]


I principali oggetti di programmazione sono

```c#
    public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
        IUpdatableRow output
```

I set di righe di input vengono passati come tipo di interfaccia `IRowset` a *sinistra* e a *destra*. Entrambi i set di righe devono essere enumerati per l'elaborazione. Ciascuna interfaccia può essere enumerata una volta sola, pertanto è necessario enumerarla e memorizzarla nella cache come da necessità.

Per la memorizzazione nella cache, è possibile creare un tipo di struttura di memoria List\<T\> come risultato dell'esecuzione di query LINQ. Nello specifico List<`IRow`>. Durante l'enumerazione è possibile usare anche il tipo di dati anonimo.

Vedere [Introduction to LINQ Queries (C#)](https://msdn.microsoft.com/library/bb397906.aspx) (Introduzione alle query LINQ (C#)) per altre informazioni sulle query LINQ e [IEnumerable\<T\> Interface](https://msdn.microsoft.com/library/9eekhta0(v=vs.110).aspx) (Interfaccia IEnumerable<T>) per altre informazioni sull'interfaccia IEnumerable\<T\>.

Per ottenere i valori dei dati effettivi dal valore `IRowset` in ingresso, viene usato il metodo Get() dell'interfaccia `IRow`

```c#
    mycolumn = row.Get<int>("mycolumn")
```

I nomi delle singole colonne possono essere determinati chiamando il metodo dello schema `IRow`.

```c#
    ISchema schema = row.Schema;
    var col = schema[i];
    string val = row.Get<string>(col.Name)
```

oppure il nome di colonna dello schema

```
    c# row.Get<int>(row.Schema[0].Name)
```

L'enumerazione generale con LINQ si presenta come di seguito:

```c#
var myRowset =
            (from row in left.Rows
                          select new
                          {
                              Mycolumn = row.Get<int>("mycolumn"),
                          }).ToList();
```

Una volta enumerati entrambi i set di righe, viene eseguito il ciclo in tutte le righe e, nel set di righe a sinistra, vengono trovate tutte le righe che soddisfano la condizione del combinatore.

I valori di output devono essere impostati con output `IUpdatableRow`

```c#
    output.Set<int>("mycolumn", mycolumn)
```

L'effettivo output viene attivato chiamando `yield return output.AsReadOnly()`.

Quello che segue è un esempio di combinatore

```c#
    [SqlUserDefinedCombiner]
    public class CombineSales : ICombiner
    {

        public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
        IUpdatableRow output)
        {
            var internetSales =
            (from row in left.Rows
                          select new
                          {
                              ProductKey = row.Get<int>("ProductKey"),
                              OrderDateKey = row.Get<int>("OrderDateKey"),
                              SalesAmount = row.Get<decimal>("SalesAmount"),
                              TaxAmt = row.Get<decimal>("TaxAmt")
                          }).ToList();

            var resellerSales =
            (from row in right.Rows
             select new
             {
                 ProductKey = row.Get<int>("ProductKey"),
                 OrderDateKey = row.Get<int>("OrderDateKey"),
                 SalesAmount = row.Get<decimal>("SalesAmount"),
                 TaxAmt = row.Get<decimal>("TaxAmt")
             }).ToList();

            foreach (var row_i in internetSales)
            {
                foreach (var row_r in resellerSales)
                {

                    if (
                        row_i.OrderDateKey > 0
                        && row_i.OrderDateKey < row_r.OrderDateKey
                        && row_i.OrderDateKey == 20010701
                        && (row_r.SalesAmount + row_r.TaxAmt) > 20000)
                    {
                        output.Set<int>("OrderDateKey", row_i.OrderDateKey);
                        output.Set<int>("ProductKey", row_i.ProductKey);
                        output.Set<decimal>("Internet_Sales_Amount", row_i.SalesAmount + row_i.TaxAmt);
                        output.Set<decimal>("Reseller_Sales_Amount", row_r.SalesAmount + row_r.TaxAmt);
                    }

                }
            }
            yield return output.AsReadOnly();
        }
    }
```

In questo scenario d'uso, viene creato il report di analisi per il rivenditore. L'obiettivo è trovare tutti i prodotti che costano più di $&20;.000 e che, in un dato intervallo di tempo, vendono più velocemente tramite il sito Web che non tramite il rivenditore.

Di seguito è riportato lo script U-SQL di base, in cui è possibile confrontare la logica JOIN con quella di un combinatore:

```sql
DECLARE @LocalURI string = @"\usql-programmability\";

DECLARE @input_file_internet_sales string = @LocalURI+"FactInternetSales.txt";
DECLARE @input_file_reseller_sales string = @LocalURI+"FactResellerSales.txt";
DECLARE @output_file1 string = @LocalURI+"output_file1.tsv";
DECLARE @output_file2 string = @LocalURI+"output_file2.tsv";

@fact_internet_sales =
EXTRACT
    ProductKey int ,
    OrderDateKey int ,
    DueDateKey int ,
    ShipDateKey int ,
    CustomerKey int ,
    PromotionKey int ,
    CurrencyKey int ,
    SalesTerritoryKey int ,
    SalesOrderNumber String ,
    SalesOrderLineNumber  int ,
    RevisionNumber int ,
    OrderQuantity int ,
    UnitPrice decimal ,
    ExtendedAmount decimal,
    UnitPriceDiscountPct float ,
    DiscountAmount float ,
    ProductStandardCost decimal ,
    TotalProductCost decimal ,
    SalesAmount decimal ,
    TaxAmt decimal ,
    Freight decimal ,
    CarrierTrackingNumber String,
    CustomerPONumber String
FROM @input_file_internet_sales
USING Extractors.Text(delimiter:'|', encoding: Encoding.Unicode);

@fact_reseller_sales =
EXTRACT
    ProductKey int ,
    OrderDateKey int ,
    DueDateKey int ,
    ShipDateKey int ,
    ResellerKey int ,
    EmployeeKey int ,
    PromotionKey int ,
    CurrencyKey int ,
    SalesTerritoryKey int ,
    SalesOrderNumber String ,
    SalesOrderLineNumber  int ,
    RevisionNumber int ,
    OrderQuantity int ,
    UnitPrice decimal ,
    ExtendedAmount decimal,
    UnitPriceDiscountPct float ,
    DiscountAmount float ,
    ProductStandardCost decimal ,
    TotalProductCost decimal ,
    SalesAmount decimal ,
    TaxAmt decimal ,
    Freight decimal ,
    CarrierTrackingNumber String,
    CustomerPONumber String
FROM @input_file_reseller_sales
USING Extractors.Text(delimiter:'|', encoding: Encoding.Unicode);

@rs1 =
SELECT
    fis.OrderDateKey,
    fis.ProductKey,
    fis.SalesAmount+fis.TaxAmt AS Internet_Sales_Amount,
    frs.SalesAmount+frs.TaxAmt AS Reseller_Sales_Amount
FROM @fact_internet_sales AS fis
     INNER JOIN @fact_reseller_sales AS frs
     ON fis.ProductKey == frs.ProductKey
WHERE
    fis.OrderDateKey < frs.OrderDateKey
    AND fis.OrderDateKey == 20010701
    AND frs.SalesAmount+frs.TaxAmt > 20000;

@rs2 =
COMBINE @fact_internet_sales AS fis
WITH @fact_reseller_sales AS frs
ON fis.ProductKey == frs.ProductKey
PRODUCE OrderDateKey int,
        ProductKey int,
        Internet_Sales_Amount decimal,
        Reseller_Sales_Amount decimal
USING new USQL_Programmability.CombineSales();

OUTPUT @rs1 TO @output_file1 USING Outputters.Tsv();
OUTPUT @rs2 TO @output_file2 USING Outputters.Tsv();
```

Il combinatore definito dall'utente può essere chiamato come una nuova istanza dell'oggetto di applicazione

```c#
    USING new MyNameSpace.MyCombiner();
```


o con la chiamata a un metodo factory wrapper

```c#
    USING MyNameSpace.MyCombiner();
```

## <a name="user-defined-reducer"></a>Riduttore definito dall'utente
U-SQL consente di scrivere riduttori di set di righe personalizzati in C# usando il framework di estendibilità degli operatori definito dall'utente mediante l'implementazione di un'interfaccia IReducer.

Il riduttore definito dall'utente, o UDR, consente di eliminare le righe non necessarie durante l'estrazione (importazione) dei dati. Può inoltre essere usato per modificare e valutare righe/colonne e definire le righe da estrarre in base a una logica di programmabilità.

Per definire una classe UDR, occorre creare un'interfaccia `IReducer` con l'attributo `SqlUserDefinedReducer` facoltativo

Questa interfaccia di classi deve contenere una definizione per l'override dei set di righe dell'interfaccia `IEnumerable`

```c#
        [SqlUserDefinedReducer]
        public class EmptyUserReducer : IReducer
        {

            public override IEnumerable<IRow> Reduce(IRowset input, IUpdatableRow output)
            {
            …
            }

        }
```

L'attributo **SqlUserDefinedReducer** indica che il tipo deve essere registrato come riduttore definito dall'utente. Questa classe non può essere ereditata.

SqlUserDefinedReducer è un attributo facoltativo per la definizione del riduttore definito dall'utente. Consente di definire la proprietà IsRecursive.

* bool     IsRecursive    
* **true**  = indica se il riduttore è idempotente

I principali oggetti di programmabilità sono *input* e *output*. L'oggetto di input serve a enumerare le righe di input e l'output, per impostare le righe di output come risultato dell'attività di riduzione.

Per l'enumerazione delle righe di input, viene usato il metodo `Row.Get`

```c#
            foreach (IRow row in input.Rows)
            {
                        row.Get<string>("mycolumn");
            }
```

Qui occorre tenere conto di alcune cose importanti. Il parametro del metodo `Row.Get` è una colonna passata come parte della classe `PRODUCE` dell'istruzione `REDUCE` dello script U-SQL di base. Anche qui occorre usare il tipo di dati corretto.

Per l'output: metodo output.Set

È importante tenere presente che il riduttore personalizzato restituirà solo i valori definiti con la chiamata al metodo output.Set.

```c#
    output.Set<string>("mycolumn", guid);
```

L'effettivo output del riduttore viene attivato chiamando `yield return output.AsReadOnly()`.

Di seguito è riportato un esempio di riduttore

```c#
        [SqlUserDefinedReducer]
        public class EmptyUserReducer : IReducer
        {

            public override IEnumerable<IRow> Reduce(IRowset input, IUpdatableRow output)
            {
                string guid;
                DateTime dt;
                string user;
                string des;

                foreach (IRow row in input.Rows)
                {
                    guid = row.Get<string>("guid");
                    dt = row.Get<DateTime>("dt");
                    user = row.Get<string>("user");
                    des = row.Get<string>("des");

                    if (user.Length > 0)
                    {
                        output.Set<string>("guid", guid);
                        output.Set<DateTime>("dt", dt);
                        output.Set<string>("user", user);
                        output.Set<string>("des", des);

                        yield return output.AsReadOnly();
                    }
                }
            }

        }
```

In questo scenario d'uso, il riduttore ignora le righe con nome utente vuoto. Per ogni riga nel set di righe viene letta ogni colonna necessaria, quindi viene valutata la lunghezza del nome utente e la riga effettiva viene restituita solo se lunghezza del valore del nome utente è superiore a 0.

Script U-SQL di base che usa un riduttore personalizzato

```sql
DECLARE @input_file string = @"\usql-programmability\input_file_reducer.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid string,
        dt DateTime,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    REDUCE @rs0 PRESORT guid
    ON guid  
    PRODUCE guid string, dt DateTime, user String, des String
    USING new USQL_Programmability.EmptyUserReducer();

@rs2 =
    SELECT guid AS start_id,
           dt AS start_time,
           DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).ToString() AS start_fiscalperiod,
           user,
           des
    FROM @rs1;

OUTPUT @rs2 TO @output_file USING Outputters.Text();
```

