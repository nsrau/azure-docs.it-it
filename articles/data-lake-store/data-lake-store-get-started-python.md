---
title: 'Python: Operazioni di gestione di account in Azure Data Lake Store | Microsoft Docs'
description: Informazioni su come usare Python SDK con le operazioni di gestione di account di Data Lake Store.
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 75f6de6f-6fd8-48f4-8707-cb27d22d27a6
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/28/2017
ms.author: nitinme
ms.openlocfilehash: 601d756e0d6554d8a4db9cc83f6919fc36d1e844
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="account-management-operations-on-azure-data-lake-store-using-python"></a>Operazioni di gestione di account in Azure Data Lake Store con Python
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [API REST](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Informazioni su come usare Python SDK per Azure Data Lake Store per eseguire operazioni di gestione di account di base, ad esempio creare un account Data Lake Store, elencare gli account Data Lake Store e così via. Per istruzioni su come eseguire le operazioni del file system in Data Lake Store con Python, vedere [Operazioni del file system in Data Lake Store con Python](data-lake-store-data-operations-python.md).

## <a name="prerequisites"></a>Prerequisiti

* **Python**. È possibile scaricare Python [qui](https://www.python.org/downloads/). Questo articolo fa riferimento a Python 3.6.2.

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="install-the-modules"></a>Installare i moduli

Per gestire Data Lake Store usando Python, è necessario installare tre moduli.

* Il modulo `azure-mgmt-resource`, che include i moduli di Azure per Active Directory e così via.
* Il modulo `azure-mgmt-datalake-store`, che include le operazioni di gestione di account di Azure Data Lake Store. Per altre informazioni su questo modulo, vedere la [documentazione di riferimento al modulo di gestione di Azure Data Lake Store](http://azure-sdk-for-python.readthedocs.io/en/latest/sample_azure-mgmt-datalake-store.html).
* Il modulo `azure-datalake-store`, che include le operazioni di gestione del file system di Azure Data Lake Store. Per altre informazioni su questo modulo, vedere la [documentazione di riferimento al modulo del file system di Azure Data Lake Store](http://azure-datalake-store.readthedocs.io/en/latest/).

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
    ## Use this only for Azure AD service-to-service authentication
    from azure.common.credentials import ServicePrincipalCredentials

    ## Use this only for Azure AD end-user authentication
    from azure.common.credentials import UserPassCredentials

    ## Use this only for Azure AD multi-factor authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Azure Data Lake Store account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Azure Data Lake Store filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, getpass, pprint, uuid, time
    ```

3. Salvare le modifiche a mysample.py.

## <a name="authentication"></a>Autenticazione

Questa sezione descrive le diverse modalità di autenticazione con Azure AD. Le opzioni disponibili sono:

* Per l'autenticazione dell'utente finale per l'applicazione, vedere [End-user authentication with Data Lake Store using Python](data-lake-store-end-user-authenticate-python.md) (Autenticazione dell'utente finale con Data Lake Store tramite Python).
* Per l'autenticazione da servizio a servizio per l'applicazione, vedere [Service-to-service authentication with Data Lake Store using Python](data-lake-store-service-to-service-authenticate-python.md) (Autenticazione da servizio a servizio con Data Lake Store tramite Python).

## <a name="create-an-azure-resource-group"></a>Creare un gruppo di risorse di Azure

Per creare un gruppo di risorse di Azure usare il frammento di codice seguente:

    ## Declare variables
    subscriptionId= 'FILL-IN-HERE'
    resourceGroup = 'FILL-IN-HERE'
    location = 'eastus2'
    
    ## Create resource management client object
    resourceClient = ResourceManagementClient(
        credentials,
        subscriptionId
    )
    
    ## Create an Azure Resource Group
    resourceClient.resource_groups.create_or_update(
        resourceGroup,
        ResourceGroup(
            location=location
        )
    )

## <a name="create-client-and-data-lake-store-account"></a>Creare il client e l'account Data Lake Store

Il frammento seguente crea prima il client account Data Lake Store, poi usa l'oggetto client per creare un account Data Lake Store e infine crea un oggetto client per il file system.

    ## Declare variables
    subscriptionId = 'FILL-IN-HERE'
    adlsAccountName = 'FILL-IN-HERE'

    ## Create data lake store account management client object
    adlsAcctClient = DataLakeStoreAccountManagementClient(credentials, subscriptionId)

    ## Create a Data Lake Store account
    adlsAcctResult = adlsAcctClient.account.create(
        resourceGroup,
        adlsAccountName,
        DataLakeStoreAccount(
            location=location
        )
    ).wait()

    
## <a name="list-the-data-lake-store-accounts"></a>Elencare gli account Data Lake Store

    ## List the existing Data Lake Store accounts
    result_list_response = adlsAcctClient.account.list()
    result_list = list(result_list_response)
    for items in result_list:
        print(items)

## <a name="delete-the-data-lake-store-account"></a>Eliminare l'account Data Lake Store

    ## Delete the existing Data Lake Store accounts
    adlsAcctClient.account.delete(adlsAccountName)
    

## <a name="next-steps"></a>Passaggi successivi
* [Operazioni del file system in Data Lake Store con Python](data-lake-store-data-operations-python.md).

## <a name="see-also"></a>Vedere anche
* [Azure Data Lake Store Python (Account management) Reference (Informazioni di riferimento su Python con Azure Data Lake Store - gestione di account)](http://azure-sdk-for-python.readthedocs.io/en/latest/sample_azure-mgmt-datalake-store.html)
* [Azure Data Lake Store Python (Filesystem) Reference (Informazioni di riferimento su Python con Azure Data Lake Store - file system)](http://azure-datalake-store.readthedocs.io/en/latest)
* [Aprire le applicazioni Big Data di origine che funzionano con Archivio Azure Data Lake](data-lake-store-compatible-oss-other-applications.md)
