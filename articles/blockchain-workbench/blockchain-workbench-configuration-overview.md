---
title: Informazioni di riferimento sulla configurazione di Azure Blockchain Workbench
description: Panoramica della configurazione dell'applicazione Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 3/20/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 6318b6aafda972242239ce91abb0f67a15721e1e
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2018
---
# <a name="azure-blockchain-workbench-configuration-reference"></a>Informazioni di riferimento sulla configurazione di Azure Blockchain Workbench

 Le applicazioni Azure Blockchain Workbench sono flussi di lavoro in più parti definiti dai metadati di configurazione e dal codice dei contratti intelligenti. I metadati di configurazione definiscono i flussi di lavoro di alto livello e il modello di interazione dell'applicazione blockchain. I contratti intelligenti definiscono la logica di business dell'applicazione blockchain. Workbench usa il codice di configurazione e dei contratti intelligenti per generare le esperienze utente dell'applicazione blockchain.

I metadati di configurazione specificano le informazioni seguenti per ogni applicazione blockchain: 

* Nome e descrizione dell'applicazione blockchain
* Ruoli univoci per gli utenti che possono operare o partecipare all'interno dell'applicazione blockchain
* Uno o più flussi di lavoro. Ogni flusso di lavoro opera come macchina a stati per controllare il flusso della logica di business. I flussi di lavoro possono essere indipendenti o interagire tra loro.

Ogni flusso di lavoro definito specifica quanto segue:

* Nome e descrizione del flusso di lavoro
* Stati del flusso di lavoro.  Ogni stato è una fase nel flusso di controllo della logica di business. 
* Azioni per la transizione allo stato successivo
* Ruoli utente consentiti per avviare ogni azione
* Contratti intelligenti che rappresentano la logica di business nei file di codice

## <a name="application"></a>Applicazione

Un'applicazione blockchain contiene metadati di configurazione, flussi di lavoro e ruoli utente che possono operare o partecipare all'interno dell'applicazione.

| Campo | DESCRIZIONE | Obbligatoria |
|-------|-------------|:--------:|
| ApplicationName | Nome applicazione univoco. Il contratto intelligente corrispondente deve usare lo stesso valore di **ApplicationName** per la classe di contratto applicabile.  | Sì |
| DisplayName | Nome visualizzato descrittivo dell'applicazione. | Sì |
| DESCRIZIONE | Descrizione dell'applicazione. | No  |
| ApplicationRoles | Raccolta di [ruoli applicazione](#application-roles). Ruoli utente che possono operare o partecipare all'interno dell'applicazione.  | Sì |
| Flussi di lavoro | Raccolta di [flussi di lavoro](#workflows). Ogni flusso di lavoro opera come macchina a stati per controllare il flusso della logica di business. | Sì |

Per un esempio, vedere il [file di configurazione di esempio](#configuration-file-example).

## <a name="workflows"></a>Flussi di lavoro

La logica di business di un'applicazione può essere modellata come macchina a stati in cui l'esecuzione di un'azione fa sì che il flusso della logica di business passi da uno stato a un altro. Un flusso di lavoro è una raccolta di questi stati e azioni. Ogni flusso di lavoro è costituito da uno o più contratti intelligenti, che rappresentano la logica di business nei file di codice. Un contratto eseguibile è un'istanza di un flusso di lavoro.

| Campo | DESCRIZIONE | Obbligatoria |
|-------|-------------|:--------:|
| NOME | Nome univoco del flusso di lavoro. Il contratto intelligente corrispondente deve usare lo stesso valore di **Name** per la classe di contratto applicabile. | Sì |
| DisplayName | Nome visualizzato descrittivo del flusso di lavoro. | Sì |
| DESCRIZIONE | Descrizione del flusso di lavoro. | No  |
| Initiators | Raccolta di [ruoli applicazione](#application-roles). Ruoli assegnati agli utenti autorizzati a creare contratti nel flusso di lavoro. | Sì |
| StartState | Nome dello stato iniziale del flusso di lavoro. | Sì |
| Properties | Raccolta di [identificatori](#identifiers). Rappresenta i dati che possono essere letti off-chain o visualizzati in uno strumento di visualizzazione dell'esperienza utente. | Sì |
| Costruttore | Definisce i parametri di input per la creazione di un'istanza del flusso di lavoro. | Sì |
| Funzioni | Raccolta di [funzioni](#functions) che possono essere eseguite nel flusso di lavoro. | Sì |
| Stati | Raccolta di [stati](#states) del flusso di lavoro. | Sì |

Per un esempio, vedere il [file di configurazione di esempio](#configuration-file-example).

## <a name="type"></a>type

Tipi di dati supportati.

| type | DESCRIZIONE |
|-------|-------------|
| Address  | Tipo di indirizzo della blockchain, ad esempio *contratto* o *utente* |
| bool     | Dati di tipo booleano |
| contract | Indirizzo di tipo contratto |
| int      | Dati di tipo Integer |
| money    | Dati di tipo Money |
| state    | Stato del flusso di lavoro |
| stringa   | Dati di tipo stringa |
| user     | Indirizzo di tipo utente |
| time     | Dati di tipo Time |
|`[ Application Role Name ]`| Qualsiasi nome specificato nel ruolo applicazione. Limita gli utenti a questo tipo di ruolo. |

## <a name="constructor"></a>Costruttore

Definisce i parametri di input per un'istanza di un flusso di lavoro.

| Campo | DESCRIZIONE | Obbligatoria |
|-------|-------------|:--------:|
| Parametri | Raccolta di [identificatori](#identifiers) richiesti per avviare un contratto intelligente. | Sì |

### <a name="constructor-example"></a>Esempio di costruttore

``` json
{
  "Parameters": [
    {
      "Name": "description",
      "Description": "The description of this asset",
      "DisplayName": "Description",
      "Type": {
        "Name": "string"
      }
    },
    {
      "Name": "price",
      "Description": "The price of this asset",
      "DisplayName": "Price",
      "Type": {
        "Name": "money"
      }
    }
  ]
}

```

## <a name="functions"></a>Funzioni

Definisce le funzioni che possono essere eseguite nel flusso di lavoro.

| Campo | DESCRIZIONE | Obbligatoria |
|-------|-------------|:--------:|
| NOME | Nome univoco della funzione. Il contratto intelligente corrispondente deve usare lo stesso valore di **Name** per la funzione applicabile. | Sì |
| DisplayName | Nome visualizzato descrittivo della funzione. | Sì |
| DESCRIZIONE | Descrizione della funzione | No  |
| Parametri | Raccolta di [identificatori](#identifiers) corrispondenti ai parametri della funzione. | Sì |

### <a name="functions-example"></a>Esempio di funzioni

``` json
"Functions": [
  {
    "Name": "Modify",
    "DisplayName": "Modify",
    "Description": "Modify the description/price attributes of this asset transfer instance",
    "Parameters": [
      {
        "Name": "description",
        "Description": "The new description of the asset",
        "DisplayName": "Description",
        "Type": {
          "Name": "string"
        }
      },
      {
        "Name": "price",
        "Description": "The new price of the asset",
        "DisplayName": "Price",
        "Type": {
          "Name": "money"
        }
      }
    ]
  },
  {
    "Name": "Terminate",
    "DisplayName": "Terminate",
    "Description": "Used to cancel this particular instance of asset transfer",
    "Parameters": []
  }
]

```

## <a name="states"></a>Stati

Raccolta di stati univoci all'interno di un flusso di lavoro. Ogni stato acquisisce una fase nel flusso di controllo della logica di business. 

| Campo | DESCRIZIONE | Obbligatoria |
|-------|-------------|:--------:|
| NOME | Nome univoco dello stato. Il contratto intelligente corrispondente deve usare lo stesso valore di **Name** per lo stato applicabile. | Sì |
| DisplayName | Nome visualizzato descrittivo dello stato. | Sì |
| DESCRIZIONE | Descrizione dello stato. | No  |
| PercentComplete | Valore intero visualizzato nell'interfaccia utente di Blockchain Workbench per mostrare lo stato di avanzamento all'interno del flusso di controllo della logica di business. | Sì |
| Style | Indicatore visivo che specifica se lo stato è di esito positivo o negativo. I due valori validi sono `Success` e `Failure`. | Sì |
| Transizioni | Raccolta di [transizioni](#transitions) disponibili dallo stato corrente al set successivo di stati. | No  |

### <a name="states-example"></a>Esempio di stati

``` json
"States": [
    {
      "Name": "Active",
      "DisplayName": "Active",
      "Description": "The initial state of the asset transfer workflow",
      "PercentComplete": 20,
      "Style": "Success",
      "Transitions": [
        {
          "AllowedRoles": [],
          "AllowedInstanceRoles": [ "InstanceOwner" ],
          "Description": "Cancels this instance of asset transfer",
          "Function": "Terminate",
          "NextStates": [ "Terminated" ],
          "DisplayName": "Terminate Offer"
        },
        {
          "AllowedRoles": [ "Buyer" ],
          "AllowedInstanceRoles": [],
          "Description": "Make an offer for this asset",
          "Function": "MakeOffer",
          "NextStates": [ "OfferPlaced" ],
          "DisplayName": "Make Offer"
        },
        {
          "AllowedRoles": [],
          "AllowedInstanceRoles": [ "InstanceOwner" ],
          "Description": "Modify attributes of this asset transfer instance",
          "Function": "Modify",
          "NextStates": [ "Active" ],
          "DisplayName": "Modify"
        }
      ]
    },
    {
      "Name": "Accepted",
      "DisplayName": "Accepted",
      "Description": "Asset transfer process is complete",
      "PercentComplete": 100,
      "Style": "Success",
      "Transitions": []
    },
    {
      "Name": "Terminated",
      "DisplayName": "Terminated",
      "Description": "Asset transfer has been cancelled",
      "PercentComplete": 100,
      "Style": "Failure",
      "Transitions": []
    }
  ]
```

## <a name="transitions"></a>Transizioni

Azioni disponibili per lo stato successivo. Uno o più ruoli utente possono eseguire un'azione in ogni stato, in cui un'azione può passare da uno stato a un altro nel flusso di lavoro. 

| Campo | DESCRIZIONE | Obbligatoria |
|-------|-------------|:--------:|
| AllowedRoles | Elenco di ruoli applicazione consentiti per avviare la transizione. Tutti gli utenti del ruolo specificato possono essere in grado di eseguire l'azione. | No  |
| AllowedInstanceRoles | Elenco di ruoli utente che fanno parte o che sono specificati nel contratto intelligente cui è consentito avviare la transizione. I ruoli di istanza vengono definiti in **proprietà** all'interno dei flussi di lavoro. Questi utenti rappresentano un utente che fa parte o è specificato nel contratto intelligente, invece di tutti gli utenti di un tipo di ruolo. | No  |
| DisplayName | Nome visualizzato descrittivo della transizione. | Sì |
| DESCRIZIONE | Descrizione della transizione. | No  |
| Funzione | Nome della funzione per avviare la transizione. | Sì |
| NextStates | Raccolta dei possibili stati successivi dopo una transizione riuscita. | Sì |

### <a name="transitions-example"></a>Esempio di transizioni

``` json
"Transitions": [
  {
    "AllowedRoles": [],
    "AllowedInstanceRoles": [ "InstanceOwner" ],
    "Description": "Cancels this instance of asset transfer",
    "Function": "Terminate",
    "NextStates": [ "Terminated" ],
    "DisplayName": "Terminate Offer"
  },
  {
    "AllowedRoles": [ "Buyer" ],
    "AllowedInstanceRoles": [],
    "Description": "Make an offer for this asset",
    "Function": "MakeOffer",
    "NextStates": [ "OfferPlaced" ],
    "DisplayName": "Make Offer"
  },
  {
    "AllowedRoles": [],
    "AllowedInstanceRoles": [ "InstanceOwner" ],
    "Description": "Modify attributes of this asset transfer instance",
    "Function": "Modify",
    "NextStates": [ "Active" ],
    "DisplayName": "Modify"
  }
]

```

## <a name="application-roles"></a>Ruoli applicazione

I ruoli applicazione definiscono un set di ruoli che possono essere assegnati agli utenti che vogliono operare o partecipare all'interno dell'applicazione. I ruoli applicazione possono essere usati per limitare le azioni e la partecipazione all'interno dell'applicazione blockchain e dei flussi di lavoro corrispondenti. 

| Campo | DESCRIZIONE | Obbligatoria |
|-------|-------------|:--------:|
| NOME | Identificatore univoco del ruolo applicazione. Il contratto intelligente corrispondente deve usare lo stesso valore di **Name** per il ruolo applicabile. I nomi del tipo di base sono riservati. Non è possibile assegnare a un ruolo applicazione lo stesso nome come [tipo](#type)| Sì |
| DESCRIZIONE | Descrizione del ruolo applicazione. | No  |

### <a name="application-roles-example"></a>Esempio di ruoli applicazione

``` json
"ApplicationRoles": [
  {
    "Name": "Appraiser",
    "Description": "User that signs off on the asset price"
  },
  {
    "Name": "Buyer",
    "Description": "User that places an offer on an asset"
  }
]
```
## <a name="identifiers"></a>Identificatori

Gli identificatori rappresentano una raccolta di informazioni usate per descrivere le proprietà, il costruttore e i parametri di funzione del flusso di lavoro. 

| Campo | DESCRIZIONE | Obbligatoria |
|-------|-------------|:--------:|
| NOME | Nome univoco della proprietà o del parametro. Il contratto intelligente corrispondente deve usare lo stesso valore di **Name** per la proprietà o il parametro applicabile. | Sì |
| DisplayName | Nome visualizzato descrittivo per la proprietà o il parametro. | Sì |
| DESCRIZIONE | Descrizione della proprietà o del parametro. | No  |

### <a name="identifiers-example"></a>Esempio di identificatori

``` json
"Properties": [
  {
    "Name": "State",
    "DisplayName": "State",
    "Description": "Holds the state of the contract",
    "Type": {
      "Name": "state"
    }
  },
  {
    "Name": "Description",
    "DisplayName": "Description",
    "Description": "Describes the asset being sold",
    "Type": {
      "Name": "string"
    }
  }
]
```

## <a name="configuration-file-example"></a>File di configurazione di esempio

L'esempio seguente definisce un'applicazione di richiesta-risposta di base in cui un richiedente invia una richiesta e un risponditore invia una risposta alla richiesta.

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
      "Name": "RequestResponse",
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
## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Distribuire Azure Blockchain Workbench](blockchain-workbench-deploy.md)

