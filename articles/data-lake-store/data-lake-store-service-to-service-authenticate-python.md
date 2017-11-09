---
title: 'Autenticazione da servizio a servizio: Python con Data Lake Store tramite Azure Active Directory | Microsoft Docs'
description: Informazioni su come ottenere l'autenticazione da servizio a servizio con Data Lake Store tramite Azure Active Directory usando Python
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
ms.date: 10/11/2017
ms.author: nitinme
ms.openlocfilehash: 9f23a5e24067dab29a21a44404a912588b46d3b0
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/12/2017
---
# <a name="service-to-service-authentication-with-data-lake-store-using-python"></a>Autenticazione da servizio a servizio con Data Lake Store tramite Python
> [!div class="op_single_selector"]
> * [Uso di Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Uso di .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Uso di Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Uso dell'API REST](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
>  

Questo articolo illustra come usare Python SDK per eseguire l'autenticazione da servizio a servizio con Azure Data Lake Store. Per l'autenticazione dell'utente finale con Data Lake Store usando Python, vedere [Autenticazione dell'utente finale con Data Lake Store usando Python](data-lake-store-end-user-authenticate-python.md).


## <a name="prerequisites"></a>Prerequisiti

* **Python**. È possibile scaricare Python [qui](https://www.python.org/downloads/). Questo articolo fa riferimento a Python 3.6.2.

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Creare un'applicazione "Web" di Azure Active Directory**. È necessario avere completato i passaggi descritti in [Autenticazione da servizio a servizio con Data Lake Store tramite Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

## <a name="install-the-modules"></a>Installare i moduli

Per gestire Data Lake Store usando Python, è necessario installare tre moduli.

* Il modulo `azure-mgmt-resource` che include i moduli di Azure per Active Directory e così via.
* Il modulo `azure-mgmt-datalake-store` che include le operazioni di gestione di account di Azure Data Lake Store. Per altre informazioni su questo modulo, vedere la [documentazione di riferimento al modulo di gestione di Azure Data Lake Store](http://azure-sdk-for-python.readthedocs.io/en/latest/sample_azure-mgmt-datalake-store.html).
* Il modulo `azure-datalake-store` che include le operazioni di gestione del file system di Azure Data Lake Store. Per altre informazioni su questo modulo, vedere la [documentazione di riferimento al modulo del file system di Azure Data Lake Store](http://azure-datalake-store.readthedocs.io/en/latest/).

Per installare i moduli, usare i comandi seguenti.

```
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Creare una nuova applicazione Python

1. In un IDE a scelta creare una nuova applicazione Python, ad esempio **mysample.py**.

2. Aggiungere il frammento di codice seguente per importare i moduli necessari

    ```
    ## Use this for Azure AD authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Azure Data Lake Store account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Azure Data Lake Store filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    import adal
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, getpass, pprint, uuid, time
    ```

3. Salvare le modifiche a mysample.py.

## <a name="service-to-service-authentication-with-client-secret-for-account-management"></a>Autenticazione da servizio a servizio con segreto client per la gestione degli account

Usare questo frammento di codice per l'autenticazione con Azure AD per le operazioni di gestione degli account su Data Lake Store quali, ad esempio, la creazione o l'eliminazione di un account Data Lake Store e così via. Il frammento di codice seguente può essere usato per autenticare l'applicazione in modo non interattivo, usando il segreto client per un'applicazione o un'entità servizio di "un'applicazione Web" di Azure AD.

    authority_host_uri = 'https://login.microsoftonline.com'
    tenant = '<TENANT>'
    authority_uri = authority_host_uri + '/' + tenant
    RESOURCE = 'https://management.core.windows.net/'
    client_id = '<CLIENT_ID>'
    client_secret = '<CLIENT_SECRET>'
    
    context = adal.AuthenticationContext(authority_uri, api_version=None)
    mgmt_token = context.acquire_token_with_client_credentials(RESOURCE, client_id, client_secret)
    armCreds = AADTokenCredentials(mgmt_token, client_id, resource=RESOURCE)

## <a name="service-to-service-authentication-with-client-secret-for-filesystem-operations"></a>Autenticazione da servizio a servizio con segreto client per le operazioni del file system

Per le operazioni di gestione del file system, ad esempio creare cartelle, caricare file e così via, usare il frammento di codice seguente per l'autenticazione con Azure AD. Il frammento di codice seguente può essere usato per autenticare l'applicazione in modo non interattivo, usando il segreto client per un'applicazione o un'entità servizio. Usare questo frammento con un'app Web di Azure AD esistente.

    adlCreds = lib.auth(tenant_id = 'FILL-IN-HERE', client_secret = 'FILL-IN-HERE', client_id = 'FILL-IN-HERE', resource = 'https://datalake.azure.net/')

<!-- ## Service-to-service authentication with certificate for account management

Use this snippet to authenticate with Azure AD for account management operations on Data Lake Store such as create Data Lake Store account, delete Data Lake Store account, etc. The following snippet can be used to authenticate your application non-interactively, using the certificate of an existing Azure AD "Web App" application. For instructions on how to create an Azure AD application, see [Create service principal with certificates](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-self-signed-certificate).

    authority_host_uri = 'https://login.microsoftonline.com'
    tenant = '<TENANT>'
    authority_uri = authority_host_uri + '/' + tenant
    resource_uri = 'https://management.core.windows.net/'
    client_id = '<CLIENT_ID>'
    client_cert = '<CLIENT_CERT>'
    client_cert_thumbprint = '<CLIENT_CERT_THUMBPRINT>'

    context = adal.AuthenticationContext(authority_uri, api_version=None)
    mgmt_token = context.acquire_token_with_client_certificate(resource_uri, client_id, client_cert, client_cert_thumbprint)
    credentials = AADTokenCredentials(mgmt_token, client_id) -->

## <a name="next-steps"></a>Passaggi successivi
In questo articolo si è appreso come usare l'autenticazione da servizio a servizio per eseguire l'autenticazione con Azure Data Lake Store usando Python. È ora possibile leggere gli articoli seguenti per informazioni su come usare Python in Azure Data Lake Store.

* [Operazioni di gestione di account in Data Lake Store con Python](data-lake-store-get-started-python.md)
* [Operazioni dati in Data Lake Store con Python](data-lake-store-data-operations-python.md)


