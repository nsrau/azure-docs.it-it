---
title: 'API REST: Operazioni del file system in Azure Data Lake Store | Microsoft Docs'
description: Usare le API REST WebHDFS per eseguire operazioni del file system in Data Lake Store
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: a850b3fdff956abe41ac9a4af10a96dc119a75f4
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2018
---
# <a name="filesystem-operations-on-azure-data-lake-store-using-rest-api"></a>Operazioni del file system in Azure Data Lake Store con l'API REST
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [SDK per Java](data-lake-store-get-started-java-sdk.md)
> * [API REST](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

Questo articolo fornisce informazioni per l'uso di API REST WebHDFS e API REST di Data Lake Store per eseguire operazioni del file system in Azure Data Lake Store. Per istruzioni su come eseguire le operazioni di gestione di account in Data Lake Store con l'API REST, vedere [Operazioni di gestione di account in Data Lake Store con l'API REST](data-lake-store-get-started-rest-api.md).

## <a name="prerequisites"></a>Prerequisiti
* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Account di Archivio Data Lake di Azure**. Seguire le istruzioni fornite in [Introduzione ad Azure Data Lake Store con il portale di Azure](data-lake-store-get-started-portal.md).

* **[cURL](http://curl.haxx.se/)**. Questo articolo usa cURL per illustrare come effettuare chiamate API REST con un account Archivio Data Lake.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Come si esegue l'autenticazione tramite Azure Active Directory?
È possibile adottare due approcci per l'autenticazione tramite Azure Active Directory.

* Per l'autenticazione dell'utente finale per l'applicazione (interattiva), vedere [End-user authentication with Data Lake Store using .NET SDK](data-lake-store-end-user-authenticate-rest-api.md) (Autenticazione dell'utente finale con Data Lake Store tramite .NET SDK).
* Per l'autenticazione da servizio a servizio per l'applicazione (non interattiva), vedere [Service-to-service authentication with Data Lake Store using .NET SDK](data-lake-store-service-to-service-authenticate-rest-api.md) (Autenticazione da servizio a servizio con Data Lake Store tramite .NET SDK).


## <a name="create-folders"></a>Creare cartelle
Questa operazione si basa sulla chiamata API REST WebHDFS definita [qui](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Make_a_Directory).

Usare il comando cURL seguente. Sostituire **\<yourstorename>** con il nome di Data Lake Store.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=MKDIRS'

Nel comando precedente sostituire \<`REDACTED`\> con il token di autorizzazione recuperato in precedenza. Questo comando crea una directory denominata **mytempdir** nella cartella radice del proprio account Data Lake Store.

Se l'operazione viene completata correttamente, verrà visualizzata una risposta simile al frammento di codice seguente:

    {"boolean":true}

## <a name="list-folders"></a>Elencare le cartelle
Questa operazione si basa sulla chiamata API REST WebHDFS definita [qui](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#List_a_Directory).

Usare il comando cURL seguente. Sostituire **\<yourstorename>** con il nome di Data Lake Store.

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS'

Nel comando precedente sostituire \<`REDACTED`\> con il token di autorizzazione recuperato in precedenza.

Se l'operazione viene completata correttamente, verrà visualizzata una risposta simile al frammento di codice seguente:

    {
    "FileStatuses": {
        "FileStatus": [{
            "length": 0,
            "pathSuffix": "mytempdir",
            "type": "DIRECTORY",
            "blockSize": 268435456,
            "accessTime": 1458324719512,
            "modificationTime": 1458324719512,
            "replication": 0,
            "permission": "777",
            "owner": "<GUID>",
            "group": "<GUID>"
        }]
    }
    }

## <a name="upload-data"></a>Caricare dati
Questa operazione si basa sulla chiamata API REST WebHDFS definita [qui](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Create_and_Write_to_a_File).

Usare il comando cURL seguente. Sostituire **\<yourstorename>** con il nome di Data Lake Store.

    curl -i -X PUT -L -T 'C:\temp\list.txt' -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/list.txt?op=CREATE'

Nella sintassi precedente il parametro **-T** indica la posizione del file che si sta caricando.

L'output è simile al frammento di codice seguente:
   
    HTTP/1.1 307 Temporary Redirect
    ...
    Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/list.txt?op=CREATE&write=true
    ...
    Content-Length: 0

    HTTP/1.1 100 Continue

    HTTP/1.1 201 Created
    ...

## <a name="read-data"></a>Leggere i dati
Questa operazione si basa sulla chiamata API REST WebHDFS definita [qui](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Open_and_Read_a_File).

La lettura dei dati da un account Archivio Data Lake è un processo in due fasi.

* È prima necessario inviare una richiesta GET all'endpoint `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN`. Questa chiamata restituisce un percorso a cui inviare la richiesta GET successiva.
* È quindi necessario inviare la richiesta GET all'endpoint `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN&read=true`. La chiamata visualizza i contenuti del file.

Tuttavia, dal momento che non esiste alcuna differenza nei parametri di input tra il primo e il secondo passaggio, è possibile usare il parametro `-L` per inviare la prima richiesta. L'opzione `-L` combina essenzialmente due richieste in una e fa ripetere a cURL la richiesta nel nuovo percorso. Infine, viene visualizzato l'output di tutte le chiamate di richiesta, come illustrato nel frammento di codice seguente. Sostituire **\<yourstorename>** con il nome di Data Lake Store.

    curl -i -L GET -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN'

L'output visualizzato dovrebbe essere simile al frammento di codice seguente:

    HTTP/1.1 307 Temporary Redirect
    ...
    Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=OPEN&read=true
    ...

    HTTP/1.1 200 OK
    ...

    Hello, Data Lake Store user!

## <a name="rename-a-file"></a>Rinominare un file
Questa operazione si basa sulla chiamata API REST WebHDFS definita [qui](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Rename_a_FileDirectory).

Per rinominare un file, usare il comando cURL seguente: Sostituire **\<yourstorename>** con il nome di Data Lake Store.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=RENAME&destination=/mytempdir/myinputfile1.txt'

L'output visualizzato dovrebbe essere simile al frammento di codice seguente:

    HTTP/1.1 200 OK
    ...

    {"boolean":true}

## <a name="delete-a-file"></a>Eliminare un file
Questa operazione si basa sulla chiamata API REST WebHDFS definita [qui](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Delete_a_FileDirectory).

Utilizzare il comando cURL seguente per eliminare un file. Sostituire **\<yourstorename>** con il nome di Data Lake Store.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile1.txt?op=DELETE'

Verrà visualizzato un output simile al seguente:

    HTTP/1.1 200 OK
    ...

    {"boolean":true}

## <a name="next-steps"></a>Passaggi successivi
* [Operazioni di gestione di account in Data Lake Store con l'API REST](data-lake-store-get-started-rest-api.md).

## <a name="see-also"></a>Vedere anche 
* [Azure Data Lake Store REST API Reference (Informazioni di riferimento sull'API REST di Data Lake Store)](https://docs.microsoft.com/rest/api/datalakestore/)
* [Aprire le applicazioni Big Data di origine che funzionano con Archivio Azure Data Lake](data-lake-store-compatible-oss-other-applications.md)

