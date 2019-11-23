---
title: Using Azure Blockchain Workbench REST APIs
description: Scenarios for how to use the Azure Blockchain Workbench Preview REST API
ms.date: 10/14/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 188bbb9a9f6d289a7950ff74596352dff36e79f2
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74324194"
---
# <a name="using-the-azure-blockchain-workbench-preview-rest-api"></a>Using the Azure Blockchain Workbench Preview REST API

Azure Blockchain Workbench Preview REST API provides developers and information workers a way to build rich integrations to blockchain applications. Questo documento illustra in modo dettagliato diversi metodi importanti dell'API REST di Workbench. Si supponga, ad esempio, che uno sviluppatore voglia creare un client di blockchain personalizzato. Questo client di blockchain consente agli utenti che eseguono l'accesso di visualizzare e interagire con le applicazioni blockchain assegnate. Il client consente agli utenti di visualizzare le istanze di contratto e di eseguire delle azioni sui contratti smart. Il client usa l'API REST di Workbench nel contesto dell'utente connesso per eseguire le azioni seguenti:

* Elenco applicazioni
* Elencare i flussi di lavoro per un'applicazione
* Elencare le istanze del contratto smart per un flusso di lavoro
* Elencare le azioni disponibili per un contratto
* Eseguire un'azione per un contratto

Le applicazioni blockchain di esempio usate negli scenari sono disponibili per il [download in GitHub](https://github.com/Azure-Samples/blockchain).

## <a name="blockchain-workbench-api-endpoint"></a>Blockchain Workbench API endpoint

Blockchain Workbench APIs are accessed through an endpoint for your deployment. To get the API endpoint URL for your deployment:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. In the left-hand navigation pane, select **Resource groups**.
1. Choose the resource group name your deployed Blockchain Workbench.
1. Selezionare l'intestazione di colonna **TIPO** per ordinare l'elenco in ordine alfabetico in base al tipo.
1. Sono presenti due risorse di tipo **Servizio app**. Select the resource of type **App Service** *with* the "-api" suffix.
1. In the App Service **Overview**, copy the **URL** value, which represents the API endpoint URL to your deployed Blockchain Workbench.

    ![App service API endpoint URL](media/use-api/app-service-api.png)

## <a name="list-applications"></a>Elenco applicazioni

Dopo che l'utente ha eseguito l'accesso al client della blockchain, la prima attività consiste nel recuperare tutte le applicazioni Blockchain Workbench per l'utente. In questo scenario l'utente ha accesso a due applicazioni:

1. [Trasferimento cespite](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer/readme.md)
2. [Trasporto refrigerato](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/refrigerated-transportation/readme.md)

Usare l'[API GET delle applicazioni](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/applicationsget):

``` http
GET /api/v1/applications
Authorization : Bearer {access token}
```

La risposta elenca tutte le applicazioni della blockchain a cui un utente ha accesso in Blockchain Workbench. Gli amministratori di Blockchain Workbench ottengono tutte le applicazioni blockchain. Gli amministratori non di Workbench ottengono tutte le blockchain per le quali hanno almeno un ruolo applicazione associato o un ruolo di istanza di contratto intelligente associato.

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

Dopo che l'utente ha selezionato l'applicazione blockchain applicabile, ad esempio **Trasferimento cespite**, il client della blockchain recupera tutti i flussi di lavoro dell'applicazione blockchain specifica. Gli utenti possono quindi selezionare il flusso di lavoro applicabile prima che vengano visualizzate tutte le istanze del contratto smart per il flusso di lavoro. Ogni applicazione della blockchain dispone di uno o più flussi di lavoro e ciascun flusso di lavoro non dispone di istanze oppure dispone delle istanze del contratto smart. Per un'applicazione client della blockchain con un solo flusso di lavoro, è consigliabile ignorare il flusso dell'esperienza utente che consente agli utenti di selezionare il flusso di lavoro appropriato. In questo caso **Trasferimento cespiti** ha un solo flusso di lavoro, denominato anch'esso **Trasferimento cespite**.

Usare l'[API GET del flusso di lavoro delle applicazioni](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/workflowsget):

``` http
GET /api/v1/applications/{applicationId}/workflows
Authorization: Bearer {access token}
```

La risposta elenca tutti i flussi di lavoro dell'applicazione specifica della blockchain a cui un utente ha accesso in Blockchain Workbench. Gli amministratori di Blockchain Workbench ottengono tutti i flussi di lavoro della blockchain. Gli amministratori non di Workbench ottengono tutti i flussi di lavoro per i quali hanno almeno un ruolo applicazione associato o associati a un ruolo di istanza di contratto intelligente.

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

Dopo che un utente ha selezionato il flusso di lavoro applicabile, in questo caso **Trasferimenti cespite**, il client della blockchain recupererà tutte le istanze del contratto smart per il flusso di lavoro specificato. È possibile usare queste informazioni per visualizzare tutte le istanze di contratto intelligente per il flusso di lavoro. Oppure è possibile consentire agli utenti di esplorare una delle istanze di contratto intelligente visualizzate. In questo esempio si supponga che un utente voglia interagire con una delle istanze del contratto smart per intervenire.

Usare l'[API GET dei contratti](/rest/api/azure-blockchain-workbench/contractsv2/contractsget):

``` http
GET api/v1/contracts?workflowId={workflowId}
Authorization: Bearer {access token}
```

La risposta elenca tutte le istanze del contratto smart del flusso di lavoro specificato. Gli amministratori di Workbench ottengono tutte le istanze di contratto intelligente. Gli amministratori non di Workbench ottengono tutte le istanze di contratto intelligente per le quali hanno almeno un ruolo applicazione associato o associate a un ruolo di istanza di contratto intelligente.

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

Quando un utente decide di esplorare un contratto, il client della blockchain può quindi visualizzare le azioni utente disponibili considerato lo stato del contratto. In questo esempio l'utente cerca tutte le azioni disponibili per il nuovo contratto smart creato:

* Modify: consente all'utente di modificare la descrizione e il prezzo di una risorsa.
* Terminate: consente all'utente di terminare il contratto della risorsa.

Usare l'[API GET dell'azione del contratto](/rest/api/azure-blockchain-workbench/contractsv2/contractactionget):

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

Un utente può quindi decidere di eseguire un'azione per l'istanza del contratto smart specificato. In questo caso si consideri lo scenario in cui un utente desidera modificare la descrizione e il prezzo di una risorsa nell'azione seguente:

* Descrizione: "My updated car"
* Prezzo: 54321

Usare l'[API POST dell'azione del contratto](/rest/api/azure-blockchain-workbench/contractsv2/contractactionpost):

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