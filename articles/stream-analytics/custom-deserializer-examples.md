---
title: Leggere input in qualsiasi formato tramite deserializzatori personalizzati .NET in Analisi di flusso di Azure
description: Questo articolo illustra il formato di serializzazione e le interfacce che definiscono deserializzatori .NET personalizzati per i processi cloud ed Edge di Analisi di flusso di Azure.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: b7994754d3ca9c43fe7935b2b52c42f2f113b1d3
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873058"
---
# <a name="read-input-in-any-format-using-net-custom-deserializers"></a>Leggere input in qualsiasi formato tramite deserializzatori personalizzati .NET

I deserializzatori personalizzati .NET consentono ai processi di Analisi di flusso di Azure di leggere dati da formati diversi dai tre [formati dati predefiniti](stream-analytics-parsing-json.md). Questo articolo illustra il formato di serializzazione e le interfacce che definiscono i deserializzatori personalizzati .NET per i processi cloud ed Edge di Analisi di flusso di Azure. Sono disponibili anche deserializzatori di esempio per il buffer del protocollo e il formato CSV.

## <a name="net-custom-deserializer"></a>Deserializzatore personalizzato .NET

Gli esempi di codice seguenti sono interfacce che definiscono il deserializzatore personalizzato e implementano `StreamDeserializer<T>`.

`UserDefinedOperator` è la classe di base per tutti gli operatori di streaming personalizzati. Inizializza `StreamingContext`, che fornisce il contesto che include il meccanismo per la diagnostica di pubblicazione per la quale è necessario eseguire il debug di eventuali problemi del deserializzatore.

```csharp
    public abstract class UserDefinedOperator
    {
        public abstract void Initialize(StreamingContext streamingContext);
    }
```

Il frammento di codice seguente rappresenta la deserializzazione per lo streaming di dati. 

Gli errori ignorabili devono essere generati passando `IStreamingDiagnostics` al metodo Initialize di `UserDefinedOperator`. Tutte le eccezioni verranno considerate come errori e il deserializzatore verrà ricreato. Dopo un certo numero di errori, il processo verrà contrassegnato come non riuscito.

`StreamDeserializer<T>` deserializza un flusso in un oggetto di tipo `T`. Le condizioni seguenti devono essere soddisfatte:

1. T è una classe o uno struct.
1. Tutti i campi pubblici in T hanno una delle seguenti caratteristiche:
    1. Sono di tipo [sbyte, byte, short, ushort, int, uint, long, DateTime, string, float, double] o rispettivi equivalenti che ammettono i valori Null.
    1. Corrispondono a un altro struct o a un'altra classe che segue le stesse regole.
    1. Sono una matrice di tipo `T2` che segue le stesse regole.
    1. Sono IList`T2` dove T2 segue le stesse regole.
    1. Non hanno tipi ricorsivi.

Il parametro `stream` è il flusso contenente l'oggetto serializzato. `Deserialize` restituisce una raccolta di istanze di `T`.

```csharp
    public abstract class StreamDeserializer<T> : UserDefinedOperator
    {
        public abstract IEnumerable<T> Deserialize(Stream stream);
    }
```

`StreamingContext` rende disponibile contesto che include il meccanismo per la diagnostica di pubblicazione per l'operatore utente.

```csharp
    public abstract class StreamingContext
    {
        public abstract StreamingDiagnostics Diagnostics { get; }
    }
```

`StreamingDiagnostics` rappresenta la diagnostica per gli operatori definiti dall'utente, che include serializzatore, deserializzatore e funzioni definite dall'utente.

`WriteError` scrive un messaggio di errore nei log delle risorse e invia l'errore alla diagnostica.

`briefMessage` è un messaggio di errore breve. Questo messaggio viene visualizzato nella diagnostica e viene usato dal team di prodotto a scopo di debug. Non includere informazioni riservate e mantenere il messaggio al di sotto dei 200 caratteri

`detailedMessage` è un messaggio di errore dettagliato che viene aggiunto solo ai log delle risorse nella risorsa di archiviazione in uso. Il messaggio deve contenere meno di 2000 caratteri.

```csharp
    public abstract class StreamingDiagnostics
    {
        public abstract void WriteError(string briefMessage, string detailedMessage);
    }
```

## <a name="deserializer-examples"></a>Esempi di deserializzatori

Questa sezione illustra come scrivere deserializzatori personalizzati per Protobuf e CSV. Per altri esempi, ad esempio per il formato AVRO per l'acquisizione di hub eventi, vedere [Analisi di flusso di Azure in GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers).

### <a name="protocol-buffer-protobuf-format"></a>Formato buffer del protocollo (Protobuf)

Questo esempio usa il formato del buffer del protocollo.

Si presupponga la definizione del buffer del protocollo seguente.

```proto
syntax = "proto3";
// protoc.exe from nuget "Google.Protobuf.Tools" is used to generate .cs file from this schema definition.
// Run below command to generate the csharp class
// protoc.exe --csharp_out=. MessageBodyProto.proto

package SimulatedTemperatureSensor;
message MessageBodyProto {
    message Ambient {
      double temperature = 1;
      int64 humidity = 2;
    }

    message Machine {
      double temperature = 1;
      double pressure = 2;
    }

    Machine machine = 1;
    Ambient ambient = 2;
    string timeCreated = 3;
}
```

Eseguendo `protoc.exe` da **Google.Protobuf.Tools** NuGet genera un file con estensione cs con la definizione. Il file generato non è visualizzato qui. È necessario assicurarsi che la versione di NuGet di Protobuf usata nel progetto di Analisi di flusso corrisponda alla versione di Protobuf usata per generare l'input. 

Il frammento di codice seguente è l'implementazione del deserializzatore. Si presume che il file generato sia incluso nel progetto. Questa implementazione è solo un thin wrapper del file generato.

```csharp
    public class MessageBodyDeserializer : StreamDeserializer<SimulatedTemperatureSensor.MessageBodyProto>
    {
        public override IEnumerable<SimulatedTemperatureSensor.MessageBodyProto> Deserialize(Stream stream)
        {
            while (stream.Position < stream.Length)
            {
                yield return SimulatedTemperatureSensor.MessageBodyProto.Parser.ParseDelimitedFrom(stream);
            }
        }

        public override void Initialize(StreamingContext streamingContext)
        {
        }
    }
```

### <a name="csv"></a>CSV

Il frammento di codice seguente è un deserializzatore CSV semplice che illustra anche la propagazione degli errori.

```csharp
using System.Collections.Generic;
using System.IO;

using Microsoft.Azure.StreamAnalytics;
using Microsoft.Azure.StreamAnalytics.Serialization;

namespace ExampleCustomCode.Serialization
{
    public class CustomCsvDeserializer : StreamDeserializer<CustomEvent>
    {
        private StreamingDiagnostics streamingDiagnostics;

        public override IEnumerable<CustomEvent> Deserialize(Stream stream)
        {
            using (var sr = new StreamReader(stream))
            {
                string line = sr.ReadLine();
                while (line != null)
                {
                    if (line.Length > 0 && !string.IsNullOrWhiteSpace(line))
                    {
                        string[] parts = line.Split(',');
                        if (parts.Length != 3)
                        {
                            streamingDiagnostics.WriteError("Did not get expected number of columns", $"Invalid line: {line}");
                        }
                        else
                        {
                            yield return new CustomEvent()
                            {
                                Column1 = parts[0],
                                Column2 = parts[1],
                                Column3 = parts[2]
                            };
                        }
                    }

                    line = sr.ReadLine();
                }
            }
        }

        public override void Initialize(StreamingContext streamingContext)
        {
            this.streamingDiagnostics = streamingContext.Diagnostics;
        }
    }

    public class CustomEvent
    {
        public string Column1 { get; set; }

        public string Column2 { get; set; }

        public string Column3 { get; set; }
    }
}

```

## <a name="serialization-format-for-rest-apis"></a>Formato di serializzazione per le API REST

Ogni input di Analisi di flusso ha un **formato di serializzazione**. Per altre informazioni sulle opzioni di input, vedere la documentazione relativa all'[API REST di input](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-input).

Il codice JavaScript seguente è un esempio del formato di serializzazione del deserializzatore .NET quando si usa l'API REST:

```javascript
{    
   "properties":{    
      "type":"stream",  
      "serialization":{    
         "type":"CustomCLR",  
         "properties":{    
            "serializationDllPath":"<path to the dll inside UserCustomCode\CLR\ folder>", 
            "serializationClassName":"<Full name of the deserializer class name>" 
         }  
      }
   }  
}  
```

`serializationClassName` deve essere una classe che implementa `StreamDeserializer<T>`. Questo concetto viene descritto nella sezione seguente.

## <a name="region-support"></a>Supporto di area

Questa funzionalità è disponibile nelle aree seguenti:

* Stati Uniti centro-occidentali
* Europa settentrionale
* Stati Uniti orientali
* Stati Uniti occidentali
* Stati Uniti orientali 2
* Europa occidentale

È possibile [richiedere il supporto](https://aka.ms/ccodereqregion) di aree aggiuntive.

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="when-will-this-feature-be-available-in-all-azure-regions"></a>Quando sarà disponibile questa funzionalità in tutte le aree di Azure?

Questa funzionalità è disponibile in [6 aree](https://docs.microsoft.com/azure/stream-analytics/custom-deserializer-examples#region-support). Se interessa usare questa funzionalità in un'altra area, è possibile [inviare una richiesta](https://aka.ms/ccodereqregion). Il supporto per tutte le aree di Azure è indicato nella roadmap.

### <a name="can-i-access-metadatapropertyvalue-from-my-inputs-similar-to-getmetadatapropertyvalue-function"></a>È possibile accedere a MetadataPropertyValue da input simili alla funzione GetMetadataPropertyValue?

Questa funzionalità non è supportata. Se questa capacità è necessaria, è possibile votare per questa richiesta in [UserVoice](https://feedback.azure.com/forums/270577-stream-analytics/suggestions/38779801-accessing-input-metadata-properties-in-custom-dese).

### <a name="can-i-share-my-deserializer-implementation-with-the-community-so-that-others-can-benefit"></a>È possibile condividere l'implementazione del deserializzatore con la community in modo che altri utenti possano trarne vantaggio?

Dopo averlo implementato, è possibile condividere il deserializzatore con la community per aiutare altri utenti. Inviare il codice al [repository GitHub di Analisi di flusso di Azure](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers).

### <a name="what-are-the-other-limitation-of-using-custom-deserializers-in-stream-analytics"></a>Quali sono le altre limitazioni dell'uso di deserializzatori personalizzati in Analisi di flusso?

Se l'input è nel formato Protobuf con uno schema contenente il tipo MapField, non è possibile implementare un deserializzatore personalizzato. Microsoft sta lavorando per supportare questo tipo in futuro.

## <a name="next-steps"></a>Passaggi successivi

* [Deserializzatori .NET personalizzati per i processi cloud di Analisi di flusso di Azure](custom-deserializer.md)
