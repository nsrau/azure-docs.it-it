---
title: Usare l'API REST di Azure Blockchain Workbench
description: Scenari su come usare l'API REST di Azure Blockchain Workbench
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/1/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 2e9124213181fe32f3492e353b05ace89a9d6992
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/03/2018
ms.locfileid: "48241846"
---
# <a name="using-the-azure-blockchain-workbench-rest-api"></a>Uso dell'API REST di Azure Blockchain Workbench 

L'API REST di Azure Blockchain Workbench offre agli sviluppatori e agli information worker un modo per compilare le integrazioni avanzate per le applicazioni della blockchain. Questo documento illustra in modo dettagliato diversi metodi importanti dell'API REST di Workbench. Si consideri uno scenario in cui uno sviluppatore desidera creare un client della blockchain personalizzato, che consente agli utenti che hanno eseguito l'accesso di visualizzare e interagire con le applicazioni della blockchain assegnate a loro. Il client consente agli utenti di visualizzare le istanze di contratto e di eseguire delle azioni sui contratti smart. Il client usa l'API REST di Workbench nel contesto dell'utente connesso per effettuare le operazioni seguenti:

* Elenco applicazioni
* Elencare i flussi di lavoro per un'applicazione
* Elencare le istanze del contratto smart per un flusso di lavoro
* Elencare le azioni disponibili per un contratto
* Eseguire un'azione per un contratto

Le applicazioni blockchain di esempio usate negli scenari sono disponibili per il [download in GitHub](https://github.com/Azure-Samples/blockchain). 

## <a name="list-applications"></a>Elenco applicazioni

Dopo che l'utente ha eseguito l'accesso al client della blockchain, la prima attività consiste nel recuperare tutte le applicazioni Blockchain Workbench per l'utente. In questo scenario l'utente ha accesso a due applicazioni:

1.  [Trasferimento cespite](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer/readme.md)
2.  [Trasporto refrigerato](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/refrigerated-transportation/readme.md)

Usare l'[API GET delle applicazioni](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/applicationsget):

``` http
GET /api/v1/applications 
Authorization : Bearer {access token}
```

La risposta elenca tutte le applicazioni della blockchain a cui un utente ha accesso in Blockchain Workbench. Gli amministratori di Blockchain Workbench ottengono tutte le applicazioni della blockchain, mentre gli altri amministratori ottengono tutte le blockchain per cui dispongono di almeno un ruolo applicazione associato o di un ruolo dell'istanza del contratto smart associato.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/applications?skip=2",
    "applications": [
        {
            "id": 1,
            "name": "AssetTransfer",
            "description": "Allows transfer of assets between a buyer and a seller, with appraisal/inspection functionality",
            "displayName": "Asset Transfer",
            "createdByUserId": 1,
            "createdDtTm": "2018-04-28T05:59:14.4733333",
            "enabled": true,
            "applicationRoles": null
        },
        {
            "id": 2,
            "name": "RefrigeratedTransportation",
            "description": "Application to track end-to-end transportation of perishable goods.",
            "displayName": "Refrigerated Transportation",
            "createdByUserId": 7,
            "createdDtTm": "2018-04-28T18:25:38.71",
            "enabled": true,
            "applicationRoles": null
        }
    ]
}
```

## <a name="list-workflows-for-an-application"></a>Elencare i flussi di lavoro per un'applicazione

Dopo che l'utente ha selezionato l'applicazione della blockchain applicabile, in questo caso **Trasferimento cespite**, il client della blockchain recupera tutti i flussi di lavoro dell'applicazione specifica della blockchain. Gli utenti possono quindi selezionare il flusso di lavoro applicabile prima che vengano visualizzate tutte le istanze del contratto smart per il flusso di lavoro. Ogni applicazione della blockchain dispone di uno o più flussi di lavoro e ciascun flusso di lavoro non dispone di istanze oppure dispone delle istanze del contratto smart. Quando si compilano le applicazioni client della blockchain, è consigliabile ignorare il flusso dell'esperienza dell'utente consentendo agli utenti di selezionare il flusso di lavoro appropriato quando è presente un solo flusso di lavoro per l'applicazione della blockchain. In questo caso **Trasferimento cespiti** ha un solo flusso di lavoro, denominato anch'esso **Trasferimento cespite**.

Usare l'[API GET del flusso di lavoro delle applicazioni](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/workflowsget):

``` http
GET /api/v1/applications/{applicationId}/workflows
Authorization: Bearer {access token}
```

La risposta elenca tutti i flussi di lavoro dell'applicazione specifica della blockchain a cui un utente ha accesso in Blockchain Workbench. Gli amministratori di Blockchain Workbench ottengono tutti i flussi di lavoro della blockchain, mentre gli altri amministratori ottengono tutti i flussi di lavoro per cui dispongono di almeno un ruolo applicazione associato o che sono associati con un ruolo dell'istanza del contratto smart.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/applications/1/workflows?skip=1",
    "workflows": [
        {
            "id": 1,
            "name": "AssetTransfer",
            "description": "Handles the business logic for the asset transfer scenario",
            "displayName": "Asset Transfer",
            "applicationId": 1,
            "constructorId": 1,
            "startStateId": 1
        }
    ]
}
```

## <a name="list-smart-contract-instances-for-a-workflow"></a>Elencare le istanze del contratto smart per un flusso di lavoro

Dopo che un utente ha selezionato il flusso di lavoro applicabile, in questo caso **Trasferimenti cespite**, il client della blockchain recupererà tutte le istanze del contratto smart per il flusso di lavoro specificato. È possibile usare queste informazioni per visualizzare tutte le istanze del contratto smart per il flusso di lavoro e consentire agli utenti di approfondire una qualsiasi delle istanze del contratto smart visualizzato. In questo esempio si supponga che un utente voglia interagire con una delle istanze del contratto smart per intervenire.

Usare l'[API GET dei contratti](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/contracts/contractsget):

``` http
GET api/v1/contracts?workflowId={workflowId}
Authorization: Bearer {access token}
```

La risposta elenca tutte le istanze del contratto smart del flusso di lavoro specificato. Gli amministratori di Workbench ottengono tutte le istanze del contratto smart, mentre gli altri amministratori ottengono tutte le istanze del contratto smart per cui dispongono di almeno un ruolo applicazione associato o che sono associate a un ruolo dell'istanza del contratto smart.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/contracts?skip=3&workflowId=1",
    "contracts": [
        {
            "id": 1,
            "provisioningStatus": 2,
            "connectionID": 1,
            "ledgerIdentifier": "0xbcb6127be062acd37818af290c0e43479a153a1c",
            "deployedByUserId": 1,
            "workflowId": 1,
            "contractCodeId": 1,
            "contractProperties": [
                {
                    "workflowPropertyId": 1,
                    "value": "0"
                },
                {
                    "workflowPropertyId": 2,
                    "value": "My first car"
                },
                {
                    "workflowPropertyId": 3,
                    "value": "54321"
                },
                {
                    "workflowPropertyId": 4,
                    "value": "0"
                },
                {
                    "workflowPropertyId": 5,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 6,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 7,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 8,
                    "value": "0xd882530eb3d6395e697508287900c7679dbe02d7"
                }
            ],
            "transactions": [
                {
                    "id": 1,
                    "connectionId": 1,
                    "transactionHash": "0xf3abb829884dc396e03ae9e115a770b230fcf41bb03d39457201449e077080f4",
                    "blockID": 241,
                    "from": "0xd882530eb3d6395e697508287900c7679dbe02d7",
                    "to": null,
                    "value": 0,
                    "isAppBuilderTx": true
                }
            ],
            "contractActions": [
                {
                    "id": 1,
                    "userId": 1,
                    "provisioningStatus": 2,
                    "timestamp": "2018-04-29T23:41:14.9333333",
                    "parameters": [
                        {
                            "name": "Description",
                            "value": "My first car"
                        },
                        {
                            "name": "Price",
                            "value": "54321"
                        }
                    ],
                    "workflowFunctionId": 1,
                    "transactionId": 1,
                    "workflowStateId": 1
                }
            ]
        }
    ]
}
```

## <a name="list-available-actions-for-a-contract"></a>Elencare le azioni disponibili per un contratto

Dopo che un utente ha deciso di approfondire uno dei contratti, il client della blockchain può visualizzare tutte le azioni disponibili per l'utente in base allo stato del contratto. In questo esempio l'utente cerca tutte le azioni disponibili per il nuovo contratto smart creato:

* Modify: consente all'utente di modificare la descrizione e il prezzo di una risorsa.
* Terminate: consente all'utente di terminare il contratto della risorsa.

Usare l'[API GET dell'azione del contratto](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/contracts/contractactionget):

``` http
GET /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
```

La risposta elenca tutte le azioni che un utente può eseguire considerato lo stato corrente dell'istanza del contratto smart specificato. Gli utenti ottengono tutte le azioni applicabili se l'utente dispone di un ruolo applicazione associata o è associato a un ruolo dell'istanza del contratto smart per lo stato corrente dell'istanza del contratto smart specificato.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/contracts/1/actions?skip=2",
    "workflowFunctions": [
        {
            "id": 2,
            "name": "Modify",
            "description": "Modify the description/price attributes of this asset transfer instance",
            "displayName": "Modify",
            "parameters": [
                {
                    "id": 1,
                    "name": "description",
                    "description": "The new description of the asset",
                    "displayName": "Description",
                    "type": {
                        "id": 2,
                        "name": "string",
                        "elementType": null,
                        "elementTypeId": 0
                    }
                },
                {
                    "id": 2,
                    "name": "price",
                    "description": "The new price of the asset",
                    "displayName": "Price",
                    "type": {
                        "id": 3,
                        "name": "money",
                        "elementType": null,
                        "elementTypeId": 0
                    }
                }
            ],
            "workflowId": 1
        },
        {
            "id": 3,
            "name": "Terminate",
            "description": "Used to cancel this particular instance of asset transfer",
            "displayName": "Terminate",
            "parameters": [],
            "workflowId": 1
        }
    ]
}
```

## <a name="execute-an-action-for-a-contract"></a>Eseguire un'azione per un contratto

Un utente può quindi decidere di eseguire un'azione per l'istanza del contratto smart specificato. In questo caso si consideri lo scenario in cui un utente desidera modificare la descrizione e il prezzo di una risorsa nel seguente modo:

* Descrizione: "My updated car"
* Prezzo: 54321

Usare l'[API POST dell'azione del contratto](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/contracts/contractactionpost):

``` http
POST /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
actionInformation: {
    "workflowFunctionId": 2,
    "workflowActionParameters": [
        {
            "name": "description",
            "value": "My updated car"
        },
        {
            "name": "price",
            "value": "54321"
        }
    ]
}
```

Gli utenti sono in grado di eseguire solo l'azione considerato lo stato corrente dell'istanza del contratto smart specificato e del ruolo applicazione associato dell'utente o del ruolo dell'istanza del contratto smart. Se il comando POST ha esito positivo, viene restituita una risposta HTTP 200 OK senza corpo della risposta.

``` http
HTTP/1.1 200 OK
Content-type: application/json
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Riferimenti all'API REST di Azure Blockchain Workbench](https://docs.microsoft.com/rest/api/azure-blockchain-workbench)