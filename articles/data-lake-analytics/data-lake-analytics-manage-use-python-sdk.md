---
title: Gestire Azure Data Lake Analytics con Python | Microsoft Docs
description: 'Informazioni su come usare Python per creare un account di Data Lake Store e inviare i processi. '
services: data-lake-analytics
documentationcenter: 
author: matt1883
manager: jhubbard
editor: cgronlun
ms.assetid: d4213a19-4d0f-49c9-871c-9cd6ed7cf731
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/18/2017
ms.author: saveenr
ms.custom: devcenter
ms.openlocfilehash: 22b56e9569ac1fd2afe2c91013fa5605f9f3ef99
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-azure-data-lake-analytics-using-python"></a>Gestire Azure Data Lake Analytics con Python

## <a name="python-versions"></a>Versioni di Python

* Usare una versione a 64 bit di Python.
* È possibile usare la distribuzione di Python standard disponibile in **[Python.org downloads](https://www.python.org/downloads/)**. 
* Molti sviluppatori trovano utile usare la **[distribuzione Anaconda Python](https://www.continuum.io/downloads)**.  
* Questo articolo si basa su Python versione 3.6 della distribuzione di Python standard

## <a name="install-azure-python-sdk"></a>Installare Azure Python SDK

Installare i moduli seguenti:

* Il modulo **azure-mgmt-resource** include altri moduli di Azure per Active Directory e così via.
* Il modulo **azure-mgmt-datalake-store** include le operazioni di gestione account di Azure Data Lake Store.
* Il modulo **azure-datalake-store** include le operazioni di file system di Azure Data Lake Store. 
* Il modulo **azure-datalake-analytics** include le operazioni di Azure Data Lake Analytics. 

Assicurarsi prima di tutto di avere la versione più recente di `pip` usando il comando seguente:

```
python -m pip install --upgrade pip
```

Questo documento si basa su `pip version 9.0.1`.

Per installare i moduli dalla riga di comando, usare i comandi `pip` seguenti:

```
pip install azure-mgmt-resource
pip install azure-datalake-store
pip install azure-mgmt-datalake-store
pip install azure-mgmt-datalake-analytics
```

## <a name="create-a-new-python-script"></a>Creare un nuovo script Python

Incollare il codice seguente nello script:

```python
## Use this only for Azure AD service-to-service authentication
#from azure.common.credentials import ServicePrincipalCredentials

## Use this only for Azure AD end-user authentication
#from azure.common.credentials import UserPassCredentials

## Required for Azure Resource Manager
from azure.mgmt.resource.resources import ResourceManagementClient
from azure.mgmt.resource.resources.models import ResourceGroup

## Required for Azure Data Lake Store account management
from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
from azure.mgmt.datalake.store.models import DataLakeStoreAccount

## Required for Azure Data Lake Store filesystem management
from azure.datalake.store import core, lib, multithread

## Required for Azure Data Lake Analytics account management
from azure.mgmt.datalake.analytics.account import DataLakeAnalyticsAccountManagementClient
from azure.mgmt.datalake.analytics.account.models import DataLakeAnalyticsAccount, DataLakeStoreAccountInfo

## Required for Azure Data Lake Analytics job management
from azure.mgmt.datalake.analytics.job import DataLakeAnalyticsJobManagementClient
from azure.mgmt.datalake.analytics.job.models import JobInformation, JobState, USqlJobProperties

## Required for Azure Data Lake Analytics catalog management
from azure.mgmt.datalake.analytics.catalog import DataLakeAnalyticsCatalogManagementClient

## Use these as needed for your application
import logging, getpass, pprint, uuid, time
```

Eseguire questo script per verificare che i moduli possano essere importati.

## <a name="authentication"></a>Autenticazione

### <a name="interactive-user-authentication-with-a-pop-up"></a>Autenticazione utente interattiva con un popup

Questo metodo non è supportato.

### <a name="interactive-user-authentication-with-a-device-code"></a>Autenticazione utente interattiva con un codice di dispositivo

```python
user = input('Enter the user to authenticate with that has permission to subscription: ')
password = getpass.getpass()
credentials = UserPassCredentials(user, password)
```

### <a name="noninteractive-authentication-with-spi-and-a-secret"></a>Autenticazione non interattiva con una SPI e un segreto

```python
credentials = ServicePrincipalCredentials(client_id = 'FILL-IN-HERE', secret = 'FILL-IN-HERE', tenant = 'FILL-IN-HERE')
```

### <a name="noninteractive-authentication-with-api-and-a-certificate"></a>Autenticazione non interattiva con un'API e un certificato

Questo metodo non è supportato.

## <a name="common-script-variables"></a>Variabili dello script comuni

Negli esempi sono usate queste variabili.

```python
subid= '<Azure Subscription ID>'
rg = '<Azure Resource Group Name>'
location = '<Location>' # i.e. 'eastus2'
adls = '<Azure Data Lake Store Account Name>'
adla = '<Azure Data Lake Analytics Account Name>'
```

## <a name="create-the-clients"></a>Creare i client

```python
resourceClient = ResourceManagementClient(credentials, subid)
adlaAcctClient = DataLakeAnalyticsAccountManagementClient(credentials, subid)
adlaJobClient = DataLakeAnalyticsJobManagementClient( credentials, 'azuredatalakeanalytics.net')
```

## <a name="create-an-azure-resource-group"></a>Creare un gruppo di risorse di Azure

```python
armGroupResult = resourceClient.resource_groups.create_or_update( rg, ResourceGroup( location=location ) )
```

## <a name="create-data-lake-analytics-account"></a>Creare un account di Analisi Data Lake

Creare prima un account di archiviazione.

```python
adlaAcctResult = adlaAcctClient.account.create(
    rg,
    adla,
    DataLakeAnalyticsAccount(
        location=location,
        default_data_lake_store_account=adls,
        data_lake_store_accounts=[DataLakeStoreAccountInfo(name=adls)]
    )
).wait()
```
Creare quindi un account ADLA che usa tale archivio.

```python
adlaAcctResult = adlaAcctClient.account.create(
    rg,
    adla,
    DataLakeAnalyticsAccount(
        location=location,
        default_data_lake_store_account=adls,
        data_lake_store_accounts=[DataLakeStoreAccountInfo(name=adls)]
    )
).wait()
```

## <a name="submit-a-job"></a>Inviare un processo

```python
script = """
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
"""

jobId = str(uuid.uuid4())
jobResult = adlaJobClient.job.create(
    adla,
    jobId,
    JobInformation(
        name='Sample Job',
        type='USql',
        properties=USqlJobProperties(script=script)
    )
)
```

## <a name="wait-for-a-job-to-end"></a>Attendere la fine di un processo

```python
jobResult = adlaJobClient.job.get(adla, jobId)
while(jobResult.state != JobState.ended):
    print('Job is not yet done, waiting for 3 seconds. Current state: ' + jobResult.state.value)
    time.sleep(3)
    jobResult = adlaJobClient.job.get(adla, jobId)

print ('Job finished with result: ' + jobResult.result.value)
```

## <a name="list-pipelines-and-recurrences"></a>Elencare pipeline e ricorrenze
A seconda se i processi hanno pipeline o metadati associati, è possibile elencare le pipeline e le ricorrenze.

```python
pipelines = adlaJobClient.pipeline.list(adla)
for p in pipelines:
    print('Pipeline: ' + p.name + ' ' + p.pipelineId)

recurrences = adlaJobClient.recurrence.list(adla)
for r in recurrences:
    print('Recurrence: ' + r.name + ' ' + r.recurrenceId)
```

## <a name="manage-compute-policies"></a>Gestire i criteri di calcolo

L'oggetto DataLakeAnalyticsAccountManagementClient offre metodi per gestire i criteri di calcolo per un account Data Lake Analytics.

### <a name="list-compute-policies"></a>Elencare i criteri di calcolo

Il codice seguente recupera un elenco di criteri di calcolo per un account Data Lake Analytics.

```python
policies = adlaAccountClient.computePolicies.listByAccount(rg, adla)
for p in policies:
    print('Name: ' + p.name + 'Type: ' + p.objectType + 'Max AUs / job: ' + p.maxDegreeOfParallelismPerJob + 'Min priority / job: ' + p.minPriorityPerJob)
```

### <a name="create-a-new-compute-policy"></a>Creare un nuovo criterio di calcolo

Il codice seguente crea un nuovo criterio di calcolo per un account Data Lake Analytics, impostando il massimo di unità di analisi disponibile per l'utente specificato su 50 e la priorità minima del processo su 250.

```python
userAadObjectId = "3b097601-4912-4d41-b9d2-78672fc2acde"
newPolicyParams = ComputePolicyCreateOrUpdateParameters(userAadObjectId, "User", 50, 250)
adlaAccountClient.computePolicies.createOrUpdate(rg, adla, "GaryMcDaniel", newPolicyParams)
```

## <a name="next-steps"></a>Passaggi successivi

- Per visualizzare la stessa esercitazione usando altri strumenti, scegliere i selettori di scheda nella parte superiore della pagina.
- Per informazioni su U-SQL, vedere [Introduzione al linguaggio U-SQL di Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
- Per informazioni sulle attività di gestione, vedere [Gestire Azure Data Lake Analytics tramite il portale di Azure](data-lake-analytics-manage-use-portal.md).

