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
ms.openlocfilehash: 98a30155c73a937042b4bea6568543fb5152d748
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2018
ms.locfileid: "34726679"
---
# <a name="query-avro-data-using-azure-data-lake-analytics"></a>Eseguire query sui dati di Avro con Azure Data Lake Analytics

Questo articolo descrive come eseguire query sui dati di Avro per un routing efficiente dei messaggi provenienti dall'hub IoT di Azure ai servizi di Azure. In seguito all'annuncio nel post di blog, [Azure IoT Hub message routing: now with routing on message body] (Routing dei messaggi dell'hub IoT di Azure: ora disponibile il routing nel corpo del messaggio), l'hub IoT supporta il routing delle proprietà o del corpo del messaggio. Vedere anche [Routing del corpo dei messaggi][Routing on message bodies]. 

Il problema è che quando l'hub IoT di Azure instrada i messaggi all'archivio BLOB, l'hub IoT scrive il contenuto in formato Avro, che include le proprietà del messaggio e del corpo del messaggio. Si noti che l'hub IoT supporta solo la scrittura dei dati nell'archivio BLOB nel formato dei dati Avro e questo formato non è usato per nessun altro endpoint. Vedere [Uso dei contenitori di Archiviazione di Azure][When using Azure storage containers]. Benché il formato Avro sia l'ideale per la conservazione di dati/messaggi, crea problemi per l'esecuzione di query sui dati. In confronto, il formato JSON o CSV è molto più semplice per l'esecuzione di query sui dati.

Per risolvere questo problema, è possibile usare molti dei modelli di Big Data per la trasformazione e il ridimensionamento dei dati per soddisfare esigenze e formati di Big Data non relazionali. Uno dei modelli, basato sul "pagamento per query", è Azure Data Lake Analytics (ADLA), che viene illustrato in dettaglio in questo articolo. Anche se è possibile eseguire facilmente la query in Hadoop o con altre soluzioni, ADLA è spesso più indicato per questo approccio basato sul "pagamento per query". È disponibile un "estrattore" per Avro in U-SQL. Vedere [U-SQL Avro Example] (Esempio di Avro U-SQL).

## <a name="query-and-export-avro-data-to-a-csv-file"></a>Eseguire query ed esportare i dati di Avro in un file CSV
Questa sezione illustra in dettaglio l'esecuzione di query sui dati di Avro e l'esportazione in un file CSV in Archiviazione BLOB di Azure, anche se è possibile inserire i dati in altri repository o archivi dati.

1. Configurare l'hub IoT di Azure per instradare i dati a un endpoint di Archiviazione BLOB di Azure usando una proprietà nel corpo del messaggio per selezionare i messaggi.

    ![Acquisizione di schermata per il passaggio 1a][img-query-avro-data-1a]

    ![Acquisizione di schermata per il passaggio 1b][img-query-avro-data-1b]

2. Verificare che il dispositivo abbia la codifica, il tipo di contenuto e i dati necessari nelle proprietà o nel corpo del messaggio, come indicato nella documentazione del prodotto. Quando viene visualizzato in Device Explorer (vedere sotto), è possibile verificare che questi attributi siano configurati correttamente.

    ![Acquisizione di schermata per il passaggio 2][img-query-avro-data-2]

3. Configurare un Azure Data Lake Store (ADLS) e un'istanza di Azure Data Lake Analytics. Anche se l'hub IoT di Azure non esegue il routing a un Azure Data Lake Store, ADLA ne richiede uno.

    ![Acquisizione di schermata per il passaggio 3][img-query-avro-data-3]

4. In ADLA configurare l'Archiviazione BLOB di Azure come archivio aggiuntivo, la stessa risorsa di archiviazione BLOB a cui l'hub IoT di Azure instrada i dati.

    ![Acquisizione di schermata per il passaggio 4][img-query-avro-data-4]
 
5. Come descritto in [U-SQL Avro Example] (Esempio di Avro U-SQL), sono necessarie 4 DLL.  Caricare questi file in un percorso in ADLS.

    ![Acquisizione di schermata per il passaggio 5][img-query-avro-data-5] 

6. In Visual Studio creare un progetto U-SQL.
 
    ![Acquisizione di schermata per il passaggio 6][img-query-avro-data-6]

7. Copiare il contenuto dello script seguente e incollarlo nel file appena creato. Modificare le tre sezioni evidenziate: l'account ADLA, i percorsi delle DLL associate e il percorso corretto dell'account di archiviazione.
    
    ![Acquisizione di schermata per il passaggio 7a][img-query-avro-data-7a]

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

    Per eseguire lo script riportato di seguito, ADLA ha impiegato 5 minuti limitatamente a 10 unità di analisi e ha elaborato 177 file, riepilogando l'output in un file CSV.
    
    ![Acquisizione di schermata per il passaggio 7b][img-query-avro-data-7b]

    Visualizzando l'output, è possibile notare che il contenuto di Avro è stato convertito in un file CSV. Se si vuole analizzare JSON, andare al passaggio 8.
    
    ![Acquisizione di schermata per il passaggio 7c][img-query-avro-data-7c]

    
8. La maggior parte dei messaggi IoT è in formato JSON.  Aggiungendo le righe seguenti, è possibile analizzare il messaggio in JSON, pertanto è possibile aggiungere clausole WHERE e restituire solo i dati necessari.

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

9. Visualizzando l'output, si notano ora colonne per ogni elemento nel comando select. 
    
    ![Acquisizione di schermata per il passaggio 8][img-query-avro-data-8]

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione si è appreso come eseguire query sui dati di Avro per un routing efficiente dei messaggi provenienti dall'hub IoT di Azure ai servizi di Azure.

Per avere degli esempi di soluzioni complete che usano l'hub IoT, vedere l'[acceleratore di soluzioni di monitoraggio remoto di Azure IoT][lnk-iot-sa-land].

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

[lnk-iot-sa-land]: ../iot-accelerators/index.md
[Guida per sviluppatori dell'hub IoT]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
