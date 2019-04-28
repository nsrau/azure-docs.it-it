---
title: Informazioni di riferimento sulla configurazione di Azure Blockchain Workbench
description: Panoramica della configurazione dell'applicazione Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 04/15/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: 4d29d8e86a30f105c4aa50ec9615f8165fa238d3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60578981"
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

| Campo | DESCRIZIONE | Obbligatorio |
|-------|-------------|:--------:|
| ApplicationName | Nome applicazione univoco. Il contratto intelligente corrispondente deve usare lo stesso valore di **ApplicationName** per la classe di contratto applicabile.  | Sì |
| DisplayName | Nome visualizzato descrittivo dell'applicazione. | Sì |
| DESCRIZIONE | Descrizione dell'applicazione. | No  |
| ApplicationRoles | Raccolta di [ruoli applicazione](#application-roles). Ruoli utente che possono operare o partecipare all'interno dell'applicazione.  | Sì |
| Flussi di lavoro | Raccolta di [flussi di lavoro](#workflows). Ogni flusso di lavoro opera come macchina a stati per controllare il flusso della logica di business. | Sì |

Per un esempio, vedere il [file di configurazione di esempio](#configuration-file-example).

## <a name="workflows"></a>Flussi di lavoro

La logica di business di un'applicazione può essere modellata come macchina a stati in cui l'esecuzione di un'azione fa sì che il flusso della logica di business passi da uno stato a un altro. Un flusso di lavoro è una raccolta di questi stati e azioni. Ogni flusso di lavoro è costituito da uno o più contratti intelligenti, che rappresentano la logica di business nei file di codice. Un contratto eseguibile è un'istanza di un flusso di lavoro.

| Campo | DESCRIZIONE | Obbligatorio | Lunghezza massima |
|-------|-------------|:--------:|-----------:|
| NOME | Nome univoco del flusso di lavoro. Il contratto intelligente corrispondente deve usare lo stesso valore di **Name** per la classe di contratto applicabile. | Sì | 50 |
| DisplayName | Nome visualizzato descrittivo del flusso di lavoro. | Sì | 255 |
| DESCRIZIONE | Descrizione del flusso di lavoro. | No  | 255 |
| Initiators | Raccolta di [ruoli applicazione](#application-roles). Ruoli assegnati agli utenti autorizzati a creare contratti nel flusso di lavoro. | Sì | |
| StartState | Nome dello stato iniziale del flusso di lavoro. | Sì | |
| Properties | Raccolta di [identificatori](#identifiers). Rappresenta i dati che possono essere letti off-chain o visualizzati in uno strumento di visualizzazione dell'esperienza utente. | Sì | |
| Costruttore | Definisce i parametri di input per la creazione di un'istanza del flusso di lavoro. | Sì | |
| Funzioni | Raccolta di [funzioni](#functions) che possono essere eseguite nel flusso di lavoro. | Sì | |
| Stati | Raccolta di [stati](#states) del flusso di lavoro. | Sì | |

Per un esempio, vedere il [file di configurazione di esempio](#configuration-file-example).

## <a name="type"></a>Type

Tipi di dati supportati.

| Type | DESCRIZIONE |
|-------|-------------|
| Address  | Tipo di indirizzo della blockchain, ad esempio *contratti* o *utenti*. |
| array    | Matrice a livello singolo di tipo integer, bool, money o time. Le matrici possono essere statiche o dinamiche. Usare **ElementType** per specificare il tipo di dati degli elementi all'interno della matrice. Vedere una [configurazione di esempio](#example-configuration-of-type-array). |
| bool     | Tipo di dati booleano. |
| contract | Indirizzo di tipo contratto. |
| enum     | Set enumerato di valori denominati. Quando si usa il tipo di enumerazione, è necessario specificare anche un elenco di EnumValues. Ogni valore non può superare i 255 caratteri. I caratteri di valore validi includono lettere maiuscole e minuscole (A-Z, a-z) e numeri (0-9). Vedere un [esempio di configurazione e uso in Solidity](#example-configuration-of-type-enum). |
| int      | Tipo di dati Integer. |
| money    | Tipo di dati Money. |
| state    | Stato del flusso di lavoro. |
| string  | Tipo di dati stringa. 4000 caratteri al massimo. Vedere una [configurazione di esempio](#example-configuration-of-type-string). |
| user     | Indirizzo di tipo utente. |
| time     | Dati di tipo Time. |
|`[ Application Role Name ]`| Qualsiasi nome specificato nel ruolo applicazione. Limita gli utenti a questo tipo di ruolo. |

### <a name="example-configuration-of-type-array"></a>Esempio di configurazione di tipo array

```json
{
  "Name": "Quotes",
  "Description": "Market quotes",
  "DisplayName": "Quotes",
  "Type": {
    "Name": "array",
    "ElementType": {
        "Name": "int"
    }
  }
}
```

#### <a name="using-a-property-of-type-array"></a>Uso di una proprietà di tipo array

Se nella configurazione si definisce una proprietà di tipo array, è necessario includere una funzione get esplicita per restituire la proprietà pubblica del tipo array in Solidity. Ad esempio: 

```
function GetQuotes() public constant returns (int[]) {
     return Quotes;
}
```

### <a name="example-configuration-of-type-string"></a>Esempio di configurazione di un tipo di stringa

``` json
{
  "Name": "description",
  "Description": "Descriptive text",
  "DisplayName": "Description",
  "Type": {
    "Name": "string"
  }
}
```

### <a name="example-configuration-of-type-enum"></a>Esempio di configurazione di un tipo di enumerazione

``` json
{
  "Name": "PropertyType",
  "DisplayName": "Property Type",
  "Description": "The type of the property",
  "Type": {
    "Name": "enum",
    "EnumValues": ["House", "Townhouse", "Condo", "Land"]
  }
}
```

#### <a name="using-enumeration-type-in-solidity"></a>Utilizzo di un tipo di enumerazione in Solidity

Dopo aver definito un'enumerazione nella configurazione, è possibile usare un tipo di enumerazione in Solidity. È possibile, ad esempio, definire un'enumerazione denominata PropertyTypeEnum.

```
enum PropertyTypeEnum {House, Townhouse, Condo, Land} PropertyTypeEnum public PropertyType; 
```

È necessario che l'elenco di stringhe della configurazione corrisponda a quello del contratto intelligente perché sia valido e generi dichiarazioni coerenti in Blockchain Workbench.

Esempio di assegnazione:

```
PropertyType = PropertyTypeEnum.Townhouse;
```

Esempio di parametro di funzione: 

``` 
function AssetTransfer(string description, uint256 price, PropertyTypeEnum propertyType) public
{
    InstanceOwner = msg.sender;
    AskingPrice = price;
    Description = description;
    PropertyType = propertyType;
    State = StateType.Active;
    ContractCreated();
}

```

## <a name="constructor"></a>Costruttore

Definisce i parametri di input per un'istanza di un flusso di lavoro.

| Campo | DESCRIZIONE | Obbligatorio |
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

| Campo | DESCRIZIONE | Obbligatorio | Lunghezza massima |
|-------|-------------|:--------:|-----------:|
| NOME | Nome univoco della funzione. Il contratto intelligente corrispondente deve usare lo stesso valore di **Name** per la funzione applicabile. | Sì | 50 |
| DisplayName | Nome visualizzato descrittivo della funzione. | Sì | 255 |
| DESCRIZIONE | Descrizione della funzione | No  | 255 |
| Parametri | Raccolta di [identificatori](#identifiers) corrispondenti ai parametri della funzione. | Sì | |

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

| Campo | DESCRIZIONE | Obbligatorio | Lunghezza massima |
|-------|-------------|:--------:|-----------:|
| NOME | Nome univoco dello stato. Il contratto intelligente corrispondente deve usare lo stesso valore di **Name** per lo stato applicabile. | Sì | 50 |
| DisplayName | Nome visualizzato descrittivo dello stato. | Sì | 255 |
| DESCRIZIONE | Descrizione dello stato. | No  | 255 |
| PercentComplete | Valore intero visualizzato nell'interfaccia utente di Blockchain Workbench per mostrare lo stato di avanzamento all'interno del flusso di controllo della logica di business. | Sì | |
| Style | Indicatore visivo che specifica se lo stato è di esito positivo o negativo. I due valori validi sono `Success` e `Failure`. | Sì | |
| Transizioni | Raccolta di [transizioni](#transitions) disponibili dallo stato corrente al set successivo di stati. | No  | |

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
      "Description": "Asset transfer has been canceled",
      "PercentComplete": 100,
      "Style": "Failure",
      "Transitions": []
    }
  ]
```

## <a name="transitions"></a>Transizioni

Azioni disponibili per lo stato successivo. Uno o più ruoli utente possono eseguire un'azione in ogni stato, in cui un'azione può passare da uno stato a un altro nel flusso di lavoro. 

| Campo | DESCRIZIONE | Obbligatorio |
|-------|-------------|:--------:|
| AllowedRoles | Elenco di ruoli applicazione consentiti per avviare la transizione. Tutti gli utenti del ruolo specificato possono essere in grado di eseguire l'azione. | No  |
| AllowedInstanceRoles | Elenco di ruoli utente che fanno parte o che sono specificati nel contratto intelligente cui è consentito avviare la transizione. I ruoli di istanza vengono definiti in **proprietà** all'interno dei flussi di lavoro. AllowedInstanceRoles rappresenta un utente che fa parte di un'istanza di un contratto intelligente. AllowedInstanceRoles consente di limitare l'adozione di un'azione a un ruolo utente in un'istanza del contratto.  Ad esempio, è possibile consentire solo all'utente che ha creato il contratto (InstanceOwner) di terminarlo anziché a tutti gli utenti nel tipo di ruolo (Owner), se il ruolo è stato specificato in AllowedRoles. | No  |
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

| Campo | DESCRIZIONE | Obbligatorio | Lunghezza massima |
|-------|-------------|:--------:|-----------:|
| NOME | Identificatore univoco del ruolo applicazione. Il contratto intelligente corrispondente deve usare lo stesso valore di **Name** per il ruolo applicabile. I nomi del tipo di base sono riservati. Non è possibile assegnare a un ruolo applicazione lo stesso nome come [tipo](#type)| Sì | 50 |
| DESCRIZIONE | Descrizione del ruolo applicazione. | No  | 255 |

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

| Campo | DESCRIZIONE | Obbligatorio | Lunghezza massima |
|-------|-------------|:--------:|-----------:|
| NOME | Nome univoco della proprietà o del parametro. Il contratto intelligente corrispondente deve usare lo stesso valore di **Name** per la proprietà o il parametro applicabile. | Sì | 50 |
| DisplayName | Nome visualizzato descrittivo per la proprietà o il parametro. | Sì | 255 |
| DESCRIZIONE | Descrizione della proprietà o del parametro. | No  | 255 |

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

Il trasferimento di cespiti è uno scenario di contratto intelligente per l'acquisto e la vendita di cespiti di valore elevato, che richiedono un responsabile controllo e un responsabile valutazione. I venditori possono elencare i loro cespiti creando un'istanza di un contratto intelligente di trasferimento cespiti. Gli acquirenti possono presentare offerte eseguendo un'azione nel contratto intelligente e altre parti possono eseguire azioni per controllare o valutare il cespite. Dopo che il cespite è stato contrassegnato come controllato e valutato, l'acquirente e il venditore confermeranno di nuovo la vendita prima che il contratto sia impostato come completato. In ogni passaggio del processo tutti i partecipanti hanno la visibilità sullo stato del contratto mentre viene aggiornato. 

Per altre informazioni, inclusi i file di codice, vedere l'[esempio di trasferimento cespiti per Azure Blockchain Workbench](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer)

Il file di configurazione seguente è relativo all'esempio di trasferimento cespiti:

``` json
{
  "ApplicationName": "AssetTransfer",
  "DisplayName": "Asset Transfer",
  "Description": "Allows transfer of assets between a buyer and a seller, with appraisal/inspection functionality",
  "ApplicationRoles": [
    {
      "Name": "Appraiser",
      "Description": "User that signs off on the asset price"
    },
    {
      "Name": "Buyer",
      "Description": "User that places an offer on an asset"
    },
    {
      "Name": "Inspector",
      "Description": "User that inspects the asset and signs off on inspection"
    },
    {
      "Name": "Owner",
      "Description": "User that signs off on the asset price"
    }
  ],
  "Workflows": [
    {
      "Name": "AssetTransfer",
      "DisplayName": "Asset Transfer",
      "Description": "Handles the business logic for the asset transfer scenario",
      "Initiators": [ "Owner" ],
      "StartState":  "Active",
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
        },
        {
          "Name": "AskingPrice",
          "DisplayName": "Asking Price",
          "Description": "The asking price for the asset",
          "Type": {
            "Name": "money"
          }
        },
        {
          "Name": "OfferPrice",
          "DisplayName": "Offer Price",
          "Description": "The price being offered for the asset",
          "Type": {
            "Name": "money"
          }
        },
        {
          "Name": "InstanceAppraiser",
          "DisplayName": "Instance Appraiser",
          "Description": "The user that appraises the asset",
          "Type": {
            "Name": "Appraiser"
          }
        },
        {
          "Name": "InstanceBuyer",
          "DisplayName": "Instance Buyer",
          "Description": "The user that places an offer for this asset",
          "Type": {
            "Name": "Buyer"
          }
        },
        {
          "Name": "InstanceInspector",
          "DisplayName": "Instance Inspector",
          "Description": "The user that inspects this asset",
          "Type": {
            "Name": "Inspector"
          }
        },
        {
          "Name": "InstanceOwner",
          "DisplayName": "Instance Owner",
          "Description": "The seller of this particular asset",
          "Type": {
            "Name": "Owner"
          }
        }
      ],
      "Constructor": {
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
      },
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
        },
        {
          "Name": "MakeOffer",
          "DisplayName": "Make Offer",
          "Description": "Place an offer for this asset",
          "Parameters": [
            {
              "Name": "inspector",
              "Description": "Specify a user to inspect this asset",
              "DisplayName": "Inspector",
              "Type": {
                "Name": "Inspector"
              }
            },
            {
              "Name": "appraiser",
              "Description": "Specify a user to appraise this asset",
              "DisplayName": "Appraiser",
              "Type": {
                "Name": "Appraiser"
              }
            },
            {
              "Name": "offerPrice",
              "Description": "Specify your offer price for this asset",
              "DisplayName": "Offer Price",
              "Type": {
                "Name": "money"
              }
            }
          ]
        },
        {
          "Name": "Reject",
          "DisplayName": "Reject",
          "Description": "Reject the user's offer",
          "Parameters": []
        },
        {
          "Name": "AcceptOffer",
          "DisplayName": "Accept Offer",
          "Description": "Accept the user's offer",
          "Parameters": []
        },
        {
          "Name": "RescindOffer",
          "DisplayName": "Rescind Offer",
          "Description": "Rescind your placed offer",
          "Parameters": []
        },
        {
          "Name": "ModifyOffer",
          "DisplayName": "Modify Offer",
          "Description": "Modify the price of your placed offer",
          "Parameters": [
            {
              "Name": "offerPrice",
              "DisplayName": "Price",
              "Type": {
                "Name": "money"
              }
            }
          ]
        },
        {
          "Name": "Accept",
          "DisplayName": "Accept",
          "Description": "Accept the inspection/appraisal results",
          "Parameters": []
        },
        {
          "Name": "MarkInspected",
          "DisplayName": "Mark Inspected",
          "Description": "Mark the asset as inspected",
          "Parameters": []
        },
        {
          "Name": "MarkAppraised",
          "DisplayName": "Mark Appraised",
          "Description": "Mark the asset as appraised",
          "Parameters": []
        }
      ],
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
          "Name": "OfferPlaced",
          "DisplayName": "Offer Placed",
          "Description": "Offer has been placed for the asset",
          "PercentComplete": 30,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Accept the proposed offer for the asset",
              "Function": "AcceptOffer",
              "NextStates": [ "PendingInspection" ],
              "DisplayName": "Accept Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the proposed offer for the asset",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you previously placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Modify the price that you specified for your offer",
              "Function": "ModifyOffer",
              "NextStates": [ "OfferPlaced" ],
              "DisplayName": "Modify Offer"
            }
          ]
        },
        {
          "Name": "PendingInspection",
          "DisplayName": "Pending Inspection",
          "Description": "Asset is pending inspection",
          "PercentComplete": 40,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the offer",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel the offer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceInspector" ],
              "Description": "Mark this asset as inspected",
              "Function": "MarkInspected",
              "NextStates": [ "Inspected" ],
              "DisplayName": "Mark Inspected"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceAppraiser" ],
              "Description": "Mark this asset as appraised",
              "Function": "MarkAppraised",
              "NextStates": [ "Appraised" ],
              "DisplayName": "Mark Appraised"
            }
          ]
        },
        {
          "Name": "Inspected",
          "DisplayName": "Inspected",
          "PercentComplete": 45,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the offer",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel the offer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceAppraiser" ],
              "Description": "Mark this asset as appraised",
              "Function": "MarkAppraised",
              "NextStates": [ "NotionalAcceptance" ],
              "DisplayName": "Mark Appraised"
            }
          ]
        },
        {
          "Name": "Appraised",
          "DisplayName": "Appraised",
          "Description": "Asset has been appraised, now awaiting inspection",
          "PercentComplete": 45,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the offer",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel the offer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceInspector" ],
              "Description": "Mark the asset as inspected",
              "Function": "MarkInspected",
              "NextStates": [ "NotionalAcceptance" ],
              "DisplayName": "Mark Inspected"
            }
          ]
        },
        {
          "Name": "NotionalAcceptance",
          "DisplayName": "Notional Acceptance",
          "Description": "Asset has been inspected and appraised, awaiting final sign-off from buyer and seller",
          "PercentComplete": 50,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "SellerAccepted" ],
              "DisplayName": "SellerAccept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the proposed offer for the asset",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "BuyerAccepted" ],
              "DisplayName": "BuyerAccept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            }
          ]
        },
        {
          "Name": "BuyerAccepted",
          "DisplayName": "Buyer Accepted",
          "Description": "Buyer has signed-off on inspection and appraisal",
          "PercentComplete": 75,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "SellerAccepted" ],
              "DisplayName": "Accept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the proposed offer for the asset",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            }
          ]
        },
        {
          "Name": "SellerAccepted",
          "DisplayName": "Seller Accepted",
          "Description": "Seller has signed-off on inspection and appraisal",
          "PercentComplete": 75,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "Accepted" ],
              "DisplayName": "Accept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
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
          "Description": "Asset transfer has been canceled",
          "PercentComplete": 100,
          "Style": "Failure",
          "Transitions": []
        }
      ]
    }
  ]
}
```
## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Riferimenti all'API REST di Azure Blockchain Workbench](https://docs.microsoft.com/rest/api/azure-blockchain-workbench)

