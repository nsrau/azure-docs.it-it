---
title: Creare e gestire le assegnazioni di ruolo-dispositivi gemelli digitali di Azure | Microsoft Docs
description: Informazioni su come creare e gestire le assegnazioni di ruolo in dispositivi gemelli digitali di Azure.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/02/2019
ms.custom: seodec18
ms.openlocfilehash: 68714a06f72a522df0245d9c044bb6ff6557d52f
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949815"
---
# <a name="create-and-manage-role-assignments-in-azure-digital-twins"></a>Creare e gestire assegnazioni di ruolo in Gemelli digitali di Azure

Gemelli digitali di Azure usa il [controllo degli accessi in base al ruolo](./security-role-based-access-control.md) per gestire l'accesso alle risorse.

## <a name="role-assignments-overview"></a>Panoramica delle assegnazioni di ruolo

Ogni assegnazione di ruolo è conforme alla definizione seguente:

```JSON
{
  "roleId": "00e00ad7-00d4-4007-853b-b9968ad000d1",
  "objectId": "be2c6daa-a3a0-0c0a-b0da-c000000fbc5f",
  "objectIdType": "ServicePrincipalId",
  "path": "/",
  "tenantId": "00f000bf-86f1-00aa-91ab-2d7cd000db47"
}
```

La tabella seguente descrive i singoli attributi:

| Attributo | Attività | Richiesto | Type | Descrizione |
| --- | --- | --- | --- | --- |
| RoleId | Identificatore della definizione di ruolo | Yes | Stringa | ID univoco dell'assegnazione di ruolo desiderata. È possibile ottenere le definizioni dei ruoli e i relativi identificatori eseguendo una query sull'API di sistema o consultando la tabella seguente. |
| objectId | Identificatore di oggetto | Yes | Stringa | ID di Azure Active Directory, ID oggetto dell'entità servizio o nome di dominio. Destinatario dell'assegnazione di ruolo. L'assegnazione di ruolo deve essere formattata in base al tipo associato. Per l'elemento objectIdType `DomainName`, objectId deve iniziare con il carattere `“@”`. |
| objectIdType | Tipo di identificatore di oggetto | Yes | Stringa | Tipo di identificatore di oggetto usato. Vedere **ObjectIdType supportati** di seguito. |
| path | Percorso di spazio | Yes | Stringa | Percorso di accesso completo dell'oggetto `Space`. Un esempio è `/{Guid}/{Guid}`. Se un identificatore richiede l'assegnazione di ruolo per l'intero grafico, specificare `"/"`. Questo carattere designa la radice, ma non è consigliabile usarlo. Seguire sempre il principio del privilegio minimo. |
| tenantId | Identificatore del tenant | Variabile | Stringa | Nella maggior parte dei casi, ID tenant di Azure Active Directory. Non consentito per gli elementi ObjectIdType `DeviceId` e `TenantId`. Obbligatorio per gli elementi ObjectIdType `UserId` e `ServicePrincipalId`. Facoltativo per l'elemento ObjectIdType DomainName. |

### <a name="supported-role-definition-identifiers"></a>Identificatori delle definizioni del ruolo supportati

Ogni assegnazione di ruolo associa una definizione del ruolo a un'entità nell'ambiente di Gemelli digitali di Azure.

[!INCLUDE [digital-twins-roles](../../includes/digital-twins-roles.md)]

### <a name="supported-object-identifier-types"></a>Tipi di identificatori di oggetto supportati

L'attributo **objectIdType** è stato presentato in precedenza.

[!INCLUDE [digital-twins-object-types](../../includes/digital-twins-object-id-types.md)]

## <a name="role-assignment-operations"></a>Operazioni di assegnazione di ruolo

Gemelli digitali di Azure supporta operazioni *CREATE*, *READ* e *DELETE* complete per le assegnazioni di ruolo. Le operazioni *UPDATE* vengono gestite aggiungendo o rimuovendo assegnazioni di ruolo o modificando i nodi di [Grafico di intelligenza spaziale](./concepts-objectmodel-spatialgraph.md) a cui le assegnazioni di ruolo concedono l'accesso.

[endpoint di assegnazione ![Role](media/security-roles/roleassignments.png)](media/security-roles/roleassignments.png#lightbox)

La documentazione di riferimento di Swagger fornita contiene ulteriori informazioni su tutti gli endpoint API, le operazioni di richiesta e le definizioni disponibili.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

### <a name="grant-permissions-to-your-service-principal"></a>Concedere autorizzazioni all'entità servizio

La concessione di autorizzazioni all'entità servizio è spesso una delle prime operazioni da eseguire quando si usa Gemelli digitali di Azure. Comporta le attività seguenti:

1. Accedere all'istanza di Azure tramite l' [interfaccia](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) della riga di comando di Azure o [PowerShell](https://docs.microsoft.com/powershell/azure/).
1. Acquisizione delle informazioni sull'entità servizio.
1. Assegnazione del ruolo desiderato all'entità servizio.

L'ID applicazione viene fornito in Azure Active Directory. Per altre informazioni sulla configurazione e il provisioning di un'istanza di Gemelli digitali di Azure in Active Directory, vedere la [guida di avvio rapido](./quickstart-view-occupancy-dotnet.md).

Una volta ottenuto l'ID applicazione, eseguire uno dei comandi seguenti. Nell'interfaccia della riga di comando di Azure:

```azurecli
az login
az ad sp show --id <ApplicationId>
```

In PowerShell:

```powershell
Login-AzAccount
Get-AzADServicePrincipal -ApplicationId <ApplicationId>
```

Un utente con il ruolo **Amministratore** può quindi assegnare il ruolo Amministratore dello spazio a un altro utente inviando una richiesta HTTP POST autenticata all'URL:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments
```

Con il corpo JSON seguente:

```JSON
{
  "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
  "objectId": "YOUR_SERVICE_PRINCIPLE_OBJECT_ID",
  "objectIdType": "ServicePrincipalId",
  "path": "YOUR_PATH",
  "tenantId": "YOUR_TENANT_ID"
}
```

### <a name="retrieve-all-roles"></a>Recuperare tutti i ruoli

[ruoli ![System](media/security-roles/system.png)](media/security-roles/system.png#lightbox)

Per ottenere un elenco di tutti i ruoli disponibili (definizioni del ruolo), inviare una richiesta HTTP GET autenticata a:

```plaintext
YOUR_MANAGEMENT_API_URL/system/roles
```

Una richiesta riuscita restituirà una matrice JSON con voci per ogni ruolo che può essere assegnato:

```JSON
[
    {
        "id": "3cdfde07-bc16-40d9-bed3-66d49a8f52ae",
        "name": "DeviceAdministrator",
        "permissions": [
            {
                "notActions": [],
                "actions": [
                    "Read",
                    "Create",
                    "Update",
                    "Delete"
                ],
                "condition": "@Resource.Type Any_of {'Device', 'DeviceBlobMetadata', 'DeviceExtendedProperty', 'Sensor', 'SensorBlobMetadata', 'SensorExtendedProperty'} || ( @Resource.Type == 'ExtendedType' && (!Exists @Resource.Category || @Resource.Category Any_of { 'DeviceSubtype', 'DeviceType', 'DeviceBlobType', 'DeviceBlobSubtype', 'SensorBlobSubtype', 'SensorBlobType', 'SensorDataSubtype', 'SensorDataType', 'SensorDataUnitType', 'SensorPortType', 'SensorType' } ) )"
            },
            {
                "notActions": [],
                "actions": [
                    "Read"
                ],
                "condition": "@Resource.Type == 'Space' && @Resource.Category == 'WithoutSpecifiedRbacResourceTypes' || @Resource.Type Any_of {'ExtendedPropertyKey', 'SpaceExtendedProperty', 'SpaceBlobMetadata', 'SpaceResource', 'Matcher'}"
            }
        ],
        "accessControlPath": "/system",
        "friendlyPath": "/system",
        "accessControlType": "System"
    }
]
```

### <a name="check-a-specific-role-assignment"></a>Controllare un'assegnazione di ruolo specifica

Per controllare un'assegnazione di ruolo specifica, inviare una richiesta HTTP GET autenticata a:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments/check?userId=YOUR_USER_ID&path=YOUR_PATH&accessType=YOUR_ACCESS_TYPE&resourceType=YOUR_RESOURCE_TYPE
```

| **Valore del parametro** | **Obbligatorio** |  **Tipo** |  **Descrizione** |
| --- | --- | --- | --- |
| YOUR_USER_ID |  True | Stringa |   objectId dell'objectIdType UserId. |
| YOUR_PATH | True | Stringa |   Percorso per cui verificare l'accesso. |
| YOUR_ACCESS_TYPE |  True | Stringa |   *Lettura*, *creazione*, *aggiornamento*o *eliminazione* |
| YOUR_RESOURCE_TYPE | True | Stringa |  *Device*, *DeviceBlobMetadata*, *DeviceExtendedProperty*, *ExtendedPropertyKey*, *ExtendedType*, *endpoint*, *keystore*, *Matcher*, *ontologia*, *report*,  *RoleDefinition*, *Sensor*, *SensorExtendedProperty*, *Space*, *SpaceBlobMetadata*, *SpaceExtendedProperty*, *SpaceResource*, *SpaceRoleAssignment*, *System* , *UerDefinedFunction*, *User*, *UserBlobMetadata*o *UserExtendedProperty* |

Una richiesta riuscita restituirà un valore booleano `true` o `false` per indicare se il tipo di accesso è stato assegnato all'utente per il percorso e la risorsa specificati.

### <a name="get-role-assignments-by-path"></a>Ottenere le assegnazioni di ruolo per percorso

Per ottenere tutte le assegnazioni di ruolo per un percorso, inviare una richiesta HTTP GET autenticata a:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments?path=YOUR_PATH
```

| Value | Sostituire con |
| --- | --- |
| YOUR_PATH | Percorso completo dello spazio |

Una richiesta riuscita restituirà una matrice JSON con ogni assegnazione di ruolo associata al parametro **path** selezionato:

```JSON
[
    {
        "id": "0000c484-698e-46fd-a3fd-c12aa11e53a1",
        "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
        "objectId": "0de38846-1aa5-000c-a46d-ea3d8ca8ee5e",
        "objectIdType": "UserId",
        "path": "/"
    }
]
```

### <a name="revoke-a-permission"></a>Revocare un'autorizzazione

Per revocare un'autorizzazione da un destinatario, eliminare l'assegnazione di ruolo effettuando una richiesta di eliminazione HTTP autenticata:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments/YOUR_ROLE_ASSIGNMENT_ID
```

| Parametro | Sostituire con |
| --- | --- |
| *YOUR_ROLE_ASSIGNMENT_ID* | **ID** dell'assegnazione di ruolo da rimuovere |

Una richiesta DELETE riuscita restituirà uno stato di risposta 204. Verificare la rimozione dell'assegnazione di ruolo [controllando](#check-a-specific-role-assignment) se è ancora presente.

### <a name="create-a-role-assignment"></a>Creare un'assegnazione di ruolo

Per creare un'assegnazione di ruolo, inviare una richiesta HTTP POST autenticata all'URL:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments
```

Verificare che il corpo JSON sia conforme allo schema seguente:

```JSON
{
  "roleId": "YOUR_ROLE_ID",
  "objectId": "YOUR_OBJECT_ID",
  "objectIdType": "YOUR_OBJECT_ID_TYPE",
  "path": "YOUR_PATH",
  "tenantId": "YOUR_TENANT_ID"
}
```

Una richiesta riuscita restituirà uno stato di risposta 201 insieme all'**ID** dell'assegnazione di ruolo appena creata:

```JSON
"d92c7823-6e65-41d4-aaaa-f5b32e3f01b9"
```

## <a name="configuration-examples"></a>Esempi di configurazione

Gli esempi seguenti illustrano come configurare il corpo JSON in diversi scenari comuni di assegnazione di ruoli.

* **Esempio**: Un utente richiede l'accesso amministrativo a un piano di uno spazio tenant.

   ```JSON
   {
    "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
    "objectId" : " 0fc863aa-eb51-4704-a312-7d635d70e000",
    "objectIdType" : "UserId",
    "tenantId": " a0c20ae6-e830-4c60-993d-a00ce6032724",
    "path": "/ 000e349c-c0ea-43d4-93cf-6b00abd23a44/ d84e82e6-84d5-45a4-bd9d-006a000e3bab"
   }
   ```

* **Esempio**: Un'applicazione esegue scenari di test che simulano dispositivi e sensori.

   ```JSON
   {
    "roleId": "98e44ad7-28d4-0007-853b-b9968ad132d1",
    "objectId" : "cabf7aaa-af0b-41c5-000a-ce2f4c20000b",
    "objectIdType" : "ServicePrincipalId",
    "tenantId": " a0c20ae6-e000-4c60-993d-a91ce6000724",
    "path": "/"
   }
    ```

* **Esempio**: Tutti gli utenti che fanno parte di un dominio riceveranno l'accesso in lettura per gli spazi, i sensori e gli utenti, inclusi gli oggetti corrispondenti correlati.

   ```JSON
   {
    "roleId": " b1ffdb77-c635-4e7e-ad25-948237d85b30",
    "objectId" : "@microsoft.com",
    "objectIdType" : "DomainName",
    "path": "/000e349c-c0ea-43d4-93cf-6b00abd23a00"
   }
   ```

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sul controllo degli accessi in base al ruolo di Gemelli digitali di Azure, vedere [Controllo degli accessi in base al ruolo](./security-authenticating-apis.md).

- Per altre informazioni sull'autenticazione delle API di Gemelli digitali di Azure, vedere [Connettersi alle API ed eseguire l'autenticazione](./security-authenticating-apis.md).
