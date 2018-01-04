---
title: "Identità del servizio di Azure Data Factory | Microsoft Docs"
description: "Informazioni sull'identità del servizio di data factory in Azure Data Factory."
services: data-factory
author: linda33wj
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: jingwang
ms.openlocfilehash: 350ce0fd9a10d862e811dd1eaa39e0e90076e1ef
ms.sourcegitcommit: 4ac89872f4c86c612a71eb7ec30b755e7df89722
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2017
---
# <a name="azure-data-factory-service-identity"></a>Identità del servizio di Azure Data Factory

Questo articolo consente di capire cos'è l'identità del servizio di data factory e come funziona.

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio di Data Factory, disponibile a livello generale, vedere la [documentazione sulla versione 1 di Data Factory](v1/data-factory-introduction.md).

## <a name="overview"></a>Panoramica

Durante la creazione di una data factory è possibile creare contemporaneamente un'identità del servizio. L'identità del servizio è un'applicazione gestita registrata in Azure Activity Directory e rappresenta la data factory specifica.

L'identità del servizio di data factory offre le due funzioni seguenti:

- [Archiviazione delle credenziali in Azure Key Vault](store-credentials-in-key-vault.md), in cui l'identità del servizio di data factory viene usata per l'autenticazione a Azure Key Vault.
- [Copia dei dati da/in Azure Data Lake Store](connector-azure-data-lake-store.md), in cui l'identità del servizio di data factory può essere usata come uno dei tipi di autenticazione a Data Lake Store.

## <a name="generate-service-identity"></a>Generare l'identità del servizio

L'identità del servizio di data factory viene generata come segue:

- Quando si crea una data factory tramite il **portale di Azure o PowerShell**, a partire dall'anteprima pubblica della versione 2 di ADF l'identità del servizio verrà sempre creata automaticamente.
- Quando si crea la data factory tramite **SDK**, l'identità del servizio verrà creata solo se si specifica "Identity = new FactoryIdentity()" nell'oggetto factory per la creazione. Vedere l'esempio nella [guida introduttiva di .NET per la creazione di una data factory](quickstart-create-data-factory-dot-net.md#create-a-data-factory).
- Quando si crea la data factory tramite l'**API REST**, l'identità del servizio verrà creata solo se si specifica la sezione "identity" nel corpo della richiesta. Vedere l'esempio nella [guida introduttiva di REST per la creazione di una data factory](quickstart-create-data-factory-rest-api.md#create-a-data-factory).

Se seguendo le istruzioni riportate in [Recuperare l'identità del servizio](#retrieve-service-identity) risulta che alla data factory non è associata un'identità del servizio, è possibile generarne esplicitamente una aggiornando la data factory con l'iniziatore di identità a livello programmatico:

- [Generare l'identità del servizio tramite PowerShell](#generate-service-identity-using-powershell)
- [Generare l'identità del servizio tramite API REST](#generate-service-identity-using-rest-api)
- [Generare l'identità del servizio tramite SDK](#generate-service-identity-using-sdk)

>[!NOTE]
>- L'identità del servizio non può essere modificata. L'aggiornamento di una data factory che ha già un'identità del servizio non avrà alcun impatto. L'identità del servizio resta invariata.
>- Se si aggiorna una data factory che ha già un'identità del servizio senza specificare il parametro "identity" nell'oggetto factory o la sezione "identity" nel corpo della richiesta REST, verrà visualizzato un errore.
>- Quando si elimina una data factory, viene eliminata anche l'identità del servizio associata.

### <a name="generate-service-identity-using-powershell"></a>Generare l'identità del servizio tramite PowerShell

Chiamare di nuovo il comando **Set-AzureRmDataFactoryV2**. Verranno visualizzati i nuovi campi "Identity" generati:

```powershell
PS C:\WINDOWS\system32> Set-AzureRmDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName> -Location <region>

DataFactoryName   : ADFV2DemoFactory
DataFactoryId     : /subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory
ResourceGroupName : <resourceGroupName>
Location          : East US
Tags              : {}
Identity          : Microsoft.Azure.Management.DataFactory.Models.FactoryIdentity
ProvisioningState : Succeeded
```

### <a name="generate-service-identity-using-rest-api"></a>Generare l'identità del servizio tramite API REST

Chiamare l'API seguente con la sezione "identity" nel corpo della richiesta:

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2017-09-01-preview
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

**Risposta**: l'identità del servizio viene creata automaticamente e la sezione "identity" viene popolata di conseguenza.

```json
{
    "name": "ADFV2DemoFactory",
    "tags": {},
    "properties": {
        "provisioningState": "Succeeded",
        "loggingStorageAccountKey": "**********",
        "createTime": "2017-09-26T04:10:01.1135678Z",
        "version": "2017-09-01-preview"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "765ad4ab-XXXX-XXXX-XXXX-51ed985819dc",
        "tenantId": "72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory",
    "type": "Microsoft.DataFactory/factories",
    "location": "EastUS"
}
```

### <a name="generate-service-identity-using-sdk"></a>Generare l'identità del servizio tramite SDK

Chiamare la funzione di creazione o aggiornamento di data factory con Identity=new FactoryIdentity(). Di seguito è riportato codice di esempio con .NET:

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="retrieve-service-identity"></a>Recuperare l'identità del servizio

È possibile recuperare l'identità del servizio dal portale di Azure o a livello di codice. Le sezioni seguenti illustrano alcuni esempi.

>[!TIP]
> Se l'identità del servizio non viene visualizzata, [generare l'identità del servizio](#generate-service-identity) aggiornando la factory.

### <a name="retrieve-service-identity-using-azure-portal"></a>Recuperare l'identità del servizio tramite il portale di Azure

È possibile trovare le informazioni sull'identità del servizio dal portale di Azure selezionando la data factory -> Impostazioni -> Proprietà:

- ID IDENTITÀ DEL SERVIZIO
- TENANT IDENTITÀ DEL SERVIZIO
- **ID APPLICAZIONE IDENTITÀ DEL SERVIZIO** > copiare il valore

![Recuperare l'identità del servizio](media/data-factory-service-identity/retrieve-service-identity-portal.png)

### <a name="retrieve-service-identity-using-powershell"></a>Recuperare l'identità del servizio tramite PowerShell

L'ID entità e l'ID tenant dell'identità del servizio verranno restituiti quando si recupera una specifica data factory come segue:

```powershell
PS C:\WINDOWS\system32> (Get-AzureRmDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

Copiare l'ID entità, quindi eseguire il comando di Azure Active Directory seguente con l'ID entità come parametro per ottenere il valore di **ApplicationId**, usato per concedere l'accesso:

```powershell
PS C:\WINDOWS\system32> Get-AzureRmADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

## <a name="next-steps"></a>Passaggi successivi
Vedere gli argomenti seguenti che presentano quando e come usare l'identità del servizio di data factory:

- [Archiviare le credenziali in Azure Key Vault](store-credentials-in-key-vault.md)
- [Copiare i dati da e in Azure Data Lake Store usando l'autenticazione gestita all'identità del servizio](connector-azure-data-lake-store.md)

Vedere [MSI Panoramica](~/articles/active-directory/msi-overview.md) per ulteriori informazioni sulle identità del servizio gestito, si basano le identità del servizio data factory. 