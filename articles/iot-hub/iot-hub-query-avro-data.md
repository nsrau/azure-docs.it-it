---
title: Eseguire query sui dati di Avro con Azure Data Lake Analytics | Microsoft Docs
description: Usare le proprietà del corpo del messaggio per instradare i dati di telemetria del dispositivo all'archivio BLOB ed eseguire query sui dati in formato Avro scritti nell'archivio BLOB.
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: asrastog
ms.openlocfilehash: 69c890cfc3db04fe625ed7ad008f545c01844834
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61441591"
---
# <a name="query-avro-data-by-using-azure-data-lake-analytics"></a>Eseguire query sui dati di Avro con Azure Data Lake Analytics

Questo articolo illustra come eseguire query sui dati di Avro per un routing efficiente dei messaggi provenienti dall'hub IoT di Azure ai servizi di Azure. [Routing dei messaggi](iot-hub-devguide-messages-d2c.md) consente di filtrare i dati usando query avanzate basate sulle proprietà dei messaggi, sul corpo del messaggio, sui tag e sulle proprietà del dispositivo gemello. Per altre informazioni sulle funzionalità di query in Routing messaggi, vedere l'articolo sulla sintassi di query di routing messaggi. 
<!--[Message Routing Query Syntax](iot-hub-devguide-routing-query-syntax.md). I don't have this article yet. -->

Il problema è che quando l'hub IoT di Azure instrada i messaggi all'archivio BLOB di Azure scrive il contenuto in formato Avro, che include una proprietà del messaggio e una proprietà del corpo del messaggio. L'hub IoT supporta la scrittura dei dati nell'archivio BLOB solo nel formato dei dati Avro e questo formato non è usato per nessun altro endpoint. Per altre informazioni, vedere uno degli articoli sull’uso dei contenitori di Archiviazione di Azure. Anche se il formato Avro è molto utile per la conservazione dei dati e dei messaggi, è difficile da usare per eseguire query sui dati. In confronto, il formato JSON o CSV è molto più semplice per l'esecuzione di query sui dati.

<!-- https://review.docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c?branch=pr-en-us-51566#azure-blob-storage  NEW LINK FOR 'WHEN USING STORAGE CONTAINERS' -->

Per soddisfare esigenze e formati di Big Data non relazionali e superare questo problema, è possibile usare molti dei modelli di Big Data per la trasformazione e il ridimensionamento dei dati. Uno dei modelli, basato sul "pagamento per query" è Azure Data Lake Analytics, che viene illustrato in dettaglio in questo articolo. Nonostante sia possibile eseguire facilmente la query in Hadoop o con altre soluzioni, Data Lake Analytics è spesso più indicato per questo approccio basato sul "pagamento per query". 

È disponibile un "estrattore" per Avro in U-SQL. Per altre informazioni, vedere [U-SQL Avro example](https://github.com/Azure/usql/tree/master/Examples/AvroExamples) (Esempio Avro U-SQL).

## <a name="query-and-export-avro-data-to-a-csv-file"></a>Eseguire query ed esportare i dati di Avro in un file CSV
In questa sezione viene eseguita una query sui dati di Avro e l'esportazione in un file CSV in Archiviazione BLOB di Azure, anche se è possibile inserire i dati in altri repository o archivi dati.

1. Configurare l'hub IoT di Azure per instradare i dati a un endpoint di Archiviazione BLOB di Azure usando una proprietà nel corpo del messaggio per selezionare i messaggi.

   ![La sezione "endpoint personalizzati"](./media/iot-hub-query-avro-data/query-avro-data-1a.png)

   ![Le regole di routing](./media/iot-hub-query-avro-data/query-avro-data-1b.png)

   Per altre informazioni sulle impostazioni di route e sugli endpoint personalizzati, vedere [Routing dei messaggi per un hub IoT](iot-hub-create-through-portal.md#message-routing-for-an-iot-hub).

2. Verificare che il dispositivo abbia la codifica, il tipo di contenuto e i dati necessari nelle proprietà o nel corpo del messaggio, come indicato nella documentazione del prodotto. Quando gli attributi vengono visualizzati in Device Explorer come indicato qui di seguito, è possibile verificare che questi siano configurati correttamente.

   ![Il riquadro Dati di hub eventi](./media/iot-hub-query-avro-data/query-avro-data-2.png)

3. Configurare un'istanza di Azure Data Lake Store e un'istanza di Azure Data Lake Analytics. L'hub IoT non viene indirizzato a un'istanza di Data Lake Store, ma un'istanza di Data Lake Analytics ne richiede uno.

   ![Istanze di Data Lake Store e Data Lake Analytics](./media/iot-hub-query-avro-data/query-avro-data-3.png)

4. In Data Lake Analytics configurare l'Archiviazione BLOB di Azure come archivio aggiuntivo, la stessa risorsa di archiviazione BLOB a cui l'hub IoT instrada i dati.

   ![Il riquadro "Origini dati"](./media/iot-hub-query-avro-data/query-avro-data-4.png)
 
5. Come descritto in [Esempio di Avro U-SQL](https://github.com/Azure/usql/tree/master/Examples/AvroExamples), sono necessari quattro file DLL. Caricare questi file in un percorso nell'istanza Data Lake Store.

   ![Quattro file DLL caricati](./media/iot-hub-query-avro-data/query-avro-data-5.png)

6. In Visual Studio creare un progetto U-SQL.
 
   !Create a U-SQL project](./media/iot-hub-query-avro-data/query-avro-data-6.png)

7. Incollare il contenuto dello script seguente nel file appena creato. Modificare le tre sezioni evidenziate: l'account Data Lake Analytics, i percorsi dei file DLL associati e il percorso corretto dell'account di archiviazione.
    
   ![Le tre sezioni da modificare](./media/iot-hub-query-avro-data/query-avro-data-7a.png)

   Ecco lo script U-SQL effettivo per un semplice output in un file CSV:
    
    ```sql
        DROP ASSEMBLY IF EXISTS [Avro];
        CREATE ASSEMBLY [Avro] FROM @"/Assemblies/Avro/Avro.dll";
        DROP ASSEMBLY IF EXISTS [Microsoft.Analytics.Samples.Formats];
        CREATE ASSEMBLY [Microsoft.Analytics.Samples.Formats] FROM @"/Assemblies/Avro/Microsoft.Analytics.Samples.Formats.dll";
        DROP ASSEMBLY IF EXISTS [Newtonsoft.Json];
        CREATE ASSEMBLY [Newtonsoft.Json] FROM @"/Assemblies/Avro/Newtonsoft.Json.dll";
        DROP ASSEMBLY IF EXISTS [log4net];
        CREATE ASSEMBLY [log4net] FROM @"/Assemblies/Avro/log4net.dll";

        REFERENCE ASSEMBLY [Newtonsoft.Json];
        REFERENCE ASSEMBLY [log4net];
        REFERENCE ASSEMBLY [Avro];
        REFERENCE ASSEMBLY [Microsoft.Analytics.Samples.Formats];

        // Blob container storage account filenames, with any path
        DECLARE @input_file string = @"wasb://hottubrawdata@kevinsayazstorage/kevinsayIoT/{*}/{*}/{*}/{*}/{*}/{*}";
        DECLARE @output_file string = @"/output/output.csv";

        @rs =
        EXTRACT
        EnqueuedTimeUtc string,
        Body byte[]
        FROM @input_file

        USING new Microsoft.Analytics.Samples.Formats.ApacheAvro.AvroExtractor(@"
        {
            ""type"":""record"",
            ""name"":""Message"",
            ""namespace"":""Microsoft.Azure.Devices"",
            ""fields"":
           [{
                ""name"":""EnqueuedTimeUtc"",
                ""type"":""string""
            },
            {
                ""name"":""Properties"",
                ""type"":
                {
                    ""type"":""map"",
                    ""values"":""string""
                }
            },
            {
                ""name"":""SystemProperties"",
                ""type"":
                {
                    ""type"":""map"",
                    ""values"":""string""
                }
            },
            {
                ""name"":""Body"",
                ""type"":[""null"",""bytes""]
            }]
        }"
        );

        @cnt =
        SELECT EnqueuedTimeUtc AS time, Encoding.UTF8.GetString(Body) AS jsonmessage
        FROM @rs;

        OUTPUT @cnt TO @output_file USING Outputters.Text(); 
    ```    

    Data Lake Analytics ha impiegato cinque minuti per eseguire lo script seguente, che è stato limitato a 10 unità di analisi ed elaborato 177 file. Il risultato è illustrato nell'output del file CSV che viene visualizzato nell'immagine seguente:
    
    ![Risultati dell'output in un file CSV](./media/iot-hub-query-avro-data/query-avro-data-7b.png)

    ![Output convertito in file CSV](./media/iot-hub-query-avro-data/query-avro-data-7c.png)

    Per analizzare JSON, andare al passaggio 8.
    
8. La maggior parte dei messaggi IoT è in formato di file JSON. Aggiungendo le righe seguenti, è possibile analizzare il messaggio in un file JSON, che consente di aggiungere clausole WHERE e restituire solo i dati necessari.

    ```sql
       @jsonify = 
         SELECT Microsoft.Analytics.Samples.Formats.Json.JsonFunctions.JsonTuple(Encoding.UTF8.GetString(Body)) 
           AS message FROM @rs;
    
        /*
        @cnt =
            SELECT EnqueuedTimeUtc AS time, Encoding.UTF8.GetString(Body) AS jsonmessage
            FROM @rs;
        
        OUTPUT @cnt TO @output_file USING Outputters.Text();
        */
        
        @cnt =
            SELECT message["message"] AS iotmessage,
                   message["event"] AS msgevent,
                   message["object"] AS msgobject,
                   message["status"] AS msgstatus,
                   message["host"] AS msghost
            FROM @jsonify;
            
        OUTPUT @cnt TO @output_file USING Outputters.Text();
    ```

    L'output mostra una colonna per ogni elemento nel comando `SELECT`. 
    
    ![Output che mostra una colonna per ogni elemento](./media/iot-hub-query-avro-data/query-avro-data-8.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come eseguire query sui dati di Avro per un routing efficiente dei messaggi provenienti dall'hub IoT ai servizi di Azure.

Per avere degli esempi di soluzioni end-to-end che usano l'hub IoT, vedere la[documentazione degli acceleratori di soluzioni IoT di Azure](/azure/iot-accelerators).

Per altre informazioni sullo sviluppo delle soluzioni con l'hub IoT, vedere la [Guida per sviluppatori dell'hub IoT](iot-hub-devguide.md).

Per altre informazioni sul routing dei messaggi nell'hub IoT, vedere [Inviare e ricevere messaggi con l'hub IoT](iot-hub-devguide-messaging.md).
