---
title: Introduzione ad Azure Data Lake Analytics con Python | Documentazione Microsoft
description: 'Informazioni su come usare Python per creare un account di Data Lake Store e inviare i processi. '
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/15/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: e94b11ab62afbea9381772a2bdb668fdc514d609
ms.openlocfilehash: 2da23d881a23cc0fc23b63a7b9e06d9af9a755eb


---


# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-python"></a>Esercitazione: Introduzione ad Azure Data Lake Analytics con Python
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Informazioni su come usare Python per creare account di Azure Data Lake Analytics, definire processi di Data Lake Analytics in [U-SQL](data-lake-analytics-u-sql-get-started.md) e inviare i processi agli account di Data Lake Analytics. Per altre informazioni su Data Lake Analytics, vedere [Panoramica di Azure Data Lake Analytics](data-lake-analytics-overview.md).

Questa esercitazione illustra come sviluppare un processo che legge un file di valori delimitati da tabulazioni (TSV) e lo converte in un file di valori delimitati da virgole (CSV). Per eseguire la stessa esercitazione usando altri strumenti supportati, fare clic sulle schede disponibili nella parte superiore di questa sezione.

##<a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione sono necessari gli elementi seguenti:

- **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Un'applicazione Azure Active Directory**. Usare l'applicazione Azure AD per autenticare l'applicazione Data Lake Store con Azure AD. Per l'autenticazione con Azure AD è possibile usare l'autenticazione dell'utente finale o l'autenticazione da servizio a servizio. Per altre informazioni e istruzioni su come eseguire l'autenticazione, vedere [Authenticate with Data Lake Store using Azure Active Directory](../data-lake-store/data-lake-store-authenticate-using-active-directory.md)(Eseguire l'autenticazione in Data Lake Store con Azure Active Directory).
- **[Python](https://www.python.org/downloads/)**. È necessario usare la versione a 64 bit. In questo articolo viene usata la versione 3.5.2 di Python.


## <a name="install-azure-python-sdk"></a>Installare Azure Python SDK

Per gestire Data Lake Store usando Python, è necessario installare tre moduli.

Il modulo azure include le operazioni di gestione degli account di Azure Data Lake Store in aggiunta ad altri moduli di Azure per Active Directory e così via. Il modulo azure-datalake-store include le operazioni di file system di Azure Data Lake Store. Il modulo azure-datalake-analytics include le operazioni di Azure Data Lake Analytics. Per installare i moduli, usare i comandi seguenti.

    pip install azure
    pip install azure-datalake-store
    pip install azure-datalake-analytics

## <a name="create-a-python-application"></a>Creare un'applicazione Python

1. Usare l'ambiente di sviluppo integrato di propria scelta per creare una nuova applicazione Python, ad esempio mysample.py.

2. Aggiungere le righe seguenti per importare i moduli necessari

        ## Use this only for Azure AD service-to-service authentication
        from azure.common.credentials import ServicePrincipalCredentials

        ## Use this only for Azure AD end-user authentication
        from azure.common.credentials import UserPassCredentials

        ## Required for Azure Resource Manager
        from azure.mgmt.resource.resources import ResourceManagementClient
        from azure.mgmt.resource.resources.models import ResourceGroup

        ## Required for Azure Data Lake Store account management
        from azure.mgmt.datalake.store.account import DataLakeStoreAccountManagementClient
        from azure.mgmt.datalake.store.account.models import DataLakeStoreAccount

        ## Required for Azure Data Lake Store filesystem management
        from azure.datalake.store import core, lib, multithread

        ## Required for Azure Data Lake Analytics account management
        from azure.mgmt.datalake.analytics.account.models import DataLakeAnalyticsAccount, DataLakeAnalyticsAccountProperties, DataLakeStoreAccountInfo

        ## Required for Azure Data Lake Analytics job management
        from azure.mgmt.datalake.analytics.job import DataLakeAnalyticsJobManagementClient
        from azure.mgmt.datalake.analytics.job.models import JobInformation, JobState, USqlJobProperties

        ## Required for Azure Data Lake Analytics catalog management
        from azure.mgmt.datalake.analytics.catalog import DataLakeAnalyticsCatalogManagementClient

        ## Use these as needed for your application
        import logging, getpass, pprint, uuid, time

3. Salvare le modifiche apportate al file dell'applicazione Python.

## <a name="authentication"></a>Autenticazione

Per eseguire l'autenticazione, usare uno dei metodi seguenti:

### <a name="end-user-authentication-for-account-management"></a>Autenticazione dell'utente finale per la gestione degli account

Usare questo metodo per eseguire l'autenticazione con Azure AD per le operazioni di gestione degli account, ad esempio creare o eliminare account di Data Lake Store e così via. È necessario specificare il nome utente e la password per un utente di Azure AD. L'account utente non può essere configurato per l'autenticazione a più fattori e non può essere un Live ID o un account Microsoft, ad esempio @outlook.com, e @live.com.

    user = input('Enter the user to authenticate with that has permission to subscription: ')
    password = getpass.getpass()

    credentials = UserPassCredentials(user, password)

### <a name="service-to-service-authentication-with-client-secret-for-account-management"></a>Autenticazione da servizio a servizio con segreto client per la gestione degli account

Usare questo metodo per eseguire l'autenticazione con Azure AD per le operazioni di gestione degli account, ad esempio creare o eliminare account di Data Lake Store e così via. Il frammento di codice seguente può essere usato per autenticare l'applicazione in modo non interattivo, usando il segreto client per un'applicazione o un'entità servizio. Usare questo frammento con un'app Web di Azure AD esistente.

    credentials = ServicePrincipalCredentials(client_id = 'FILL-IN-HERE', secret = 'FILL-IN-HERE', tenant = 'FILL-IN-HERE')

## <a name="create-resource-management-group"></a>Creare un gruppo di gestione delle risorse

Per creare un gruppo di risorse di Azure usare il frammento di codice seguente:

    ## Declare variables
    subscriptionId= '<Azure Subscription ID>'
    resourceGroup = '<Azure Resource Group Name>'
    location = '<Location>' # i.e. 'eastus2'

    ## Create management client object
    resourceClient = ResourceManagementClient(
        credentials,
        subscriptionId
    )

    ## Create an Azure Resource Group
    armGroupResult = resourceClient.resource_groups.create_or_update(
        resourceGroup,
        ResourceGroup(
            location=location
        )
    )


## <a name="create-data-lake-store-account"></a>Creare un account di Data Lake Store

Per ogni account di Data Lake Analytics deve essere configurato un account di Data Lake Store. Per istruzioni, vedere [Creare un account di Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md#create-an-azure-data-lake-store-account).



## <a name="create-data-lake-analytics-account"></a>Creare un account di Analisi Data Lake

    ## Declare variables
    subscriptionId= '<Azure Subscription ID>'
    resourceGroup = '<Azure Resource Group Name>'
    location = '<Location>' # i.e. 'eastus2'
    adlsAccountName = '<Azure Data Lake Store Account Name>'
    adlaAccountName = '<Azure Data Lake Analytics Account Name>'

    ## Create management client object
    adlaAcctClient = DataLakeAnalyticsAccountManagementClient(
        credentials,
        subscriptionId
    )

    ## Create an Azure Data Lake Analytics account
    adlaAcctResult = adlaAcctClient.account.create(
        resourceGroup,
        adlaAccountName,
        DataLakeAnalyticsAccount(
            location=location,
            default_data_lake_store_account=adlsAccountName,
            data_lake_store_accounts=[DataLakeStoreAccountInfo(name=adlsAccountName)]
        )
    ).wait()


##<a name="submit-data-lake-analytics-jobs"></a>Inviare processi di Data Lake Analytics

I processi di Data Lake Analtyics vengono scritti nel linguaggio U-SQL. Per altre informazioni su U-SQL, vedere [Introduzione al linguaggio U-SQL](data-lake-analytics-u-sql-get-started.md) e [U-SQL Language Reference](http://go.microsoft.com/fwlink/?LinkId=691348) (Riferimenti al linguaggio U-SQL).

    ## Declare variables
    adlsAccountName = '<Azure Data Lake Store Account Name>'

    ## Create management client object
    adlaJobClient = DataLakeAnalyticsJobManagementClient(
        credentials,
        'azuredatalakeanalytics.net'
    )

    ## Submit a U-SQL job
    jobId = str(uuid.uuid4())
    jobResult = adlaJobClient.job.create(
        adlaAccountName,
        jobId,
        JobInformation(
            name='Sample Job',
            type='USql',
            properties=USqlJobProperties(
                script='DROP DATABASE IF EXISTS FOO; CREATE DATABASE FOO;'
            )
        )
    )

    ## Print the job result
    while(jobResult.state != JobState.ended):
        print('Job is not yet done, waiting for 3 seconds. Current state: ' + jobResult.state.value)
        time.sleep(3)
        jobResult = adlaJobClient.job.get(adlaAccountName, jobId)
        
    print ('Job finished with result: ' + jobResult.result.value)

## <a name="next-steps"></a>Passaggi successivi

- Per visualizzare la stessa esercitazione usando altri strumenti, scegliere i selettori di scheda nella parte superiore della pagina.
- Per visualizzare una query più complessa, vedere [Analizzare i log del sito Web mediante Analisi Data Lake di Azure](data-lake-analytics-analyze-weblogs.md).
- Per iniziare a sviluppare applicazioni U-SQL, vedere [Sviluppare script U-SQL tramite Strumenti di Data Lake per Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Per informazioni su U-SQL, vedere [Introduzione al linguaggio U-SQL di Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
- Per informazioni sulle attività di gestione, vedere [Gestire Azure Data Lake Analytics tramite il portale di Azure](data-lake-analytics-manage-use-portal.md).
- Per una panoramica su Analisi Data Lake, vedere [Panoramica di Analisi Data Lake di Azure](data-lake-analytics-overview.md).




<!--HONumber=Nov16_HO3-->


