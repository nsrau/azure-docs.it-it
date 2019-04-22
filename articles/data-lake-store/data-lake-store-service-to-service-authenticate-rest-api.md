---
title: 'Autenticazione da servizio a: API REST con Gen1 di archiviazione di Azure Data Lake tramite Azure Active Directory | Microsoft Docs'
description: Informazioni su come ottenere l'autenticazione da servizio a servizio con Azure Data Lake Storage Gen1 usando Azure Active Directory con l'API REST
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
ms.openlocfilehash: c48f7d7608b2b70f4ae41e2af5792cff72bb0dd2
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58885724"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-rest-api"></a>Autenticazione da servizio a servizio con Azure Data Lake Storage Gen1 tramite API REST
> [!div class="op_single_selector"]
> * [Uso di Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Uso di .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Uso di Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Uso dell'API REST](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
> 

Questo articolo illustra come usare l'API REST per eseguire l'autenticazione da servizio a servizio con Azure Data Lake Storage Gen1. Per l'autenticazione dell'utente finale con Data Lake Storage Gen1 tramite API REST, vedere [Autenticazione dell'utente finale con Data Lake Storage Gen1 usando l'API REST](data-lake-store-end-user-authenticate-rest-api.md).

## <a name="prerequisites"></a>Prerequisiti
* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Creare un'applicazione "Web" di Azure Active Directory**. È necessario avere completato i passaggi descritti in [Autenticazione da servizio a servizio con Data Lake Storage Gen1 tramite Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

## <a name="service-to-service-authentication"></a>Autenticazione da servizio a servizio
In questo scenario, l'applicazione fornisce le proprie credenziali per eseguire le operazioni. A tale scopo, è necessario inviare una richiesta POST come quella mostrata nel frammento di codice seguente: 

    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

L'output della richiesta include un token di autorizzazione (indicato da `access-token` nell'output riportato di seguito) che verrà passato successivamente con le chiamate API REST. Salvare il token di autenticazione in un file di testo. Sarà necessario per eseguire chiamate REST a Data Lake Storage Gen1.

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

Questo articolo usa l'approccio **non interattivo** . Per altre informazioni sull'autenticazione non interattiva (chiamate da servizio a servizio), vedere [Chiamate da servizio a servizio tramite le credenziali](https://msdn.microsoft.com/library/azure/dn645543.aspx). 

## <a name="next-steps"></a>Passaggi successivi
In questo articolo si è appreso come usare l'autenticazione da servizio a servizio per eseguire l'autenticazione con Data Lake Storage Gen1 usando l'API REST. È ora possibile leggere gli articoli seguenti per informazioni su come usare l'API REST in Data Lake Storage Gen1.

* [Operazioni di gestione di account in Data Lake Storage Gen1 con API REST](data-lake-store-get-started-rest-api.md)
* [Operazioni dati in Data Lake Storage Gen1 con API REST](data-lake-store-data-operations-rest-api.md)

