---
title: Gestire un account Azure Data Lake Storage Gen1 con REST
description: Usare l'API REST WebHDFS per eseguire operazioni di gestione degli account in un account Azure Data Lake Storage Gen1.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 8a106b55fb90f320b90c81216a205dd10a9bf934
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82692084"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-rest-api"></a>Operazioni di gestione di account in Azure Data Lake Storage Gen1 con API REST
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Questo articolo descrive come eseguire operazioni di gestione di account in Azure Data Lake Storage Gen1 con l'API REST. Le operazioni di gestione degli account includono la creazione di un account Data Lake Storage Gen1, l'eliminazione di un account Data Lake Storage Gen1 e così via. Per istruzioni su come eseguire operazioni del file System su Data Lake Storage Gen1 usando l'API REST, vedere [operazioni del file System su data Lake storage Gen1 con l'API REST](data-lake-store-data-operations-rest-api.md).

## <a name="prerequisites"></a>Prerequisiti
* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

* **[curl](https://curl.haxx.se/)**. In questo articolo viene usato cURL per illustrare come effettuare chiamate API REST con un account Data Lake Storage Gen1.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Come si esegue l'autenticazione tramite Azure Active Directory?
È possibile adottare due approcci per l'autenticazione tramite Azure Active Directory.

* Per l'autenticazione dell'utente finale per l'applicazione (interattiva), consultare [Autenticazione dell'utente finale con Data Lake Storage Gen1 tramite .NET SDK](data-lake-store-end-user-authenticate-rest-api.md).
* Per l'autenticazione da servizio a servizio per l'applicazione (non interattiva), vedere [Autenticazione da servizio a servizio con Data Lake Storage Gen1 tramite .NET SDK](data-lake-store-service-to-service-authenticate-rest-api.md).


## <a name="create-a-data-lake-storage-gen1-account"></a>Creare un account Data Lake Storage Gen1
Questa operazione si basa sulla chiamata API REST definita [qui](https://docs.microsoft.com/rest/api/datalakestore/accounts/create).

Usare il comando cURL seguente. Sostituire ** \<yourstoragegen1name>** con il nome del data Lake storage Gen1.

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

Usare il comando cURL seguente per eliminare un account Data Lake Storage Gen1. Sostituire ** \<yourstoragegen1name>** con il nome dell'account di data Lake storage Gen1.

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

