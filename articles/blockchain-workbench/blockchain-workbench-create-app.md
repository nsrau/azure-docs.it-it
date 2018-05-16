---
title: Creare un'applicazione blockchain in Azure Blockchain Workbench
description: Come creare un'applicazione blockchain in Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 4/9/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: ffc8be29cecac05fda440eaf72b51ffc6f024424
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
---
# <a name="create-a-blockchain-application-in-azure-blockchain-workbench"></a>Creare un'applicazione blockchain in Azure Blockchain Workbench

È possibile usare Azure Blockchain Workbench per creare applicazioni blockchain che rappresentano flussi di lavoro a più parti definiti dalla configurazione e dal codice di un contratto intelligente.

Si apprenderà come:

> [!div class="checklist"]
> * Configurare un'applicazione blockchain
> * Creare un file di codice per il contratto intelligente
> * Aggiungere un'applicazione blockchain a Blockchain Workbench
> * Aggiungere membri all'applicazione blockchain

## <a name="prerequisites"></a>prerequisiti

* Una distribuzione di Blockchain Workbench. Per informazioni dettagliate sulla distribuzione, vedere [Distribuzione di Azure Blockchain Workbench](blockchain-workbench-deploy.md).
* Gli utenti di Azure Active Directory nel tenant associato con Blockchain Workbench. Per altre informazioni, vedere [Aggiungere utenti di Azure AD in Azure Blockchain Workbench](blockchain-workbench-manage-users.md#add-azure-ad-users).
* Un account di amministratore di Blockchain Workbench. Per altre informazioni, vedere [Aggiungere amministratori di Blockchain Workbench in Azure Blockchain Workbench](blockchain-workbench-manage-users.md#manage-blockchain-workbench-administrators).

Creare ora un'applicazione di base in cui un richiedente invia una richiesta e un risponditore invia una risposta alla richiesta. Una richiesta può essere per esempio "Ciao, come stai?" e la risposta "Sto benissimo!". Sia la richiesta che la risposta vengono registrate nel blockchain sottostante. 

## <a name="configuration-file"></a>File di configurazione

I metadati di configurazione definiscono i flussi di lavoro di alto livello e il modello di interazione dell'applicazione blockchain. I metadati di configurazione rappresentano le fasi dei flussi di lavoro e il modello di interazione dell'applicazione blockchain.

1. Usare l'editor preferito per creare un file denominato `HelloBlockchain.json`.
2. Aggiungere il codice JSON seguente per definire la configurazione dell'applicazione blockchain.

    ``` json
    {
      "ApplicationName": "HelloBlockchain",
      "DisplayName": "Hello, Blockchain!",
      "Description": "A simple application to send request and get response",
      "ApplicationRoles": [
        {
          "Name": "Requestor",
          "Description": "A person sending a request."
        },
        {
          "Name": "Responder",
          "Description": "A person responding to a request"
        }
      ],
      "Workflows": [
        {
          "Name": "HelloBlockchain",
          "DisplayName": "Request Response",
          "Description": "A simple workflow to send a request and receive a response.",
          "Initiators": [ "Requestor" ],
          "StartState": "Request",
          "Properties": [
            {
              "Name": "State",
              "DisplayName": "State",
              "Description": "Holds the state of the contract.",
              "Type": {
                "Name": "state"
              }
            },
            {
              "Name": "Requestor",
              "DisplayName": "Requestor",
              "Description": "A person sending a request.",
              "Type": {
                "Name": "Requestor"
              }
            },
            {
              "Name": "Responder",
              "DisplayName": "Responder",
              "Description": "A person sending a response.",
              "Type": {
                "Name": "Responder"
              }
            },
            {
              "Name": "RequestMessage",
              "DisplayName": "Request Message",
              "Description": "A request message.",
              "Type": {
                "Name": "string"
              }
            },
            {
              "Name": "ResponseMessage",
              "DisplayName": "Response Message",
              "Description": "A response message.",
              "Type": {
                "Name": "string"
              }
            }
          ],
          "Constructor": {
            "Parameters": [
              {
                "Name": "message",
                "Description": "...",
                "DisplayName": "Request Message",
                "Type": {
                  "Name": "string"
                }
              }
            ]
          },
          "Functions": [
            {
              "Name": "SendRequest",
              "DisplayName": "Request",
              "Description": "...",
              "Parameters": [
                {
                  "Name": "requestMessage",
                  "Description": "...",
                  "DisplayName": "Request Message",
                  "Type": {
                    "Name": "string"
                  }
                }
              ]
            },
            {
              "Name": "SendResponse",
              "DisplayName": "Response",
              "Description": "...",
              "Parameters": [
                {
                  "Name": "responseMessage",
                  "Description": "...",
                  "DisplayName": "Response Message",
                  "Type": {
                    "Name": "string"
                  }
                }
              ]
            }
          ],
          "States": [
            {
              "Name": "Request",
              "DisplayName": "Request",
              "Description": "...",
              "PercentComplete": 50,
              "Value": 0,
              "Style": "Success",
              "Transitions": [
                {
                  "AllowedRoles": ["Responder"],
                  "AllowedInstanceRoles": [],
                  "Description": "...",
                  "Function": "SendResponse",
                  "NextStates": [ "Respond" ],
                  "DisplayName": "Send Response"
                }
              ]
            },
            {
              "Name": "Respond",
              "DisplayName": "Respond",
              "Description": "...",
              "PercentComplete": 90,
              "Value": 1,
              "Style": "Success",
              "Transitions": [
                {
                  "AllowedRoles": [],
                  "AllowedInstanceRoles": ["Requestor"],
                  "Description": "...",
                  "Function": "SendRequest",
                  "NextStates": [ "Request" ],
                  "DisplayName": "Send Request"
                }
              ]
            }
          ]
        }
      ]
    }
    ```

3. Salvare il file.`HelloBlockchain.json`

Il file di configurazione ha molte sezioni. I dettagli di ciascuna sezione sono indicati di seguito:

### <a name="application-metadata"></a>Metadati dell'applicazione

L'inizio del file di configurazione contiene informazioni sull'applicazione, fra cui descrizione e nome dell'applicazione.

### <a name="application-roles"></a>Ruoli applicazione

La sezione dei ruoli applicazione definisce i ruoli utente che possono agire o partecipare all'interno dell'applicazione blockchain. Viene definito un set di ruoli distinti in base alle funzionalità. Nello scenario di richiesta-risposta c'è distinzione tra le funzionalità del richiedente, ovvero l'entità che genera le richieste, e quelle del risponditore, l'entità che genera le risposte.

### <a name="workflows"></a>Flussi di lavoro

I flussi di lavoro definiscono una o più fasi e azioni del contratto. Nello scenario di richiesta-risposta la prima fase (stato) del flusso di lavoro è quella in cui un richiedente (ruolo) intraprende un'azione (transizione) per inviare una richiesta (funzione). La fase (stato) successiva è quella in cui un risponditore (ruolo) intraprende un'azione (transizione) per inviare una risposta (funzione). Il flusso di lavoro di un'applicazione può coinvolgere proprietà, funzioni e stati necessari per descrivere il flusso di un contratto. 

Per altre informazioni sui contenuti dei file di configurazione, vedere [Riferimento alla configurazione del flusso di lavoro di Azure Blockchain](blockchain-workbench-configuration-overview.md).

## <a name="smart-contract-code-file"></a>File di codice del contratto intelligente

I contratti intelligenti rappresentano la logica di business dell'applicazione blockchain. Attualmente Blockchain Workbench supporta Ethereum per il libro mastro blockchain. Ethereum usa [Solidity](https://solidity.readthedocs.io) come linguaggio di programmazione per scrivere la logica di business auto-applicante per i contratti intelligenti.

I contratti intelligenti in Solidity sono simili alle classi nei linguaggi di programmazione orientati a oggetti. Ogni contratto contiene lo stato e le funzioni per implementare le fasi e le azioni del contratto intelligente.

Usare l'editor preferito per creare un file denominato `HelloBlockchain.sol`.

### <a name="version-pragma"></a>Pragma di versione

Come procedura consigliata, indicare la versione di Solidity di destinazione. Specificando la versione si evitano problemi di incompatibilità con le versioni future di Solidity. 

Aggiungere il pragma di versione seguente nella parte superiore del file di codice del contratto intelligente `HelloBlockchain.sol`.


  ``` solidity
  pragma solidity ^0.4.20;
  ```

### <a name="base-class"></a>Classe di base

La classe di base **WorkbenchBase** consente a Blockchain Workbench di creare e aggiornare il contratto. La classe di base è necessaria per il codice del contratto intelligente specifico di Blockchain Workbench. Il contratto deve ereditare dalla classe di base **WorkbenchBase**.

Nel file di codice del contratto intelligente `HelloBlockchain.sol` aggiungere la classe **WorkbenchBase** all'inizio del file. 

```
contract WorkbenchBase {
    event WorkbenchContractCreated(string applicationName, string workflowName, address originatingAddress);
    event WorkbenchContractUpdated(string applicationName, string workflowName, string action, address originatingAddress);

    string internal ApplicationName;
    string internal WorkflowName;

    function WorkbenchBase(string applicationName, string workflowName) internal {
        ApplicationName = applicationName;
        WorkflowName = workflowName;
    }

    function ContractCreated() internal {
        WorkbenchContractCreated(ApplicationName, WorkflowName, msg.sender);
    }

    function ContractUpdated(string action) internal {
        WorkbenchContractUpdated(ApplicationName, WorkflowName, action, msg.sender);
    }
}
```
La classe di base include due funzioni importanti:

|Funzione della classe di base  | Scopo  | Quando chiamarla  |
|---------|---------|---------|
| ContractCreated() | Notifica a Blockchain Workbench che è stato creato un contratto | Prima di uscire dal costruttore del contratto |
| ContractUpdated() | Notifica a Blockchain Workbench che lo stato di un contratto è stato aggiornato | Prima di uscire da una funzione del contratto |

### <a name="configuration-and-smart-contract-code-relationship"></a>Relazione fra configurazione e codice del contratto intelligente

Blockchain Workbench usa il file di configurazione e il file di codice del contratto intelligente per creare un'applicazione blockchain. Esiste una relazione tra quanto definito nella configurazione e il codice del contratto intelligente. Dettagli del contratto, funzioni, parametri e tipi devono corrispondere per creare l'applicazione. Blockchain Workbench verifica i file prima della creazione dell'applicazione. 

### <a name="contract"></a>Contratto

Per Blockchain Workbench i contratti devono ereditare dalla classe di base **WorkbenchBase**. Quando si dichiara il contratto, è necessario passare come argomenti il nome dell'applicazione e il nome del flusso di lavoro.

Aggiungere l'intestazione **contract** al file di codice del contratto intelligente `HelloBlockchain.sol`. 

```
contract HelloBlockchain is WorkbenchBase('HelloBlockchain', 'HelloBlockchain') {
```

Il contratto deve ereditare dalla classe di base **WorkbenchBase** e passare i parametri **ApplicationName** e il **nome** del flusso di lavoro come definito nel file di configurazione. In questo caso il nome dell'applicazione e il nome del flusso di lavoro corrispondono.

### <a name="state-variables"></a>Variabili di stato

Le variabili di stato archiviano i valori di stato per ogni istanza del contratto. Le variabili di stato nel contratto devono corrispondere alle proprietà del flusso di lavoro definite nel file di configurazione.

Aggiungere le variabili di stato al contratto nel file di codice del contratto intelligente `HelloBlockchain.sol`. 

```
    //Set of States
    enum StateType { Request, Respond}
    
    //List of properties
    StateType public  State;
    address public  Requestor;
    address public  Responder;
    
    string public RequestMessage;
    string public ResponseMessage;
```

### <a name="constructor"></a>Costruttore

Il costruttore definisce i parametri da immettere per una nuova istanza del contratto intelligente di un flusso di lavoro. Il costruttore viene dichiarato come funzione con lo stesso nome del contratto. I parametri richiesti per il costruttore sono definiti come parametri del costruttore nel file di configurazione. Il numero, l'ordine e il tipo dei parametri devono corrispondere in entrambi i file.

Nella funzione costruttore scrivere la logica di business che deve essere eseguita prima di creare il contratto. Ad esempio, inizializzare le variabili di stato con i valori iniziali.

Prima di disattivare la funzione costruttore, chiamare la funzione `ContractCreated()`. Questa funzione notifica a Blockchain Workbench che è stato creato un contratto.

Aggiungere la funzione costruttore al contratto nel file di codice del contratto intelligente `HelloBlockchain.sol`. 

```
    // constructor function
    function HelloBlockchain(string message) public
    {
        Requestor = msg.sender;
        RequestMessage = message;
        State = StateType.Request;
    
        // call ContractCreated() to create an instance of this workflow
        ContractCreated();
    }
```

### <a name="functions"></a>Funzioni

Le funzioni sono le unità eseguibili della logica di business all'interno di un contratto. I parametri richiesti per la funzione sono definiti come parametri di funzione nel file di configurazione. Il numero, l'ordine e il tipo dei parametri devono corrispondere in entrambi i file. Le funzioni sono associate a transizioni in un flusso di lavoro Blockchain Workbench nel file di configurazione. Una transizione è un'azione eseguita per passare alla fase successiva del flusso di lavoro di un'applicazione come determinato dal contratto.

Scrivere tutte le regole di business da eseguire nella funzione. Ad esempio, la modifica del valore di una variabile di stato.

Prima di uscire dalla funzione, chiamare la funzione `ContractUpdated()`. La funzione notifica a Blockchain Workbench che lo stato del contratto è stato aggiornato. Se si vuole annullare le modifiche di stato apportate nella funzione, chiamare revert(). La funzione revert() elimina le modifiche apportate dall'ultima chiamata di ContractUpdated().

1. Aggiungere la funzione seguente al contratto nel file di codice del contratto intelligente `HelloBlockchain.sol`. 

    ```
        // call this function to send a request
        function SendRequest(string requestMessage) public
        {
            if (Requestor != msg.sender)
            {
                revert();
            }
    
            RequestMessage = requestMessage;
            State = StateType.Request;
    
            // call ContractUpdated() to record this action
            ContractUpdated('SendRequest');
        }
    
        // call this function to send a response
        function SendResponse(string responseMessage) public
        {
            Responder = msg.sender;
    
            // call ContractUpdated() to record this action
            ResponseMessage = responseMessage;
            State = StateType.Respond;
            ContractUpdated('SendResponse');
        }
    }
    ```

2. Salvare il file di codice del contratto intelligente `HelloBlockchain.sol`.

## <a name="add-blockchain-application-to-blockchain-workbench"></a>Aggiungere un'applicazione blockchain a Blockchain Workbench

Per aggiungere un'applicazione blockchain a Blockchain Workbench, caricare i file di configurazione e del contratto intelligente per definire l'applicazione.

1. In un Web browser passare all'indirizzo Web di Blockchain Workbench. Ad esempio `https://{workbench URL}.azurewebsites.net/` L'applicazione Web viene creata quando si distribuisce Blockchain Workbench. Per informazioni su come trovare l'indirizzo Web di Blockchain Workbench, vedere [URL Web di Blockchain Workbench](blockchain-workbench-deploy.md#blockchain-workbench-web-url)
2. Eseguire l'accesso come amministratore di Blockchain Workbench. Per altre informazioni sulla gestione degli utenti, vedere [Gestire gli utenti in Azure Blockchain Workbench](blockchain-workbench-manage-users.md).
3. Selezionare **Applicazioni** > **Nuovo**. Viene visualizzato il riquadro **Nuova applicazione**.
4. Selezionare **Carica la configurazione del contratto** > **Sfoglia** per individuare il file di configurazione **HelloBlockchain.json** che è stato creato. Il file di configurazione viene convalidato automaticamente. Selezionare il collegamento **Mostra** per visualizzare gli errori di convalida. Correggere gli errori di convalida prima di distribuire l'applicazione.
5. Selezionare **Carica il codice del contratto** > **Sfoglia** per individuare il file del codice contratto intelligente **HelloBlockchain.sol**. Il file di codice viene convalidato automaticamente. Selezionare il collegamento **Mostra** per visualizzare gli errori di convalida. Correggere gli errori di convalida prima di distribuire l'applicazione.
6. Selezionare **Distribuisci** per creare l'applicazione blockchain in base ai file di configurazione e del contratto intelligente.

La distribuzione dell'applicazione blockchain richiede alcuni minuti. Al termine dell'installazione la nuova applicazione è visualizzata in **Applicazioni**. 

> [!NOTE]
> È possibile creare applicazioni blockchain anche usando l'[API REST di Azure Blockchain Workbench](https://docs.microsoft.com/rest/api/azure-blockchain-workbench). 

## <a name="add-blockchain-application-members"></a>Aggiungere membri a un'applicazione blockchain

Aggiungere membri all'applicazione per avviare ed eseguire operazioni sui contratti. Per aggiungere membri all'applicazione, è necessario essere un [amministratore di Blockchain Workbench](blockchain-workbench-manage-users.md#manage-blockchain-workbench-administrators).

1. Selezionare **Applicazioni** > **Hello, Blockchain!**.
2. Il numero di membri associati all'applicazione è mostrato nell'angolo superiore destro della pagina. Per una nuova applicazione il numero di membri è zero.
3. Selezionare il collegamento **membri** nell'angolo superiore destro della pagina. Compare l'elenco dei membri attuali per l'applicazione.
4. Nell'elenco dei membri selezionare **Aggiungi membri**.
5. Selezionare o immettere il nome del membro da aggiungere. Sono elencati solo gli utenti di Azure AD che esistono nel tenant di Blockchain Workbench. Se l'utente non viene trovato, è necessario [aggiungere utenti di Azure AD](blockchain-workbench-manage-users.md#add-azure-ad-users).
6. Selezionare il **ruolo** per il membro. Per il primo membro selezionare il ruolo **Richiedente**.
7. Selezionare **Aggiungi** per aggiungere il membro con il ruolo associato all'applicazione.
8. Aggiungere un altro membro all'applicazione con il ruolo **Risponditore**.

Per altre informazioni sulla gestione degli utenti in Blockchain Workbench, vedere [gestione degli utenti in Azure Blockchain Workbench](blockchain-workbench-manage-users.md)

## <a name="next-steps"></a>Passaggi successivi

Dopo avere creato un'applicazione, provare a [usare l'applicazione blockchain](blockchain-workbench-use.md).
