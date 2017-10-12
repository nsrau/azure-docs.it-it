---
title: Archiviare le credenziali in Azure Key Vault | Microsoft Docs
description: "Informazioni su come archiviare le credenziali per gli archivi dati usati in un insieme di credenziali delle chiavi di Azure che Azure Data Factory può recuperare automaticamente in fase di esecuzione."
services: data-factory
author: linda33wj
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: jingwang
ms.openlocfilehash: cb4545dcd41fe9a2c73bc86096843b717d61a785
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="store-credential-in-azure-key-vault"></a>Archiviare le credenziali in Azure Key Vault

È possibile archiviare le credenziali per gli archivi dati in un [insieme di credenziali delle chiavi di Azure](../key-vault/key-vault-whatis.md). Azure Data Factory Azure recupera le credenziali durante l'esecuzione di un'attività che usa l'archivio dati.

> [!NOTE]
> Attualmente solo il [connettore Dynamics](connector-dynamics-crm-office-365.md) supporta questa funzionalità.

## <a name="steps"></a>Passi

Durante la creazione di una data factory è possibile creare contemporaneamente un'identità del servizio. L'identità del servizio è un'applicazione gestita registrata in Azure Activity Directory e rappresenta la data factory specifica.

- Quando si crea la data factory tramite il **portale di Azure o PowerShell**, a partire dall'anteprima pubblica l'identità del servizio verrà sempre creata automaticamente.
- Quando si crea la data factory tramite **SDK**, l'identità del servizio verrà creata solo se si specifica "Identity = new FactoryIdentity()" nell'oggetto factory per la creazione. Vedere l'esempio nella sezione relativa alla [creazione di una data factory nella guida introduttiva per .NET](quickstart-create-data-factory-dot-net.md#create-a-data-factory).
- Quando si crea la data factory tramite l'**API REST**, l'identità del servizio verrà creata solo se si specifica la sezione "identity" nel corpo della richiesta. Vedere l'esempio nella sezione relativa alla [creazione di una data factory nella guida introduttiva per REST](quickstart-create-data-factory-rest-api.md#create-a-data-factory).

Per fare riferimento a una credenziale archiviata in Azure Key Vault, è necessario:

1. Copiare l'"ID APPLICAZIONE IDENTITÀ DEL SERVIZIO" generato con la data factory. Vedere [Recuperare l'identità del servizio](#retrieve-service-identity).
2. Concedere l'accesso dell'identità del servizio ad Azure Key Vault. Nell'insieme di credenziali delle chiavi -> Controllo di accesso -> Aggiungi -> cercare l'ID applicazione identità del servizio per aggiungere l'autorizzazione di lettura. Consente a questa factory designata di accedere al segreto nell'insieme di credenziali.
3. Creare un servizio collegato che punta ad Azure Key Vault. Fare riferimento a [Servizio collegato di Azure Key Vault](#azure-key-vault-linked-service).
4. Creare il servizio collegato di archiviazione dati, nel cui riferimento il segreto corrispondente è archiviato nell'insieme di credenziali delle chiavi. Vedere [Credenziali di riferimento archiviate nell'insieme di credenziali delle chiavi](#reference-credential-stored-in-key-vault).

## <a name="retrieve-service-identity"></a>Recuperare l'identità del servizio

È possibile recuperare l'identità del servizio dal portale di Azure o a livello di codice. Le sezioni seguenti illustrano alcuni esempi.

>[!TIP]
> Se l'identità del servizio non viene visualizzata, [generare l'identità del servizio](#generate-service-identity) aggiornando la factory.

### <a name="using-azure-portal"></a>Uso del portale di Azure

È possibile trovare le informazioni sull'identità del servizio dal portale di Azure selezionando la data factory -> Impostazioni -> Proprietà:

- ID IDENTITÀ DEL SERVIZIO
- TENANT IDENTITÀ DEL SERVIZIO
- **ID APPLICAZIONE DELL'IDENTITÀ DEL SERVIZIO**. Copiare questo valore per concedere l'accesso in Key Vault

![Recuperare l'identità del servizio](media/store-credentials-in-key-vault/retrieve-service-identity-portal.png)

### <a name="using-powershell"></a>Tramite PowerShell

L'ID entità e l'ID tenant dell'identità del servizio verranno restituiti quando si recupera una specifica data factory come segue:

```powershell
PS C:\WINDOWS\system32> (Get-AzureRmDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

Copiare l'ID entità, quindi eseguire il comando di Azure Active Directory seguente con l'ID entità come parametro per ottenere il valore di **ApplicationId**, usato per concedere l'accesso in Key Vault:

```powershell
PS C:\WINDOWS\system32> Get-AzureRmADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

## <a name="azure-key-vault-linked-service"></a>Servizio collegato di Azure Key Vault

Per il servizio collegato di Azure Key Vault sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su **AzureKeyVault**. | Sì |
| baseUrl | Specificare l'URL di Azure Key Vault. | Sì |

**Esempio:**

```json
{
    "name": "AzureKeyVaultLinkedService",
    "properties": {
        "type": "AzureKeyVault",
        "typeProperties": {
        "baseUrl": "https://<azureKeyVaultName>.vault.azure.net"
        }
    }
}
```

## <a name="reference-credential-stored-in-key-vault"></a>Credenziali di riferimento archiviate nell'insieme di credenziali delle chiavi

Quando si configura un campo nel servizio collegato che fa riferimento a un segreto dell'insieme di credenziali delle chiavi, sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| type | La proprietà type del campo deve essere impostata su: **AzureKeyVaultSecret**. | Sì |
| secretName | Il nome del segreto in Azure Key Vault. | Sì |
| secretVersion | La versione del segreto in Azure Key Vault.<br/>Se non specificata, usare sempre la versione più recente del segreto.<br/>Se specificata, corrisponde alla versione specificata.| No |
| store | Fa riferimento a un servizio collegato di Azure Key Vault che si usa per archiviare la credenziale. | Sì |

**Esempio: (vedere la sezione "password")**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "typeProperties": {
            "deploymentType": "<>",
            "organizationName": "<>",
            "authenticationType": "<>",
            "username": "<>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "mySecret",
                "store":{
                    "linkedServiceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
}
```

## <a name="generate-service-identity"></a>Generare l'identità del servizio

Se seguendo le istruzioni riportate in [Recuperare l'identità del servizio](#retrieve-service-identity) risulta che alla data factory non è associata un'identità del servizio, è possibile generarne una aggiornando la data factory con l'iniziatore di identità a livello di codice.

> [!NOTE]
> - **L'identità del servizio non può essere modificata**. L'aggiornamento di una data factory che ha già un'identità del servizio non avrà alcun impatto. L'identità del servizio resterà invariata.
> - **L'identità del servizio non può essere eliminata**. Se si aggiorna una data factory che ha già un'identità del servizio senza specificare il parametro "identity" nell'oggetto factory o la sezione "identity" nel corpo della richiesta REST, verrà visualizzato un errore.

Le sezioni seguenti illustrano alcuni esempi per generare l'identità del servizio per la factory, se non esiste.

### <a name="using-powershell"></a>Tramite PowerShell

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

### <a name="using-rest-api"></a>Uso dell'API REST

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

### <a name="using-sdk"></a>Uso dell'SDK

Chiamare la funzione di creazione o aggiornamento di data factory con Identity=new FactoryIdentity(). Di seguito è riportato codice di esempio con .NET:

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md##supported-data-stores-and-formats).