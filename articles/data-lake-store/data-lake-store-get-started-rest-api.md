---
title: 'API REST: Operazioni di gestione di account in Azure Data Lake Store | Microsoft Docs'
description: Usare Azure Data Lake Store e l'API REST WebHDFS per eseguire operazioni di gestione di account in Data Lake Store
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 57ac6501-cb71-4f75-82c2-acc07c562889
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/28/2017
ms.author: nitinme
ms.openlocfilehash: 6c43f2b341280731707e486ba6f22f11560102c6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="account-management-operations-on-azure-data-lake-store-using-rest-api"></a>Operazioni di gestione di account in Azure Data Lake Store con l'API REST
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [API REST](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Questo articolo fornisce informazioni per l'esecuzione di operazioni di gestione di account in Data Lake Store con l'API REST. Le operazioni di gestione di account includono la creazione di un account Data Lake Store, l'eliminazione di un account Data Lake Store e così via. Per istruzioni su come eseguire le operazioni del file system in Data Lake Store con l'API REST, vedere [Operazioni del file system in Data Lake Store con l'API REST](data-lake-store-data-operations-rest-api.md).

## <a name="prerequisites"></a>Prerequisiti
* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

* **[cURL](http://curl.haxx.se/)**. Questo articolo usa cURL per illustrare come effettuare chiamate API REST con un account Archivio Data Lake.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Come si esegue l'autenticazione tramite Azure Active Directory?
È possibile adottare due approcci per l'autenticazione tramite Azure Active Directory.

* Per l'autenticazione dell'utente finale per l'applicazione (interattiva), vedere [End-user authentication with Data Lake Store using .NET SDK](data-lake-store-end-user-authenticate-rest-api.md) (Autenticazione dell'utente finale con Data Lake Store tramite .NET SDK).
* Per l'autenticazione da servizio a servizio per l'applicazione (non interattiva), vedere [Service-to-service authentication with Data Lake Store using .NET SDK](data-lake-store-service-to-service-authenticate-rest-api.md) (Autenticazione da servizio a servizio con Data Lake Store tramite .NET SDK).


## <a name="create-a-data-lake-store-account"></a>Creare un account Archivio Data Lake
Questa operazione si basa sulla chiamata API REST definita [qui](https://msdn.microsoft.com/library/mt694078.aspx).

Usare il comando cURL seguente. Sostituire **\<yourstorename>** con il nome di Data Lake Store.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview -d@"C:\temp\input.json"

Nel comando sopra sostituire \<`REDACTED`\> con il token di autorizzazione recuperato in precedenza. Il payload della richiesta per questo comando è contenuto nel file **input.json** fornito per il parametro `-d` precedente. Il contenuto del file input.json è simile al frammento di codice seguente:

    {
    "location": "eastus2",
    "tags": {
        "department": "finance"
        },
    "properties": {}
    }    

## <a name="delete-a-data-lake-store-account"></a>Eliminare un account Archivio Data Lake
Questa operazione si basa sulla chiamata API REST definita [qui](https://msdn.microsoft.com/library/mt694075.aspx).

Usare il comando cURL seguente per eliminare un account Archivio Data Lake. Sostituire **\<yourstorename>** con il nome di Data Lake Store.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview

Dovrebbe venire visualizzato un output simile al frammento di codice seguente:

    HTTP/1.1 200 OK
    ...
    ...

## <a name="next-steps"></a>Passaggi successivi
* [Operazioni del file system in Data Lake Store con l'API REST](data-lake-store-data-operations-rest-api.md).

## <a name="see-also"></a>Vedere anche
* [Azure Data Lake Store REST API Reference (Informazioni di riferimento sull'API REST di Data Lake Store)](https://docs.microsoft.com/rest/api/datalakestore/)
* [Aprire le applicazioni Big Data di origine che funzionano con Archivio Azure Data Lake](data-lake-store-compatible-oss-other-applications.md)

