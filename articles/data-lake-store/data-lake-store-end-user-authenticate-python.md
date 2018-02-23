---
title: 'Autenticazione dell''utente finale: Python con Data Lake Store tramite Azure Active Directory | Microsoft Docs'
description: Informazioni su come ottenere l'autenticazione dell'utente finale con Data Lake Store tramite Azure Active Directory con Python
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
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: 1fa8df760ac22ae915765895b498f21d628eea76
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2018
---
# <a name="end-user-authentication-with-data-lake-store-using-python"></a>Autenticazione dell'utente finale con Data Lake Store tramite Python
> [!div class="op_single_selector"]
> * [Uso di Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Uso di .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Uso di Python](data-lake-store-end-user-authenticate-python.md)
> * [Uso dell'API REST](data-lake-store-end-user-authenticate-rest-api.md)
> 
> 

Questo articolo illustra come usare Python SDK per eseguire l'autenticazione dell'utente finale con Azure Data Lake Store. L'autenticazione dell'utente finale può essere suddivisa ulteriormente in due categorie:

* Autenticazione dell'utente finale senza autenticazione a più fattori
* Autenticazione dell'utente finale con autenticazione a più fattori

Questo articolo descrive entrambe le opzioni. Per l'autenticazione da servizio a servizio con Data Lake Store tramite Python, vedere [Service-to-service authentication with Data Lake Store using Python](data-lake-store-service-to-service-authenticate-python.md) (Autenticazione da servizio a servizio con Data Lake Store tramite Python).

## <a name="prerequisites"></a>prerequisiti

* **Python**. È possibile scaricare Python [qui](https://www.python.org/downloads/). Questo articolo fa riferimento a Python 3.6.2.

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Creare un'applicazione "nativa" di Azure Active Directory**. È necessario avere completato i passaggi descritti in [Autenticazione dell'utente finale con Data Lake Store usando Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

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
    import logging, pprint, uuid, time
    ```

3. Salvare le modifiche a mysample.py.

## <a name="end-user-authentication-with-multi-factor-authentication"></a>Autenticazione dell'utente finale con autenticazione a più fattori

### <a name="for-account-management"></a>Per la gestione dell'account

Usare i frammenti di codice seguenti per l'autenticazione con Azure AD per le operazioni di gestione degli account in un account Data Lake Store. Il frammento di codice seguente consente di autenticare l'applicazione con Multi-Factor Authentication. Fornire i valori seguenti per un'applicazione **nativa** di Azure AD esistente.

    authority_host_url = "https://login.microsoftonline.com"
    tenant = "FILL-IN-HERE"
    authority_url = authority_host_url + '/' + tenant
    client_id = 'FILL-IN-HERE'
    redirect = 'urn:ietf:wg:oauth:2.0:oob'
    RESOURCE = 'https://management.core.windows.net/'
    
    context = adal.AuthenticationContext(authority_url)
    code = context.acquire_user_code(RESOURCE, client_id)
    print(code['message'])
    mgmt_token = context.acquire_token_with_device_code(RESOURCE, code, client_id)
    armCreds = AADTokenCredentials(mgmt_token, client_id, resource = RESOURCE)

### <a name="for-filesystem-operations"></a>Per operazioni di gestione del file system

Usare questo tipo di autenticazione con Azure AD per le operazioni di gestione del file system in un account Data Lake Store. Il frammento di codice seguente consente di autenticare l'applicazione con Multi-Factor Authentication. Fornire i valori seguenti per un'applicazione **nativa** di Azure AD esistente.

    adlCreds = lib.auth(tenant_id='FILL-IN-HERE', resource = 'https://datalake.azure.net/')

## <a name="end-user-authentication-without-multi-factor-authentication"></a>Autenticazione dell'utente finale senza autenticazione a più fattori

Deprecato. Per altre informazioni, vedere [Autenticazione con Python SDK](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate?view=azure-python#mgmt-auth-token).
   
## <a name="next-steps"></a>Passaggi successivi
In questo articolo si è appreso come usare l'autenticazione dell'utente finale per eseguire l'autenticazione con Azure Data Lake Store tramite Python. È ora possibile leggere gli articoli seguenti per informazioni su come usare Python in Azure Data Lake Store.

* [Operazioni di gestione di account in Data Lake Store con Python](data-lake-store-get-started-python.md)
* [Operazioni dati in Data Lake Store con Python](data-lake-store-data-operations-python.md)

