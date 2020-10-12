---
title: Sviluppare per Azure NetApp Files con API REST | Microsoft Docs
description: L'API REST per il servizio Azure NetApp Files definisce le operazioni HTTP per le risorse, ad esempio l'account NetApp, il pool di capacità, i volumi e gli snapshot.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/02/2020
ms.author: b-juche
ms.openlocfilehash: 90d3ff807275c74ce4495c5631ab913971ebe3e3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87513010"
---
# <a name="develop-for-azure-netapp-files-with-rest-api"></a>Sviluppare per Azure NetApp Files con API REST 

L'API REST per il servizio Azure NetApp Files definisce le operazioni HTTP sulle risorse, ad esempio l'account di NetApp, il pool di capacità, i volumi e gli snapshot. Questo articolo dimostra come iniziare a usare l'API REST di Azure NetApp Files.

## <a name="azure-netapp-files-rest-api-specification"></a>Specifica API REST di Azure NetApp Files

La specifica dell'API REST per Azure NetApp Files viene pubblicata tramite [GitHub](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager):

`https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager`


## <a name="access-the-azure-netapp-files-rest-api"></a>Accedere all'API REST di Azure NetApp Files  

1. [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) se non lo si è già fatto.
2. Creare un'entità servizio in Azure Active Directory (Azure AD):
   1. Verificare di avere [autorizzazioni sufficienti](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions).

   2. Immettere il comando seguente nell'interfaccia della riga di comando di Azure: 
    
        ```azurecli
        az ad sp create-for-rbac --name $YOURSPNAMEGOESHERE
        ```

      L'output del comando è simile a quello dell'esempio seguente:  

        ```output
        { 
            "appId": "appIDgoeshere", 
            "displayName": "APPNAME", 
            "name": "http://APPNAME", 
            "password": "supersecretpassword", 
            "tenant": "tenantIDgoeshere" 
        } 
        ```

      Mantenere l'output del comando.  Sono necessari i valori `appId`, `password`, e `tenant`. 

3. Richiedere un token di accesso OAuth:

    Gli esempi in questo articolo usano cURL. È anche possibile usare vari strumenti API, ad esempio [Postman](https://www.getpostman.com/), [Insomnia](https://insomnia.rest/) e [Paw](https://paw.cloud/).  

    Sostituire le variabili nell'esempio seguente con l'output del comando dal passaggio 2 precedente. 
    
    ```azurecli
    curl -X POST -d 'grant_type=client_credentials&client_id=[APP_ID]&client_secret=[PASSWORD]&resource=https%3A%2F%2Fmanagement.azure.com%2F' https://login.microsoftonline.com/[TENANT_ID]/oauth2/token
    ```

    L'output fornisce un token di accesso simile all'esempio seguente:

    `eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSIsImtpZCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSJ9`

    Il token visualizzato è valido per 3600 secondi. Successivamente, è necessario richiedere un nuovo token. 
    Salvare il token in un editor di testo.  Sarà necessario nel passaggio successivo.

4. Inviare un test call e includere il token per convalidare l'accesso all'API REST:

    ```azurecli
    curl -X GET -H "Authorization: Bearer [TOKEN]" -H "Content-Type: application/json" https://management.azure.com/subscriptions/[SUBSCRIPTION_ID]/providers/Microsoft.Web/sites?api-version=2019-11-01
    ```

## <a name="examples-using-the-api"></a>Esempi di utilizzo dell'API  

Questo articolo usa l'URL seguente per la linea di base delle richieste. Questo URL punta alla radice dello spazio dei nomi di Azure NetApp Files. 

`https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts?api-version=2019-11-01`

È necessario sostituire i valori `SUBIDGOESHERE` e `RESOURCEGROUPGOESHERE` nei seguenti esempi con i valori desiderati. 

### <a name="get-request-examples"></a>Esempi di richiesta GET

Usare una richiesta GET per eseguire query sugli oggetti Azure NetApp Files in una sottoscrizione, come negli esempi seguenti: 

```azurecli
#get NetApp accounts 
curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts?api-version=2019-11-01
```

```azurecli
#get capacity pools for NetApp account 
curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools?api-version=2019-11-01
```

```azurecli
#get volumes in NetApp account & capacity pool 
curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes?api-version=2019-11-01
```

```azurecli
#get snapshots for a volume 
curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/VOLUMEGOESHERE/snapshots?api-version=2019-11-01
```

### <a name="put-request-examples"></a>Esempi di richiesta PUT

Utilizzare una richiesta PUT per creare nuovi oggetti Azure NetApp Files, come mostrato negli esempi seguenti. Il corpo della richiesta PUT può includere i dati in formato JSON per le modifiche. Deve essere incluso nel comando curl come testo o come riferimento come file. Per fare riferimento al corpo come file, salvare l'esempio JSON in un file e aggiungerlo `-d @<filename>` al comando curl.

```azurecli
#create a NetApp account  
curl -d @<filename> -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE?api-version=2019-11-01
```

```azurecli
#create a capacity pool  
curl -d @<filename> -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE?api-version=2019-11-01
```

```azurecli
#create a volume  
curl -d @<filename> -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/MYNEWVOLUME?api-version=2019-11-01
```

```azurecli
 #create a volume snapshot  
curl -d @<filename> -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/MYNEWVOLUME/Snapshots/SNAPNAME?api-version=2019-11-01
```

### <a name="json-examples"></a>Esempi JSON

L'esempio seguente mostra come creare un account NetApp:

```json
{ 
    "name": "MYNETAPPACCOUNT", 
    "type": "Microsoft.NetApp/netAppAccounts", 
    "location": "westus2", 
    "properties": { 
        "name": "MYNETAPPACCOUNT" 
    }
} 
```

L'esempio seguente mostra come creare un pool di capacità: 

```json
{
    "name": "MYNETAPPACCOUNT/POOLNAME",
    "type": "Microsoft.NetApp/netAppAccounts/capacityPools",
    "location": "westus2",
    "properties": {
        "name": "POOLNAME",
        "size": "4398046511104",
        "serviceLevel": "Premium"
    }
}
```

Nell'esempio seguente viene illustrato come creare un nuovo volume. Il protocollo predefinito per il volume è NFSV3. 

```json
{
    "name": "MYNEWVOLUME",
    "type": "Microsoft.NetApp/netAppAccounts/capacityPools/volumes",
    "location": "westus2",
    "properties": {
        "serviceLevel": "Premium",
        "usageThreshold": "322122547200",
        "creationToken": "MY-FILEPATH",
        "snapshotId": "",
        "subnetId": "/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.Network/virtualNetworks/VNETGOESHERE/subnets/MYDELEGATEDSUBNET.sn"
         }
}
```

L'esempio seguente mostra come creare uno snapshot di un volume: 

```json
{
    "name": "apitest2/apiPool01/apiVol01/snap02",
    "type": "Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/Snapshots",
    "location": "westus2",
    "properties": {
         "name": "snap02",
        "fileSystemId": "0168704a-bbec-da81-2c29-503825fe7420"
    }
}
```

> [!NOTE] 
> È necessario specificare `fileSystemId` per creare uno snapshot.  È possibile ottenere il valore `fileSystemId` con una richiesta GET di un volume. 

## <a name="next-steps"></a>Passaggi successivi

[Vedere il riferimento all'API REST di Azure NetApp Files](https://docs.microsoft.com/rest/api/netapp/)
