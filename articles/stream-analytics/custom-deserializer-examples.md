---
title: Leggere l'input in qualsiasi formato usando i deserializzatori personalizzati .NET in Azure Stream AnalyticsRead input in any format using .NET custom deserializers in Azure Stream Analytics
description: Questo articolo illustra il formato di serializzazione e le interfacce che definiscono deserializzatori .NET personalizzati per i processi cloud e edge di Analisi di flusso di Azure.This article explains the serialization format and the interfaces that define custom .NET deserializers for Azure Stream Analytics cloud and edge jobs.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 270e9a31c28e7209cfe43ea8307b928ed3257a35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76845269"
---
# <a name="read-input-in-any-format-using-net-custom-deserializers"></a>Leggere l'input in qualsiasi formato usando i deserializzatori personalizzati .NETRead input in any format using .NET custom deserializers

I deserializzatori personalizzati .NET consentono al processo di Analisi di flusso di Azure di leggere i dati da formati esterni ai tre [formati di dati predefiniti.](stream-analytics-parsing-json.md) Questo articolo illustra il formato di serializzazione e le interfacce che definiscono i deserializzatori personalizzati .NET per i processi cloud e edge di Analisi di flusso di Azure.This article explains the serialization format and the interfaces that define .NET custom deserializers for Azure Stream Analytics cloud and edge jobs. Esistono anche deserializzatori di esempio per il buffer di protocollo e il formato CSV.

## <a name="net-custom-deserializer"></a>Deserializzatore personalizzato .NET

Gli esempi di codice seguenti sono le interfacce che definiscono il deserializzatore personalizzato e implementano `StreamDeserializer<T>`.

`UserDefinedOperator`è la classe base per tutti gli operatori di streaming personalizzati. Inizializza `StreamingContext`, che fornisce il contesto che include il meccanismo per la pubblicazione della diagnostica per il quale sarà necessario eseguire il debug di eventuali problemi con il deserializzatore.

```csharp
    public abstract class UserDefinedOperator
    {
        public abstract void Initialize(StreamingContext streamingContext);
    }
```

Il frammento di codice seguente è la deserializzazione per i dati di streaming. 

Gli errori ignorabili devono `IStreamingDiagnostics` essere `UserDefinedOperator`generati utilizzando passato tramite il metodo Initialize passato tramite il metodo Initialize. Tutte le eccezioni verranno considerate come errori e il deserializzatore verrà ricreato. Dopo un certo numero di errori, il processo passerà allo stato non riuscito.

`StreamDeserializer<T>`deserializza un flusso `T`nell'oggetto di tipo . Le condizioni seguenti devono essere soddisfatte:

1. T è una classe o uno struct.
1. Tutti i campi pubblici in T sono
    1. Uno di [sbyte, byte, short, ushort, int, uint, long, DateTime, string, float, double] o i relativi equivalenti nullable.
    1. Un'altra struttura o classe che segue le stesse regole.
    1. Matrice di `T2` tipo che segue le stesse regole.
    1. IList`T2` dove T2 segue le stesse regole.
    1. Non dispone di tipi ricorsivi.

Il `stream` parametro è il flusso contenente l'oggetto serializzato. `Deserialize`restituisce una `T` raccolta di istanze.

```csharp
    public abstract class StreamDeserializer<T> : UserDefinedOperator
    {
        public abstract IEnumerable<T> Deserialize(Stream stream);
    }
```

`StreamingContext`fornisce il contesto che include il meccanismo per la pubblicazione della diagnostica per l'operatore utente.

```csharp
    public abstract class StreamingContext
    {
        public abstract StreamingDiagnostics Diagnostics { get; }
    }
```

`StreamingDiagnostics`è la diagnostica per gli operatori definiti dall'utente, inclusi serializzatore, deserializzatore e funzioni definite dall'utente.

`WriteError`scrive un messaggio di errore nei log di diagnostica e invia l'errore alla diagnostica.

`briefMessage`è un breve messaggio di errore. Questo messaggio viene visualizzato nella diagnostica e viene utilizzato dal team del prodotto per scopi di debug. Non includere informazioni riservate e mantenere il messaggio inferiore a 200 caratteri

`detailedMessage`è un messaggio di errore dettagliato che viene aggiunto solo ai log di diagnostica nell'archivio. Questo messaggio deve essere inferiore a 2000 caratteri.

```csharp
    public abstract class StreamingDiagnostics
    {
        public abstract void WriteError(string briefMessage, string detailedMessage);
    }
```

## <a name="deserializer-examples"></a>Esempi di deserializzatoreDeserializer examples

In questa sezione viene illustrato come scrivere deserializzatori personalizzati per Protobuf e CSV. Per altri esempi, ad esempio il formato AVRO per Event Hub Capture, visitare [Azure Stream Analytics in GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers).

### <a name="protocol-buffer-protobuf-format"></a>Formato buffer protocollo (Protobuf)

Questo è un esempio di utilizzo del formato del buffer di protocollo.

Si supponga la seguente definizione di buffer di protocollo.

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

L'esecuzione `protoc.exe` da **Google.Protobuf.Tools** NuGet genera un file con estensione cs con la definizione. Il file generato non viene visualizzato qui.

Il frammento di codice seguente è l'implementazione del deserializzatore presupponendo che il file generato sia incluso nel progetto. Questa implementazione è solo un wrapper sottile sul file generato.

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

Il frammento di codice seguente è un semplice deserializzatore CSV che illustra anche gli errori di propagazione.

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

## <a name="serialization-format-for-rest-apis"></a>Formato di serializzazione per le API RESTSerialization format for REST APIs

Ogni input di Analisi di flusso ha un formato di **serializzazione.** Per altre informazioni sulle opzioni di input, vedere la documentazione [dell'API REST di input.](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-input)

Il codice Javascript seguente è un esempio del formato di serializzazione del deserializzatore .NET quando si usa l'API REST:

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

`serializationClassName`deve essere una `StreamDeserializer<T>`classe che implementa . Ciò è descritto nella sezione seguente.

## <a name="region-support"></a>Supporto di area

Questa funzione è disponibile nelle seguenti aree geografiche:

* Stati Uniti centro-occidentali
* Europa settentrionale
* Stati Uniti orientali
* Stati Uniti occidentali
* Stati Uniti orientali 2
* Europa occidentale

È possibile richiedere supporto per altre [aree](https://aka.ms/ccodereqregion) geografiche.

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="when-will-this-feature-be-available-in-all-azure-regions"></a>Quando questa funzionalità sarà disponibile in tutte le aree di Azure?

Questa funzione è disponibile in [6 regioni.](https://docs.microsoft.com/azure/stream-analytics/custom-deserializer-examples#region-support) Se si è interessati a utilizzare questa funzionalità in un'altra area, è possibile [inviare una richiesta](https://aka.ms/ccodereqregion). Il supporto per tutte le aree di Azure è nella roadmap.

### <a name="can-i-access-metadatapropertyvalue-from-my-inputs-similar-to-getmetadatapropertyvalue-function"></a>È possibile accedere a MetadataPropertyValue dagli input in modo simile alla funzione GetMetadataPropertyValue?

Questa funzionalità non è supportata. Se è necessaria questa funzionalità, è possibile votare questa richiesta su [UserVoice](https://feedback.azure.com/forums/270577-stream-analytics/suggestions/38779801-accessing-input-metadata-properties-in-custom-dese).

### <a name="can-i-share-my-deserializer-implementation-with-the-community-so-that-others-can-benefit"></a>Posso condividere la mia implementazione del deserializzatore con la comunità in modo che gli altri possano beneficiarne?

Una volta implementato il tuo deserializzatore, puoi aiutare gli altri condividendolo con la comunità. Inviare il codice al repository GitHub di Analisi di flusso di Azure.Submit your code to the [Azure Stream Analytics GitHub repo](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers).

## <a name="next-steps"></a>Passaggi successivi

* [Deserializzatori personalizzati .NET per i processi cloud di Analisi di flusso di Azure](custom-deserializer.md)
