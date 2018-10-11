---
title: Panoramica dell'integrazione dei messaggi di Azure Blockchain Workbench
description: Panoramica dell'uso dei messaggi in Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/1/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: dcf31bd6b128115962e30dd6653364c4cd65047d
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/03/2018
ms.locfileid: "48241586"
---
# <a name="azure-blockchain-workbench-messaging-integration"></a>Integrazione della messaggistica di Azure Blockchain Workbench

Oltre a fornire un'API REST, Azure Blockchain Workbench offre anche un'integrazione basata sulla messaggistica. Workbench pubblica eventi basati sul libro mastro tramite Griglia di eventi di Azure, consentendo ai consumer downstream di inserire dati o eseguire azioni in base a tali eventi. Per i client che richiedono una messaggistica affidabile, Azure Blockchain Workbench recapita anche i messaggi a un endpoint del bus di servizio di Azure.

Gli sviluppatori hanno inoltre espresso interesse per la possibilità di consentire a sistemi esterni di comunicare e avviare transazioni per creare utenti, creare contratti e aggiornare i contratti in un libro mastro. Sebbene questa funzionalità non sia attualmente esposta nell'anteprima pubblica, un esempio che offre tale capacità è disponibile in [http://aka.ms/blockchain-workbench-integration-sample](http://aka.ms/blockchain-workbench-integration-sample).

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

1.  Creare una nuova **app per la logica di Azure** nel portale di Azure.
2.  Quando si apre l'app per la logica di Azure nel portale, viene richiesto di selezionare un trigger. Selezionare **Griglia di eventi di Azure - Quando si verifica un evento della risorsa**.
3. Quando viene visualizzata la finestra di progettazione del flusso di lavoro, viene richiesto di eseguire l'accesso.
4. Selezionare la sottoscrizione. Specificare la risorsa **Microsoft.EventGrid.Topics**. Per **Nome risorsa** selezionare il nome della risorsa dal gruppo di risorse di Azure Blockchain Workbench.
5. Selezionare Griglia di eventi dal gruppo di risorse di Blockchain Workbench.

## <a name="using-service-bus-topics-for-notifications"></a>Uso degli argomenti del bus di servizio per le notifiche

Gli argomenti del bus di servizio possono essere usati per notificare agli utenti gli eventi che si verificano in Blockchain Workbench. 

1.  Selezionare il bus di servizio nel gruppo di risorse di Workbench.
2.  Selezionare **Argomenti**.
3.  Selezionare **workbench-external**.
4.  Creare una nuova sottoscrizione per questo argomento. Ottenere una chiave per la sottoscrizione.
5.  Creare un programma, che sottoscrive gli eventi di questa sottoscrizione.

### <a name="consuming-service-bus-messages-with-logic-apps"></a>Uso dei messaggi del bus di servizio con le app per la logica

1. Creare una nuova **app per la logica di Azure** nel portale di Azure.
2. Quando si apre l'app per la logica di Azure nel portale, viene richiesto di selezionare un trigger. Digitare **Bus di servizio** nella casella di ricerca e selezionare il trigger appropriato per il tipo di interazione da impostare per il bus di servizio. Ad esempio, **Bus di servizio -- Quando un messaggio è ricevuto nella sottoscrizione dell'argomento (compl. aut.)**.
3. Quando viene visualizzata la finestra di progettazione del flusso di lavoro, specificare le informazioni di connessione per il bus di servizio.
4. Selezionare la sottoscrizione e specificare l'argomento **workbench-external**.
5. Sviluppare la logica per l'applicazione che usa il messaggio dal trigger.

## <a name="notification-message-reference"></a>Informazioni di riferimento sui messaggi di notifica

A seconda di **OperationName**, i messaggi di notifica hanno uno dei tipi di messaggio seguenti.

### <a name="accountcreated"></a>AccountCreated

Indica che è stato richiesto un nuovo account da aggiungere alla catena specificata.

| NOME    | DESCRIZIONE  |
|----------|--------------|
| UserId  | ID dell'utente che è stato creato. |
| ChainIdentifier | Indirizzo dell'utente che è stato creato nella rete blockchain. In Ethereum è l'indirizzo dell'utente **nella catena**. |

``` csharp
public class NewAccountRequest : MessageModelBase
{
  public int UserID { get; set; }
  public string ChainIdentifier { get; set; }
}
```

### <a name="contractinsertedorupdated"></a>ContractInsertedOrUpdated

Indica che è stata effettuata una richiesta per l'inserimento o l'aggiornamento di un contratto in un libro mastro distribuito.

| NOME | DESCRIZIONE |
|-----|--------------|
| ChainID | Identificatore univoco della catena associata alla richiesta.|
| BlockId | Identificatore univoco di un blocco nel libro mastro.|
| ContractId | Identificatore univoco del contratto.|
| ContractAddress |       Indirizzo del contratto nel libro mastro.|
| TransactionHash  |     Hash della transazione nel libro mastro.|
| OriginatingAddress |   Indirizzo dell'iniziatore della transazione.|
| ActionName       |     Nome dell'azione.|
| IsUpdate        |      Specifica se si tratta di un aggiornamento.|
| Parametri       |     Elenco di oggetti che identificano il nome, il valore e il tipo di dati dei parametri inviati a un'azione.|
| TopLevelInputParams |  Negli scenari in cui un contratto è connesso a uno o più altri contratti, questi sono i parametri del contratto di primo livello. |

``` csharp
public class ContractInsertOrUpdateRequest : MessageModelBase
{
    public int ChainId { get; set; }
    public int BlockId { get; set; }
    public int ContractId { get; set; }
    public string ContractAddress { get; set; }
    public string TransactionHash { get; set; }
    public string OriginatingAddress { get; set; }
    public string ActionName { get; set; }
    public bool IsUpdate { get; set; }
    public List<ContractProperty> Parameters { get; set; }
    public bool IsTopLevelUpdate { get; set; }
    public List<ContractInputParameter> TopLevelInputParams { get; set; }
}
```

#### <a name="updatecontractaction"></a>UpdateContractAction

Indica che è stata effettuata una richiesta per l'esecuzione di un'azione su contratto specifico in un libro mastro distribuito.

| NOME                     | DESCRIZIONE                                                                                                                                                                   |
|--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ContractActionId         | Identificatore univoco dell'azione sul contratto                                                                                                                                |
| ChainIdentifier          | Identificatore univoco della catena                                                                                                                                           |
| ConnectionId             | Identificatore univoco della connessione                                                                                                                                      |
| UserChainIdentifier      | Indirizzo dell'utente che è stato creato nella rete blockchain. In Ethereum, è l'indirizzo dell'utente "nella catena".                                                     |
| ContractLedgerIdentifier | Indirizzo del contratto nel libro mastro.                                                                                                                                        |
| WorkflowFunctionName     | Nome della funzione del flusso di lavoro.                                                                                                                                                |
| WorkflowName             | Nome del flusso di lavoro.                                                                                                                                                         |
| WorkflowBlobStorageURL   | URL del contratto nell'archiviazione BLOB.                                                                                                                                      |
| ContractActionParameters | Parametri per l'azione sul contratto.                                                                                                                                           |
| TransactionHash          | Hash della transazione nel libro mastro.                                                                                                                                    |
| Stato del provisioning      | Stato di provisioning corrente dell'azione.</br>0 - Creata</br>1 - In corso</br>2 - Completata</br> Lo stato Completata indica una conferma dal libro mastro del completamento dell'aggiunta.                                               |
|                          |                                                                                                                                                                               |

```csharp
public class ContractActionRequest : MessageModelBase
{
    public int ContractActionId { get; set; }
    public int ConnectionId { get; set; }
    public string UserChainIdentifier { get; set; }
    public string ContractLedgerIdentifier { get; set; }
    public string WorkflowFunctionName { get; set; }
    public string WorkflowName { get; set; }
    public string WorkflowBlobStorageURL { get; set; }
    public IEnumerable<ContractActionParameter> ContractActionParameters { get; set; }
    public string TransactionHash { get; set; }
    public int ProvisioningStatus { get; set; }
}
```

### <a name="updateuserbalance"></a>UpdateUserBalance

Indica che è stata effettuata una richiesta per l'aggiornamento del saldo dell'utente in un libro mastro distribuito specifico.

> [!NOTE]
> Questo messaggio viene generato solo per i libri mastri che richiedono il finanziamento degli account.
> 

| NOME    | DESCRIZIONE                              |
|---------|------------------------------------------|
| Indirizzo | Indirizzo dell'utente finanziato. |
| Balance | Saldo dell'utente.         |
| ChainID | Identificatore univoco della catena.     |


``` csharp
public class UpdateUserBalanceRequest : MessageModelBase
{
    public string Address { get; set; }
    public decimal Balance { get; set; }
    public int ChainID { get; set; }
}
```

### <a name="insertblock"></a>InsertBlock

Il messaggio indica che è stata effettuata una richiesta per l'aggiunta di un blocco in un libro mastro distribuito.

| NOME           | DESCRIZIONE                                                            |
|----------------|------------------------------------------------------------------------|
| ChainId        | Identificatore univoco della catena a cui è stato aggiunto il blocco.             |
| BlockId        | Identificatore univoco del blocco in Azure Blockchain Workbench. |
| BlockHash      | Hash del blocco.                                                 |
| BlockTimeStamp | Timestamp del blocco.                                            |

``` csharp
public class InsertBlockRequest : MessageModelBase
{
    public int ChainId { get; set; }
    public int BlockId { get; set; }
    public string BlockHash { get; set; }
    public int BlockTimestamp { get; set; }
}
```

### <a name="inserttransaction"></a>InsertTransaction

Il messaggio fornisce i dettagli su una richiesta per l'aggiunta di una transazione in un libro mastro distribuito.

| NOME            | DESCRIZIONE                                                            |
|-----------------|------------------------------------------------------------------------|
| ChainId         | Identificatore univoco della catena a cui è stato aggiunto il blocco.             |
| BlockId         | Identificatore univoco del blocco in Azure Blockchain Workbench. |
| TransactionHash | Hash della transazione.                                           |
| Da            | Indirizzo dell'iniziatore della transazione.                      |
| A              | Indirizzo del destinatario della transazione.              |
| Valore           | Valore incluso nella transazione.                                 |
| IsAppBuilderTx  | Specifica se si tratta di una transazione di Blockchain Workbench.                         |

``` csharp
public class InsertTransactionRequest : MessageModelBase
{
    public int ChainId { get; set; }
    public int BlockId { get; set; }
    public string TransactionHash { get; set; }
    public string From { get; set; }
    public string To { get; set; }
    public decimal Value { get; set; }
    public bool IsAppBuilderTx { get; set; }
}
```

### <a name="assigncontractchainidentifier"></a>AssignContractChainIdentifier

Fornisce i dettagli sull'assegnazione di un identificatore di catena per un contratto. Ad esempio, nella blockchain Ethereum, è l'indirizzo di un contratto nel libro mastro.

| NOME            | DESCRIZIONE                                                                       |
|-----------------|-----------------------------------------------------------------------------------|
| ContractId      | Identificatore univoco del contratto in Azure Blockchain Workbench. |
| ChainIdentifier | Identificatore del contratto nella catena.                             |

``` csharp
public class AssignContractChainIdentifierRequest : MessageModelBase
{
    public int ContractId { get; set; }
    public string ChainIdentifier { get; set; }
}
```

## <a name="classes-used-by-message-types"></a>Classi usate dai tipi di messaggio

### <a name="messagemodelbase"></a>MessageModelBase

Modello di base per tutti i messaggi.

| NOME          | DESCRIZIONE                          |
|---------------|--------------------------------------|
| OperationName | Nome dell'operazione.           |
| RequestId     | Identificatore univoco per la richiesta. |

``` csharp
public class MessageModelBase
{
    public string OperationName { get; set; }
    public string RequestId { get; set; }
}
```

### <a name="contractinputparameter"></a>ContractInputParameter

Contiene il nome, il valore e il tipo di un parametro.

| NOME  | DESCRIZIONE                 |
|-------|-----------------------------|
| NOME  | Nome del parametro.  |
| Valore | Valore del parametro. |
| type  | Il tipo del parametro.  |

``` csharp
public class ContractInputParameter
{
    public string Name { get; set; }
    public string Value { get; set; }
    public string Type { get; set; }
}
```

#### <a name="contractproperty"></a>ContractProperty

Contiene l'ID, il nome, il valore e il tipo di una proprietà.

| NOME  | DESCRIZIONE                |
|-------|----------------------------|
| ID    | ID della proprietà.    |
| NOME  | Nome della proprietà.  |
| Valore | Valore della proprietà. |
| type  | Tipo della proprietà.  |

``` csharp
public class ContractProperty
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string Value { get; set; }
    public string DataType { get; set; }
}
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Modelli di integrazione dei contratti intelligenti](integration-patterns.md)