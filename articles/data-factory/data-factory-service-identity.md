---
title: Identità gestita per Data Factory
description: Informazioni sull'identità gestita per Azure Data Factory.
services: data-factory
author: linda33wj
manager: shwang
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: jingwang
ms.openlocfilehash: ede1e56384c75c64765962b7db196973a30c605b
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928438"
---
# <a name="managed-identity-for-data-factory"></a>Identità gestita per Data Factory

Questo articolo consente di comprendere l'identità gestita per Data Factory (noto in precedenza come identità del servizio gestita/MSI) e il relativo funzionamento.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Panoramica

Quando si crea una data factory, è possibile creare un'identità gestita insieme alla creazione della factory. L'identità gestita è un'applicazione gestita registrata per Azure Active Directory e rappresenta questo data factory specifico.

L'identità gestita per Data Factory avvantaggia le funzionalità seguenti:

- [Archiviare le credenziali in Azure Key Vault](store-credentials-in-key-vault.md), nel qual caso data factory identità gestita viene utilizzata per l'autenticazione di Azure Key Vault.
- I connettori tra cui [archiviazione Blob di Azure](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Database SQL di Azure](connector-azure-sql-database.md) e [Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md).
- [Attività Web](control-flow-web-activity.md).

## <a name="generate-managed-identity"></a>Genera identità gestita

L'identità gestita per Data Factory viene generata come segue:

- Quando si crea data factory tramite **portale di Azure o PowerShell**, l'identità gestita viene sempre creata automaticamente.
- Quando si crea data factory tramite **SDK**, l'identità gestita viene creata solo se si specifica "Identity = New FactoryIdentity ()" nell'oggetto factory per la creazione. Vedere l'esempio nella [guida introduttiva di .NET per la creazione di una data factory](quickstart-create-data-factory-dot-net.md#create-a-data-factory).
- Quando si crea data factory tramite l' **API REST**, l'identità gestita viene creata solo se si specifica la sezione "Identity" nel corpo della richiesta. Vedere l'esempio nella [guida introduttiva di REST per la creazione di una data factory](quickstart-create-data-factory-rest-api.md#create-a-data-factory).

Se la data factory non dispone di un'identità gestita associata al recupero dell'istruzione di [identità gestita](#retrieve-managed-identity) , è possibile generarne una in modo esplicito aggiornando il data factory con l'iniziatore di identità a livello di codice:

- [Generare identità gestite con PowerShell](#generate-managed-identity-using-powershell)
- [Generare identità gestite con l'API REST](#generate-managed-identity-using-rest-api)
- [Generare un'identità gestita usando un modello di Azure Resource Manager](#generate-managed-identity-using-an-azure-resource-manager-template)
- [Genera identità gestita con SDK](#generate-managed-identity-using-sdk)

>[!NOTE]
>- Non è possibile modificare l'identità gestita. L'aggiornamento di un data factory che ha già un'identità gestita non avrà alcun effetto, l'identità gestita viene mantenuta invariata.
>- Se si aggiorna un data factory che ha già un'identità gestita senza specificare il parametro "Identity" nell'oggetto factory o se non si specifica la sezione "Identity" nel corpo della richiesta REST, verrà ricevuto un errore.
>- Quando si elimina una data factory, l'identità gestita associata verrà eliminata insieme a.

### <a name="generate-managed-identity-using-powershell"></a>Generare identità gestite con PowerShell

Chiamare di nuovo il comando **set-AzDataFactoryV2** . vengono visualizzati i campi "Identity" appena generati:

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

### <a name="generate-managed-identity-using-rest-api"></a>Generare identità gestite con l'API REST

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

**Risposta**: l'identità gestita viene creata automaticamente e la sezione "Identity" viene popolata di conseguenza.

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

### <a name="generate-managed-identity-using-an-azure-resource-manager-template"></a>Generare un'identità gestita usando un modello di Azure Resource Manager

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

### <a name="generate-managed-identity-using-sdk"></a>Genera identità gestita con SDK

Chiamare la funzione di creazione o aggiornamento di data factory con Identity=new FactoryIdentity(). Di seguito è riportato codice di esempio con .NET:

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="retrieve-managed-identity"></a>Recupera identità gestita

È possibile recuperare l'identità gestita da portale di Azure o a livello di codice. Le sezioni seguenti illustrano alcuni esempi.

>[!TIP]
> Se l'identità gestita non è visibile, [generare l'identità gestita](#generate-managed-identity) aggiornando la factory.

### <a name="retrieve-managed-identity-using-azure-portal"></a>Recuperare l'identità gestita usando portale di Azure

È possibile trovare le informazioni sull'identità gestita da portale di Azure > le proprietà di data factory >:

- ID oggetto identità gestita
- Tenant di identità gestita
- **ID applicazione identità gestita** > copiare questo valore

![Recupera identità gestita](media/data-factory-service-identity/retrieve-service-identity-portal.png)

### <a name="retrieve-managed-identity-using-powershell"></a>Recuperare l'identità gestita con PowerShell

L'ID dell'entità di identità gestita e l'ID tenant verranno restituiti quando si ottiene uno specifico data factory come indicato di seguito:

```powershell
PS C:\WINDOWS\system32> (Get-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

Copiare l'ID entità, quindi eseguire il comando di Azure Active Directory seguente con l'ID entità come parametro per ottenere il valore di **ApplicationId**, usato per concedere l'accesso:

```powershell
PS C:\WINDOWS\system32> Get-AzADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

## <a name="next-steps"></a>Passaggi successivi
Vedere gli argomenti seguenti che introducono quando e come usare data factory identità gestita:

- [Archiviare le credenziali in Azure Key Vault](store-credentials-in-key-vault.md)
- [Copiare dati da e verso Azure Data Lake Store usando identità gestite per l'autenticazione di risorse di Azure](connector-azure-data-lake-store.md)

Vedere [Panoramica delle identità gestite per le risorse di Azure](/azure/active-directory/managed-identities-azure-resources/overview) per altre informazioni di base sulle identità gestite per le risorse di Azure, che data factory identità gestita è basata su. 
