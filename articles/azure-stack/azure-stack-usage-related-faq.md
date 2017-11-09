---
title: Utilizzo API correlate domande frequenti | Documenti Microsoft
description: Elenco di misuratori di Stack di Azure, confronto con API di Azure-utilizzo, tempo di utilizzo e l'ora di segnalato, codici di errore.
services: azure-stack
documentationcenter: 
author: AlfredoPizzirani
manager: byronr
editor: 
ms.assetid: 847f18b2-49a9-4931-9c09-9374e932a071
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: alfredop
ms.openlocfilehash: 166147c8cb4949be1b23e0a06868e66c8a5844f4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="frequently-asked-questions-in-azure-stack-usage-api"></a>Domande frequenti nell'utilizzo di Azure Stack API 
Questo articolo risponde ad alcune domande frequenti sull'API di utilizzo dello Stack di Azure.

## <a name="what-meter-ids-can-i-see"></a>Il misuratore ID è possibile vedere
Attualmente, viene segnalato l'utilizzo per la rete, archiviazione e i provider di risorse di calcolo.

| **Provider di risorse** | **ID contatore** | **Nome del misuratore** | **Unità** | **Informazioni aggiuntive** |
| --- | --- | --- | --- | --- | 
| **Rete** |F271A8A388C44D93956A063E1D2FA80B |Utilizzo degli indirizzi IP statici |Indirizzi IP|Tutti gli indirizzi IP conteggio utilizzato | 
| |9E2739BA86744796B465F64674B822BA |Utilizzo degli indirizzi IP dinamici |Indirizzi IP|Tutti gli indirizzi IP conteggio utilizzato | 
| **Archiviazione** |B4438D5D-453B-4EE1-B42A-DC72E377F1E4 |TableCapacity |GB\*ore |Capacità totale utilizzata dalle tabelle |
| | B5C15376-6C94-4FDD-B655-1A69D138ACA3 |PageBlobCapacity |GB\*ore |Capacità totale utilizzata dal BLOB di pagine |
| | B03C6AE7-B080-4BFA-84A3-22C800F315C6 |QueueCapacity |GB\*ore |Capacità totale utilizzata dalla coda |
| | 09F8879E-87E9-4305-A572-4B7BE209F857 |BlockBlobCapacity |GB\*ore |Capacità totale utilizzata dal BLOB in blocchi |
| | B9FF3CD0-28AA-4762-84BB-FF8FBAEA6A90 |TableTransactions |Numero di richieste di 10.000 |Richieste di servizio di tabella (in 10.000 s) |
| | 50A1AEAF-8ECA-48A0-8973-A5B3077FEE0D |TableDataTransIn |Dati in entrata in GB |Tabella del servizio dati in arrivo in GB |
| | 1B8C1DEC-EE42-414B-AA36-6229CF199370 |TableDataTransOut |Outgress in GB |Tabella del servizio dati in uscita in GB |
| | 43DAF82B-4618-444A-B994-40C23F7CD438 |BlobTransactions |Numero di richieste in 10.000 |Richieste di servizio BLOB (in 10.000 s) |
| | 9764F92C-E44A-498E-8DC1-AAD66587A810 |BlobDataTransIn |Dati in entrata in GB |BLOB del servizio dati in arrivo in GB |
| | 3023FEF4-ECA5-4D7B-87B3-CFBC061931E8 |BlobDataTransOut |Outgress in GB |BLOB del servizio dati in uscita in GB |
| | EB43DD12-1AA6-4C4B-872C-FAF15A6785EA |QueueTransactions |Numero di richieste in 10.000 |Coda di richieste di servizio (in 10.000 s) |
| | E518E809-E369-4A45-9274-2017B29FFF25 |QueueDataTransIn |Dati in entrata in GB |Coda del servizio dati in arrivo in GB | 
| | DD0A10BA-A5D6-4CB6-88C0-7D585CEF9FC2 |QueueDataTransOut |Outgress in GB |Coda del servizio dati in uscita in GB |
| **Calcolo** |FAB6EB84-500B-4A09-A8CA-7358F8BBAEA5 |Ore di dimensioni di macchina virtuale di base |Minuti di core virtuali | Numero di vcores volte minuti che è stata eseguita la macchina virtuale |
| |9CD92D4C-BAFD-4492-B278-BEDC2DE8232A |Ore di dimensioni di macchina virtuale Windows |Minuti di core virtuali | Numero di vcores volte minuti che è stata eseguita la macchina virtuale |
| |6DAB500F-A4FD-49C4-956D-229BB9C8C793 |Ore di dimensioni di macchina virtuale |Ore di VM |Acquisisce una macchina virtuale di Base sia di Windows. Non viene regolato per vcores |
| **Insieme di credenziali di chiave** | EBF13B9F-B3EA-46FE-BF54-396E93D48AB4 |Transazioni di insieme di credenziali chiave | Numero di richieste in 10.000 s| Numero di richieste di API REST ricevute dal piano dati insieme di credenziali chiave |

## <a name="how-do-the-azure-stack-usage-apis-compare-to-the-azure-usage-apihttpsmsdnmicrosoftcomlibraryazure1ea5b323-54bb-423d-916f-190de96c6a3c-currently-in-public-preview"></a>Come eseguire l'utilizzo di Azure Stack confrontare con l'API di [Azure-utilizzo API](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) (attualmente in anteprima pubblica)?
* L'API Tenant di utilizzo è coerenza con l'API di Azure, con una sola eccezione: il *showDetails* flag attualmente non è supportata nello Stack di Azure.
* L'API di utilizzo del Provider si applica solo allo Stack di Azure.
* Attualmente, il [RateCard API](https://msdn.microsoft.com/en-us/library/azure/mt219004.aspx) che è disponibile in Azure non è disponibile nello Stack di Azure.

## <a name="what-is-the-difference-between-usage-time-and-reported-time"></a>Che cos'è la differenza tra l'ora di utilizzo e l'ora segnalata?
Report sui dati di utilizzo con due valori dell'ora principale:

* **Ora registrata**. Il tempo quando l'evento di utilizzo è entrato nel sistema di utilizzo
* **Tempo di utilizzo**. Il tempo impiegata quando la risorsa di Azure Stack

È possibile notare una discrepanza nei valori per tempo di utilizzo e l'ora segnalati per un evento di utilizzo specifico. Il ritardo può essere fino a più ore in qualsiasi ambiente.

Attualmente, è possibile eseguire una query *solo da tempo segnalati*.

## <a name="what-do-these-usage-api-error-codes-mean"></a>Cosa indicano questi codici di errore API utilizzo?
| **Codice di stato HTTP** | **Codice errore** | **Descrizione** |
| --- | --- | --- |
| Richiesta non valida di 400 / |*NoApiVersion* |Il *api-version* manca il parametro di query. |
| Richiesta non valida di 400 / |*InvalidProperty* |Una proprietà manca o con un valore non valido. Il messaggio nel codice di errore nel corpo della risposta identifica la proprietà mancante. |
| Richiesta non valida di 400 / |*RequestEndTimeIsInFuture* |Il valore per *ReportedEndTime* è nel futuro. In futuro non sono consentiti valori per questo argomento. |
| Richiesta non valida di 400 / |*SubscriberIdIsNotDirectTenant* |Una chiamata API del provider utilizzato un ID di sottoscrizione che non è un tenant valido del chiamante. |
| Richiesta non valida di 400 / |*SubscriptionIdMissingInRequest* |Manca l'ID sottoscrizione del chiamante. |
| Richiesta non valida di 400 / |*InvalidAggregationGranularity* |È stata richiesta una granularità di aggregazione non valido. I valori validi sono giornaliera e oraria. |
| 503 |*ServiceUnavailable* |Si è verificato un errore non irreversibile poiché il servizio è occupato o la chiamata è limitata. |

## <a name="next-steps"></a>Passaggi successivi
[Fatturazione del cliente e chargeback nello Stack di Azure](azure-stack-billing-and-chargeback.md)

[Utilizzo del provider di risorse API](azure-stack-provider-resource-api.md)

[Utilizzo delle risorse API tenant](azure-stack-tenant-resource-usage-api.md)

