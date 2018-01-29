---
title: Utilizzo API correlate domande frequenti | Documenti Microsoft
description: Elenco di misuratori di Stack di Azure, confronto con API di Azure-utilizzo, tempo di utilizzo e l'ora di segnalato, codici di errore.
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 847f18b2-49a9-4931-9c09-9374e932a071
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2018
ms.author: alfredop
ms.openlocfilehash: 65b9ff0881e46836d9f19a04cf470835679e7b2f
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2018
---
# <a name="frequently-asked-questions-in-azure-stack-usage-api"></a>Domande frequenti nell'utilizzo di Azure Stack API
Questo articolo risponde ad alcune domande frequenti sull'API di utilizzo dello Stack di Azure.

## <a name="what-meter-ids-can-i-see"></a>Il misuratore ID è possibile vedere
Viene segnalato l'utilizzo per i provider di risorse seguenti:

| **Provider di risorse** | **ID contatore** | **Nome del misuratore** | **Unità** | **Informazioni aggiuntive** |
| --- | --- | --- | --- | --- |
| **Rete** |F271A8A388C44D93956A063E1D2FA80B |Utilizzo degli indirizzi IP statici |Indirizzi IP| Numero di indirizzi IP utilizzati |
| |9E2739BA86744796B465F64674B822BA |Utilizzo degli indirizzi IP dinamici |Indirizzi IP| Numero di indirizzi IP utilizzati |
| **Archiviazione** |B4438D5D-453B-4EE1-B42A-DC72E377F1E4 |TableCapacity |GB\*ore |Capacità totale utilizzata dalle tabelle |
| |B5C15376-6C94-4FDD-B655-1A69D138ACA3 |PageBlobCapacity |GB\*ore |Capacità totale utilizzata dal BLOB di pagine |
| |B03C6AE7-B080-4BFA-84A3-22C800F315C6 |QueueCapacity |GB\*ore |Capacità totale utilizzata dalla coda |
| |09F8879E-87E9-4305-A572-4B7BE209F857 |BlockBlobCapacity |GB\*ore |Capacità totale utilizzata dal BLOB in blocchi |
| |B9FF3CD0-28AA-4762-84BB-FF8FBAEA6A90 |TableTransactions |Numero di richieste in 10, 000's |Tabella le richieste del servizio (10, 000's) |
| |50A1AEAF-8ECA-48A0-8973-A5B3077FEE0D |TableDataTransIn |Dati in entrata in GB |Tabella del servizio dati in arrivo in GB |
| |1B8C1DEC-EE42-414B-AA36-6229CF199370 |TableDataTransOut |Uscita in GB |Tabella del servizio dati in uscita in GB |
| |43DAF82B-4618-444A-B994-40C23F7CD438 |BlobTransactions |Numero di richieste in 10, 000's |BLOB le richieste del servizio (10, 000's) |
| |9764F92C-E44A-498E-8DC1-AAD66587A810 |BlobDataTransIn |Dati in entrata in GB |BLOB del servizio dati in arrivo in GB |
| |3023FEF4-ECA5-4D7B-87B3-CFBC061931E8 |BlobDataTransOut |Uscita in GB |BLOB del servizio dati in uscita in GB |
| |EB43DD12-1AA6-4C4B-872C-FAF15A6785EA |QueueTransactions |Numero di richieste in 10, 000's |Coda le richieste del servizio (10, 000's) |
| |E518E809-E369-4A45-9274-2017B29FFF25 |QueueDataTransIn |Dati in entrata in GB |Coda del servizio dati in arrivo in GB |
| |DD0A10BA-A5D6-4CB6-88C0-7D585CEF9FC2 |QueueDataTransOut |Uscita in GB |Coda del servizio dati in uscita in GB |
| **Componente SQL**            | CBCFEF9A-B91F-4597-A4D3-01FE334BED82 | DatabaseSizeHourSqlMeter   | MB\*ore   | Capacità totale di database al momento della creazione, ha segnalata ogni ora.  |
| **RP MySql**          | E6D8CFCD-7734-495E-B1CC-5AB0B9C24BD3 | DatabaseSizeHourMySqlMeter | MB\*ore    | Capacità totale di database al momento della creazione, ha segnalata ogni ora. |
| **Calcolo** |FAB6EB84-500B-4A09-A8CA-7358F8BBAEA5 |Ore di dimensioni di macchina virtuale di base |Minuti di core virtuali | Numero di core virtuali volte minuti che è stata eseguita la macchina virtuale |
| |9CD92D4C-BAFD-4492-B278-BEDC2DE8232A |Ore di dimensioni di macchina virtuale Windows |Minuti di core virtuali | Numero di core virtuali volte minuti che è stata eseguita la macchina virtuale |
| |6DAB500F-A4FD-49C4-956D-229BB9C8C793 |Ore di dimensioni di macchina virtuale |Ore di VM |Acquisisce una macchina virtuale di Base sia di Windows. Non viene regolato per core |
| **Insieme di credenziali di chiave** |EBF13B9F-B3EA-46FE-BF54-396E93D48AB4 |Transazioni di insieme di credenziali chiave | Numero di richieste in 10, 000's| Numero di richieste di API REST ricevute dal piano dati insieme di credenziali chiave |
| **Servizio App** |190C935E-9ADA-48FF-9AB8-56EA1CF9ADAA  | Servizio app   | Ore core virtuale  | Numero di core virtuale utilizzato per eseguire il servizio app |
|             | 67CC4AFC-0691-48E1-A4B8-D744D1FEDBDE | Funzioni - richieste di calcolo      | 10 richieste              | Si applica a funzioni  |
|             | 957E9F36-2C14-45A1-B6A1-1723EF71A01D | Ore di servizio App condiviso          | 1 ora                   |                       |
|             | 539CDEC7-B4F5-49F6-AAC4-1F15CFF0EDA9 | Ore di servizio App gratuito            | 1 ora                   |                       |
|             | 88039D51-A206-3A89-E9DE-C5117E2D10A6 | Ore di servizio App Standard Small  | 1 ora                   |                       |
|             | 83A2A13E-4788-78DD-5D55-2831B68ED825 | Ore di servizio App Standard Medium | 1 ora                   |                       |
|             | 1083B9DB-E9BB-24BE-A5E9-D6FDD0DDEFE6 | Ore di servizio App Standard Large  | 1 ora                   |                       |
|             | 264ACB47-AD38-47F8-ADD3-47F01DC4F473 | SNI SSL                           | Per ogni associazione SNI SSL      | Si applica al servizio App |
|             | 60B42D72-DC1C-472C-9895-6C516277EDB4 | IP SSL                            | Per ogni indirizzo IP associazione SSL basata su | Si applica al servizio App |

## <a name="how-do-the-azure-stack-usage-apis-compare-to-the-azure-usage-apihttpsmsdnmicrosoftcomlibraryazure1ea5b323-54bb-423d-916f-190de96c6a3c-currently-in-public-preview"></a>Come eseguire l'utilizzo di Azure Stack confrontare con l'API di [Azure-utilizzo API](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) (attualmente in anteprima pubblica)?
* L'API Tenant di utilizzo è coerenza con l'API di Azure, con una sola eccezione: il *showDetails* flag attualmente non è supportata nello Stack di Azure.
* L'API di utilizzo del Provider si applica solo allo Stack di Azure.
* Attualmente, il [RateCard API](https://msdn.microsoft.com/en-us/library/azure/mt219004.aspx) che è disponibile in Azure non è disponibile nello Stack di Azure.

## <a name="what-is-the-difference-between-usage-time-and-reported-time"></a>Che cos'è la differenza tra l'ora di utilizzo e l'ora segnalata?
Report sui dati di utilizzo con due valori dell'ora principale:

* **Ora registrata**. Il tempo quando l'evento di utilizzo è entrato nel sistema di utilizzo
* **Tempo di utilizzo**. Il tempo impiegata quando la risorsa di Azure Stack

È possibile notare una discrepanza nei valori per tempo di utilizzo e l'ora segnalati per un evento di utilizzo specifico. Il ritardo può essere fino a più ore in qualsiasi ambiente.

Attualmente, è possibile eseguire query solo da *segnalati tempo*.

## <a name="what-do-these-usage-api-error-codes-mean"></a>Cosa indicano questi codici di errore API utilizzo?
| **Codice di stato HTTP** | **Codice errore** | **Descrizione** |
| --- | --- | --- |
| Richiesta non valida di 400 / |*NoApiVersion* |Il *api-version* manca il parametro di query. |
| Richiesta non valida di 400 / |*InvalidProperty* |Una proprietà manca o con un valore non valido. Il messaggio nel codice di errore nel corpo della risposta identifica la proprietà mancante. |
| Richiesta non valida di 400 / |*RequestEndTimeIsInFuture* |Il valore per *ReportedEndTime* è nel futuro. In futuro non sono consentiti valori per questo argomento. |
| Richiesta non valida di 400 / |*SubscriberIdIsNotDirectTenant* |Una chiamata API del provider è utilizzato un ID di sottoscrizione che non è un tenant valido del chiamante. |
| Richiesta non valida di 400 / |*SubscriptionIdMissingInRequest* |Manca l'ID sottoscrizione del chiamante. |
| Richiesta non valida di 400 / |*InvalidAggregationGranularity* |È stata richiesta una granularità di aggregazione non valido. I valori validi sono giornaliera e oraria. |
| 503 |*ServiceUnavailable* |Si è verificato un errore non irreversibile poiché il servizio è occupato o la chiamata è limitata. |

## <a name="next-steps"></a>Fasi successive
[Fatturazione del cliente e chargeback nello Stack di Azure](azure-stack-billing-and-chargeback.md)

[Utilizzo del provider di risorse API](azure-stack-provider-resource-api.md)

[Utilizzo delle risorse API tenant](azure-stack-tenant-resource-usage-api.md)
