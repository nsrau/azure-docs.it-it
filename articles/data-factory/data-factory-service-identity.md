---
title: Identità gestita per Data Factory
description: Informazioni sull'identità gestita per Azure Data Factory.Learn about managed identity for Azure Data Factory.
services: data-factory
author: linda33wj
manager: shwang
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: jingwang
ms.openlocfilehash: 45699680ad2003c034bce588857f8b102a0b6d26
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261125"
---
# <a name="managed-identity-for-data-factory"></a>Identità gestita per Data Factory

Questo articolo consente di comprendere che cos'è l'identità gestita per Data Factory (precedentemente nota come Identità del servizio gestito/MSI) e come funziona.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Panoramica

Quando si crea una data factory, è possibile creare un'identità gestita insieme alla creazione di una factory. L'identità gestita è un'applicazione gestita registrata in Azure Active Directory e rappresenta questa data factory specifica.

L'identità gestita per Data Factory offre le funzionalità seguenti:Managed identity for Data Factory benefits the following features:

- [Archiviare le credenziali in Archiviazione chiave](store-credentials-in-key-vault.md)di Azure , nel qual caso l'identità gestita dalla data factory viene usata per l'autenticazione dell'insieme di credenziali delle chiavi di Azure.Store credential in Azure Key Vault , in which case data factory managed identity is used for Azure Key Vault authentication.
- I connettori tra cui [archiviazione Blob di Azure](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Database SQL di Azure](connector-azure-sql-database.md) e [Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md).
- [Attività Web](control-flow-web-activity.md).

## <a name="generate-managed-identity"></a>Generare l'identità gestita

L'identità gestita per Data Factory viene generata come segue:Managed identity for Data Factory is generated as follows:

- Quando si crea una data factory tramite **il portale di Azure o PowerShell,** l'identità gestita verrà sempre creata automaticamente.
- Quando si crea la data factory tramite **SDK**, l'identità gestita verrà creata solo se si specifica "Identity - new FactoryIdentity()" nell'oggetto factory per la creazione. Vedere l'esempio nella [guida introduttiva di .NET per la creazione di una data factory](quickstart-create-data-factory-dot-net.md#create-a-data-factory).
- Quando si crea una data factory tramite **l'API REST,** l'identità gestita verrà creata solo se si specifica la sezione "identity" nel corpo della richiesta. Vedere l'esempio nella [guida introduttiva di REST per la creazione di una data factory](quickstart-create-data-factory-rest-api.md#create-a-data-factory).

Se si rileva che la data factory non dispone di un'identità gestita associata dopo recuperare l'istruzione di [identità gestita,](#retrieve-managed-identity) è possibile generarne in modo esplicito aggiornando la data factory con l'infinitore di identità a livello di codice:If you find your data factory doesn't have a managed identity associated following retrieve managed identity instruction, you can explicitly generate one by updating the data factory with identity initiator programmatically:

- [Generare l'identità gestita tramite PowerShellGenerate managed identity using PowerShell](#generate-managed-identity-using-powershell)
- [Generare un'identità gestita tramite l'API RESTGenerate managed identity using REST API](#generate-managed-identity-using-rest-api)
- [Generare un'identità gestita usando un modello di Azure Resource ManagerGenerate managed identity using an Azure Resource Manager template](#generate-managed-identity-using-an-azure-resource-manager-template)
- [Generare un'identità gestita tramite SDKGenerate managed identity using SDK](#generate-managed-identity-using-sdk)

>[!NOTE]
>- Impossibile modificare l'identità gestita. L'aggiornamento di una data factory che dispone già di un'identità gestita non avrà alcun impatto, l'identità gestita viene mantenuta invariata.
>- Se si aggiorna una data factory che dispone già di un'identità gestita senza specificare il parametro "identity" nell'oggetto factory o senza specificare la sezione "identity" nel corpo della richiesta REST, verrà visualizzato un errore.
>- Quando si elimina una data factory, l'identità gestita associata verrà eliminata insieme.

### <a name="generate-managed-identity-using-powershell"></a>Generare l'identità gestita tramite PowerShellGenerate managed identity using PowerShell

Chiamare nuovamente il comando **Set-AzDataFactoryV2,** quindi vedere i campi "Identità" appena generati:

```powershell
PS C:\WINDOWS\system32> Set-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName> -Location <region>

DataFactoryName   : ADFV2DemoFactory
DataFactoryId     : /subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory
ResourceGroupName : <resourceGroupName>
Location          : East US
Tags              : {}
Identity          : Microsoft.Azure.Management.DataFactory.Models.FactoryIdentity
ProvisioningState : Succeeded
```

### <a name="generate-managed-identity-using-rest-api"></a>Generare un'identità gestita tramite l'API RESTGenerate managed identity using REST API

Chiamare l'API seguente con la sezione "identity" nel corpo della richiesta:

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2018-06-01
```

**Corpo della richiesta**: aggiungere "identity": { "type": "SystemAssigned" }.

```json
{
    "name": "<dataFactoryName>",
    "location": "<region>",
    "properties": {},
    "identity": {
        "type": "SystemAssigned"
    }
}
```

**Risposta**: l'identità gestita viene creata automaticamente e la sezione "identità" viene popolata di conseguenza.

```json
{
    "name": "<dataFactoryName>",
    "tags": {},
    "properties": {
        "provisioningState": "Succeeded",
        "loggingStorageAccountKey": "**********",
        "createTime": "2017-09-26T04:10:01.1135678Z",
        "version": "2018-06-01"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "765ad4ab-XXXX-XXXX-XXXX-51ed985819dc",
        "tenantId": "72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory",
    "type": "Microsoft.DataFactory/factories",
    "location": "<region>"
}
```

### <a name="generate-managed-identity-using-an-azure-resource-manager-template"></a>Generare un'identità gestita usando un modello di Azure Resource ManagerGenerate managed identity using an Azure Resource Manager template

**Modello**: aggiungere "identity": { "type": "SystemAssigned" }.

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "resources": [{
        "name": "<dataFactoryName>",
        "apiVersion": "2018-06-01",
        "type": "Microsoft.DataFactory/factories",
        "location": "<region>",
        "identity": {
            "type": "SystemAssigned"
        }
    }]
}
```

### <a name="generate-managed-identity-using-sdk"></a>Generare un'identità gestita tramite SDKGenerate managed identity using SDK

Chiamare la funzione di creazione o aggiornamento di data factory con Identity=new FactoryIdentity(). Di seguito è riportato codice di esempio con .NET:

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="retrieve-managed-identity"></a>Recuperare l'identità gestitaRetrieve managed identity

È possibile recuperare l'identità gestita dal portale di Azure o a livello di codice. Le sezioni seguenti illustrano alcuni esempi.

>[!TIP]
> Se non vedi l'identità gestita, [genera l'identità gestita](#generate-managed-identity) aggiornando la tua fabbrica.

### <a name="retrieve-managed-identity-using-azure-portal"></a>Recuperare l'identità gestita tramite il portale di AzureRetrieve managed identity using Azure portal

È possibile trovare le informazioni sull'identità gestita dal portale di Azure > le proprietà di > della data factory.

- ID oggetto identità gestita
- Identità gestita Tenant
- ID applicazione identità gestita

Le informazioni sull'identità gestita verranno visualizzate anche quando si crea un servizio collegato che supporta l'autenticazione dell'identità gestita, ad esempio BLOB di Azure, Archiviazione data server di Azure, Archiviazione chiavi di Azure e così via.

Quando si concede l'autorizzazione, utilizzare l'ID oggetto o il nome della data factory (come nome di identità gestita) per trovare questa identità.

### <a name="retrieve-managed-identity-using-powershell"></a>Recuperare l'identità gestita tramite PowerShellRetrieve managed identity using PowerShell

L'ID entità entità identità gestita e l'ID tenant verranno restituiti quando si ottiene una data factory specifica come indicato di seguito. Utilizzare il PrincipalId per concedere l'accesso:Use the **PrincipalId** to grant access:

```powershell
PS C:\WINDOWS\system32> (Get-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

È possibile ottenere l'ID applicazione copiando l'ID entità precedente, quindi eseguendo sotto il comando di Azure Active Directory con ID principale come parametro.

```powershell
PS C:\WINDOWS\system32> Get-AzADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

## <a name="next-steps"></a>Passaggi successivi
Vedere gli argomenti seguenti che illustrano quando e come usare l'identità gestita nella data factory:See the following topics and introduce when and how to use data factory managed identity:

- [Archiviare le credenziali nell'insieme di credenziali delle chiavi di AzureStore credential in Azure Key Vault](store-credentials-in-key-vault.md)
- [Copiare dati da e verso Azure Data Lake Store usando identità gestite per l'autenticazione di risorse di Azure](connector-azure-data-lake-store.md)

Per altre informazioni sulle identità gestite per le risorse di Azure, [vedere Panoramica delle identità gestite per](/azure/active-directory/managed-identities-azure-resources/overview) le risorse di Azure su cui si basa l'identità gestita della data factory. 
