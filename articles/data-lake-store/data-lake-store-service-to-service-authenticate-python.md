---
title: 'Autenticazione da servizio a: Python con Azure Data Lake archiviazione Gen1 tramite Azure Active Directory | Microsoft Docs'
description: Informazioni su come ottenere l'autenticazione da servizio a servizio con Azure Data Lake Storage Gen1 usando Azure Active Directory con Python
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
ms.openlocfilehash: 84b7fac10374c1c8f23d17ad775d522b4cb261e8
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58877755"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-python"></a>Autenticazione da servizio a servizio con Azure Data Lake Storage Gen1 tramite Python
> [!div class="op_single_selector"]
> * [Uso di Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Uso di .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Uso di Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Uso dell'API REST](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
>  

Questo articolo illustra come usare Python SDK per eseguire l'autenticazione da servizio a servizio con Azure Data Lake Storage Gen1. Per l'autenticazione dell'utente finale con Data Lake Storage Gen1 tramite Python, vedere [Autenticazione dell'utente finale con Data Lake Storage Gen1 usando Python](data-lake-store-end-user-authenticate-python.md).


## <a name="prerequisites"></a>Prerequisiti

* **Python**. È possibile scaricare Python [qui](https://www.python.org/downloads/). Questo articolo fa riferimento a Python 3.6.2.

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Creare un'applicazione "Web" di Azure Active Directory**. È necessario avere completato i passaggi descritti in [Autenticazione da servizio a servizio con Data Lake Storage Gen1 tramite Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

## <a name="install-the-modules"></a>Installare i moduli

Per usare Data Lake Storage Gen1 con Python, è necessario installare tre moduli.

* Il modulo `azure-mgmt-resource` che include i moduli di Azure per Active Directory e così via.
* Il modulo `azure-mgmt-datalake-store` che include le operazioni di gestione di account di Data Lake Storage Gen1. Per altre informazioni su questo modulo, vedere le [informazioni di riferimento sul modulo di gestione di Azure Data Lake Storage Gen1](https://docs.microsoft.com/python/api/azure.mgmt.datalake.store?view=azure-python).
* Il modulo `azure-datalake-store`, che include le operazioni del file system di Data Lake Storage Gen1. Per altre informazioni su questo modulo, vedere le [informazioni di riferimento sul modulo del file system azure-datalake-store](https://azure-datalake-store.readthedocs.io/en/latest/).

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

    ## Required for Data Lake Storage Gen1 account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Data Lake Storage Gen1 filesystem management
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

Usare questo frammento di codice per l'autenticazione con Azure AD per le operazioni di gestione degli account in Data Lake Storage Gen1, ad esempio la creazione o l'eliminazione di un account Data Lake Storage Gen1 e così via. Il frammento di codice seguente può essere usato per autenticare l'applicazione in modo non interattivo, usando il segreto client per un'applicazione o un'entità servizio di "un'applicazione Web" di Azure AD.

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

Usare il frammento di codice seguente per eseguire l'autenticazione con Azure AD per le operazioni del file system in Data Lake Storage Gen1, ad esempio la creazione di una cartella, il caricamento di un file e così via. Il frammento di codice seguente può essere usato per autenticare l'applicazione in modo non interattivo, usando il segreto client per un'applicazione o un'entità servizio. Usare questo frammento con un'app Web di Azure AD esistente.

    tenant = '<TENANT>'
    RESOURCE = 'https://datalake.azure.net/'
    client_id = '<CLIENT_ID>'
    client_secret = '<CLIENT_SECRET>'
    
    adlCreds = lib.auth(tenant_id = tenant,
                    client_secret = client_secret,
                    client_id = client_id,
                    resource = RESOURCE)

<!-- ## Service-to-service authentication with certificate for account management

Use this snippet to authenticate with Azure AD for account management operations on Data Lake Storage Gen1 such as create a Data Lake Storage Gen1 account, delete a Data Lake Storage Gen1 account, etc. The following snippet can be used to authenticate your application non-interactively, using the certificate of an existing Azure AD "Web App" application. For instructions on how to create an Azure AD application, see [Create service principal with certificates](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).

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
In questo articolo si è appreso come usare l'autenticazione da servizio a servizio per eseguire l'autenticazione con Data Lake Storage Gen1 usando Python. È ora possibile leggere gli articoli seguenti per informazioni su come usare Python in Data Lake Storage Gen1.

* [Operazioni di gestione di account in Data Lake Storage Gen1 con Phyton](data-lake-store-get-started-python.md)
* [Operazioni dati in Data Lake Storage Gen1 con Python](data-lake-store-data-operations-python.md)


