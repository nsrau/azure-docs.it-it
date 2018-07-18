---
title: Eseguire query sui dati di Avro con Azure Data Lake Analytics | Microsoft Docs
description: Usare le proprietà del corpo del messaggio per instradare i dati di telemetria del dispositivo all'archivio BLOB ed eseguire query sui dati in formato Avro scritti nell'archivio BLOB.
services: iot-hub
documentationcenter: ''
author: ksaye
manager: obloch
ms.service: iot-hub
ms.topic: article
ms.date: 05/29/2018
ms.author: Kevin.Saye
ms.openlocfilehash: c56b567498047ee996018675134c252ec1de7e0c
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2018
ms.locfileid: "37081369"
---
# <a name="query-avro-data-by-using-azure-data-lake-analytics"></a>Eseguire query sui dati di Avro con Azure Data Lake Analytics

Questo articolo illustra come eseguire query sui dati di Avro per un routing efficiente dei messaggi provenienti dall'hub IoT di Azure ai servizi di Azure. Come annunciato nel post di blog, [Azure IoT Hub message routing: now with routing on message body] (Routing dei messaggi dell'hub IoT di Azure: ora disponibile con il routing nel corpo del messaggio), l'hub IoT supporta il routing nelle proprietà o nel corpo del messaggio. Per altre informazioni, vedere [Routing nel corpo dei messaggi][Routing on message bodies]. 

Il problema è che quando l'hub IoT di Azure instrada i messaggi all'archivio BLOB di Azure scrive il contenuto in formato Avro, che include una proprietà del messaggio e una proprietà del corpo del messaggio. L'hub IoT supporta la scrittura dei dati nell'archivio BLOB solo nel formato dei dati Avro e questo formato non è usato per nessun altro endpoint. Per altre informazioni, vedere [Uso dei contenitori di Archiviazione di Azure][When using Azure storage containers]. Anche se il formato Avro è molto utile per la conservazione dei dati e dei messaggi, è difficile da usare per eseguire query sui dati. In confronto, il formato JSON o CSV è molto più semplice per l'esecuzione di query sui dati.

Per soddisfare esigenze e formati di Big Data non relazionali e superare questo problema, è possibile usare molti dei modelli di Big Data per la trasformazione e il ridimensionamento dei dati. Uno dei modelli, basato sul "pagamento per query" è Azure Data Lake Analytics, che viene illustrato in dettaglio in questo articolo. Anche se è possibile eseguire facilmente la query in Hadoop o con altre soluzioni, Data Lake Analytics è spesso più indicato per questo approccio basato sul "pagamento per query". 

È disponibile un "estrattore" per Avro in U-SQL. Per altre informazioni, vedere [U-SQL Avro example] (Esempio Avro U-SQL).

## <a name="query-and-export-avro-data-to-a-csv-file"></a>Eseguire query ed esportare i dati di Avro in un file CSV
In questa sezione viene eseguita una query sui dati di Avro e l'esportazione in un file CSV in Archiviazione BLOB di Azure, anche se è possibile inserire i dati in altri repository o archivi dati.

1. Configurare l'hub IoT di Azure per instradare i dati a un endpoint di Archiviazione BLOB di Azure usando una proprietà nel corpo del messaggio per selezionare i messaggi.

    ![La sezione "endpoint personalizzati"][img-query-avro-data-1a]

    ![Il comando Route][img-query-avro-data-1b]

2. Verificare che il dispositivo abbia la codifica, il tipo di contenuto e i dati necessari nelle proprietà o nel corpo del messaggio, come indicato nella documentazione del prodotto. Quando gli attributi vengono visualizzati in Device Explorer come indicato qui di seguito, è possibile verificare che questi siano configurati correttamente.

    ![Il riquadro Dati di hub eventi][img-query-avro-data-2]

3. Configurare un'istanza di Azure Data Lake Store e un'istanza di Azure Data Lake Analytics. L'hub IoT non viene indirizzato a un'istanza di Data Lake Store, ma un'istanza di Data Lake Analytics ne richiede uno.

    ![Istanze di Data Lake Store e Data Lake Analytics][img-query-avro-data-3]

4. In Data Lake Analytics configurare l'Archiviazione BLOB di Azure come archivio aggiuntivo, la stessa risorsa di archiviazione BLOB a cui l'hub IoT instrada i dati.

    ![Il riquadro "Origini dati"][img-query-avro-data-4]
 
5. Come descritto in [U-SQL Avro example] (Esempio di Avro U-SQL), sono necessari quattro file DLL. Caricare questi file in un percorso nell'istanza Data Lake Store.

    ![Quattro file DLL caricati][img-query-avro-data-5] 

6. In Visual Studio creare un progetto U-SQL.
 
    ![Creare uno script U-SQL][img-query-avro-data-6]

7. Incollare il contenuto dello script seguente nel file appena creato. Modificare le tre sezioni evidenziate: l'account Data Lake Analytics, i percorsi dei file DLL associati e il percorso corretto dell'account di archiviazione.
    
    ![Le tre sezioni da modificare][img-query-avro-data-7a]

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
        ""fields"":[{
        ""name"":""EnqueuedTimeUtc"",
        ""type"":""string""
        },
        {
        ""name"":""Properties"",
        ""type"":{
        ""type"":""map"",
        ""values"":""string""
        }
        },
        {
        ""name"":""SystemProperties"",
        ""type"":{
        ""type"":""map"",
        ""values"":""string""
        }
        },
        {
        ""name"":""Body"",
        ""type"":[""null"",""bytes""]
        }
        ]
        }");

        @cnt =
        SELECT EnqueuedTimeUtc AS time, Encoding.UTF8.GetString(Body) AS jsonmessage
        FROM @rs;

        OUTPUT @cnt TO @output_file USING Outputters.Text(); 
    ```    

    Data Lake Analytics ha impiegato cinque minuti per eseguire lo script seguente, che è stato limitato a 10 unità di analisi ed elaborato 177 file. Il risultato è illustrato nell'output del file CSV che viene visualizzato nell'immagine seguente:
    
    ![Risultati dell'output in un file CSV][img-query-avro-data-7b]

    ![Output convertito in file CSV][img-query-avro-data-7c]

    Per analizzare JSON, andare al passaggio 8.
    
8. La maggior parte dei messaggi IoT è in formato di file JSON. Aggiungendo le righe seguenti, è possibile analizzare il messaggio in un file JSON, che consente di aggiungere clausole WHERE e restituire solo i dati necessari.

    ```sql
       @jsonify = SELECT Microsoft.Analytics.Samples.Formats.Json.JsonFunctions.JsonTuple(Encoding.UTF8.GetString(Body)) AS message FROM @rs;
    
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
    
    ![Output che mostra una colonna per ogni elemento][img-query-avro-data-8]

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione si è appreso come eseguire query sui dati di Avro per un routing efficiente dei messaggi provenienti dall'hub IoT ai servizi di Azure.

Per avere degli esempi di soluzioni complete che usano l'hub IoT, vedere l'[Acceleratore di soluzioni di monitoraggio remoto di Azure IoT][lnk-iot-sa-land].

Per altre informazioni sullo sviluppo delle soluzioni con l'hub IoT, vedere la [Guida per sviluppatori dell'hub IoT].

Per ulteriori informazioni sul routing dei messaggi nell'hub IoT, vedere [Inviare e ricevere messaggi con l'hub IoT][lnk-devguide-messaging].

<!-- Images -->
[img-query-avro-data-1a]: ./media/iot-hub-query-avro-data/query-avro-data-1a.png
[img-query-avro-data-1b]: ./media/iot-hub-query-avro-data/query-avro-data-1b.png
[img-query-avro-data-2]: ./media/iot-hub-query-avro-data/query-avro-data-2.png
[img-query-avro-data-3]: ./media/iot-hub-query-avro-data/query-avro-data-3.png
[img-query-avro-data-4]: ./media/iot-hub-query-avro-data/query-avro-data-4.png
[img-query-avro-data-5]: ./media/iot-hub-query-avro-data/query-avro-data-5.png
[img-query-avro-data-6]: ./media/iot-hub-query-avro-data/query-avro-data-6.png
[img-query-avro-data-7a]: ./media/iot-hub-query-avro-data/query-avro-data-7a.png
[img-query-avro-data-7b]: ./media/iot-hub-query-avro-data/query-avro-data-7b.png
[img-query-avro-data-7c]: ./media/iot-hub-query-avro-data/query-avro-data-7c.png
[img-query-avro-data-8]: ./media/iot-hub-query-avro-data/query-avro-data-8.png

<!-- Links -->
[Azure IoT Hub message routing: now with routing on message body]: https://azure.microsoft.com/blog/iot-hub-message-routing-now-with-routing-on-message-body/ (Routing dei messaggi dell'hub IoT di Azure: ora disponibile il routing nel corpo del messaggio)

[Routing on message bodies]: iot-hub-devguide-query-language.md#routing-on-message-bodies
[When using Azure storage containers]:iot-hub-devguide-endpoints.md#when-using-azure-storage-containers

[U-SQL Avro Example]:https://github.com/Azure/usql/tree/master/Examples/AvroExamples (Esempio di Avro U-SQL)

[lnk-iot-sa-land]: ../iot-accelerators/index.yml
[Guida per sviluppatori dell'hub IoT]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
