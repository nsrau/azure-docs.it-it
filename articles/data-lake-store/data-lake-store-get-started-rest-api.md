---
title: 'API REST: Operazioni di gestione in Azure Data Lake archiviazione Gen1 account | Microsoft Docs'
description: Usare Azure Data Lake Storage Gen1 e l'API REST WebHDFS per eseguire operazioni di gestione di account nell'account Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 57ac6501-cb71-4f75-82c2-acc07c562889
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 97fe33309f36cd7545f8c9d6c2d34671641caa1f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60877106"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-rest-api"></a>Operazioni di gestione di account in Azure Data Lake Storage Gen1 con API REST
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [API REST](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Questo articolo descrive come eseguire operazioni di gestione di account in Azure Data Lake Storage Gen1 con l'API REST. Le operazioni di gestione di account includono la creazione di un account Data Lake Storage Gen1, l'eliminazione di un account Data Lake Storage Gen1 e così via. Per istruzioni su come eseguire le operazioni del file system in Data Lake Storage Gen1 con l'API REST, vedere [Operazioni del file system in Data Lake Storage Gen1 con l'API REST](data-lake-store-data-operations-rest-api.md).

## <a name="prerequisites"></a>Prerequisiti
* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

* **[cURL](https://curl.haxx.se/)**. In questo articolo viene usato cURL per illustrare come effettuare chiamate API REST con un account Data Lake Storage Gen1.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Come si esegue l'autenticazione tramite Azure Active Directory?
È possibile adottare due approcci per l'autenticazione tramite Azure Active Directory.

* Per l'autenticazione dell'utente finale per l'applicazione (interattiva), consultare [Autenticazione dell'utente finale con Data Lake Storage Gen1 tramite .NET SDK](data-lake-store-end-user-authenticate-rest-api.md).
* Per l'autenticazione da servizio a servizio per l'applicazione (non interattiva), vedere [Autenticazione da servizio a servizio con Data Lake Storage Gen1 tramite .NET SDK](data-lake-store-service-to-service-authenticate-rest-api.md).


## <a name="create-a-data-lake-storage-gen1-account"></a>Creare un account Data Lake Storage Gen1
Questa operazione si basa sulla chiamata API REST definita [qui](https://docs.microsoft.com/rest/api/datalakestore/accounts/create).

Usare il comando cURL seguente. Sostituire **\<nomestoragegen1>** con il nome Data Lake Storage Gen1.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstoragegen1name>?api-version=2015-10-01-preview -d@"C:\temp\input.json"

Nel comando sopra sostituire \<`REDACTED`\> con il token di autorizzazione recuperato in precedenza. Il payload della richiesta per questo comando è contenuto nel file **input.json** fornito per il parametro `-d` precedente. Il contenuto del file input.json è simile al frammento di codice seguente:

    {
    "location": "eastus2",
    "tags": {
        "department": "finance"
        },
    "properties": {}
    }    

## <a name="delete-a-data-lake-storage-gen1-account"></a>Eliminare un account Data Lake Storage Gen1
Questa operazione si basa sulla chiamata API REST definita [qui](https://docs.microsoft.com/rest/api/datalakestore/accounts/delete).

Usare il comando cURL seguente per eliminare un account Data Lake Storage Gen1. Sostituire **\<nomestoragegen1>** con il nome dell'account Data Lake Storage Gen1.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstoragegen1name>?api-version=2015-10-01-preview

Dovrebbe venire visualizzato un output simile al frammento di codice seguente:

    HTTP/1.1 200 OK
    ...
    ...

## <a name="next-steps"></a>Passaggi successivi
* [Operazioni del file system in Data Lake Storage Gen1 con l'API REST](data-lake-store-data-operations-rest-api.md).

## <a name="see-also"></a>Vedere anche 
* [Riferimento all'API REST di Azure Data Lake Storage Gen1](https://docs.microsoft.com/rest/api/datalakestore/)
* [Aprire le applicazioni Big Data di origine compatibili con Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)

