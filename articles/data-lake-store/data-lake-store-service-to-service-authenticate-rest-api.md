---
title: 'Autenticazione da servizio a servizio: API REST con Data Lake Store tramite Azure Active Directory | Microsoft Docs'
description: Informazioni su come ottenere l'autenticazione da servizio a servizio con Data Lake Store tramite Azure Active Directory usando l'API REST
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/28/2017
ms.author: nitinme
ms.openlocfilehash: 754e65c4bcf8574a16b9620e2f21938ecc62b735
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="service-to-service-authentication-with-data-lake-store-using-rest-api"></a>Autenticazione da servizio a servizio con Data Lake Store tramite API REST
> [!div class="op_single_selector"]
> * [Uso di Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Uso di .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Uso di Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Uso dell'API REST](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
> 

Questo articolo illustra come usare l'API REST per eseguire l'autenticazione da servizio a servizio con Azure Data Lake Store. Per l'autenticazione dell'utente finale con Data Lake Store usando API REST, vedere [Autenticazione dell'utente finale con Data Lake Store tramite API REST](data-lake-store-end-user-authenticate-rest-api.md).

## <a name="prerequisites"></a>Prerequisiti
* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Creare un'applicazione "Web" di Azure Active Directory**. È necessario avere completato i passaggi descritti in [Autenticazione da servizio a servizio con Data Lake Store tramite Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

## <a name="service-to-service-authentication"></a>Autenticazione da servizio a servizio
In questo scenario, l'applicazione fornisce le proprie credenziali per eseguire le operazioni. A tale scopo, è necessario inviare una richiesta POST come quella mostrata nel frammento di codice seguente: 

    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

L'output della richiesta include un token di autorizzazione (indicato da `access-token` nell'output riportato di seguito) che verrà passato successivamente con le chiamate API REST. Salvare il token di autenticazione in un file di testo. Sarà necessario per eseguire chiamate REST a Data Lake Store.

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

Questo articolo usa l'approccio **non interattivo** . Per altre informazioni sull'autenticazione non interattiva (chiamate da servizio a servizio), vedere [Chiamate da servizio a servizio tramite le credenziali](https://msdn.microsoft.com/library/azure/dn645543.aspx). 

## <a name="next-steps"></a>Passaggi successivi
In questo articolo si è appreso come usare l'autenticazione da servizio a servizio per eseguire l'autenticazione con Azure Data Lake Store usando l'API REST. È ora possibile leggere gli articoli seguenti per informazioni su come usare l'API REST in Azure Data Lake Store.

* [Operazioni di gestione di account in Data Lake Store con l'API REST](data-lake-store-get-started-rest-api.md)
* [Operazioni sui dati in Data Lake Store con l'API REST](data-lake-store-data-operations-rest-api.md)


