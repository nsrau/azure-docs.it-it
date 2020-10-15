---
title: Uso delle API REST di Azure blockchain Workbench
description: Scenari per l'uso dell'API REST di Azure blockchain Workbench Preview
ms.date: 03/05/2020
ms.topic: how-to
ms.reviewer: brendal
ms.openlocfilehash: 696f1f2f96034f7a044f6a39182774c02804518f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87003067"
---
# <a name="using-the-azure-blockchain-workbench-preview-rest-api"></a>Uso dell'API REST di Azure blockchain Workbench Preview

L'API REST di Azure blockchain Workbench Preview fornisce a sviluppatori e Information Worker un modo per creare integrazioni avanzate per le applicazioni blockchain. Questo articolo illustra diversi scenari di utilizzo dell'API REST di Workbench. Si supponga, ad esempio, di voler creare un client blockchain personalizzato che consenta agli utenti che hanno eseguito l'accesso di visualizzare e interagire con le applicazioni blockchain assegnate. Il client può usare l'API di blockchain Workbench per visualizzare le istanze di contratto e intraprendere azioni su contratti intelligenti.

## <a name="blockchain-workbench-api-endpoint"></a>Endpoint API blockchain Workbench

È possibile accedere alle API di blockchain Workbench tramite un endpoint per la distribuzione. Per ottenere l'URL dell'endpoint API per la distribuzione:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Nel riquadro di spostamento a sinistra selezionare gruppi di **risorse**.
1. Scegliere il nome del gruppo di risorse blockchain Workbench distribuito.
1. Selezionare l'intestazione di colonna **TIPO** per ordinare l'elenco in ordine alfabetico in base al tipo.
1. Sono presenti due risorse di tipo **Servizio app**. Selezionare la risorsa di tipo **servizio app** *con* il suffisso "-API".
1. Nella **Panoramica**del servizio app, copiare il valore dell' **URL** , che rappresenta l'URL dell'endpoint API per il Workbench blockchain distribuito.

    ![URL dell'endpoint dell'API del servizio app](media/use-api/app-service-api.png)

## <a name="authentication"></a>Authentication

Le richieste all'API REST di blockchain Workbench sono protette con Azure Active Directory (Azure AD).

Per effettuare una richiesta autenticata alle API REST, il codice client richiede l'autenticazione con credenziali valide prima di poter chiamare l'API. L'autenticazione è coordinata tra i vari attori per Azure AD e fornisce al client un [token di accesso](../../active-directory/develop/developer-glossary.md#access-token) come prova dell'autenticazione. Il token viene quindi inviato nell'intestazione di autorizzazione HTTP delle richieste dell'API REST. Per ulteriori informazioni sull'autenticazione Azure AD, vedere [Azure Active Directory per gli sviluppatori](../../active-directory/develop/index.yml).

Per esempi su come eseguire l'autenticazione, vedere [esempi di API REST](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/rest-api-samples) .

## <a name="using-postman"></a>Uso di Postman

Se si vuole testare o sperimentare le API di Workbench, è possibile usare il [post](https://www.postman.com) per effettuare chiamate all'API per la distribuzione. [Scaricare una raccolta di post di esempio di richieste API Workbench](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/rest-api-samples/postman) da GitHub. Per informazioni dettagliate sull'autenticazione e sull'uso delle richieste API di esempio, vedere il file Leggimi.

## <a name="create-an-application"></a>Creare un'applicazione

Per creare un'applicazione blockchain Workbench si usano due chiamate API. Questo metodo può essere eseguito solo dagli utenti che sono amministratori di Workbench.

Usare l' [API post delle applicazioni](/rest/api/azure-blockchain-workbench/applications/applicationspost) per caricare il file JSON dell'applicazione e ottenere un ID applicazione.

### <a name="applications-post-request"></a>Richiesta POST applicazioni

Usare il parametro **appFile** per inviare il file di configurazione come parte del corpo della richiesta.

``` http
POST /api/v1/applications
Content-Type: multipart/form-data;
Authorization : Bearer {access token}
Content-Disposition: form-data; name="appFile"; filename="/C:/smart-contract-samples/HelloWorld.json"
Content-Type: application/json
```

### <a name="applications-post-response"></a>Risposta POST delle applicazioni

L'ID applicazione creato viene restituito nella risposta. È necessario l'ID applicazione per associare il file di configurazione al file di codice quando si chiama l'API successiva.

``` http
HTTP/1.1 200 OK
Content-Type: "application/json"
1
```

### <a name="contract-code-post-request"></a>Richiesta POST del codice del contratto

Usare l' [API post di codice del contratto](/rest/api/azure-blockchain-workbench/applications/contractcodepost) per le applicazioni passando l'ID applicazione per caricare il file di codice di solidità dell'applicazione. Il payload può essere un singolo file di solidità o un file compresso contenente file di solidità.

Sostituire i valori seguenti:

| Parametro | Valore |
|-----------|-------|
| ApplicationId | Valore restituito dall'API POST delle applicazioni. |
| {ledgerId} | Indice del Ledger. Il valore è in genere 1. È anche possibile controllare la [tabella Ledger](data-sql-management-studio.md) per il valore. |

``` http
POST /api/v1/applications/{applicationId}/contractCode?ledgerId={ledgerId}
Content-Type: multipart/form-data;
Authorization : Bearer {access token}
Content-Disposition: form-data; name="contractFile"; filename="/C:/smart-contract-samples/HelloWorld.sol"
```

### <a name="contract-code-post-response"></a>Risposta POST del codice del contratto

Se l'esito è positivo, la risposta include l'ID del codice del contratto creato dalla [tabella ContractCode](data-sql-management-studio.md).

``` http
HTTP/1.1 200 OK
Content-Type: "application/json"
2
```

## <a name="assign-roles-to-users"></a>Assegnare ruoli agli utenti

Usare le [assegnazioni di ruolo applicazione Post API](/rest/api/azure-blockchain-workbench/applications/roleassignmentspost) passando l'ID applicazione, l'ID utente e l'ID del ruolo applicazione per creare un mapping da utente a ruolo nell'applicazione blockchain specificata. Questo metodo può essere eseguito solo dagli utenti che sono amministratori di Workbench.

### <a name="role-assignments-post-request"></a>Richiesta POST per assegnazioni di ruolo

Sostituire i valori seguenti:

| Parametro | Valore |
|-----------|-------|
| ApplicationId | Valore restituito dall'API POST delle applicazioni. |
| UserID | Valore dell'ID utente della [tabella utente](data-sql-management-studio.md). |
| {applicationRoleId} | Valore ID del ruolo applicazione associato all'ID applicazione dalla [tabella](data-sql-management-studio.md)in base al ruolo. |

``` http
POST /api/v1/applications/{applicationId}/roleAssignments
Content-Type: application/json;
Authorization : Bearer {access token}

{
  "userId": {userId},
  "applicationRoleId": {applicationRoleId}
}
```

### <a name="role-assignments-post-response"></a>POST-risposta assegnazioni di ruolo

In caso di esito positivo, la risposta include l'ID assegnazione ruolo creato dalla [tabella RoleAssignment](data-sql-management-studio.md).

``` http
HTTP/1.1 200
1
```

## <a name="list-applications"></a>Elenco applicazioni

Usare l' [API Get delle applicazioni](/rest/api/azure-blockchain-workbench/applications/applicationsget) per recuperare tutte le applicazioni blockchain Workbench per l'utente. In questo esempio, l'utente connesso può accedere a due applicazioni:

- [Trasferimento cespite](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer/readme.md)
- [Trasporto refrigerato](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/refrigerated-transportation/readme.md)

### <a name="applications-get-request"></a>Richieste GET delle applicazioni

``` http
GET /api/v1/applications
Authorization : Bearer {access token}
```

### <a name="applications-get-response"></a>Risposta GET delle applicazioni

La risposta elenca tutte le applicazioni della blockchain a cui un utente ha accesso in Blockchain Workbench. Gli amministratori di Blockchain Workbench ottengono tutte le applicazioni blockchain. Gli amministratori non Workbench ottengono tutte le applicazioni blockchain per le quali hanno almeno un ruolo applicazione associato o un ruolo di istanza di Smart Contract associato.

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

Usare i [flussi di lavoro delle applicazioni Get API](/rest/api/azure-blockchain-workbench/applications/workflowsget) per elencare tutti i flussi di lavoro di un'applicazione blockchain specificata a cui un utente può accedere in blockchain Workbench. Ogni applicazione della blockchain dispone di uno o più flussi di lavoro e ciascun flusso di lavoro non dispone di istanze oppure dispone delle istanze del contratto smart. Per un'applicazione client della blockchain con un solo flusso di lavoro, è consigliabile ignorare il flusso dell'esperienza utente che consente agli utenti di selezionare il flusso di lavoro appropriato.

### <a name="application-workflows-request"></a>Richiesta flussi di lavoro applicazione

``` http
GET /api/v1/applications/{applicationId}/workflows
Authorization: Bearer {access token}
```

### <a name="application-workflows-response"></a>Risposta flussi di lavoro applicazione

Gli amministratori di Blockchain Workbench ottengono tutti i flussi di lavoro della blockchain. Gli amministratori non di Workbench ottengono tutti i flussi di lavoro per i quali hanno almeno un ruolo applicazione associato o associati a un ruolo di istanza di contratto intelligente.

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

## <a name="create-a-contract-instance"></a>Creare un'istanza di contratto

Usare i [contratti V2 post API](/rest/api/azure-blockchain-workbench/contractsv2/contractpost) per creare una nuova istanza di Smart Contract per un flusso di lavoro. Gli utenti sono in grado di creare una nuova istanza di Smart Contract solo se l'utente è associato a un ruolo applicazione, che può avviare un'istanza di Smart Contract per il flusso di lavoro.

> [!NOTE]
> In questo esempio viene usata la versione 2 dell'API. Le API dei contratti della versione 2 forniscono una maggiore granularità per i campi ProvisioningStatus associati.

### <a name="contracts-post-request"></a>Richiesta POST contratti

Sostituire i valori seguenti:

| Parametro | Valore |
|-----------|-------|
| WorkflowId | Il valore ID del flusso di lavoro è il ConstructorID del contratto dalla [tabella del flusso di lavoro](data-sql-management-studio.md). |
| {contractCodeId} | Valore ID del codice del contratto dalla [tabella ContractCode](data-sql-management-studio.md). Correlare l'ID applicazione e l'ID Ledger per l'istanza del contratto che si vuole creare. |
| ConnectionId | Valore dell'ID connessione dalla [tabella di connessione](data-sql-management-studio.md). |

Per il corpo della richiesta, impostare i valori usando le seguenti informazioni:

| Parametro | Valore |
|-----------|-------|
| workflowFunctionID | ID della [tabella WorkflowFunction](data-sql-management-studio.md). |
| workflowActionParameters | Coppie nome-valore dei parametri passati al costruttore. Per ogni parametro, usare il valore workflowFunctionParameterID dalla tabella [WorkflowFunctionParameter](data-sql-management-studio.md) . |

``` http
POST /api/v2/contracts?workflowId={workflowId}&contractCodeId={contractCodeId}&connectionId={connectionId}
Content-Type: application/json;
Authorization : Bearer {access token}

{
  "workflowFunctionID": 2,
  "workflowActionParameters": [
    {
      "name": "message",
      "value": "Hello, world!",
      "workflowFunctionParameterId": 3
    }
  ]
}
```

### <a name="contracts-post-response"></a>Contratti POST-risposta

In caso di esito positivo, l'API assegnazioni di ruolo restituisce ContractActionID dalla [tabella ContractActionParameter](data-sql-management-studio.md).

``` http
HTTP/1.1 200 OK
4
```

## <a name="list-smart-contract-instances-for-a-workflow"></a>Elencare le istanze del contratto smart per un flusso di lavoro

Usare i [contratti Get API](/rest/api/azure-blockchain-workbench/contractsv2/contractsget) per visualizzare tutte le istanze di Smart Contract per un flusso di lavoro. Oppure è possibile consentire agli utenti di esplorare una delle istanze di contratto intelligente visualizzate.

### <a name="contracts-request"></a>Richiesta di contratti

In questo esempio si supponga che un utente voglia interagire con una delle istanze del contratto smart per intervenire.

``` http
GET api/v1/contracts?workflowId={workflowId}
Authorization: Bearer {access token}
```

### <a name="contracts-response"></a>Risposta ai contratti

La risposta elenca tutte le istanze di Smart Contract del flusso di lavoro specificato. Gli amministratori di Workbench ottengono tutte le istanze di contratto intelligente. Gli amministratori non di Workbench ottengono tutte le istanze di contratto intelligente per le quali hanno almeno un ruolo applicazione associato o associate a un ruolo di istanza di contratto intelligente.

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

Usare l' [azione del contratto Get API](/rest/api/azure-blockchain-workbench/contractsv2/contractactionget) per visualizzare le azioni utente disponibili in base allo stato del contratto. 

### <a name="contract-action-request"></a>Richiesta di azione del contratto

In questo esempio, l'utente sta esaminando tutte le azioni disponibili per un nuovo contratto intelligente creato.

``` http
GET /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
```

### <a name="contract-action-response"></a>Risposta all'azione del contratto

La risposta elenca tutte le azioni che un utente può eseguire considerato lo stato corrente dell'istanza del contratto smart specificato.

* Modify: consente all'utente di modificare la descrizione e il prezzo di una risorsa.
* Terminate: consente all'utente di terminare il contratto della risorsa.

Gli utenti ottengono tutte le azioni applicabili se l'utente dispone di un ruolo applicazione associata o è associato a un ruolo dell'istanza del contratto smart per lo stato corrente dell'istanza del contratto smart specificato.

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

Usare l' [API post dell'azione del contratto](/rest/api/azure-blockchain-workbench/contractsv2/contractactionpost) per eseguire un'azione per l'istanza di Smart Contract specificata.

### <a name="contract-action-post-request"></a>Richiesta POST azione contratto

In questo caso, si consideri lo scenario in cui un utente desidera modificare la descrizione e il prezzo di un asset.

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

Gli utenti sono in grado di eseguire solo l'azione considerato lo stato corrente dell'istanza del contratto smart specificato e del ruolo applicazione associato dell'utente o del ruolo dell'istanza del contratto smart.

### <a name="contract-action-post-response"></a>Risposta POST azione contratto

Se il comando POST ha esito positivo, viene restituita una risposta HTTP 200 OK senza corpo della risposta.

``` http
HTTP/1.1 200 OK
Content-type: application/json
```

## <a name="next-steps"></a>Passaggi successivi

Per informazioni di riferimento sulle API di blockchain Workbench, vedere le informazioni di [riferimento sull'API REST di Azure blockchain Workbench](/rest/api/azure-blockchain-workbench).
