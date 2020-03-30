---
title: Uso delle API REST di Azure Blockchain WorkbenchUsing Azure Blockchain Workbench REST APIs
description: Scenari per l'uso dell'API REST di Azure Blockchain Workbench PreviewScenarios for how to use the Azure Blockchain Workbench Preview REST API
ms.date: 03/05/2020
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 3084fcf343bc42fe01bf352b6791916d62f63540
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672741"
---
# <a name="using-the-azure-blockchain-workbench-preview-rest-api"></a>Uso dell'API REST di Azure Blockchain Workbench PreviewUsing the Azure Blockchain Workbench Preview REST API

L'API REST di Azure Blockchain Workbench Preview offre agli sviluppatori e agli information worker un modo per creare integrazioni avanzate per le applicazioni blockchain. Questo articolo evidenzia diversi scenari di utilizzo dell'API REST di Workbench. Si supponga, ad esempio, di voler creare un client blockchain personalizzato che consenta agli utenti connessi di visualizzare e interagire con le applicazioni blockchain assegnate. Il client può utilizzare l'API Blockchain Workbench per visualizzare le istanze del contratto ed eseguire azioni sui contratti intelligenti.

## <a name="blockchain-workbench-api-endpoint"></a>Endpoint API Blockchain Workbench

Le API Blockchain Workbench sono accessibili tramite un endpoint per la distribuzione. Per ottenere l'URL dell'endpoint API per la distribuzione:

1. Accedere al [portale](https://portal.azure.com)di Azure .
1. Nel riquadro di spostamento sinistro selezionare **Gruppi di**risorse .
1. Scegliere il nome del gruppo di risorse distribuito Blockchain Workbench.
1. Selezionare l'intestazione di colonna **TIPO** per ordinare l'elenco in ordine alfabetico in base al tipo.
1. Sono presenti due risorse di tipo **Servizio app**. Selezionare la risorsa di tipo **Servizio app** *con* il suffisso "-api".
1. In **Panoramica**del servizio app copiare il valore **dell'URL** che rappresenta l'URL dell'endpoint API in Blockchain Workbench distribuito.

    ![URL dell'endpoint dell'API del servizio app](media/use-api/app-service-api.png)

## <a name="authentication"></a>Authentication

Le richieste all'API REST Di Blockchain Workbench sono protette con Azure Active Directory (Azure AD).

Per effettuare una richiesta autenticata alle API REST, il codice client richiede l'autenticazione con credenziali valide prima di poter chiamare l'API. L'autenticazione è coordinata tra i vari attori di Azure AD e fornisce al client un token di [accesso](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#access-token) come prova dell'autenticazione. Il token viene quindi inviato nell'intestazione di autorizzazione HTTP delle richieste API REST. Per altre informazioni sull'autenticazione di Azure AD, vedere [Azure Active Directory per sviluppatori.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)

Vedere esempi di [API REST](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/rest-api-samples) per esempi su come eseguire l'autenticazione.

## <a name="using-postman"></a>Uso di Postman

Se si vuole testare o sperimentare con le API Workbench, è possibile usare [Postman](https://www.postman.com) per effettuare chiamate API alla distribuzione. [Scaricare una raccolta Postman di esempio di richieste API Workbench](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/rest-api-samples/postman) da GitHub.Download a sample Postman collection of Workbench API requests from GitHub. Vedere il file README per informazioni dettagliate sull'autenticazione e l'utilizzo delle richieste API di esempio.

## <a name="create-an-application"></a>Creare un'applicazione

Utilizzare due chiamate API per creare un'applicazione Blockchain Workbench.You use two API calls to create a Blockchain Workbench application. Questo metodo può essere eseguito solo da utenti amministratori di Workbench.

Usare [l'API POST delle](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/applicationspost) applicazioni per caricare il file JSON dell'applicazione e ottenere un ID applicazione.

### <a name="applications-post-request"></a>Richiesta POST applicazioni

Usare il parametro **appFile** per inviare il file di configurazione come parte del corpo della richiesta.

``` http
POST /api/v1/applications
Content-Type: multipart/form-data;
Authorization : Bearer {access token}
Content-Disposition: form-data; name="appFile"; filename="/C:/smart-contract-samples/HelloWorld.json"
Content-Type: application/json
```

### <a name="applications-post-response"></a>Risposte POST applicazioni

L'ID applicazione creato viene restituito nella risposta. È necessario l'ID applicazione per associare il file di configurazione al file di codice quando si chiama l'API successiva.

``` http
HTTP/1.1 200 OK
Content-Type: "application/json"
1
```

### <a name="contract-code-post-request"></a>Codice contratto RICHIESTA POST

Utilizzare l'API POST del [codice del contratto Applicazioni](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/contractcodepost) passando l'ID applicazione per caricare il file di codice Solidity dell'applicazione. Il payload può essere un singolo file Solidity o un file compresso contenente file Solidity.

Sostituire i valori seguenti:

| Parametro | valore |
|-----------|-------|
| "IDapplicazione" | Restituisce il valore dall'API POST delle applicazioni. |
| "ledgerId" (LedgerId) | Indice della contabilità generale. Il valore è in genere 1. È inoltre possibile controllare la [tabella Ledger](data-sql-management-studio.md) per il valore. |

``` http
POST /api/v1/applications/{applicationId}/contractCode?ledgerId={ledgerId}
Content-Type: multipart/form-data;
Authorization : Bearer {access token}
Content-Disposition: form-data; name="contractFile"; filename="/C:/smart-contract-samples/HelloWorld.sol"
```

### <a name="contract-code-post-response"></a>Risposta POST del codice del contratto

In caso di esito positivo, la risposta include l'ID codice contratto creato dalla [tabella ContractCode](data-sql-management-studio.md).

``` http
HTTP/1.1 200 OK
Content-Type: "application/json"
2
```

## <a name="assign-roles-to-users"></a>Assegnare ruoli agli utenti

Utilizzare [l'API POST delle assegnazioni](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/roleassignmentspost) di ruolo Applicazioni passando l'ID applicazione, l'ID utente e l'ID ruolo applicazione per creare un mapping da utente a ruolo nell'applicazione blockchain specificata. Questo metodo può essere eseguito solo da utenti amministratori di Workbench.

### <a name="role-assignments-post-request"></a>Assegnazioni di ruolo richiesta POST

Sostituire i valori seguenti:

| Parametro | valore |
|-----------|-------|
| "IDapplicazione" | Valore restituito dall'API POST delle applicazioni. |
| "UserId" (IDutente) | ID utente dalla [tabella Utente](data-sql-management-studio.md). |
| "applicationRoleId" (ID ruolo applicazione) | Valore dell'ID ruolo applicazione associato all'ID applicazione dalla [tabella ApplicationRole](data-sql-management-studio.md). |

``` http
POST /api/v1/applications/{applicationId}/roleAssignments
Content-Type: application/json;
Authorization : Bearer {access token}

{
  "userId": {userId},
  "applicationRoleId": {applicationRoleId}
}
```

### <a name="role-assignments-post-response"></a>Assegnazioni di ruolo risposta POST

In caso di esito positivo, la risposta include l'ID assegnazione di ruolo creato dalla [tabella RoleAssignment](data-sql-management-studio.md).

``` http
HTTP/1.1 200
1
```

## <a name="list-applications"></a>Elenco applicazioni

Utilizzare [l'API GET delle applicazioni](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/applicationsget) per recuperare tutte le applicazioni Blockchain Workbench per l'utente. In questo esempio, l'utente connesso ha accesso a due applicazioni:In this example, the signed-in user has access to two applications:

- [Trasferimento cespite](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer/readme.md)
- [Trasporto refrigerato](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/refrigerated-transportation/readme.md)

### <a name="applications-get-request"></a>Richiesta GET applicazioni

``` http
GET /api/v1/applications
Authorization : Bearer {access token}
```

### <a name="applications-get-response"></a>Risposte GET applicazioni

La risposta elenca tutte le applicazioni della blockchain a cui un utente ha accesso in Blockchain Workbench. Gli amministratori di Blockchain Workbench ottengono tutte le applicazioni blockchain. Gli amministratori non Workbench ottengono tutte le applicazioni blockchain per le quali hanno almeno un ruolo applicazione associato o un ruolo di istanza smart contract associato.

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

Utilizzare [i flussi di lavoro](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/workflowsget) di applicazioni GET API per elencare tutti i flussi di lavoro di un'applicazione blockchain specificata a cui un utente ha accesso in Blockchain Workbench. Ogni applicazione della blockchain dispone di uno o più flussi di lavoro e ciascun flusso di lavoro non dispone di istanze oppure dispone delle istanze del contratto smart. Per un'applicazione client della blockchain con un solo flusso di lavoro, è consigliabile ignorare il flusso dell'esperienza utente che consente agli utenti di selezionare il flusso di lavoro appropriato.

### <a name="application-workflows-request"></a>Richiesta di flussi di lavoro dell'applicazione

``` http
GET /api/v1/applications/{applicationId}/workflows
Authorization: Bearer {access token}
```

### <a name="application-workflows-response"></a>Risposta ai flussi di lavoro delle applicazioni

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

## <a name="create-a-contract-instance"></a>Creare un'istanza del contrattoCreate a contract instance

Usare [l'API POST Contratti V2](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/contractsv2/contractpost) per creare una nuova istanza del contratto intelligente per un flusso di lavoro. Gli utenti sono in grado di creare una nuova istanza del contratto intelligente solo se l'utente è associato a un ruolo applicazione, che può avviare un'istanza del contratto intelligente per il flusso di lavoro.

> [!NOTE]
> In questo esempio viene utilizzata la versione 2 dell'API. Le API del contratto versione 2 forniscono maggiore granularità per i campi ProvisioningStatus associati.

### <a name="contracts-post-request"></a>Richiesta POST contratti

Sostituire i valori seguenti:

| Parametro | valore |
|-----------|-------|
| -workflowId' | Il valore ID flusso di lavoro è il ConstructorID del contratto dalla [tabella Workflow](data-sql-management-studio.md). |
| ContrattoCodeId | Valore ID codice contratto dalla [tabella ContractCode](data-sql-management-studio.md). Correlare l'ID applicazione e l'ID contabile per l'istanza di contratto che si desidera creare. |
| :connectionId' | Valore ID connessione dalla [tabella Connessione](data-sql-management-studio.md). |

Per il corpo della richiesta, impostare i valori utilizzando le informazioni seguenti:For the request body, set values using the following information:

| Parametro | valore |
|-----------|-------|
| workflowFunctionID (informazioni in stato inaziendale) | ID della [tabella WorkflowFunction](data-sql-management-studio.md). |
| workflowActionParameters (parametri) di workflow | Coppie di parametri name value passati al costruttore. Per ogni parametro, utilizzare il valore workflowFunctionParameterID della tabella [WorkflowFunctionParameter.](data-sql-management-studio.md) |

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

### <a name="contracts-post-response"></a>Contrattuali risposta POST

Se l'API delle assegnazioni di ruolo ha esito positivo, l'API ContractActionID viene restituito dalla [tabella ContractActionParameter](data-sql-management-studio.md).

``` http
HTTP/1.1 200 OK
4
```

## <a name="list-smart-contract-instances-for-a-workflow"></a>Elencare le istanze del contratto smart per un flusso di lavoro

Usare [l'API GET Contratti](/rest/api/azure-blockchain-workbench/contractsv2/contractsget) per visualizzare tutte le istanze di contratto intelligente per un flusso di lavoro. Oppure è possibile consentire agli utenti di esplorare una delle istanze di contratto intelligente visualizzate.

### <a name="contracts-request"></a>Richiesta di contratti

In questo esempio si supponga che un utente voglia interagire con una delle istanze del contratto smart per intervenire.

``` http
GET api/v1/contracts?workflowId={workflowId}
Authorization: Bearer {access token}
```

### <a name="contracts-response"></a>Risposta ai contratti

La risposta elenca tutte le istanze del contratto intelligente del flusso di lavoro specificato. Gli amministratori di Workbench ottengono tutte le istanze di contratto intelligente. Gli amministratori non di Workbench ottengono tutte le istanze di contratto intelligente per le quali hanno almeno un ruolo applicazione associato o associate a un ruolo di istanza di contratto intelligente.

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

Usare [l'API GET dell'azione contratto](/rest/api/azure-blockchain-workbench/contractsv2/contractactionget) per visualizzare le azioni utente disponibili in base allo stato del contratto. 

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

Utilizzare [l'API POST dell'azione contratto](/rest/api/azure-blockchain-workbench/contractsv2/contractactionpost) per eseguire un'azione per l'istanza del contratto intelligente specificata.

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

Per informazioni di riferimento sulle API Blockchain Workbench, vedere le informazioni di [riferimento sull'API REST di Azure Blockchain Workbench.](https://docs.microsoft.com/rest/api/azure-blockchain-workbench)
