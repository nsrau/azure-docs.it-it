<properties 
	pageTitle="Serializzare i dati con la libreria Microsoft Avro | Azure" 
	description="Informazioni sull'uso di Avro in Azure HDInsight per serializzare Big Data." 
	services="hdinsight" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/10/2014" 
	ms.author="bradsev"/>


# Serializzare i dati con la libreria Microsoft Avro

##Panoramica
Questo argomento illustra come usare la <a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">libreria Microsoft Avro</a> per serializzare oggetti e altre strutture di dati in flussi, allo scopo di salvarli in modo permanente in una memoria, in un database o in un file, e anche come deserializzarli per recuperare gli oggetti originali. 

## Contenuto dell'articolo

- [Apache Avro](#apacheAvro)
- [Scenario Hadoop](#hadoopScenario)
- [Serializzazione nella libreria Microsoft Avro](#serializationMAL) 
- [Prerequisiti della libreria Microsoft Avro](#prerequisites)
- [Installazione della libreria Microsoft Avro](#installation)
- [Codice sorgente della libreria Microsoft Avro](#sourceCode)
- [Compilazione dello schema con la libreria Microsoft Avro](#compiling)
- [Guida agli esempi per la libreria Microsoft Avro](#samples)


##<a name="apacheAvro"></a>Apache Avro
La <a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">libreria Microsoft Avro</a> implementa il sistema di serializzazione dei dati Apache Avro per l'ambiente Microsoft .NET. Apache Avro offre un formato compatto di interscambio dei dati binari per la serializzazione e usa <a href="http://www.json.org" target="_blank">JSON</a> per definire lo schema indipendente dal linguaggio che sottoscrive l'interoperabilità del linguaggio. I dati serializzati in un unico linguaggio possono essere letti in un altro linguaggio. I formati attualmente supportati sono C, C++, C#, Java, PHP, Python e Ruby. Informazioni dettagliate sul formato sono disponibili nelle <a href="http://avro.apache.org/docs/current/spec.html" target="_blank">specifiche di Apache Avro</a>. Si noti che la versione corrente della libreria Microsoft Avro non supporta la parte relativa alle RPC (Remote Procedure Call) di queste specifiche.

La rappresentazione serializzata di un oggetto nel sistema Avro consiste in due parti: schema e valore effettivo. Lo schema Avro descrive il modello di dati indipendente dal linguaggio dei dati serializzati con JSON. È presente side-by-side con una rappresentazione binaria dei dati.  La separazione dello schema dalla rappresentazione binaria permette di scrivere ciascun oggetto senza sovraccarichi per ogni valore, velocizzando la serializzazione e riducendo le dimensioni della rappresentazione. 

##<a name="hadoopScenario"></a>Scenario Hadoop 
Il formato di serializzazione Apache Avro è ampiamente usato in Azure HDInsight e in altri ambienti Apache Hadoop. Avro rappresenta un modo pratico per rappresentare le strutture di dati complesse con un processo MapReduce di Hadoop. Il formato dei file Avro (file contenitore di oggetti di Avro) è stato progettato per supportare il modello di programmazione MapReduce distribuito. La funzione chiave che abilita la distribuzione è la possibilità di suddividere i file, rendendo possibile iniziare la lettura da un particolare blocco in qualsiasi punto di un file. 
 
##<a name="serializationMAL"></a> Serializzazione nella libreria Microsoft Avro
La libreria .NET per Avro supporta due modalità di serializzazione degli oggetti:

- **reflection**: lo schema JSON per i tipi viene automaticamente creato dagli attributi di contratto dati dei tipi .NET da serializzare. 
- **record generico**: lo schema JSON viene specificato esplicitamente in un record rappresentato dalla classe [**AvroRecord**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) quando non è presente alcun tipo .NET per descrivere lo schema dei dati da serializzare. 

Quando lo schema di dati è noto sia al writer che al lettore del flusso, sarà possibile inviare i dati senza il relativo schema. Se viene usato un file contenitore di oggetti Avro, lo schema viene archiviato all'interno del file. È anche possibile specificare altri parametri, come il codec usato per la compressione dei dati. Questi scenari vengono delineati più dettagliatamente e illustrati negli esempi di codice riportati di seguito.


##<a name="prerequisites"></a> Prerequisiti della libreria Microsoft Avro
- <a href="http://www.microsoft.com/it-it/download/details.aspx?id=17851" target="_blank">Microsoft .NET Framework v4.0</a>
- <a href="http://james.newtonking.com/json" target="_blank">Newtonsoft Json.NET</a> (v6.0.4 o versione successiva) 

Notare che la dipendenza Newtonsoft.Json.dll viene scaricata automaticamente con l'installazione della libreria Microsoft Avro, la cui procedura è descritta nella sezione seguente.

##<a name="installation"></a> Installazione della libreria Microsoft Avro
La libreria Microsoft Avro viene distribuita come pacchetto NuGet che può essere installato da Visual Studio con la procedura seguente: 

- Selezionare la scheda **Progetto** -> **Gestisci pacchetti NuGet**.
- Cercare "Microsoft.Hadoop.Avro" nella casella **Ricerca in linea**.
- Fare clic su **Installa** accanto a **Libreria Microsoft Avro**. 

Anche la dipendenza Newtonsoft.Json.dll (>= .6.0.4) viene scaricata automaticamente assieme alla libreria Microsoft Avro.

Visitare la <a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">home page della libreria Microsoft Avro</a> per le note sulla versione più recenti.
 
##<a name="sourceCode"></a>Codice sorgente della libreria Microsoft Avro

Il codice sorgente della libreria Microsoft Avro è disponibile nella <a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">home page della libreria Microsoft Avro</a>.

##<a name="compiling"></a>Compilazione dello schema con la libreria Microsoft Avro 

La libreria Microsoft Avro contiene un'utilità di generazione del codice che consente la creazione di tipi C# in modo automatico, in base allo schema JSON definito in precedenza. Tale utilità non viene distribuita come eseguibile binario, ma può essere facilmente compilata usando la procedura seguente:

1. Scaricare il file ZIP con la versione più recente del codice sorgente dell'SDK di HDInsight da <a href="http://hadoopsdk.codeplex.com/SourceControl/latest" target="_blank">Microsoft .NET SDK per Hadoop</a>  (fare clic sull'icona di **download**).
2. Estrarre HDInsight SDK in una directory nel computer in cui .NET Framework 4.0 è installato e connesso a Internet per il download dei necessari pacchetti NuGet. Di seguito si presupporrà che il codice sorgente venga estratto in C:\SDK
3. Passare alla cartella C:\SDK\src\Microsoft.Hadoop.Avro.Tools ed eseguire build.bat (il file chiamerà MSBuild dalla distribuzione a 32 bit di .NET Framework. Se si vuole usare la versione a 64 bit, modificare build.bat seguendo i commenti presenti nel file). Assicurarsi che la compilazione venga completata correttamente. In alcuni sistemi MSBuild potrebbe generare degli avvisi, che però non influiranno sull'utilità a meno che non siano presenti anche errori di compilazione.
4. L'utilità compilata si troverà quindi in C:\SDK\Bin\Unsigned\Release\Microsoft.Hadoop.Avro.Tools


Per acquisire familiarità con la sintassi della riga di comando, eseguire il comando seguente dalla cartella in cui è presente l'utilità di generazione del codice: `Microsoft.Hadoop.Avro.Tools help /c:codegen`

Per testare l'utilità è possibile generare classi C# dal semplice file dello schema JSON fornito con il codice sorgente. Eseguire il comando seguente:

	Microsoft.Hadoop.Avro.Tools codegen /i:C:\SDK\src\Microsoft.Hadoop.Avro.Tools\SampleJSON\SampleJSONSchema.avsc /o:

Ciò dovrebbe creare due file C# nella directory corrente: SensorData.cs e Location.cs.

Per comprendere la logica impiegata dall'utilità di generazione del codice durante la conversione dello schema JSON in tipi C# types, vedere il file GenerationVerification.feature che si trova in C:\SDK\src\Microsoft.Hadoop.Avro.Tools\Doc

Si noti che gli spazi dei nomi vengono estratti dallo schema JSON usando la logica descritta nel file menzionato nel paragrafo precedente. Gli spazi dei nomi estratti dallo schema hanno la precedenza su qualsiasi altro elemento fornito con il parametro /n nella riga di comando dell'utilità. Se si vuole eseguire l'override degli spazi dei nomi contenuti nello schema, assicurarsi che venga usato il parametro /nf. Ad esempio per cambiare tutti gli spazi dei nomi da SampleJSONSchema.avsc a my.own.nspace, eseguire il comando seguente:

    Microsoft.Hadoop.Avro.Tools codegen /i:C:\SDK\src\Microsoft.Hadoop.Avro.Tools\SampleJSON\SampleJSONSchema.avsc /o:. /nf:my.own.nspace

##<a name="samples"></a>Guida agli esempi per la libreria Microsoft Avro
I sei esempi forniti in questo argomento illustrano ciascuno diversi scenari supportati dalla libreria Microsoft Avro. La libreria Microsoft Avro è progettata per funzionare con qualsiasi flusso. In questi esempi i dati vengono modificati usando flussi di memoria piuttosto che flussi di file o database, a scopi di semplicità e coerenza. L'approccio adottato in un ambiente di produzione dipenderà dagli esatti requisiti dello scenario, dall'origine dati e dal volume, dai vincoli di prestazioni e da altri fattori.

I primi due esempi mostrano come serializzare e deserializzare i dati nei buffer del flusso di memoria usando la reflection e i record generici. In questi due casi si presuppone che lo schema sia condiviso tra lettori e writer fuori programma. 

Il terzo e il quarto esempio mostrano come serializzare e deserializzare i dati usando i file contenitore di oggetti di Avro. Quando i dati vengono archiviati in un file contenitore di Avro, il relativo schema verrà sempre archiviato assieme ad essi, perché per completare la deserializzazione è necessario condividere lo schema.

È possibile scaricare i primi quattro esempio dal sito degli <a href="http://code.msdn.microsoft.com/windowsazure/Serialize-data-with-the-86055923" target="_blank">esempi di codice di Azure</a> .

Il quinto esempio mostra come usare un codec di compressione personalizzato per i file contenitori di oggetti Avro. È possibile scaricare questo esempio di codice dal sito degli <a href="http://code.msdn.microsoft.com/windowsazure/Serialize-data-with-the-67159111" target="_blank">esempi di codice di Azure</a> .

Il sesto esempio mostra come usare la serializzazione Avro per caricare dati nell'archiviazione BLOB di Azure e quindi analizzarli usando Hive con un cluster HDInsight (Hadoop). È possibile scaricarlo dal sito degli <a href="https://code.msdn.microsoft.com/windowsazure/Using-Avro-to-upload-data-ae81b1e3" target="_blank">esempi di codice di Azure</a> .

Di seguito sono riportati i sei esempi contenuti nel presente argomento:

 * <a href="#Scenario1">**Serializzazione con la reflection**</a>: lo schema JSON per i tipi da serializzare viene automaticamente creato dagli attributi di contratto dati.
 * <a href="#Scenario2">**Serializzazione con record generici**</a>: lo schema JSON viene specificato esplicitamente in un record quando non è disponibile alcun tipo .NET per la reflection.
 * <a href="#Scenario3">**Serializzazione tramite file contenitori di oggetti con la reflection**</a>: lo schema JSON viene automaticamente compilato e condiviso insieme ai dati serializzati usando un file contenitore di oggetti Avro.
 * <a href="#Scenario4">**Serializzazione tramite file contenitori di oggetti con record generici**</a>: lo schema JSON viene specificato in modo esplicito prima della serializzazione e condiviso insieme ai dati usando un file contenitore di oggetti Avro.
 * <a href="#Scenario5">**Serializzazione tramite file contenitori di oggetti con codec di compressione personalizzato**</a>: Questo esempio mostra come creare un file contenitore di oggetti Avro con un'implementazione .NET personalizzata del codec di compressione dati Deflate.
 * <a href="#Scenario6">**Uso di Avro per il caricamento di dati nel servizio Microsoft Azure HDInsight**</a>: illustra in che modo la serializzazione Avro interagisce con il servizio HDInsight. Per eseguire questo esempio sono necessari una sottoscrizione di Azure e l'accesso a un cluster Microsoft Azure HDInsight.

<h3> <a name="Scenario1"></a>Esempio 1. Serializzazione con la reflection</h3>
 
Lo schema JSON per i tipi può essere automaticamente creato dalla libreria Microsoft Avro usando la reflection dagli attributi di contratto dati degli oggetti C# da serializzare. La libreria Microsoft Avro crea un oggetto [**IAvroSeralizer<T>**](http://msdn.microsoft.com/library/dn627341.aspx) per identificare i campi da serializzare.

In questo esempio, gli oggetti (una classe **SensorData** con uno struct **Location** del membro) vengono serializzati in un flusso di memoria, che a sua volta viene deserializzato. Il risultato verrà quindi confrontato con l'istanza iniziale per confermare che l'oggetto **SensorData** recuperato sia identico all'originale.

Si suppone che lo schema in questo esempio venga condiviso tra lettore e writer, in modo che il formato del contenitore di oggetti di Avro non sia necessario. Per un esempio di come serializzare e deserializzare i dati nei buffer di memoria usando la reflection con il formato di contenitore di oggetti quando è necessario serializzare lo schema con i dati, vedere <a href="#Scenario3">Serializzazione tramite file contenitori di oggetti con la reflection.</a>

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;

        //Sample Class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }

        //This class contains all methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serialize and deserialize sample data set represented as an object using Reflection
            //No explicit schema definition is required - schema of serialized objects is automatically built
            public void SerializeDeserializeObjectUsingReflection()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION\n");
                Console.WriteLine("Serializing Sample Data Set...");

                //Create a new AvroSerializer instance and specify a custom serialization strategy AvroDataContractResolver
                //for serializing only properties attributed with DataContract/DateMember
                var avroSerializer = AvroSerializer.Create<SensorData>();

                //Create a Memory Stream buffer
                using (var buffer = new MemoryStream())
                {
                    //Create a data set using sample Class and struct 
                    var expected = new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } };

                    //Serialize the data to the specified stream
                    avroSerializer.Serialize(buffer, expected);


                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Deserialize data from the stream and cast it to the same type used for serialization
                    var actual = avroSerializer.Deserialize(buffer);

                    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                    //Finally, verify that deserialized data matches the original one
                    bool isEqual = this.Equal(expected, actual);

                    Console.WriteLine("Result of Data Set Identity Comparison is {0}", isEqual);

                }
            }

            //
            //Helper methods
            //

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }



            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample Class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization to memory using Reflection
                Sample.SerializeDeserializeObjectUsingReflection();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output: 
    // SERIALIZATION USING REFLECTION
    //
    // Serializing Sample Data Set...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // Result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.


<h3> <a name="Scenario2"></a>Esempio 2. Serializzazione con un record generico</h3>

È possibile specificare esplicitamente uno schema JSON in un record generico quando non è possibile usare la reflection in quanto non è possibile rappresentare i dati usando le classi .NET con un contratto di dati. Questo metodo risulta in genere più lento rispetto all'uso della reflection. In questi casi lo schema per i dati può anche essere dinamico, perché non è noto fino al momento della compilazione. I dati rappresentati come file CSV (Comma Separated Values) il cui schema è sconosciuto fino alla sua trasformazione in formato Avro al runtime è un esempio di questo genere di scenario dinamico.

Questo esempio illustra come creare e usare un [**AvroRecord**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) per specificare esplicitamente uno schema JSON, inserirvi i dati, quindi procedere alla serializzazione e deserializzazione. Il risultato verrà quindi confrontato con l'istanza iniziale per confermare che il record recuperato sia identico all'originale.

Si suppone che lo schema in questo esempio venga condiviso tra lettore e writer, in modo che il formato del contenitore di oggetti di Avro non sia necessario. Per un esempio di come serializzare e deserializzare i dati nei buffer di memoria usando un record generico con il formato di contenitore di oggetti quando è necessario includere lo schema con i dati serializzati, vedere l'esempio <a href="#Scenario4">Serializzazione tramite file contenitori di oggetti con record generici</a> .


	namespace Microsoft.Hadoop.Avro.Sample
	{
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;
    using Microsoft.Hadoop.Avro.Schema;

    //This class contains all methods demonstrating
    //the usage of Microsoft Avro Library
    public class AvroSample
    {

        //Serialize and deserialize sample data set using Generic Record.
        //Generic Record is a special class with the schema explicitly defined in JSON.
        //All serialized data should be mapped to the fields of Generic Record,
        //which in turn will be then serialized.
        public void SerializeDeserializeObjectUsingGenericRecords()
        {
            Console.WriteLine("SERIALIZATION USING GENERIC RECORD\n");
            Console.WriteLine("Defining the Schema and creating Sample Data Set...");

            //Define the schema in JSON
            const string Schema = @"{
                                ""type"":""record"",
                                ""name"":""Microsoft.Hadoop.Avro.Specifications.SensorData"",
                                ""fields"":
                                    [
                                        { 
                                            ""name"":""Location"", 
                                            ""type"":
                                                {
                                                    ""type"":""record"",
                                                    ""name"":""Microsoft.Hadoop.Avro.Specifications.Location"",
                                                    ""fields"":
                                                        [
                                                            { ""name"":""Floor"", ""type"":""int"" },
                                                            { ""name"":""Room"", ""type"":""int"" }
                                                        ]
                                                }
                                        },
                                        { ""name"":""Value"", ""type"":""bytes"" }
                                    ]
                            }";

            //Create a generic serializer based on the schema
            var serializer = AvroSerializer.CreateGeneric(Schema);
            var rootSchema = serializer.WriterSchema as RecordSchema;

            //Create a Memory Stream buffer
            using (var stream = new MemoryStream())
            {
                //Create a generic record to represent the data
                dynamic location = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location.Floor = 1;
                location.Room = 243;

                dynamic expected = new AvroRecord(serializer.WriterSchema);
                expected.Location = location;
                expected.Value = new byte[] { 1, 2, 3, 4, 5 };

                Console.WriteLine("Serializing Sample Data Set...");

                //Serialize the data
                serializer.Serialize(stream, expected);

                stream.Seek(0, SeekOrigin.Begin);

                Console.WriteLine("Deserializing Sample Data Set...");

                //Deserialize the data into a generic record
                dynamic actual = serializer.Deserialize(stream);

                Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                //Finally, verify the results
                bool isEqual = expected.Location.Floor.Equals(actual.Location.Floor);
                isEqual = isEqual && expected.Location.Room.Equals(actual.Location.Room);
                isEqual = isEqual && ((byte[])expected.Value).SequenceEqual((byte[])actual.Value);
                Console.WriteLine("Result of Data Set Identity Comparison is {0}", isEqual);
            }
        }

        static void Main()
        {

            string sectionDivider = "---------------------------------------- ";

            //Create an instance of AvroSample Class and invoke methods
            //illustrating different serializing approaches
            AvroSample Sample = new AvroSample();

            //Serialization to memory using Generic Record
            Sample.SerializeDeserializeObjectUsingGenericRecords();

            Console.WriteLine(sectionDivider);
            Console.WriteLine("Press any key to exit.");
            Console.Read();
        }
    }
	}
    // The example is expected to display the following output: 
    // SERIALIZATION USING GENERIC RECORD
    //
    // Defining the Schema and creating Sample Data Set...
    // Serializing Sample Data Set...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // Result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.


<h3> <a name="Scenario3"></a>Esempio 3. Serializzazione tramite file contenitori di oggetti e serializzazione con reflection</h3>

Questo esempio è simile allo scenario illustrato nel <a href="#Scenario1"> primo esempio</a> in cui lo schema viene specificato implicitamente con la reflection, ma in questo caso non si suppone che lo schema sia noto al lettore che lo deserializza. Gli oggetti **SensorData** da serializzare e il relativo schema specificato implicitamente vengono archiviati in un file contenitore di oggetti Avro rappresentato dalla classe [**AvroContainer**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.container.avrocontainer.aspx). 

In questo esempio i dati vengono serializzati con [**SequentialWriter<SensorData>**](http://msdn.microsoft.com/library/dn627340.aspx) e deserializzati con [**SequentialReader<SensorData>**](http://msdn.microsoft.com/library/dn627340.aspx). Il risultato verrà quindi confrontato con le istanze iniziali per verificare l'identità.

I dati nel file contenitore di oggetti vengono compressi usando il codec di compressione [**Deflate**][deflate-100] predefinito di .NET Framework 4.0. Vedere il <a href="#Scenario5"> quinto esempio</a> in questo argomento per apprendere come usare una versione superiore e più recente del codec di compressione [**Deflate**][deflate-110] disponibile in .NET Framework 4.5.

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;

        //Sample Class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }

        //This class contains all methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serializes and deserializes sample data set using Reflection and Avro Object Container Files
            //Serialized data is compressed with Deflate codec
            public void SerializeDeserializeUsingObjectContainersReflection()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION AND AVRO OBJECT CONTAINER FILES\n");

                //Path for Avro Object Container File
                string path = "AvroSampleReflectionDeflate.avro";

                //Create a data set using sample Class and struct
                var testData = new List<SensorData>
                        {
                            new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
                            new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
                        };

                //Serializing and saving data to file
                //Creating a Memory Stream buffer
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData which can serialize a sequence of SensorData objects to stream
                    //Data will be compressed using Deflate codec
                    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
                    {
                        using (var writer = new SequentialWriter<SensorData>(w, 24))
                        {
                            // Serialize the data to stream using the sequential writer
                            testData.ForEach(writer.Write);
                        }
                    }

                    //Save stream to file
                    Console.WriteLine("Saving serialized data to file...");
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializing data
                //Creating a Memory Stream buffer
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from Object Container File
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Create a SequentialReader for type SensorData which will derserialize all serialized objects from the given stream
                    //It allows iterating over the deserialized objects because it implements IEnumerable<T> interface
                    using (var reader = new SequentialReader<SensorData>(
                        AvroContainer.CreateReader<SensorData>(buffer, true)))
                    {
                        var results = reader.Objects;

                        //Finally, verify that deserialized data matches the original one
                        Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
                        int count = 1;
                        var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
                        foreach (var pair in pairs)
                        {
                            bool isEqual = this.Equal(pair.expected, pair.actual);
                            Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual);
                            count++;
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }

            //
            //Helper methods
            //

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
                    return false;

                }
            }

            //Reading a file content using given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file using given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
                }
            }

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample Class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using Reflection to Avro Object Container File
                Sample.SerializeDeserializeUsingObjectContainersReflection();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION AND AVRO OBJECT CONTAINER FILES
    //
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    // For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.
  

<h3> <a name="Scenario4"></a>Esempio 4. Serializzazione tramite file contenitori di oggetti e serializzazione con record generico</h3>

Questo esempio è simile allo scenario illustrato nel <a href="#Scenario2"> secondo esempio</a> in cui lo schema viene specificato esplicitamente con JSON, ma in questo caso non si suppone che lo schema sia noto al reader che lo deserializza. 

Il set di dati di test viene raccolto in un elenco di oggetti [**AvroRecord**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) usando uno schema JSON definito esplicitamente, quindi archiviato in un file contenitore di oggetti rappresentato dalla classe [**AvroContainer**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.container.avrocontainer.aspx). Questo file contenitore crea un writer che viene usato per serializzare i dati, non compressi, in un flusso di memoria che verrà quindi salvato su file. È il parametro [**Codex.Null**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.container.codec.null.aspx) usato durante la creazione del lettore che specifica che i dati non verranno compressi. 

I dati vengono quindi letti dal file e deserializzati in una raccolta di oggetti, che viene quindi confrontata con l'elenco iniziale di record Avro per confermare che sono identici.


    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro.Schema;

        //This class contains all methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serializes and deserializes sample data set using Generic Record and Avro Object Container Files
            //Serialized data is not compressed
            public void SerializeDeserializeUsingObjectContainersGenericRecord()
            {
                Console.WriteLine("SERIALIZATION USING GENERIC RECORD AND AVRO OBJECT CONTAINER FILES\n");

                //Path for Avro Object Container File
                string path = "AvroSampleGenericRecordNullCodec.avro";

                Console.WriteLine("Defining the Schema and creating Sample Data Set...");

                //Define the schema in JSON
                const string Schema = @"{
                                ""type"":""record"",
                                ""name"":""Microsoft.Hadoop.Avro.Specifications.SensorData"",
                                ""fields"":
                                    [
                                        { 
                                            ""name"":""Location"", 
                                            ""type"":
                                                {
                                                    ""type"":""record"",
                                                    ""name"":""Microsoft.Hadoop.Avro.Specifications.Location"",
                                                    ""fields"":
                                                        [
                                                            { ""name"":""Floor"", ""type"":""int"" },
                                                            { ""name"":""Room"", ""type"":""int"" }
                                                        ]
                                                }
                                        },
                                        { ""name"":""Value"", ""type"":""bytes"" }
                                    ]
                            }";

                //Create a generic serializer based on the schema
                var serializer = AvroSerializer.CreateGeneric(Schema);
                var rootSchema = serializer.WriterSchema as RecordSchema;

                //Create a generic record to represent the data
                var testData = new List<AvroRecord>();

                dynamic expected1 = new AvroRecord(rootSchema);
                dynamic location1 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location1.Floor = 1;
                location1.Room = 243;
                expected1.Location = location1;
                expected1.Value = new byte[] { 1, 2, 3, 4, 5 };
                testData.Add(expected1);

                dynamic expected2 = new AvroRecord(rootSchema);
                dynamic location2 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location2.Floor = 1;
                location2.Room = 244;
                expected2.Location = location2;
                expected2.Value = new byte[] { 6, 7, 8, 9 };
                testData.Add(expected2);

                //Serializing and saving data to file
                //Create a MemoryStream buffer
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData which can serialize a sequence of SensorData objects to stream
                    //Data will not be compressed (Null compression codec)
                    using (var writer = AvroContainer.CreateGenericWriter(Schema, buffer, Codec.Null))
                    {
                        using (var streamWriter = new SequentialWriter<object>(writer, 24))
                        {
                            // Serialize the data to stream using the sequential writer
                            testData.ForEach(streamWriter.Write);
                        }
                    }

                    Console.WriteLine("Saving serialized data to file...");

                    //Save stream to file
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializng the data
                //Create a Memory Stream buffer
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from Object Container File
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Create a SequentialReader for type SensorData which will derserialize all serialized objects from the given stream
                    //It allows iterating over the deserialized objects because it implements IEnumerable<T> interface
                    using (var reader = AvroContainer.CreateGenericReader(buffer))
                    {
                        using (var streamReader = new SequentialReader<object>(reader))
                        {
                            var results = streamReader.Objects;

                            Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                            //Finally, verify the results
                            var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = (dynamic)serialized, actual = (dynamic)deserialized });
                            int count = 1;
                            foreach (var pair in pairs)
                            {
                                bool isEqual = pair.expected.Location.Floor.Equals(pair.actual.Location.Floor);
                                isEqual = isEqual && pair.expected.Location.Room.Equals(pair.actual.Location.Room);
                                isEqual = isEqual && ((byte[])pair.expected.Value).SequenceEqual((byte[])pair.actual.Value);
                                Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
                                count++;
                            }
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }

            //
            //Helper methods
            //

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
                    return false;

                }
            }

            //Reading a file content using given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file using given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
                }
            }

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample Class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using Generic Record to Avro Object Container File
                Sample.SerializeDeserializeUsingObjectContainersGenericRecord();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING GENERIC RECORD AND AVRO OBJECT CONTAINER FILES
    //
    // Defining the Schema and creating Sample Data Set...
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    // For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.




<h3> <a name="Scenario5"></a>Esempio 5. Serializzazione tramite file contenitori di oggetti con codec di compressione personalizzato</h3>

Il quinto esempio mostra come usare un codec di compressione personalizzato per i file contenitori di oggetti Avro. È possibile scaricare questo esempio di codice dal sito  [degli esempi di codice di Azure](http://code.msdn.microsoft.com/windowsazure/Serialize-data-with-the-67159111).

Le [specifiche di Avro](http://avro.apache.org/docs/current/spec.html#Required+Codecs) consentono l'uso di un codec di compressione facoltativo (in aggiunta ai codec predefiniti **Null** e **Deflate**). In questo esempio non viene implementato il nuovo codec, ad esempio Snappy (citato come codec facoltativo supportato nelle [specifiche di Avro](http://avro.apache.org/docs/current/spec.html#snappy)), ma viene illustrato come usare l'implementazione del codec [**Deflate**][deflate-110] di .NET Framework 4.5, che fornisce un miglior algoritmo di compressione basato sulla libreria di compressione [zlib](http://zlib.net/) rispetto alla versione predefinita, cioè .NET Framework 4.0.


    // 
    // This code needs to be compiled with the parameter Target Framework set as ".NET Framework 4.5"
    // to ensure the desired implementation of Deflate compression algorithm is used
    // Ensure your C# Project is set up accordingly
    //

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.Diagnostics;
        using System.IO;
        using System.IO.Compression;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;

        #region Defining objects for serialization
        //Sample Class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }
        #endregion

        #region Defining custom codec based on .NET Framework V.4.5 Deflate
        //Avro.NET Codec class contains two methods 
        //GetCompressedStreamOver(Stream uncompressed) and GetDecompressedStreamOver(Stream compressed)
        //which are the key ones for data compression.
        //To enable a custom codec one needs to implement these methods for the required codec

        #region Defining Compression and Decompression Streams
        //DeflateStream (class from System.IO.Compression namespace that implements Deflate algorithm)
        //can not be directly used for Avro because it does not support vital operations like Seek.
        //Thus one needs to implement two classes inherited from Stream
        //(one for compressed and one for decompressed stream)
        //that use Deflate compression and implement all required features 
        internal sealed class CompressionStreamDeflate45 : Stream
        {
            private readonly Stream buffer;
            private DeflateStream compressionStream;

            public CompressionStreamDeflate45(Stream buffer)
            {
                Debug.Assert(buffer != null, "Buffer is not allowed to be null.");

                this.compressionStream = new DeflateStream(buffer, CompressionLevel.Fastest, true);
                this.buffer = buffer;
            }

            public override bool CanRead
            {
                get { return this.buffer.CanRead; }
            }

            public override bool CanSeek
            {
                get { return true; }
            }

            public override bool CanWrite
            {
                get { return this.buffer.CanWrite; }
            }

            public override void Flush()
            {
                this.compressionStream.Close();
            }

            public override long Length
            {
                get { return this.buffer.Length; }
            }

            public override long Position
            {
                get
                {
                    return this.buffer.Position;
                }

                set
                {
                    this.buffer.Position = value;
                }
            }

            public override int Read(byte[] buffer, int offset, int count)
            {
                return this.buffer.Read(buffer, offset, count);
            }

            public override long Seek(long offset, SeekOrigin origin)
            {
                return this.buffer.Seek(offset, origin);
            }

            public override void SetLength(long value)
            {
                throw new NotSupportedException();
            }

            public override void Write(byte[] buffer, int offset, int count)
            {
                this.compressionStream.Write(buffer, offset, count);
            }

            protected override void Dispose(bool disposed)
            {
                base.Dispose(disposed);

                if (disposed)
                {
                    this.compressionStream.Dispose();
                    this.compressionStream = null;
                }
            }
        }

        internal sealed class DecompressionStreamDeflate45 : Stream
        {
            private readonly DeflateStream decompressed;

            public DecompressionStreamDeflate45(Stream compressed)
            {
                this.decompressed = new DeflateStream(compressed, CompressionMode.Decompress, true);
            }

            public override bool CanRead
            {
                get { return true; }
            }

            public override bool CanSeek
            {
                get { return true; }
            }

            public override bool CanWrite
            {
                get { return false; }
            }

            public override void Flush()
            {
                this.decompressed.Close();
            }

            public override long Length
            {
                get { return this.decompressed.Length; }
            }

            public override long Position
            {
                get
                {
                    return this.decompressed.Position;
                }

                set
                {
                    throw new NotSupportedException();
                }
            }

            public override int Read(byte[] buffer, int offset, int count)
            {
                return this.decompressed.Read(buffer, offset, count);
            }

            public override long Seek(long offset, SeekOrigin origin)
            {
                throw new NotSupportedException();
            }

            public override void SetLength(long value)
            {
                throw new NotSupportedException();
            }

            public override void Write(byte[] buffer, int offset, int count)
            {
                throw new NotSupportedException();
            }

            protected override void Dispose(bool disposing)
            {
                base.Dispose(disposing);

                if (disposing)
                {
                    this.decompressed.Dispose();
                }
            }
        }
        #endregion

        #region Define Codec
        //Define the actual codec class containing the required methods for manipulating streams:
        //GetCompressedStreamOver(Stream uncompressed) and GetDecompressedStreamOver(Stream compressed)
        //Codec class uses classes for comressed and decompressed streams defined above
        internal sealed class DeflateCodec45 : Codec
        {

            //We merely use different IMPLEMENTATION of Deflate, so the CodecName remains "deflate"
            public static readonly string CodecName = "deflate";

            public DeflateCodec45()
                : base(CodecName)
            {
            }

            public override Stream GetCompressedStreamOver(Stream decompressed)
            {
                if (decompressed == null)
                {
                    throw new ArgumentNullException("decompressed");
                }

                return new CompressionStreamDeflate45(decompressed);
            }

            public override Stream GetDecompressedStreamOver(Stream compressed)
            {
                if (compressed == null)
                {
                    throw new ArgumentNullException("compressed");
                }

                return new DecompressionStreamDeflate45(compressed);
            }
        }
        #endregion

        #region Define modified Codec Factory
        //Define modified Codec Factory to be used in Reader
        //It will catch the attempt to use "deflate" and provide Custom Codec 
        //For all other cases it will rely on the base class (CodecFactory)
        internal sealed class CodecFactoryDeflate45 : CodecFactory
        {

            public override Codec Create(string codecName)
            {
                if (codecName == DeflateCodec45.CodecName)
                    return new DeflateCodec45();
                else
                    return base.Create(codecName);
            }
        }
        #endregion

        #endregion

        #region Sample Class with demonstration methods
        //This class contains methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serializes and deserializes sample data set using Reflection and Avro Object Container Files
            //Serialized data is compressed with the Custom compression codec (Deflate of .NET Framework 4.5)
            //
            //This sample uses Memory Stream for all operations related to serialization, deserialization and
            //Object Container manipulation, though File Stream could be easily used.
            public void SerializeDeserializeUsingObjectContainersReflectionCustomCodec()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC\n");

                //Path for Avro Object Container File
                string path = "AvroSampleReflectionDeflate45.avro";

                //Create a data set using sample Class and struct
                var testData = new List<SensorData>
                        {
                            new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
                            new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
                        };

                //Serializing and saving data to file
                //Creating a Memory Stream buffer
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData which can serialize a sequence of SensorData objects to stream
                    //Here the custom Codec is introduced. For convenience the next commented code line shows how to use built-in Deflate.
                    //Note, that because the sample deals with different IMPLEMENTATIONS of Deflate, built-in and custom codecs are interchangeable
                    //in read-write operations
                    //using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
                    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, new DeflateCodec45()))
                    {
                        using (var writer = new SequentialWriter<SensorData>(w, 24))
                        {
                            // Serialize the data to stream using the sequential writer
                            testData.ForEach(writer.Write);
                        }
                    }

                    //Save stream to file
                    Console.WriteLine("Saving serialized data to file...");
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializing data
                //Creating a Memory Stream buffer
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from Object Container File
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Because of SequentialReader<T> constructor signature an AvroSerializerSettings instance is required
                    //when Codec Factory is explicitly specified
                    //You may comment the line below if you want to use built-in Deflate (see next comment)
                    AvroSerializerSettings settings = new AvroSerializerSettings();

                    //Create a SequentialReader for type SensorData which will derserialize all serialized objects from the given stream
                    //It allows iterating over the deserialized objects because it implements IEnumerable<T> interface
                    //Here the custom Codec Factory is introduced.
                    //For convenience the next commented code line shows how to use built-in Deflate
                    //(no explicit Codec Factory parameter is required in this case).
                    //Note, that because the sample deals with different IMPLEMENTATIONS of Deflate, built-in and custom codecs are interchangeable
                    //in read-write operations
                    //using (var reader = new SequentialReader<SensorData>(AvroContainer.CreateReader<SensorData>(buffer, true)))
                    using (var reader = new SequentialReader<SensorData>(
                        AvroContainer.CreateReader<SensorData>(buffer, true, settings, new CodecFactoryDeflate45())))
                    {
                        var results = reader.Objects;

                        //Finally, verify that deserialized data matches the original one
                        Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
                        bool isEqual;
                        int count = 1;
                        var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
                        foreach (var pair in pairs)
                        {
                            isEqual = this.Equal(pair.expected, pair.actual);
                            Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
                            count++;
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }
        #endregion

            #region Helper Methods

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
                    return false;

                }
            }

            //Reading a file content using given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file using given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
                }
            }
            #endregion

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample Class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using Reflection to Avro Object Container File using Custom Codec
                Sample.SerializeDeserializeUsingObjectContainersReflectionCustomCodec();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC
    //
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    //For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.

<h3> <a name="Scenario6"></a> Esempio 6. Uso di Avro per il caricamento di dati nel servizio Microsoft Azure HDInsight</h3>

Il sesto esempio illustra alcune tecniche di programmazione relative all'interazione con il servizio Microsoft Azure HDInsight. È possibile scaricare questo esempio di codice dal sito degli [esempi di codice di Azure](https://code.msdn.microsoft.com/windowsazure/Using-Avro-to-upload-data-ae81b1e3).

L'esempio esegue le operazioni seguenti:

* Connessione a un cluster esistente del servizio HDInsight.
* Serializzazione di diversi file CSV e caricamento dei risultati nell'archiviazione BLOB di Azure. I file CSV vengono distribuiti insieme all'esempio e rappresentano un estratto dei dati cronologici di borsa di AMEX distribuiti da [Infochimps](http://www.infochimps.com/) per il periodo 1970-2010. L'esempio legge file di dati CSV, converte i record in istanze della classe **Stock** e quindi li serializza usando la reflection. Viene creata una definizione di tipo Stock da uno schema JSON tramite l'utilità per la generazione di codice della libreria Microsoft Avro.
* Viene creata una tabella esterna denominata **Stocks** in Hive e quindi viene collegata ai dati caricati nel passaggio precedente.
* Usando Hive, viene eseguita una query sulla tabella **Stocks**.

L'esempio esegue inoltre una procedura di pulizia prima e dopo l'esecuzione delle operazioni principali. Durante la pulizia, tutte le cartelle e i dati correlati del BLOB di Azure vengono rimossi e la tabella Hive viene eliminata. È anche possibile richiamare la procedura di pulizia dalla riga di comando dell'esempio. 

Per eseguire questo esempio, è necessario disporre dei prerequisiti seguenti:

* Una sottoscrizione attiva di Microsoft Azure con il relativo ID sottoscrizione.
* Certificato di gestione per la sottoscrizione con la chiave privata corrispondente. Il certificato deve essere installato nell'archiviazione privata dell'utente corrente, nel computer usato per eseguire l'esempio.
* Un cluster HDInsight attivo.
* Un account di archiviazione di Azure collegato al cluster HDInsight di cui al prerequisito precedente insieme alla chiave di accesso primaria o secondaria.

Tutte le informazioni dei prerequisiti devono essere inserite nel file di configurazione prima di eseguire l'esempio. È possibile procedere in due modi:

* Modificare il file app.config nella directory radice dell'esempio, quindi compilare l'esempio. 
* Oppure compilare prima l'esempio, quindi modificare AvroHDISample.exe.config nella directory di compilazione. 

In entrambi i casi tutte le modifiche devono essere apportate nella sezione delle impostazioni **<appSettings>**. Attenersi ai commenti forniti nel file stesso.
L'esempio viene eseguito da riga di comando eseguendo il comando riportato di seguito (presupponendo che il file ZIP dell'esempio sia stato estratto in C:\AvroHDISample; in caso contrario usare il percorso appropriato).

    AvroHDISample run C:\AvroHDISample\Data

Per procedere alla pulizia del cluster, eseguire il comando seguente:

    AvroHDISample clean




[deflate-100]: http://msdn.microsoft.com/library/system.io.compression.deflatestream(v=vs.100).aspx
[deflate-110]: http://msdn.microsoft.com/library/system.io.compression.deflatestream(v=vs.110).aspx



<!--HONumber=42-->
