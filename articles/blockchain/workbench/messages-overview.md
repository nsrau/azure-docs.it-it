---
title: Panoramica dell'integrazione dei messaggi di Azure Blockchain Workbench
description: Panoramica dell'uso dei messaggi in Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 1/8/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 34731bb96a83a901b3fc1a59ce1846083d69bfd7
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2019
ms.locfileid: "54103384"
---
# <a name="azure-blockchain-workbench-messaging-integration"></a>Integrazione della messaggistica di Azure Blockchain Workbench

Oltre a fornire un'API REST, Azure Blockchain Workbench offre anche un'integrazione basata sulla messaggistica. Workbench pubblica eventi basati sul libro mastro tramite Griglia di eventi di Azure, consentendo ai consumer downstream di inserire dati o eseguire azioni in base a tali eventi. Per i client che richiedono una messaggistica affidabile, Azure Blockchain Workbench recapita anche i messaggi a un endpoint del bus di servizio di Azure.

## <a name="input-apis"></a>API di input

Per avviare transazioni da sistemi esterni per creare utenti, creare contratti e aggiornare contratti, è possibile usare le API di input di messaggistica per eseguire le transazioni in un libro mastro. Per un esempio che illustra le API di input, vedere gli [esempi di integrazione di messaggistica](https://aka.ms/blockchain-workbench-integration-sample).

Le seguenti sono le API di input attualmente disponibili.

### <a name="create-user"></a>Create user

Crea un nuovo utente.

Per la richiesta sono necessari i campi seguenti:

| **Nome**             | **Descrizione**                                      |
|----------------------|------------------------------------------------------|
| requestId            | GUID fornito dal client                                |
| firstName            | Nome dell'utente                              |
| lastName             | Cognome dell'utente                               |
| emailAddress         | Indirizzo di posta elettronica dell'utente                           |
| externalId           | ID oggetto Azure AD dell'utente                      |
| connectionId         | Identificatore univoco della connessione blockchain |
| messageSchemaVersion | Versione dello schema di messaggistica                            |
| messageName          | **CreateUserRequest**                               |

Esempio:

``` json
{
    "requestId": "e2264523-6147-41fc-bbbb-edba8e44562d",
    "firstName": "Ali",
    "lastName": "Alio",
    "emailAddress": "aa@contoso.com",
    "externalId": "6a9b7f65-ffff-442f-b3b8-58a35abd1bcd",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateUserRequest"
}
```

Blockchain Workbench restituisce una risposta con i campi seguenti:

| **Nome**              | **Descrizione**                                                                                                             |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------|
| requestId             | GUID fornito dal client |
| userId                | ID dell'utente che è stato creato |
| userChainIdentifier   | Indirizzo dell'utente che è stato creato nella rete blockchain. In Ethereum l'indirizzo è l'indirizzo **nella catena** dell'utente. |
| connectionId          | Identificatore univoco della connessione blockchain|
| messageSchemaVersion  | Versione dello schema di messaggistica |
| messageName           | **CreateUserUpdate** |
| status                | Stato della richiesta di creazione dell'utente.  In caso di esito positivo, il valore è **Success**. In caso di esito negativo, il valore è **Failure**.     |
| additionalInformation | Informazioni aggiuntive fornite in base allo stato |

Risposta di esempio di **creazione utente** riuscita da Blockchain Workbench:

``` json
{ 
    "requestId": "e2264523-6147-41fc-bb59-edba8e44562d", 
    "userId": 15, 
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398", 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateUserUpdate", 
    "status": "Success", 
    "additionalInformation": { } 
} 
```

Se la richiesta ha esito negativo, i dettagli sull'errore sono inclusi nelle informazioni aggiuntive.

``` json
{
    "requestId": "e2264523-6147-41fc-bb59-edba8e44562d", 
    "userId": 15, 
    "userChainIdentifier": null, 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateUserUpdate", 
    "status": "Failure", 
    "additionalInformation": { 
        "errorCode": 4000, 
        "errorMessage": "User cannot be provisioned on connection." 
    }
}
```

### <a name="create-contract"></a>Create contract

Crea un nuovo contatto.

Per la richiesta sono necessari i campi seguenti:

| **Nome**             | **Descrizione**                                                                                                           |
|----------------------|---------------------------------------------------------------------------------------------------------------------------|
| requestId            | GUID fornito dal client |
| userChainIdentifier  | Indirizzo dell'utente che è stato creato nella rete blockchain. In Ethereum questo indirizzo è l'indirizzo dell'utente **nella catena**. |
| applicationName      | Nome dell'applicazione |
| workflowName         | Nome del flusso di lavoro |
| Parametri           | Input di parametri per la creazione del contratto |
| connectionId         | Identificatore univoco della connessione blockchain |
| messageSchemaVersion | Versione dello schema di messaggistica |
| messageName          | **CreateContractRequest** |

Esempio:

``` json
{ 
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211", 
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398", 
    "applicationName": "AssetTransfer", 
    "workflowName": "AssetTransfer", 
    "parameters": [ 
        { 
            "name": "description", 
            "value": "a 1969 dodge charger" 
        }, 
        { 
            "name": "price", 
            "value": "12345" 
        } 
    ], 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateContractRequest" 
}
```

Blockchain Workbench restituisce una risposta con i campi seguenti:

| **Nome**                 | **Descrizione**                                                                   |
|--------------------------|-----------------------------------------------------------------------------------|
| requestId                | GUID fornito dal client                                                             |
| contractId               | Identificatore univoco del contratto in Azure Blockchain Workbench |
| contractLedgerIdentifier | Indirizzo del contratto nel libro mastro                                            |
| connectionId             | Identificatore univoco della connessione blockchain                               |
| messageSchemaVersion     | Versione dello schema di messaggistica                                                         |
| messageName              | **CreateContractUpdate**                                                      |
| status                   | Stato della richiesta di creazione del contratto.  Valori possibili: **Submitted**, **Committed**, **Failure**.  |
| additionalInformation    | Informazioni aggiuntive fornite in base allo stato                              |

Esempio di risposta inviata di **creazione contratto** da Blockchain Workbench:

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Submitted"
    "additionalInformation": { }
}
```

Esempio di risposta di cui è stato eseguito il commit di **creazione contratto** da Blockchain Workbench:

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Committed",
    "additionalInformation": { }
}
```

Se la richiesta ha esito negativo, i dettagli sull'errore sono inclusi nelle informazioni aggiuntive.

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": null,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Failure"
    "additionalInformation": {
        "errorCode": 4000,
        "errorMessage": "Contract cannot be provisioned on connection."
    }
}
```

### <a name="create-contract-action"></a>Create contract action

Crea una nuova azione del contatto.

Per la richiesta sono necessari i campi seguenti:

| **Nome**                 | **Descrizione**                                                                                                           |
|--------------------------|---------------------------------------------------------------------------------------------------------------------------|
| requestId                | GUID fornito dal client |
| userChainIdentifier      | Indirizzo dell'utente che è stato creato nella rete blockchain. In Ethereum questo indirizzo è l'indirizzo dell'utente **nella catena**. |
| contractLedgerIdentifier | Indirizzo del contratto nel libro mastro |
| workflowFunctionName     | Nome della funzione del flusso di lavoro |
| Parametri               | Input di parametri per la creazione del contratto |
| connectionId             | Identificatore univoco della connessione blockchain |
| messageSchemaVersion     | Versione dello schema di messaggistica |
| messageName              | **CreateContractActionRequest** |

Esempio:

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398",
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "workflowFunctionName": "modify",
    "parameters": [
        {
            "name": "description",
            "value": "a 1969 dodge charger"
        },
        {
            "name": "price",
            "value": "12345"
        }
    ],
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionRequest"
}
```

Blockchain Workbench restituisce una risposta con i campi seguenti:

| **Nome**              | **Descrizione**                                                                   |
|-----------------------|-----------------------------------------------------------------------------------|
| requestId             | GUID fornito dal client|
| contractId            | Identificatore univoco del contratto in Azure Blockchain Workbench |
| connectionId          | Identificatore univoco della connessione blockchain |
| messageSchemaVersion  | Versione dello schema di messaggistica |
| messageName           | **CreateContractActionUpdate** |
| status                | Stato della richiesta di azione del contratto. Valori possibili: **Submitted**, **Committed**, **Failure**.                         |
| additionalInformation | Informazioni aggiuntive fornite in base allo stato |

Esempio di risposta inviata di **creazione azione del contratto** da Blockchain Workbench:

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Submitted",
    "additionalInformation": { }
}
```

Esempio di risposta di cui è stato eseguito il commit di **creazione azione del contratto** da Blockchain Workbench:

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Committed"
    "additionalInformation": { }
}
```

Se la richiesta ha esito negativo, i dettagli sull'errore sono inclusi nelle informazioni aggiuntive.

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Failure"
    "additionalInformation": {
        "errorCode": 4000,
        "errorMessage": "Contract action cannot be provisioned on connection."
    }
}
```

### <a name="input-api-error-codes-and-messages"></a>Messaggi e codici di errore delle API di input

**Codice errore 4000: errore di richiesta non valida**
- connectionId non valido
- Deserializzazione di CreateUserRequest non riuscita
- Deserializzazione di CreateContractRequest non riuscita
- Deserializzazione di CreateContractActionRequest non riuscita
- L'applicazione {identificata dal nome dell'applicazione} non esiste
- L'applicazione {identificata dal nome dell'applicazione} non ha un flusso di lavoro
- UserChainIdentifier non esiste
- Il contratto {identificato dall'identificatore del libro mastro} non esiste
- Il contratto {identificato dall'identificatore del libro mastro} non ha la funzione {nome della funzione del flusso di lavoro}
- UserChainIdentifier non esiste

**Codice errore 4090: errore di conflitto**
- L'utente esiste già
- Il contratto esiste già
- L'azione del contratto esiste già

**Codice di errore 5000: errore interno del server**
- Messaggi di eccezione

## <a name="event-notifications"></a>Notifiche degli eventi

Le notifiche degli eventi possono essere usate per informare utenti e sistemi downstream degli eventi che si verificano in Blockchain Workbench e nella rete blockchain a cui è connesso. Le notifiche degli eventi possono essere usate direttamente nel codice o con strumenti come le app per la logica e Flow per attivare il flusso di dati per i sistemi downstream.

Per informazioni dettagliate sui vari messaggi che possono essere ricevuti, vedere [Informazioni di riferimento sui messaggi di notifica](#notification-message-reference).

### <a name="consuming-event-grid-events-with-azure-functions"></a>Uso degli eventi di Griglia di eventi con Funzioni di Azure

Se un utente vuole usare Griglia di eventi per ricevere notifiche sugli eventi che si verificano in Blockchain Workbench, può usare gli eventi in Griglia di eventi tramite Funzioni di Azure.

1. Creare un'**app per le funzioni di Azure** nel portale di Azure.
2. Creare una nuova funzione.
3. Individuare il modello per Griglia di eventi. Verrà visualizzato il codice del modello di base per la lettura del messaggio. Modificare il codice in base alle esigenze.
4. Salvare la funzione. 
5. Selezionare Griglia di eventi dal gruppo di risorse di Blockchain Workbench.

### <a name="consuming-event-grid-events-with-logic-apps"></a>Uso degli eventi di Griglia di eventi con le app per la logica

1. Creare una nuova **app per la logica di Azure** nel portale di Azure.
2. Quando si apre l'app per la logica di Azure nel portale, viene richiesto di selezionare un trigger. Selezionare **Griglia di eventi di Azure - Quando si verifica un evento della risorsa**.
3. Quando viene visualizzata la finestra di progettazione del flusso di lavoro, viene richiesto di eseguire l'accesso.
4. Selezionare la sottoscrizione. Specificare la risorsa **Microsoft.EventGrid.Topics**. Per **Nome risorsa** selezionare il nome della risorsa dal gruppo di risorse di Azure Blockchain Workbench.
5. Selezionare Griglia di eventi dal gruppo di risorse di Blockchain Workbench.

## <a name="using-service-bus-topics-for-notifications"></a>Uso degli argomenti del bus di servizio per le notifiche

Gli argomenti del bus di servizio possono essere usati per notificare agli utenti gli eventi che si verificano in Blockchain Workbench. 

1. Selezionare il bus di servizio nel gruppo di risorse di Workbench.
2. Selezionare **Argomenti**.
3. Selezionare **egress-topic**.
4. Creare una nuova sottoscrizione per questo argomento. Ottenere una chiave per la sottoscrizione.
5. Creare un programma, che sottoscrive gli eventi di questa sottoscrizione.

### <a name="consuming-service-bus-messages-with-logic-apps"></a>Uso dei messaggi del bus di servizio con le app per la logica

1. Creare una nuova **app per la logica di Azure** nel portale di Azure.
2. Quando si apre l'app per la logica di Azure nel portale, viene richiesto di selezionare un trigger. Digitare **Bus di servizio** nella casella di ricerca e selezionare il trigger appropriato per il tipo di interazione da impostare per il bus di servizio. Ad esempio, **Bus di servizio -- Quando un messaggio è ricevuto nella sottoscrizione dell'argomento (compl. aut.)**.
3. Quando viene visualizzata la finestra di progettazione del flusso di lavoro, specificare le informazioni di connessione per il bus di servizio.
4. Selezionare la sottoscrizione e specificare l'argomento **workbench-external**.
5. Sviluppare la logica per l'applicazione che usa il messaggio dal trigger.

## <a name="notification-message-reference"></a>Informazioni di riferimento sui messaggi di notifica

A seconda di **OperationName**, i messaggi di notifica hanno uno dei tipi di messaggio seguenti.

### <a name="block-message"></a>Messaggio di blocco

Contiene le informazioni sui singoli blocchi. *BlockMessage* include una sezione con le informazioni sul livello di blocco e una sezione con le informazioni sulla transazione.

| NOME | DESCRIZIONE |
|------|-------------|
| block | Contiene le [informazioni sul blocco](#block-information) |
| transactions | Contiene una raccolta di [informazioni sulla transazione](#transaction-information) per il blocco |
| connectionId | Identificatore univoco della connessione |
| messageSchemaVersion | Versione dello schema di messaggistica |
| messageName | **BlockMessage** |
| additionalInformation | Vengono specificate informazioni aggiuntive |

#### <a name="block-information"></a>Informazioni sul blocco

| NOME              | DESCRIZIONE |
|-------------------|-------------|
| blockId           | Identificatore univoco del blocco in Azure Blockchain Workbench |
| blockNumber       | Identificatore univoco di un blocco nel libro mastro |
| blockHash         | Hash del blocco |
| previousBlockHash | Hash del blocco precedente |
| blockTimestamp    | Timestamp del blocco |

#### <a name="transaction-information"></a>Informazioni sulla transazione

| NOME               | DESCRIZIONE |
|--------------------|-------------|
| transactionId      | Identificatore univoco della transazione in Azure Blockchain Workbench |
| transactionHash    | Hash della transazione nel libro mastro |
| from               | Identificatore univoco nel libro mastro per l'origine della transazione |
| to                 | Identificatore univoco nel libro mastro per la destinazione della transazione |
| provisioningStatus | Identifica lo stato corrente del processo di provisioning per la transazione. I valori possibili sono: </br>0: la transazione è stata creata dall'API nel database</br>1: la transazione è stata inviata al libro mastro</br>2: la transazione è stata correttamente sottoposta a commit nel libro mastro</br>3 o 4: non è stato possibile eseguire il commit della transazione nel libro mastro</br>5: la transazione è stata correttamente sottoposta a commit nel libro mastro |

Esempio di *BlockMessage* da Blockchain Workbench:

``` json
{
    "block": {
        "blockId": 123
        "blockNumber": 1738312,
        "blockHash": "0x03a39411e25e25b47d0ec6433b73b488554a4a5f6b1a253e0ac8a200d13fffff",
        "previousBlockHash": null,
        "blockTimestamp": "2018-10-09T23:35:58Z",
    },
    "transactions": [
        {
            "transactionId": 234
            "transactionHash": "0xa4d9c95b581f299e41b8cc193dd742ef5a1d3a4ddf97bd11b80d123fec27ffff",
            "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
            "to": null,
            "provisioningStatus": 1
        },
        {
            "transactionId": 235
            "transactionHash": "0x5c1fddea83bf19d719e52a935ec8620437a0a6bdaa00ecb7c3d852cf92e1ffff",
            "from": "0xadd97e1e595916e29ea94fda894941574000ffff",
            "to": "0x9a8DDaCa9B7488683A4d62d0817E965E8f24ffff",
            "provisioningStatus": 2
        }
    ],
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "BlockMessage",
    "additionalInformation": {}
}
```

### <a name="contract-message"></a>Messaggio di contratto

Contiene le informazioni su un contratto. Il messaggio include una sezione con le proprietà del contratto e una sezione con le informazioni sulla transazione. Tutte le transazioni che hanno modificato il contratto per il blocco specifico sono incluse nella sezione delle transazioni.

| NOME | DESCRIZIONE |
|------|-------------|
| blockId | Identificatore univoco del blocco in Azure Blockchain Workbench |
| blockHash | Hash del blocco |
| modifyingTransactions | [Transazioni che hanno modificato](#modifying-transaction-information) il contratto |
| contractId | Identificatore univoco del contratto in Azure Blockchain Workbench |
| contractLedgerIdentifier | Identificatore univoco del contratto nel libro mastro |
| contractProperties | [Proprietà del contratto](#contract-properties) |
| isNewContract | Indica se questo contratto è stato appena creato o meno. I valori possibili sono: true se questo contratto è un nuovo contratto creato, false se il presente contratto è un aggiornamento. |
| connectionId | Identificatore univoco della connessione |
| messageSchemaVersion | Versione dello schema di messaggistica |
| messageName | **ContractMessage** |
| additionalInformation | Vengono specificate informazioni aggiuntive |

#### <a name="modifying-transaction-information"></a>Modifica delle informazioni sulla transazione

| NOME               | DESCRIZIONE |
|--------------------|-------------|
| transactionId | Identificatore univoco della transazione in Azure Blockchain Workbench |
| transactionHash | Hash della transazione nel libro mastro |
| from | Identificatore univoco nel libro mastro per l'origine della transazione |
| to | Identificatore univoco nel libro mastro per la destinazione della transazione |

#### <a name="contract-properties"></a>Proprietà del contratto

| NOME               | DESCRIZIONE |
|--------------------|-------------|
| workflowPropertyId | Identificatore univoco della proprietà del flusso di lavoro in Azure Blockchain Workbench |
| name | Nome della proprietà del flusso di lavoro |
| value | Valore della proprietà del flusso di lavoro |

Esempio di *ContractMessage* da Blockchain Workbench:

``` json
{
    "blockId": 123,
    "blockhash": "0x03a39411e25e25b47d0ec6433b73b488554a4a5f6b1a253e0ac8a200d13fffff",
    "modifyingTransactions": [
        {
            "transactionId": 234,
            "transactionHash": "0x5c1fddea83bf19d719e52a935ec8620437a0a6bdaa00ecb7c3d852cf92e1ffff",
            "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
            "to": "0xf8559473b3c7197d59212b401f5a9f07ffff"
        },
        {
            "transactionId": 235,
            "transactionHash": "0xa4d9c95b581f299e41b8cc193dd742ef5a1d3a4ddf97bd11b80d123fec27ffff",
            "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
            "to": "0xf8559473b3c7197d59212b401f5a9f07b429ffff"
        }
    ],
    "contractId": 111,
    "contractLedgerIdentifier": "0xf8559473b3c7197d59212b401f5a9f07b429ffff",
    "contractProperties": [
        {
            "workflowPropertyId": 1,
            "name": "State",
            "value": "0"
        },
        {
            "workflowPropertyId": 2,
            "name": "Description",
            "value": "1969 Dodge Charger"
        },
        {
            "workflowPropertyId": 3,
            "name": "AskingPrice",
            "value": "30000"
        },
        {
            "workflowPropertyId": 4,
            "name": "OfferPrice",
            "value": "0"
        },
        {
            "workflowPropertyId": 5,
            "name": "InstanceAppraiser",
            "value": "0x0000000000000000000000000000000000000000"
        },
        {
            "workflowPropertyId": 6,
            "name": "InstanceBuyer",
            "value": "0x0000000000000000000000000000000000000000"
        },
        {
            "workflowPropertyId": 7,
            "name": "InstanceInspector",
            "value": "0x0000000000000000000000000000000000000000"
        },
        {
            "workflowPropertyId": 8,
            "name": "InstanceOwner",
            "value": "0x9a8DDaCa9B7488683A4d62d0817E965E8f24ffff"
        },
        {
            "workflowPropertyId": 9,
            "name": "ClosingDayOptions",
            "value": "[21,48,69]"
        }
    ],
    "isNewContract": false,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "ContractMessage",
    "additionalInformation": {}
}
```

### <a name="event-message-contract-function-invocation"></a>Messaggio di evento: chiamata di funzione del contratto

Contiene informazioni quando viene richiamata una funzione del contratto, ad esempio il nome della funzione, l'input di parametri e il chiamante della funzione.

| NOME | DESCRIZIONE |
|------|-------------|
| eventName                   | **ContractFunctionInvocation** |
| caller                      | [Informazioni sul chiamante](#caller-information) |
| contractId                  | Identificatore univoco del contratto in Azure Blockchain Workbench |
| contractLedgerIdentifier    | Identificatore univoco del contratto nel libro mastro |
| functionName                | Nome della funzione |
| Parametri                  | [Informazioni sui parametri](#parameter-information) |
| transaction                 | [Informazioni sulla transazione](#eventmessage-transaction-information) |
| inTransactionSequenceNumber | Numero di sequenza della transazione nel blocco |
| connectionId                | Identificatore univoco della connessione |
| messageSchemaVersion        | Versione dello schema di messaggistica |
| messageName                 | **EventMessage** |
| additionalInformation       | Vengono specificate informazioni aggiuntive |

#### <a name="caller-information"></a>Informazioni sul chiamante

| NOME | DESCRIZIONE |
|------|-------------|
| type | Tipo del chiamante, ad esempio un utente o un contratto |
| id | Identificatore univoco del chiamante in Azure Blockchain Workbench |
| ledgerIdentifier | Identificatore univoco del chiamante nel libro mastro |

#### <a name="parameter-information"></a>Informazioni sui parametri

| NOME | DESCRIZIONE |
|------|-------------|
| name | Nome parametro |
| value | Valore del parametro |

#### <a name="event-message-transaction-information"></a>Informazioni sulla transazione del messaggio di evento

| NOME               | DESCRIZIONE |
|--------------------|-------------|
| transactionId      | Identificatore univoco della transazione in Azure Blockchain Workbench |
| transactionHash    | Hash della transazione nel libro mastro |
| from               | Identificatore univoco nel libro mastro per l'origine della transazione |
| to                 | Identificatore univoco nel libro mastro per la destinazione della transazione |

Esempio di *EventMessage ContractFunctionInvocation* da Blockchain Workbench:

``` json
{
    "eventName": "ContractFunctionInvocation",
    "caller": {
        "type": "User",
        "id": 21,
        "ledgerIdentifier": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60ffff"
    },
    "contractId": 34,
    "contractLedgerIdentifier": "0xf8559473b3c7197d59212b401f5a9f07b429ffff",
    "functionName": "Modify",
    "parameters": [
        {
            "name": "description",
            "value": "a new description"
        },
        {
            "name": "price",
            "value": "4567"
        }
    ],
    "transaction": {
        "transactionId": 234,
        "transactionHash": "0x5c1fddea83bf19d719e52a935ec8620437a0a6bdaa00ecb7c3d852cf92e1ffff",
        "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
        "to": "0xf8559473b3c7197d59212b401f5a9f07b429ffff"
    },
    "inTransactionSequenceNumber": 1,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "EventMessage",
    "additionalInformation": { }
}
```

### <a name="event-message-application-ingestion"></a>Messaggio di evento: inserimento dell'applicazione

Contiene informazioni quando un'applicazione viene caricata in Workbench, ad esempio il nome e la versione dell'applicazione caricata.

| NOME | DESCRIZIONE |
|------|-------------|
| eventName | **ApplicationIngestion** |
| applicationId | Identificatore univoco dell'applicazione in Azure Blockchain Workbench |
| applicationName | Nome dell'applicazione |
| applicationDisplayName | Nome visualizzato dell'applicazione |
| applicationVersion | Versione dell'applicazione |
| applicationDefinitionLocation | URL in cui si trova il file di configurazione dell'applicazione |
| contractCodes | Raccolta di [codici di contratto](#contract-code-information) per l'applicazione |
| applicationRoles | Raccolta di [ruoli applicazione](#application-role-information) per l'applicazione |
| applicationWorkflows | Raccolta di [flussi di lavoro applicazione](#application-workflow-information) per l'applicazione |
| connectionId | Identificatore univoco della connessione |
| messageSchemaVersion | Versione dello schema di messaggistica |
| messageName | **EventMessage** |
| additionalInformation | Le informazioni aggiuntive disponibili in questa pagina includono le informazioni sulla transizione e gli stati del flusso di lavoro dell'applicazione. |

#### <a name="contract-code-information"></a>Informazioni sul codice del contratto

| NOME | DESCRIZIONE |
|------|-------------|
| id | Identificatore univoco del codice del contratto in Azure Blockchain Workbench |
| ledgerId | Identificatore univoco del libro mastro in Azure Blockchain Workbench |
| location | URL in cui si trova il file di codice del contratto |

#### <a name="application-role-information"></a>Informazioni sul ruolo applicazione

| NOME | DESCRIZIONE |
|------|-------------|
| id | Identificatore univoco del ruolo applicazione in Azure Blockchain Workbench |
| name | Nome del ruolo applicazione |

#### <a name="application-workflow-information"></a>Informazioni sul flusso di lavoro dell'applicazione

| NOME | DESCRIZIONE |
|------|-------------|
| id | Identificatore univoco del flusso di lavoro dell'applicazione in Azure Blockchain Workbench |
| name | Nome del flusso di lavoro dell'applicazione |
| displayName | Nome visualizzato del flusso di lavoro dell'applicazione |
| functions | Raccolta di [funzioni per il flusso di lavoro dell'applicazione](#workflow-function-information)|
| stati | Raccolta di [stati per il flusso di lavoro dell'applicazione](#workflow-state-information) |
| properties | [Informazioni sulle proprietà del flusso di lavoro](#workflow-property-information) dell'applicazione |

##### <a name="workflow-function-information"></a>Informazioni sulla funzione del flusso di lavoro

| NOME | DESCRIZIONE |
|------|-------------|
| id | Identificatore univoco della funzione del flusso di lavoro dell'applicazione in Azure Blockchain Workbench |
| name | Nome della funzione |
| Parametri | Parametri della funzione |

##### <a name="workflow-state-information"></a>Informazioni sullo stato del flusso di lavoro

| NOME | DESCRIZIONE |
|------|-------------|
| name | Nome dello stato |
| displayName | Nome visualizzato dello stato |
| style | Stile dello stato (esito positivo o negativo) |

##### <a name="workflow-property-information"></a>Informazioni sulla proprietà del flusso di lavoro

| NOME | DESCRIZIONE |
|------|-------------|
| id | Identificatore univoco della proprietà del flusso di lavoro dell'applicazione in Azure Blockchain Workbench |
| name | Nome proprietà |
| type | Tipo di proprietà |

Esempio di *EventMessage ApplicationIngestion* da Blockchain Workbench:

``` json
{
    "eventName": "ApplicationIngestion",
    "applicationId": 31,
    "applicationName": "AssetTransfer",
    "applicationDisplayName": "Asset Transfer",
    "applicationVersion": “1.0”,
    "applicationDefinitionLocation": "http://url"
    "contractCodes": [
        {
            "id": 23,
            "ledgerId": 1,
            "location": "http://url"
        }
    ],
    "applicationRoles": [
            {
                "id": 134,
                "name": "Buyer"
            },
            {
                "id": 135,
                "name": "Seller"
            }
       ],
    "applicationWorkflows": [
        {
            "id": 89,
            "name": "AssetTransfer",
            "displayName": "Asset Transfer",
            "functions": [
                {
                    "id": 912,
                    "name": "",
                    "parameters": [
                        {
                            "name": "description",
                            "type": {
                                "name": "string"
                             }
                        },
                        {
                            "name": "price",
                            "type": {
                                "name": "int"
                            }
                        }
                    ]
                },
                {
                    "id": 913,
                    "name": "modify",
                    "parameters": [
                        {
                            "name": "description",
                            "type": {
                                "name": "string"
                             }
                        },
                        {
                            "name": "price",
                            "type": {
                                "name": "int"
                            }
                        }
                    ]
                }
            ],
            "states": [ 
                 {
                      "name": "Created",
                      "displayName": "Created",
                      "style" : "Success"
                 },
                 {
                      "name": "Terminated",
                      "displayName": "Terminated",
                      "style" : "Failure"
                 }
            ],
            "properties": [
                {
                    "id": 879,
                    "name": "Description",
                    "type": {
                                "name": "string"
                     }
                },
                {
                    "id": 880,
                    "name": "Price",
                    "type": {
                                "name": "int"
                     }
                }
            ]
        }
    ]
    "connectionId": [ ],
    "messageSchemaVersion": "1.0.0",
    "messageName": "EventMessage",
    "additionalInformation":
        {
            "states" :
            [
                {
                    "Name": "BuyerAccepted",
                    "Transitions": [
                        {
                            "DisplayName": "Accept"
                            "AllowedRoles": [ ],
                            "AllowedInstanceRoles": [ "InstanceOwner" ],
                            "Function": "Accept",
                            "NextStates": [ "SellerAccepted" ]
                        }
                    ]
                }
            ]
        }
}
```

### <a name="event-message-role-assignment"></a>Messaggio di evento: Assegnazione di ruolo

Contiene informazioni quando a un utente viene assegnato un ruolo in Workbench, ad esempio chi ha eseguito l'assegnazione di ruolo e il nome del ruolo e dell'applicazione corrispondente.

| NOME | DESCRIZIONE |
|------|-------------|
| eventName | **RoleAssignment** |
| applicationId | Identificatore univoco dell'applicazione in Azure Blockchain Workbench |
| applicationName | Nome dell'applicazione |
| applicationDisplayName | Nome visualizzato dell'applicazione |
| applicationVersion | Versione dell'applicazione |
| applicationRole        | Informazioni sul [ruolo applicazione](#roleassignment-application-role) |
| assigner               | Informazioni sull'[assegnatore](#roleassignment-assigner) |
| assignee               | Informazioni sull'[assegnatario](#roleassignment-assignee) |
| connectionId           | Identificatore univoco della connessione |
| messageSchemaVersion   | Versione dello schema di messaggistica |
| messageName            | **EventMessage** |
| additionalInformation  | Vengono specificate informazioni aggiuntive |

#### <a name="roleassignment-application-role"></a>Ruolo applicazione di RoleAssignment

| NOME | DESCRIZIONE |
|------|-------------|
| id | Identificatore univoco del ruolo applicazione in Azure Blockchain Workbench |
| name | Nome del ruolo applicazione |

#### <a name="roleassignment-assigner"></a>Assegnatore di RoleAssignment

| NOME | DESCRIZIONE |
|------|-------------|
| id | Identificatore univoco dell'utente in Azure Blockchain Workbench |
| type | Tipo dell'assegnatore |
| chainIdentifier | Identificatore univoco dell'utente nel libro mastro |

#### <a name="roleassignment-assignee"></a>Assegnatario di RoleAssignment

| NOME | DESCRIZIONE |
|------|-------------|
| id | Identificatore univoco dell'utente in Azure Blockchain Workbench |
| type | Tipo dell'assegnatario |
| chainIdentifier | Identificatore univoco dell'utente nel libro mastro |

Esempio di *EventMessage RoleAssignment* da Blockchain Workbench:

``` json
{
    "eventName": "RoleAssignment",
    "applicationId": 31,
    "applicationName": "AssetTransfer",
    "applicationDisplayName": "Asset Transfer",
    "applicationVersion": “1.0”,
    "applicationRole": {
        "id": 134,
        "name": "Buyer"
    },
    "assigner": {
        "id": 1,
        "type": null,
        "chainIdentifier": "0xeFFC7766d38aC862d79706c3C5CEEf089564ffff"
    },
    "assignee": {
        "id": 3,
        "type": null,
        "chainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f24ffff"
    },
    "connectionId": [ ],
    "messageSchemaVersion": "1.0.0",
    "messageName": "EventMessage",
    "additionalInformation": { }
}
```

## <a name="next-steps"></a>Passaggi successivi

- [Modelli di integrazione dei contratti intelligenti](integration-patterns.md)