---
title: 'API REST: Operazioni del file system in Azure Data Lake Storage Gen1 | Microsoft Docs'
description: Usare le API REST WebHDFS per eseguire operazioni del file system in Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 351c92f1e1a698893f61004d523ba79ebca253e8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60878784"
---
# <a name="filesystem-operations-on-azure-data-lake-storage-gen1-using-rest-api"></a>Operazioni del file system in Azure Data Lake Storage Gen1 usando l'API REST
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [SDK per Java](data-lake-store-get-started-java-sdk.md)
> * [API REST](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

In questo articolo, viene descritto come usare le API REST WebHDFS e le API REST di Data Lake Storage Gen1 per eseguire operazioni del file system in Azure Data Lake Storage Gen1. Per istruzioni su come eseguire operazioni di gestione account in Data Lake Storage Gen1 usando l'API REST, consultare [Operazioni di gestione dell'account su Data Lake Storage Gen1 usando l'API REST](data-lake-store-get-started-rest-api.md).

## <a name="prerequisites"></a>Prerequisiti
* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Account Azure Data Lake Storage Gen1**. Seguire le istruzioni fornite in [Introduzione ad Azure Data Lake Storage Gen1 con il portale di Azure](data-lake-store-get-started-portal.md).

* **[cURL](https://curl.haxx.se/)**. In questo articolo viene usato cURL per illustrare come effettuare chiamate API REST con un account Data Lake Storage Gen1.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Come si esegue l'autenticazione tramite Azure Active Directory?
È possibile adottare due approcci per l'autenticazione tramite Azure Active Directory.

* Per l'autenticazione dell'utente finale per l'applicazione (interattiva), consultare [Autenticazione dell'utente finale con Data Lake Storage Gen1 tramite .NET SDK](data-lake-store-end-user-authenticate-rest-api.md).
* Per l'autenticazione da servizio a servizio per l'applicazione (non interattiva), vedere [Autenticazione da servizio a servizio con Data Lake Storage Gen1 tramite .NET SDK](data-lake-store-service-to-service-authenticate-rest-api.md).


## <a name="create-folders"></a>Creare cartelle
Questa operazione si basa sulla chiamata API REST WebHDFS definita [qui](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Make_a_Directory).

Usare il comando cURL seguente. Sostituire **\<yourstorename>** con il nome dell'account Data Lake Storage Gen1.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=MKDIRS'

Nel comando precedente sostituire \<`REDACTED`\> con il token di autorizzazione recuperato in precedenza. Questo comando crea una directory denominata **mytempdir** sotto la cartella radice dell'account Data Lake Storage Gen1.

Se l'operazione viene completata correttamente, verrà visualizzata una risposta simile al frammento di codice seguente:

    {"boolean":true}

## <a name="list-folders"></a>Elencare le cartelle
Questa operazione si basa sulla chiamata API REST WebHDFS definita [qui](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#List_a_Directory).

Usare il comando cURL seguente. Sostituire **\<yourstorename>** con il nome dell'account Data Lake Storage Gen1.

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
Questa operazione si basa sulla chiamata API REST WebHDFS definita [qui](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Create_and_Write_to_a_File).

Usare il comando cURL seguente. Sostituire **\<yourstorename>** con il nome dell'account Data Lake Storage Gen1.

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
Questa operazione si basa sulla chiamata API REST WebHDFS definita [qui](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Open_and_Read_a_File).

La lettura dei dati di un account Data Lake Storage Gen1 è un processo in due passaggi.

* È prima necessario inviare una richiesta GET all'endpoint `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN`. Questa chiamata restituisce un percorso a cui inviare la richiesta GET successiva.
* È quindi necessario inviare la richiesta GET all'endpoint `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN&read=true`. La chiamata visualizza i contenuti del file.

Tuttavia, dal momento che non esiste alcuna differenza nei parametri di input tra il primo e il secondo passaggio, è possibile usare il parametro `-L` per inviare la prima richiesta. L'opzione `-L` combina essenzialmente due richieste in una e fa ripetere a cURL la richiesta nel nuovo percorso. Infine, viene visualizzato l'output di tutte le chiamate di richiesta, come illustrato nel frammento di codice seguente. Sostituire **\<yourstorename>** con il nome dell'account Data Lake Storage Gen1.

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
Questa operazione si basa sulla chiamata API REST WebHDFS definita [qui](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Rename_a_FileDirectory).

Per rinominare un file, usare il comando cURL seguente: Sostituire **\<yourstorename>** con il nome dell'account Data Lake Storage Gen1.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=RENAME&destination=/mytempdir/myinputfile1.txt'

L'output visualizzato dovrebbe essere simile al frammento di codice seguente:

    HTTP/1.1 200 OK
    ...

    {"boolean":true}

## <a name="delete-a-file"></a>Eliminare un file
Questa operazione si basa sulla chiamata API REST WebHDFS definita [qui](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Delete_a_FileDirectory).

Utilizzare il comando cURL seguente per eliminare un file. Sostituire **\<yourstorename>** con il nome dell'account Data Lake Storage Gen1.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile1.txt?op=DELETE'

Verrà visualizzato un output simile al seguente:

    HTTP/1.1 200 OK
    ...

    {"boolean":true}

## <a name="next-steps"></a>Passaggi successivi
* [Operazioni di gestione di account in Data Lake Storage Gen1 con API REST](data-lake-store-get-started-rest-api.md).

## <a name="see-also"></a>Vedere anche 
* [Riferimento all'API REST di Azure Data Lake Storage Gen1](https://docs.microsoft.com/rest/api/datalakestore/)
* [Aprire le applicazioni Big Data di origine compatibili con Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)

