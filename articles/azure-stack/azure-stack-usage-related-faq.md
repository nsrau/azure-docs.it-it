---
title: Domande frequenti su aspetti relativi all'utilizzo della API | Documenti Microsoft
description: Elenco di misuratori di Stack di Azure, confronto con API di Azure-utilizzo, tempo di utilizzo e l'ora riportato, i codici di errore.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 847f18b2-49a9-4931-9c09-9374e932a071
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: ac22ba34bff1d5321c05bc0a0a1b14ca742079a7
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/27/2018
ms.locfileid: "37051501"
---
# <a name="frequently-asked-questions-in-azure-stack-usage-api"></a>Domande frequenti nell'utilizzo dello Stack Azure API

Questo articolo risponde ad alcune domande frequenti sull'API di utilizzo dello Stack di Azure.

## <a name="what-meter-ids-can-i-see"></a>Il misuratore ID posso vedere?
L'utilizzo viene segnalato per il provider di risorse seguenti:

**Rete**  
  
**ID di misurazione**: F271A8A388C44D93956A063E1D2FA80B  
**Nome del misuratore**: utilizzo degli indirizzi IP statici  
**Unità**: gli indirizzi IP  
**Note sulla**: numero di indirizzi IP utilizzati. Se si chiama l'API di utilizzo con granularità quotidiana, il misuratore restituisce indirizzo IP moltiplicato per il numero di ore.  
  
**ID di misurazione**: 9E2739BA86744796B465F64674B822BA  
**Nome del misuratore**: utilizzo degli indirizzi IP dinamici  
**Unità**: gli indirizzi IP  
**Note sulla**: numero di indirizzi IP utilizzati. Se si chiama l'API di utilizzo con granularità quotidiana, il misuratore restituisce indirizzo IP moltiplicato per il numero di ore.  
  
**Archiviazione**  
  
**ID di misurazione**: B4438D5D-453B-4EE1-B42A-DC72E377F1E4  
**Nome del misuratore**: TableCapacity  
**Unità**: GB\*ore  
**Note sulla**: capacità utilizzata dalle tabelle totale.  
  
**ID di misurazione**: B5C15376-6C94-4FDD-B655-1A69D138ACA3  
**Nome del misuratore**: PageBlobCapacity  
**Unità**: GB\*ore  
**Note sulla**: totale capacità utilizzata dal BLOB di pagine.  
  
**ID di misurazione**: B03C6AE7-B080-4BFA-84A3-22C800F315C6  
**Nome del misuratore**: QueueCapacity  
**Unità**: GB\*ore  
**Note sulla**: totale capacità utilizzata dalla coda.  
  
**ID di misurazione**: 09F8879E-87E9-4305-A572-4B7BE209F857  
**Nome del misuratore**: BlockBlobCapacity  
**Unità**: GB\*ore  
**Note sulla**: totale capacità utilizzata dal BLOB in blocchi.  
  
**ID di misurazione**: B9FF3CD0-28AA-4762-84BB-FF8FBAEA6A90  
**Nome del misuratore**: TableTransactions  
**Unità**: 10, del numero di richieste 000's  
**Note sulla**: tabella richieste di servizio (in 10.000 s).  
  
**ID di misurazione**: 50A1AEAF-8ECA-48A0-8973-A5B3077FEE0D  
**Nome del misuratore**: TableDataTransIn  
**Unità**: i dati in entrata in GB  
**Note sulla**: tabella servizio dati in arrivo in GB.  
  
**ID di misurazione**: 1B8C1DEC-EE42-414B-AA36-6229CF199370  
**Nome del misuratore**: TableDataTransOut  
**Unità**: in uscita in GB  
**Note sulla**: tabella servizio dati in uscita in GB  
  
**ID di misurazione**: 43DAF82B-4618-444A-B994-40C23F7CD438  
**Nome del misuratore**: BlobTransactions  
**Unità**: numero di richieste in 10 000's  
**Note sulla**: Blob le richieste di servizio (in 10.000 s).  
  
**ID di misurazione**: 9764F92C-E44A-498E-8DC1-AAD66587A810  
**Nome del misuratore**: BlobDataTransIn  
**Unità**: i dati in entrata in GB  
**Note sulla**: servizio dati in arrivo in GB del Blob.  
  
**ID di misurazione**: 3023FEF4-ECA5-4D7B-87B3-CFBC061931E8  
**Nome del misuratore**: BlobDataTransOut  
**Unità**: in uscita in GB  
**Note sulla**: servizio dati in uscita in GB del Blob.  
  
**ID di misurazione**: EB43DD12-1AA6-4C4B-872C-FAF15A6785EA  
**Nome del misuratore**: QueueTransactions  
**Unità**: numero di richieste in 10 000's  
**Note sulla**: coda le richieste di servizio (in 10.000 s).  
  
**ID di misurazione**: E518E809-E369-4A45-9274-2017B29FFF25  
**Nome del misuratore**: QueueDataTransIn  
**Unità**: i dati in entrata in GB  
**Note sulla**: coda di servizio dati in arrivo in GB.  
  
**ID di misurazione**: DD0A10BA-A5D6-4CB6-88C0-7D585CEF9FC2  
**Nome del misuratore**: QueueDataTransOut  
**Unità**: in uscita in GB  
**Note sulla**: coda di servizio dati in uscita in GB  
  
**RP SQL**  
  
**ID di misurazione**: CBCFEF9A-B91F-4597-A4D3-01FE334BED82  
**Nome del misuratore**: DatabaseSizeHourSqlMeter  
**Unità**: MB\*ore  
**Note sulla**: capacità totale DB al momento della creazione. Se si chiama l'API di utilizzo con granularità quotidiana, il misuratore restituisce MB moltiplicato per il numero di ore.  
  
**RP MySql**  
  
**ID di misurazione**: E6D8CFCD-7734-495E-B1CC-5AB0B9C24BD3  
**Nome del misuratore**: DatabaseSizeHourMySqlMeter  
**Unità**: MB\*ore  
**Note sulla**: capacità totale DB al momento della creazione. Se si chiama l'API di utilizzo con granularità quotidiana, il misuratore restituisce MB moltiplicato per il numero di ore.  
  
**Calcolo**  
  
**ID di misurazione**: FAB6EB84-500B-4A09-A8CA-7358F8BBAEA5  
**Nome del misuratore**: ore dimensioni macchina virtuale di Base  
**Unità**: ore core virtuali  
**Note sulla**: numero di core virtuali moltiplicato per le ore è stata eseguita la macchina virtuale.  
  
**ID di misurazione**: 9CD92D4C-BAFD-4492-B278-BEDC2DE8232A  
**Nome del misuratore**: ore dimensioni di macchina virtuale Windows  
**Unità**: ore core virtuali  
**Note sulla**: numero di core virtuali moltiplicato per ore è stata eseguita la macchina virtuale.  
  
**ID di misurazione**: 6DAB500F-A4FD-49C4-956D-229BB9C8C793  
**Nome del misuratore**: ore dimensioni VM  
**Unità**: ore per VM  
**Note sulla**: acquisisce Base sia VM di Windows. Non viene regolato per core.  
  
**Insieme di credenziali di chiave**  
  
**ID di misurazione**: EBF13B9F-B3EA-46FE-BF54-396E93D48AB4  
**Nome del misuratore**: le transazioni di insieme di credenziali chiave  
**Unità**: 10, del numero di richieste 000's  
**Note sulla**: richieste numero dell'API REST ricevute dal piano dati insieme di credenziali chiave.  
  
**ID di misurazione**: 2C354225-B2FE-42E5-AD89-14F0EA302C87  
**Nome del misuratore**: le transazioni di chiavi avanzate  
**Unità**: le transazioni pari a 10 KB  
**Note sulla**: RSA 3 K/4K, le transazioni chiave ECC. (anteprima).  
  
*Servizio App**  
  
**ID di misurazione**: 190C935E-9ADA-48FF-9AB8-56EA1CF9ADAA  
**Nome del misuratore**: servizio App  
**Unità**: ore core virtuali  
**Note sulla**: numero di core virtuali usati per eseguire il servizio app. Nota: Microsoft Usa questa modalità di calcolo ad addebitare i costi di servizio App nello Stack di Azure. Provider di servizi cloud possono utilizzare l'altro servizio App metri (sotto) per calcolare l'utilizzo per i tenant.  
  
**ID di misurazione**: 67CC4AFC-0691-48E1-A4B8-D744D1FEDBDE  
**Nome del misuratore**: le richieste di funzioni  
**Unità**: 10 richieste  
**Note sulla**: numero totale di esecuzioni richieste (per 10 esecuzioni). Esecuzioni vengono calcolate ogni volta che viene eseguito in risposta a un evento, una funzione o viene attivata da un'associazione.  
  
**ID di misurazione**: D1D04836-075C-4F27-BF65-0A1130EC60ED  
**Nome del misuratore**: funzioni - calcolo  
**Unità**: GB-s  
**Note sulla**: utilizzo delle risorse misurato in secondi gigabyte (GB/s). **Osservare l'utilizzo delle risorse** viene calcolata moltiplicando dimensione media della memoria in GB per il tempo in millisecondi impiegato per l'esecuzione della funzione. Memoria utilizzata da una funzione è misurata dall'arrotondamento fino a 128 MB più vicino, fino a dimensioni massime della memoria di 1,536 MB, con tempo di esecuzione calcolato dall'arrotondamento fino a 1 più vicino ms. Il tempo di esecuzione minimo e la memoria per l'esecuzione di una singola funzione va rispettivamente 128 mb e 100 ms.  
  
**ID di misurazione**: 957E9F36-2C14-45A1-B6A1-1723EF71A01D  
**Nome del misuratore**: ore di servizio App condiviso  
**Unità**: 1 ora  
**Note sulla**: in base all'utilizzo di ora della partizione piano di servizio App. I piani vengono misurati in base a una per ogni App.  
  
**ID di misurazione**: 539CDEC7-B4F5-49F6-AAC4-1F15CFF0EDA9  
**Nome del misuratore**: ore di servizio App gratuito  
**Unità**: 1 ora  
**Note sulla**: in base all'utilizzo ora del piano di servizio App gratuito. I piani vengono misurati in base a una per ogni App.  
  
**ID di misurazione**: 88039D51-A206-3A89-E9DE-C5117E2D10A6  
**Nome del misuratore**: ore di servizio App Standard Small  
**Unità**: 1 ora  
**Note sulla**: calcolato in base alle dimensioni e il numero di istanze.  
  
**ID di misurazione**: 83A2A13E-4788-78DD-5D55-2831B68ED825  
**Nome del misuratore**: ore di servizio App Standard Medium  
**Unità**: 1 ora  
**Note sulla**: calcolato in base alle dimensioni e il numero di istanze.  
  
**ID di misurazione**: 1083B9DB-E9BB-24BE-A5E9-D6FDD0DDEFE6  
**Nome del misuratore**: ore di servizio App Standard Large  
**Unità**: 1 ora  
**Note sulla**: calcolato in base alle dimensioni e il numero di istanze.  
  
**Piani di lavoro personalizzato**  
  
**ID di misurazione**: *piani di lavoro personalizzato*  
**Nome del misuratore**: i piani di lavoro personalizzato  
**Unità**: ore  
**Note sulla**: ID misuratore deterministica viene creato in base alle SKU e nome del livello di lavoro personalizzato. Questo ID misuratore è univoco per ogni livello di lavoro personalizzata.  
  
**ID di misurazione**: 264ACB47-AD38-47F8-ADD3-47F01DC4F473  
**Nome del misuratore**: SNI SSL  
**Unità**: per ogni associazione SNI SSL  
**Note sulla**: servizio App supporta due tipi di connessioni SSL: le connessioni SSL con indicazione nome Server (SNI) e le connessioni SSL di indirizzo IP. Il protocollo SSL basato su SNI funziona in browser moderni mentre quello basato su IP funziona in tutti i browser.  
  
**ID di misurazione**: 60B42D72-DC1C-472C-9895-6C516277EDB4  
**Nome del misuratore**: IP SSL  
**Unità**: per ogni indirizzo IP associazione SSL basata su  
**Note sulla**: servizio App supporta due tipi di connessioni SSL: le connessioni SSL con indicazione nome Server (SNI) e le connessioni SSL di indirizzo IP. Il protocollo SSL basato su SNI funziona in browser moderni mentre quello basato su IP funziona in tutti i browser.  
  
**ID di misurazione**: 73215A6C-FA54-4284-B9C1-7E8EC871CC5B  
**Nome del misuratore**: il processo Web  
**Unità**:  
**Note sulla**: calcolata per ogni sito attivo all'ora.  
  
**ID di misurazione**: 5887D39B-0253-4E12-83C7-03E1A93DFFD9  
**Nome del misuratore**: larghezza di banda in uscita esterna  
**Unità**: GB  
**Note sulla**: Totale richieste in ingresso risposta byte + richiesta in uscita totale byte + totale FTP in ingresso richiesta risposta byte + totale web in ingresso distribuisce byte di risposta richiesta.  
  

## <a name="how-do-the-azure-stack-usage-apis-compare-to-the-azure-usage-apihttpsmsdnmicrosoftcomlibraryazure1ea5b323-54bb-423d-916f-190de96c6a3c-currently-in-public-preview"></a>Come eseguire l'utilizzo di Azure Stack API confrontare con il [di Azure-utilizzo API](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) (attualmente in anteprima pubblica)?
* L'API Tenant di utilizzo è coerenza con l'API di Azure, con una sola eccezione: il *showDetails* flag attualmente non è supportato nello Stack di Azure.
* L'API dell'utilizzo del Provider si applica solo allo Stack di Azure.
* Attualmente, il [RateCard API](https://msdn.microsoft.com/library/azure/mt219004.aspx) vale a dire disponibili in Azure non è disponibili nello Stack di Azure.

## <a name="what-is-the-difference-between-usage-time-and-reported-time"></a>Che cos'è la differenza tra ora di utilizzo e di ora segnalata?
Report sui dati di utilizzo con due valori dell'ora principale:

* **Ora registrata**. L'ora in cui l'evento di utilizzo immessa il sistema di utilizzo
* **Tempo di utilizzo**. Il tempo utilizzata quando la risorsa di Azure Stack

È possibile notare una discrepanza nei valori per tempo di utilizzo e l'ora segnalati per un evento di utilizzo specifico. Il ritardo può essere fino a più ore in qualsiasi ambiente.

Attualmente, è possibile eseguire query solo da *segnalati tempo*.

## <a name="what-do-these-usage-api-error-codes-mean"></a>Cosa indicano questi codici di errore API utilizzo?
| **Codice di stato HTTP** | **Codice errore** | **Descrizione** |
| --- | --- | --- |
| Richiesta 400/non valida |*NoApiVersion* |Il *api-version* manca il parametro di query. |
| Richiesta 400/non valida |*InvalidProperty* |Una proprietà mancante o con un valore non valido. Il messaggio nel codice di errore nel corpo della risposta identifica la proprietà mancante. |
| Richiesta 400/non valida |*RequestEndTimeIsInFuture* |Il valore per *ReportedEndTime* è nel futuro. I valori in futuro non consentiti per questo argomento. |
| Richiesta 400/non valida |*SubscriberIdIsNotDirectTenant* |Una chiamata API del provider ha utilizzato un ID di sottoscrizione che non è un tenant valido del chiamante. |
| Richiesta 400/non valida |*SubscriptionIdMissingInRequest* |Manca l'ID sottoscrizione del chiamante. |
| Richiesta 400/non valida |*InvalidAggregationGranularity* |È stato richiesto un livello di dettaglio delle aggregazioni non valido. I valori validi sono giornaliera e oraria. |
| 503 |*ServiceUnavailable* |Si è verificato un errore non irreversibile poiché il servizio è occupato o la chiamata è limitata. |

## <a name="next-steps"></a>Fasi successive
[Fatturazione del cliente e chargeback nello Stack di Azure](azure-stack-billing-and-chargeback.md)

[Utilizzo del provider di risorse API](azure-stack-provider-resource-api.md)

[Utilizzo delle risorse API tenant](azure-stack-tenant-resource-usage-api.md)
