---
title: Managed identity per Data Factory | Microsoft Docs
description: Informazioni sulle identità gestita per Azure Data Factory.
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: jingwang
ms.openlocfilehash: 3663526dc32b0a607c9fca3d7c76496bfb5566f4
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57549146"
---
# <a name="managed-identity-for-data-factory"></a>Identità gestita per Data Factory

Questo articolo aiuta a comprendere che cos'è l'identità gestita per Data Factory (precedentemente noto come Managed Service Identity/MSI) e il relativo funzionamento.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Panoramica

Quando si crea una data factory, è possibile creare un'identità gestita e la creazione della factory. L'identità gestita è un'applicazione gestita registrata in Azure Activity Directory e rappresenta la data factory specifica.

Identità gestita per Data Factory vantaggi le funzionalità seguenti:

- [Store delle credenziali in Azure Key Vault](store-credentials-in-key-vault.md), nel qual caso data factory managed identity viene utilizzata per l'autenticazione di Azure Key Vault.
- I connettori tra cui [archiviazione Blob di Azure](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Database SQL di Azure](connector-azure-sql-database.md) e [Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md).
- [Attività Web](control-flow-web-activity.md).

## <a name="generate-managed-identity"></a>Generare l'identità gestita

Identità gestita per Data Factory viene generato come indicato di seguito:

- Durante la creazione di data factory tramite **portale di Azure o PowerShell**gestiti, identità verrà sempre creata automaticamente.
- Durante la creazione di data factory tramite **SDK**gestiti, identità verrà creata solo se si specifica "Identity = new factoryidentity ()" nell'oggetto factory per la creazione. Vedere l'esempio nella [guida introduttiva di .NET per la creazione di una data factory](quickstart-create-data-factory-dot-net.md#create-a-data-factory).
- Durante la creazione di data factory tramite **API REST**gestiti, identità verrà creata solo se si specifica sezione "identity" nel corpo della richiesta. Vedere l'esempio nella [guida introduttiva di REST per la creazione di una data factory](quickstart-create-data-factory-rest-api.md#create-a-data-factory).

Se si trova alla data factory non è un'identità gestita associata seguendo [recuperare l'identità gestita](#retrieve-managed-identity) (istruzione), è possibile generarne esplicitamente una aggiornando la data factory con l'iniziatore di identità a livello di codice:

- [Generare l'identità gestita tramite PowerShell](#generate-managed-identity-using-powershell)
- [Generare l'identità gestita tramite l'API REST](#generate-managed-identity-using-rest-api)
- Generare l'identità gestita tramite un modello Azure Resource Manager
- [Generare l'identità gestita tramite SDK](#generate-managed-identity-using-sdk)

>[!NOTE]
>- Identità gestita non può essere modificato. L'aggiornamento di una data factory che ha già un'identità gestita non avrà alcun impatto, l'identità gestita resta invariata.
>- Se si aggiorna una data factory che ha già un'identità gestita senza specificare il parametro "identity" nell'oggetto factory o sezione "identity" nel corpo della richiesta REST, si otterrà un errore.
>- Quando si elimina una data factory, l'identità gestita associato verrà eliminato insieme.

### <a name="generate-managed-identity-using-powershell"></a>Generare l'identità gestita tramite PowerShell

Chiamare **Set-AzDataFactoryV2** nuovo il comando è visualizzare campi "Identity" generati:

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

### <a name="generate-managed-identity-using-rest-api"></a>Generare l'identità gestita tramite l'API REST

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

**Risposta**: identità gestita viene creata automaticamente e sezione "identity" viene popolata di conseguenza.

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

### <a name="generate-managed-identity-using-an-azure-resource-manager-template"></a>Generare l'identità gestita tramite un modello Azure Resource Manager

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

### <a name="generate-managed-identity-using-sdk"></a>Generare l'identità gestita tramite SDK

Chiamare la funzione di creazione o aggiornamento di data factory con Identity=new FactoryIdentity(). Di seguito è riportato codice di esempio con .NET:

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="retrieve-managed-identity"></a>Recuperare l'identità gestita

È possibile recuperare l'identità gestita dal portale di Azure o a livello di codice. Le sezioni seguenti illustrano alcuni esempi.

>[!TIP]
> Se non viene visualizzata l'identità gestita [generare l'identità gestita](#generate-managed-identity) aggiornando la factory.

### <a name="retrieve-managed-identity-using-azure-portal"></a>Recuperare l'identità gestita tramite il portale di Azure

È possibile trovare le informazioni sull'identità gestito dal portale di Azure -> data factory -> Impostazioni -> proprietà:

- ID IDENTITÀ DEL SERVIZIO
- TENANT IDENTITÀ DEL SERVIZIO
- **ID APPLICAZIONE IDENTITÀ DEL SERVIZIO** > copiare il valore

![Recuperare l'identità gestita](media/data-factory-service-identity/retrieve-service-identity-portal.png)

### <a name="retrieve-managed-identity-using-powershell"></a>Recuperare l'identità gestita usando PowerShell

Verrà restituito l'identità gestita ID dell'entità e l'ID tenant quando si riceve una data factory specificata come indicato di seguito:

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
Vedere gli argomenti seguenti che presentano quando e come usare data factory identità gestito:

- [Archiviare le credenziali in Azure Key Vault](store-credentials-in-key-vault.md)
- [Copiare dati da e verso Azure Data Lake Store usando identità gestite per l'autenticazione di risorse di Azure](connector-azure-data-lake-store.md)

Visualizzare [identità gestite per la panoramica delle risorse di Azure](/azure/active-directory/managed-identities-azure-resources/overview) per altre informazioni sull'identità gestita per le risorse di Azure, quali data factory di identità gestita in base al momento. 
