---
title: Copiare dati da Xero usando Azure Data Factory (beta) | Microsoft Docs
description: "Informazioni su come copiare dati da Xero in archivi dati di sink supportati usando un'attività di copia in una pipeline di Azure Data Factory."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2018
ms.author: jingwang
ms.openlocfilehash: 458ad702b510c0fd01ab63541b2026b8a9a06e91
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2018
---
# <a name="copy-data-from-xero-using-azure-data-factory-beta"></a>Copiare dati da Xero usando Azure Data Factory (beta)

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da e in Xero. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere [Attività di copia nella versione 1](v1/data-factory-data-movement-activities.md).

> [!IMPORTANT]
> Questo connettore è attualmente disponibile in versione beta. È possibile provarlo e inviare commenti e suggerimenti. Non usarlo in ambienti di produzione.

## <a name="supported-capabilities"></a>Funzionalità supportate

È possibile copiare dati da Xero a qualsiasi archivio dati di sink supportato. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

In particolare, il connettore Xero supporta:

- L'[applicazione privata](https://developer.xero.com/documentation/getting-started/api-application-types) Xero, ma non l'applicazione pubblica.
- Tutte le tabelle di Xero (endpoint dell'API), ad eccezione di "Reports". 

Azure Data Factory offre un driver predefinito per consentire la connettività, pertanto non è necessario installare manualmente alcun driver usando questo connettore.

## <a name="getting-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started-2](../../includes/data-factory-v2-connector-get-started-2.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità di Data Factory specifiche per il connettore Xero.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato Xero sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su **Xero** | Sì |
| host | Endpoint del server Xero (`api.xero.com`).  | Sì |
| consumerKey | Chiave utente associata all'applicazione Xero. Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Azure Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | Sì |
| privateKey | Chiave privata dal file con estensione pem generato per l'applicazione Xero privata; vedere [Create a public/private key pair](https://developer.xero.com/documentation/api-guides/create-publicprivate-key) (Creare una coppia di chiavi pubblica/privata). Includere tutto il testo dal file con estensione pem, incluse le terminazioni riga Unix (\n): vedere l'esempio seguente.<br/>Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Azure Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | Sì |
| useEncryptedEndpoints | Specifica se gli endpoint dell'origine dati vengono crittografati tramite HTTPS. Il valore predefinito è true.  | No  |
| useHostVerification | Specifica se è necessario che il nome host nel certificato del server corrisponda al nome host del server per la connessione tramite SSL. Il valore predefinito è true.  | No  |
| usePeerVerification | Specifica se verificare l'identità del server durante la connessione tramite SSL. Il valore predefinito è true.  | No  |

**Esempio:**

```json
{
    "name": "XeroLinkedService",
    "properties": {
        "type": "Xero",
        "typeProperties": {
            "host" : "api.xero.com",
            "consumerKey": {
                 "type": "SecureString",
                 "value": "<consumerKey>"
            },
            "privateKey": {
                 "type": "SecureString",
                 "value": "<privateKey>"
            }
        }
    }
}
```

**Valore di chiave privata di esempio:**

Includere tutto il testo dal file con estensione pem, incluse le terminazioni riga Unix (\n).

```
"-----BEGIN RSA PRIVATE KEY-----\nMII***************************************************P\nbu****************************************************s\nU/****************************************************B\nA*****************************************************W\njH****************************************************e\nsx*****************************************************l\nq******************************************************X\nh*****************************************************i\nd*****************************************************s\nA*****************************************************dsfb\nN*****************************************************M\np*****************************************************Ly\nK*****************************************************Y=\n-----END RSA PRIVATE KEY-----"
```

## <a name="dataset-properties"></a>Proprietà dei set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui [set di dati](concepts-datasets-linked-services.md). Questa sezione presenta un elenco delle proprietà supportate dal set di dati Xero.

Per copiare dati da Xero, impostare la proprietà type del set di dati su **XeroObject**. Non sono presenti proprietà aggiuntive specifiche del tipo in questo tipo di set di dati.

**Esempio**

```json
{
    "name": "XeroDataset",
    "properties": {
        "type": "XeroObject",
        "linkedServiceName": {
            "referenceName": "<Xero linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine Xero.

### <a name="xero-as-source"></a>Xero come origine

Per copiare dati da Xero, impostare il tipo di origine nell'attività di copia su **XeroSource**. Nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata su **XeroSource** | Sì |
| query | Usare la query SQL personalizzata per leggere i dati. Ad esempio: `"SELECT * FROM Contacts"`. | Sì |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromXero",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Xero input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "XeroSource",
                "query": "SELECT * FROM Contacts"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Notare quanto segue quando si specifica la query Xero:

- Le tabelle con elementi complessi verranno suddivise in più tabelle. Le transazioni bancarie, ad esempio, hanno una struttura di dati complessa "LineItems" e i dati delle transazioni vengono quindi mappati alla tabella `Bank_Transaction` e a `Bank_Transaction_Line_Items`, con `Bank_Transaction_ID` come chiave esterna per collegarli tra loro.

- I dati Xero sono disponibili tramite due schemi: `Minimal` (impostazione predefinita) e `Complete`. Lo schema Complete contiene tabelle di chiamate obbligatorie che richiedono dati aggiuntivi (ad esempio, la colonna ID) per poter eseguire la query desiderata.

Le tabelle seguenti contengono le stesse informazioni degli schemi Minimal e Complete. Per ridurre il numero di chiamate API, usare uno schema Minimal (impostazione predefinita).

- Bank_Transactions
- Contact_Groups 
- Contatti 
- Contacts_Sales_Tracking_Categories 
- Contacts_Phones 
- Contacts_Addresses 
- Contacts_Purchases_Tracking_Categories 
- Credit_Notes 
- Credit_Notes_Allocations 
- Expense_Claims 
- Expense_Claim_Validation_Errors
- Fatture 
- Invoices_Credit_Notes
- Invoices_ Prepayments 
- Invoices_Overpayments 
- Manual_Journals 
- Overpayments 
- Overpayments_Allocations 
- Prepayments 
- Prepayments_Allocations 
- Receipts 
- Receipt_Validation_Errors 
- Tracking_Categories

Nelle tabelle seguenti è possibile eseguire query solo con lo schema Complete:

- Complete.Bank_Transaction_Line_Items 
- Complete.Bank_Transaction_Line_Item_Tracking 
- Complete.Contact_Group_Contacts 
- Complete.Contacts_Contact_ Persons 
- Complete.Credit_Note_Line_Items 
- Complete.Credit_Notes_Line_Items_Tracking 
- Complete.Expense_Claim_ Payments 
- Complete.Expense_Claim_Receipts 
- Complete.Invoice_Line_Items 
- Complete.Invoices_Line_Items_Tracking
- Complete.Manual_Journal_Lines 
- Complete.Manual_Journal_Line_Tracking 
- Complete.Overpayment_Line_Items 
- Complete.Overpayment_Line_Items_Tracking 
- Complete.Prepayment_Line_Items 
- Complete.Prepayment_Line_Item_Tracking 
- Complete.Receipt_Line_Items 
- Complete.Receipt_Line_Item_Tracking 
- Complete.Tracking_Category_Options

## <a name="next-steps"></a>Passaggi successivi
Per un elenco di archivi dati supportati dall'attività di copia, vedere [Archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
