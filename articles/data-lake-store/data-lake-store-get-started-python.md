---
title: Usare Python SDK per iniziare a usare Azure Data Lake Store | Documentazione Microsoft
description: Informazioni su come usare Python SDK insieme agli account Data Lake Store e al file system.
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
ms.date: 04/03/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: 09f24fa2b55d298cfbbf3de71334de579fbf2ecd
ms.openlocfilehash: fb8715b715cc69136ad3a1ca3bf82df3afd2948a
ms.contentlocale: it-it
ms.lasthandoff: 06/07/2017


---

# <a name="get-started-with-azure-data-lake-store-using-python"></a>Introduzione ad Azure Data Lake Store con Python

> [!div class="op_single_selector"]
> * [Portale](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [SDK per Java](data-lake-store-get-started-java-sdk.md)
> * [API REST](data-lake-store-get-started-rest-api.md)
> * [Interfaccia della riga di comando di Azure 2.0](data-lake-store-get-started-cli-2.0.md)
> * [Node.js](data-lake-store-manage-use-nodejs.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Informazioni su come usare Python SDK per Azure e Azure Data Lake Store per eseguire operazioni di base, ad esempio creare cartelle, caricare e scaricare file di dati e così via. Per altre informazioni su Data Lake, vedere [Azure Data Lake Store](data-lake-store-overview.md).

## <a name="prerequisites"></a>Prerequisiti

* **Python**. È possibile scaricare Python [qui](https://www.python.org/downloads/). In questo articolo viene usata la versione 3.5.2 di Python.

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Creare un'applicazione di Azure Active Directory**. Usare l'applicazione Azure AD per autenticare l'applicazione Data Lake Store con Azure AD. Per l'autenticazione con Azure AD è possibile usare l'**autenticazione dell'utente finale** o l'**autenticazione da servizio a servizio**. Per altre informazioni e istruzioni su come eseguire l'autenticazione, vedere [Autenticazione dell'utente finale](data-lake-store-end-user-authenticate-using-active-directory.md) o [Autenticazione da servizio a servizio](data-lake-store-authenticate-using-active-directory.md).

## <a name="install-the-modules"></a>Installare i moduli

Per gestire Data Lake Store usando Python, è necessario installare tre moduli.

* Il modulo `azure-mgmt-resource` include i moduli Azure per Active Directory e così via.
* Il modulo `azure-mgmt-datalake-store` include le operazioni di gestione account di Azure Data Lake Store. Per altre informazioni su questo modulo, vedere la [documentazione di riferimento al modulo di gestione di Azure Data Lake Store](http://azure-sdk-for-python.readthedocs.io/en/latest/sample_azure-mgmt-datalake-store.html).
* Il modulo `azure-datalake-store` include le operazioni del file system di Azure Data Lake Store. Per altre informazioni su questo modulo, vedere la [documentazione di riferimento al modulo del file system di Azure Data Lake Store](http://azure-datalake-store.readthedocs.io/en/latest/).

Per installare i moduli, usare i comandi seguenti.

```
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Creare una nuova applicazione Python

1. In un IDE a scelta creare una nuova applicazione Python, ad esempio **mysample.py**.

2. Aggiungere le righe seguenti per importare i moduli necessari

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

* Autenticazione dell'utente finale
* Autenticazione da servizio a servizio
* Autenticazione a più fattori

È necessario usare queste opzioni di autenticazione per i moduli di gestione degli account e di gestione del file system.

### <a name="end-user-authentication-for-account-management"></a>Autenticazione dell'utente finale per la gestione degli account

Per le operazioni di gestione degli account, ad esempio creare o eliminare account Data Lake Store e così via, è necessario eseguire questo tipo di autenticazione con Azure AD. È necessario specificare il nome utente e la password per un utente di Azure AD. L'utente non deve essere configurato per l'autenticazione a più fattori.

    user = input('Enter the user to authenticate with that has permission to subscription: ')
    password = getpass.getpass()

    credentials = UserPassCredentials(user, password)

### <a name="end-user-authentication-for-filesystem-operations"></a>Autenticazione dell'utente finale per le operazioni del file system

Per le operazioni di gestione del file system, ad esempio creare cartelle, caricare file e così via, è necessario eseguire questo tipo di autenticazione con Azure AD. Usare questo tipo di autenticazione con un'applicazione **client nativa** di Azure AD. L'utente AD Azure del quale sono state specificate le credenziali non deve essere configurato per l'autenticazione a più fattori.

    tenant_id = 'FILL-IN-HERE'
    client_id = 'FILL-IN-HERE'
    user = input('Enter the user to authenticate with that has permission to subscription: ')
    password = getpass.getpass()

    token = lib.auth(tenant_id, user, password, client_id)

### <a name="service-to-service-authentication-with-client-secret-for-account-management"></a>Autenticazione da servizio a servizio con segreto client per la gestione degli account

Per le operazioni di gestione degli account, ad esempio creare o eliminare account Data Lake Store e così via, è necessario eseguire questo tipo di autenticazione con Azure AD. Il frammento di codice seguente può essere usato per autenticare l'applicazione in modo non interattivo, usando il segreto client per un'applicazione o un'entità servizio. Usare questo frammento con un'app Web di Azure AD esistente.

    credentials = ServicePrincipalCredentials(client_id = 'FILL-IN-HERE', secret = 'FILL-IN-HERE', tenant = 'FILL-IN-HERE')

### <a name="service-to-service-authentication-with-client-secret-for-filesystem-operations"></a>Autenticazione da servizio a servizio con segreto client per le operazioni del file system

Per le operazioni di gestione del file system, ad esempio creare cartelle, caricare file e così via, è necessario eseguire questo tipo di autenticazione con Azure AD. Il frammento di codice seguente può essere usato per autenticare l'applicazione in modo non interattivo, usando il segreto client per un'applicazione o un'entità servizio. Usare questo frammento con un'app Web di Azure AD esistente.

    token = lib.auth(tenant_id = 'FILL-IN-HERE', client_secret = 'FILL-IN-HERE', client_id = 'FILL-IN-HERE')

### <a name="multi-factor-authentication-for-account-management"></a>Multi-Factor Authentication per la gestione degli account

Per le operazioni di gestione degli account, ad esempio creare o eliminare account Data Lake Store e così via, è necessario eseguire questo tipo di autenticazione con Azure AD. Il frammento di codice seguente consente di autenticare l'applicazione con Multi-Factor Authentication. Usare questo frammento con un'app Web di Azure AD esistente.

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
    credentials = AADTokenCredentials(mgmt_token, client_id)

### <a name="multi-factor-authentication-for-filesystem-management"></a>Multi-Factor Authentication per la gestione del file system

Per le operazioni di gestione del file system, ad esempio creare cartelle, caricare file e così via, è necessario eseguire questo tipo di autenticazione con Azure AD. Il frammento di codice seguente consente di autenticare l'applicazione con Multi-Factor Authentication. Usare questo frammento con un'app Web di Azure AD esistente.

    token = lib.auth(tenant_id='FILL-IN-HERE')

## <a name="create-an-azure-resource-group"></a>Creare un gruppo di risorse di Azure

Per creare un gruppo di risorse di Azure usare il frammento di codice seguente:

    ## Declare variables
    subscriptionId= 'FILL-IN-HERE'
    resourceGroup = 'FILL-IN-HERE'
    location = 'eastus2'
    
    ## Create management client object
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

## <a name="create-clients-and-data-lake-store-account"></a>Creare client e account Data Lake Store

Il frammento seguente crea prima il client account Data Lake Store, poi usa l'oggetto client per creare un account Data Lake Store e infine crea un oggetto client per il file system.

    ## Declare variables
    subscriptionId = 'FILL-IN-HERE'
    adlsAccountName = 'FILL-IN-HERE'

    ## Create management client object
    adlsAcctClient = DataLakeStoreAccountManagementClient(credentials, subscriptionId)

    ## Create a Data Lake Store account
    adlsAcctResult = adlsAcctClient.account.create(
        resourceGroup,
        adlsAccountName,
        DataLakeStoreAccount(
            location=location
        )
    ).wait()

    ## Create a filesystem client object
    adlsFileSystemClient = core.AzureDLFileSystem(token, store_name=adlsAccountName)

## <a name="list-the-data-lake-store-accounts"></a>Elencare gli account Data Lake Store

    ## List the existing Data Lake Store accounts
    result_list_response = adlsAcctClient.account.list()
    result_list = list(result_list_response)
    for items in result_list:
        print(items)

## <a name="create-a-directory"></a>Creare una directory

    ## Create a directory
    adlsFileSystemClient.mkdir('/mysampledirectory')

## <a name="upload-a-file"></a>Caricare un file


    ## Upload a file
    multithread.ADLUploader(adlsFileSystemClient, lpath='C:\\data\\mysamplefile.txt', rpath='/mysampledirectory/mysamplefile.txt', nthreads=64, overwrite=True, buffersize=4194304, blocksize=4194304)


## <a name="download-a-file"></a>Scaricare un file

    ## Download a file
    multithread.ADLDownloader(adlsFileSystemClient, lpath='C:\\data\\mysamplefile.txt.out', rpath='/mysampledirectory/mysamplefile.txt', nthreads=64, overwrite=True, buffersize=4194304, blocksize=4194304)

## <a name="delete-a-directory"></a>Eliminare una directory

    ## Delete a directory
    adlsFileSystemClient.rm('/mysampledirectory', recursive=True)

## <a name="see-also"></a>Vedere anche

- [Proteggere i dati in Data Lake Store](data-lake-store-secure-data.md)
- [Usare Azure Data Lake Analytics con Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Usare Azure HDInsight con Archivio Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Informazioni di riferimento su .NET SDK con Data Lake Store](https://msdn.microsoft.com/library/mt581387.aspx)
- [Data Lake Store REST Reference (Informazioni di riferimento su REST per Data Lake Store)](https://msdn.microsoft.com/library/mt693424.aspx)

