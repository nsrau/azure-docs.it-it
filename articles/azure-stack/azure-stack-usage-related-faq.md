---
title: Utilizzo API-domande frequenti | Microsoft Docs
description: Elenco di contatori di Azure Stack, confronto con API di utilizzo di Azure, tempo di utilizzo e ora segnalata, codici di errore.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: 362ee7e84229c421f69ff0db0c8cfb040b105c97
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2018
ms.locfileid: "49394513"
---
# <a name="frequently-asked-questions-in-azure-stack-usage-api"></a>Domande frequenti in API di utilizzo di Azure Stack

Questo articolo risponde ad alcune domande frequenti sull'API di utilizzo di Azure Stack.

## <a name="what-meter-ids-can-i-see"></a>Quali ID contatore posso vedere?
L'utilizzo viene segnalato il provider di risorse seguenti:

### <a name="network"></a>Rete
  
**ID contatore**: F271A8A388C44D93956A063E1D2FA80B  
**Nome misuratore**: utilizzo di indirizzi IP statici  
**Unità**: indirizzi IP  
**Note sulla**: numero di indirizzi IP usati. Se si chiama l'API di utilizzo con granularità giornaliera, il contatore restituisce indirizzo IP moltiplicato per il numero di ore.  
  
**ID contatore**: 9E2739BA86744796B465F64674B822BA  
**Nome misuratore**: utilizzo di indirizzi IP dinamici  
**Unità**: indirizzi IP  
**Note sulla**: numero di indirizzi IP usati. Se si chiama l'API di utilizzo con granularità giornaliera, il contatore restituisce indirizzo IP moltiplicato per il numero di ore.  
  
### <a name="storage"></a>Archiviazione
  
**ID contatore**: B4438D5D-453B-4EE1-B42A-DC72E377F1E4  
**Nome misuratore**: TableCapacity  
**Unit**: GB\*ore  
**Note sulla**: capacità utilizzata dalle tabelle totale.  
  
**ID contatore**: B5C15376-6C94-4FDD-B655-1A69D138ACA3  
**Nome misuratore**: PageBlobCapacity  
**Unit**: GB\*ore  
**Note sulla**: totale capacità utilizzata dal BLOB di pagine.  
  
**ID contatore**: B03C6AE7-B080-4BFA-84A3-22C800F315C6  
**Nome misuratore**: QueueCapacity  
**Unit**: GB\*ore  
**Note sulla**: totale capacità utilizzata dalla coda.  
  
**ID contatore**: 09F8879E-87E9-4305-A572-4B7BE209F857  
**Nome misuratore**: BlockBlobCapacity  
**Unit**: GB\*ore  
**Note sulla**: totale capacità utilizzata dai BLOB in blocchi.  
  
**ID contatore**: B9FF3CD0-28AA-4762-84BB-FF8FBAEA6A90  
**Nome misuratore**: TableTransactions  
**Unità**: 10, numero di richieste 000's  
**Note sulla**: tabella richieste di servizio (in 10.000).  
  
**ID contatore**: 50A1AEAF-8ECA-48A0-8973-A5B3077FEE0D  
**Nome misuratore**: TableDataTransIn  
**Unità**: i dati in ingresso in GB  
**Note sulla**: tabella di inserimento dei dati del servizio in GB.  
  
**ID contatore**: 1B8C1DEC-EE42-414B-AA36-6229CF199370  
**Nome misuratore**: TableDataTransOut  
**Unità**: in uscita in GB  
**Note sulla**: tabella del servizio dati in uscita in GB  
  
**ID contatore**: 43DAF82B-4618-444A-B994-40C23F7CD438  
**Nome misuratore**: BlobTransactions  
**Unità**: conteggio richieste in 10 000's  
**Note sulla**: Blob le richieste di servizio (in 10.000).  
  
**ID contatore**: 9764F92C-E44A-498E-8DC1-AAD66587A810  
**Nome misuratore**: BlobDataTransIn  
**Unità**: i dati in ingresso in GB  
**Note sulla**: Blob in ingresso dei dati del servizio in GB.  
  
**ID contatore**: 3023FEF4-ECA5-4D7B-87B3-CFBC061931E8  
**Nome misuratore**: BlobDataTransOut  
**Unità**: in uscita in GB  
**Note sulla**: Blob del servizio dati in uscita in GB.  
  
**ID contatore**: EB43DD12-1AA6-4C4B-872C-FAF15A6785EA  
**Nome misuratore**: QueueTransactions  
**Unità**: conteggio richieste in 10 000's  
**Note sulla**: accodare le richieste di servizio (in 10.000).  
  
**ID contatore**: E518E809-E369-4A45-9274-2017B29FFF25  
**Nome misuratore**: QueueDataTransIn  
**Unità**: i dati in ingresso in GB  
**Note sulla**: coda di inserimento dei dati del servizio in GB.  
  
**ID contatore**: DD0A10BA-A5D6-4CB6-88C0-7D585CEF9FC2  
**Nome misuratore**: QueueDataTransOut  
**Unità**: in uscita in GB  
**Note sulla**: coda di servizio dati in uscita in GB  

### <a name="compute"></a>Calcolo 
  
**ID contatore**: FAB6EB84-500B-4A09-A8CA-7358F8BBAEA5  
**Nome misuratore**: ore di dimensioni della macchina virtuale di Base  
**Unità**: ore core virtuali  
**Note sulla**: numero di memorie centrali virtuali moltiplicato per le ore in cui è stata eseguita la macchina virtuale.  
  
**ID contatore**: 9CD92D4C-BAFD-4492-B278-BEDC2DE8232A  
**Nome misuratore**: ore delle dimensioni di VM Windows  
**Unità**: ore core virtuali  
**Note sulla**: numero di memorie centrali virtuali moltiplicato per ore è stata eseguita la macchina virtuale.  
  
**ID contatore**: 6DAB500F-A4FD-49C4-956D-229BB9C8C793  
**Nome misuratore**: ore di dimensioni di macchina virtuale  
**Unità**: ore di macchina virtuale  
**Note sulla**: acquisisce Base e Windows della macchina virtuale. Non viene regolato per core.  
  
### <a name="managed-disks"></a>Managed Disks

**ID contatore**: 5d76e09f-4567-452a-94cc-7d1f097761f0   
**Nome misuratore**: S4   
**Unit**: numero di dischi\*ore   
**Note sulla**: disco: 32 GB gestito Standard 

**ID contatore**: dc9fc6a9-0782-432a-b8dc-978130457494   
**Nome misuratore**: S6   
**Unit**: numero di dischi\*ore   
**Note sulla**: Standard di Managed Disks: 64 GB 

**ID contatore**: e5572fce-9f58-49d7-840c-b168c0f01fff   
**Nome misuratore**: S10   
**Unit**: numero di dischi\*ore   
**Note sulla**: disco – 128 GB gestito Standard 

**ID contatore**: 9a8caedd-1195-4cd5-80b4-a4c22f9302b8   
**Nome misuratore**: Ds15   
**Unit**: numero di dischi\*ore   
**Note sulla**: disco: 256 GB gestito Standard 

**ID contatore**: 5938f8da-0ecd-4c48-8d5a-c7c6c23546be   
**Nome misuratore**: S20   
**Unit**: numero di dischi\*ore      
**Note sulla**: Standard di Managed Disks: 512 GB 

**ID contatore**: 7705a158-bd8b-4b2b-b4c2-0782343b81e6   
**Nome misuratore**: S30   
**Unit**: numero di dischi\*ore   
**Note sulla**: Standard di Managed Disks: 1024 GB 

**ID contatore**: d9aac1eb-a5d1-42f2-b617-9e3ea94fed88   
**Nome misuratore**: S40   
**Unit**: numero di dischi\*ore   
**Note sulla**: Standard di Managed Disks: 2048 GB 

**ID contatore**: a54899dd-458e-4a40-9abd-f57cafd936a7   
**Nome misuratore**: S50   
**Unit**: numero di dischi\*ore   
**Note sulla**: Standard di Managed Disks: 4096 GB 

**ID contatore**: 5c105f5f-cbdf-435c-b49b-3c7174856dcc   
**Nome misuratore**: P4   
**Unit**: numero di dischi\*ore   
**Note sulla**: disco: 32 GB gestito Premium 

**ID contatore**: 518b412b-1927-4f25-985f-4aea24e55c4f   
**Nome misuratore**: P6   
**Unit**: numero di dischi\*ore   
**Note sulla**: disco – 64 GB gestito Premium 

**ID contatore**: 5cfb1fed-0902-49e3-8217-9add946fd624   
**Nome misuratore**: P10   
**Unit**: numero di dischi\*ore   
**Note sulla**: disco – 128 GB gestito Premium  

**ID contatore**: 8de91c94-f740-4d9a-b665-bd5974fa08d4   
**Nome misuratore**: P15  
**Unit**: numero di dischi\*ore   
**Note sulla**: disco: 256 GB gestito Premium 

**ID contatore**: c7e7839c-293b-4761-ae4c-848eda91130b   
**Nome misuratore**: P20   
**Unit**: numero di dischi\*ore   
**Note sulla**: disco – 512 GB gestito Premium 

**ID contatore**: 9f502103-adf4-4488-b494-456c95d23a9f   
**Nome misuratore**: P30   
**Unit**: numero di dischi\*ore   
**Note sulla**: Premium Managed Disks: 1024 GB 

**ID contatore**: 043757fc-049f-4e8b-8379-45bb203c36b1   
**Nome misuratore**: P40   
**Unit**: numero di dischi\*ore    
**Note sulla**: disco – 2048 GB gestito Premium 

**ID contatore**: c0342c6f-810b-4942-85d3-6eaa561b6570   
**Nome misuratore**: P50   
**Unit**: numero di dischi\*ore   
**Note sulla**: disco – 4096 GB gestito Premium 

**ID contatore**: 8a409390-1913-40ae-917b-08d0f16f3c38   
**Nome misuratore**: ActualStandardDiskSize   
**Unit**: Byte\*ore      
**Note sulla**: le dimensioni effettive su disco del disco gestito standard  

**ID contatore**: 1273b16f-8458-4c34-8ce2-a515de551ef6  
**Nome misuratore**: ActualPremiumDiskSize   
**Unit**: Byte\*ore      
**Note sulla**: le dimensioni effettive su disco Premium un disco gestito 

**ID contatore**: 89009682-df7f-44fe-aeb1-63fba3ddbf4c  
**Nome misuratore**: ActualStandardSnapshotSize   
**Unit**: Byte\*ore   
**Note sulla**: le dimensioni effettive sul disco di snapshot gestito standard.  

**ID contatore**: 95b0c03f-8a82-4524-8961-ccfbf575f536   
**Nome misuratore**: ActualPremiumSnapshotSize   
**Unit**: Byte\*ore   
**Note sulla**: le dimensioni effettive sul disco gestito Premium.   

### <a name="sql-rp"></a>Provider di risorse SQL
  
**ID contatore**: CBCFEF9A-B91F-4597-A4D3-01FE334BED82  
**Nome misuratore**: DatabaseSizeHourSqlMeter  
**Unit**: MB\*ore  
**Note sulla**: DB totale capacità al momento della creazione. Se si chiama l'API di utilizzo con granularità giornaliera, il contatore restituisce MB moltiplicato per il numero di ore.  
  
### <a name="mysql-rp"></a>Provider di risorse MySql   
  
**ID contatore**: E6D8CFCD-7734-495E-B1CC-5AB0B9C24BD3  
**Nome misuratore**: DatabaseSizeHourMySqlMeter  
**Unit**: MB\*ore  
**Note sulla**: DB totale capacità al momento della creazione. Se si chiama l'API di utilizzo con granularità giornaliera, il contatore restituisce MB moltiplicato per il numero di ore.    
### <a name="key-vault"></a>Key Vault   
  
**ID contatore**: EBF13B9F-B3EA-46FE-BF54-396E93D48AB4  
**Nome misuratore**: le transazioni di Key Vault  
**Unità**: 10, numero di richieste 000's  
**Note sulla**: richieste di numero di API REST ricevute in base al piano dati Key Vault.  
  
**ID contatore**: 2C354225-B2FE-42E5-AD89-14F0EA302C87  
**Nome misuratore**: le transazioni di chiavi avanzate  
**Unità**: 10 k rpm transazioni  
**Note sulla**: RSA K di 3 o 4 KB, le transazioni di chiavi ECC. (anteprima).  
  
### <a name="app-service"></a>Servizio app   
  
**ID contatore**: 190C935E-9ADA-48FF-9AB8-56EA1CF9ADAA  
**Nome misuratore**: servizio App  
**Unità**: ore core virtuali  
**Note sulla**: numero di memorie centrali virtuali usato per eseguire il servizio app. Nota: Microsoft Usa questa misurazione per l'importo addebitato il servizio App in Azure Stack. Provider di servizi cloud possono usare l'altro servizio di App metri (sotto) per calcolare l'utilizzo per i tenant.  
  
**ID contatore**: 67CC4AFC-0691-48E1-A4B8-D744D1FEDBDE  
**Nome misuratore**: richieste di funzioni  
**Unità**: 10 richieste  
**Note sulla**: numero totale di esecuzioni richieste (ogni 10 esecuzioni). Le esecuzioni vengono conteggiate ogni volta che viene eseguito in risposta a un evento, una funzione o viene attivata da un'associazione.  
  
**ID contatore**: D1D04836-075C-4F27-BF65-0A1130EC60ED  
**Nome misuratore**: calcolare funzioni -  
**Unità**: GB-s  
**Note sulla**: utilizzo delle risorse misurate in secondi di gigabyte (GB/s). **Consumo di risorse osservato** viene calcolato moltiplicando la dimensione di memoria Media in GB per il tempo in millisecondi impiegato per l'esecuzione della funzione. Memoria usata da una funzione viene misurata tramite arrotondamento fino a 128 MB più vicini, fino alla dimensione massima di memoria di 1.536 MB, tempo di esecuzione viene calcolato tramite arrotondamento fino al più vicino 1 ms. Il tempo di esecuzione minimo e della memoria per una singola esecuzione di funzione è rispettivamente 100 ms e 128 mb.  
  
**ID contatore**: 957E9F36-2C14-45A1-B6A1-1723EF71A01D  
**Nome misuratore**: ore di servizio App condivisi  
**Unità**: 1 ora  
**Note sulla**: in base all'utilizzo ora del piano di servizio App di partizioni. I piani vengono conteggiati in base a una per ogni App.  
  
**ID contatore**: 539CDEC7-B4F5-49F6-AAC4-1F15CFF0EDA9  
**Nome misuratore**: ore di servizio App gratuito  
**Unità**: 1 ora  
**Note sulla**: in base all'utilizzo ora del piano di servizio App gratuito. I piani vengono conteggiati in base a una per ogni App.  
  
**ID contatore**: 88039D51-A206-3A89-E9DE-C5117E2D10A6  
**Nome misuratore**: ore di servizio App Standard Small  
**Unità**: 1 ora  
**Note sulla**: calcolato in base alle dimensioni e numero di istanze.  
  
**ID contatore**: 83A2A13E-4788-78DD-5D55-2831B68ED825  
**Nome misuratore**: ore di servizio App Standard Medium  
**Unità**: 1 ora  
**Note sulla**: calcolato in base alle dimensioni e numero di istanze.  
  
**ID contatore**: 1083B9DB-E9BB-24BE-A5E9-D6FDD0DDEFE6  
**Nome misuratore**: ore di servizio App Standard Large  
**Unità**: 1 ora  
**Note sulla**: calcolato in base alle dimensioni e numero di istanze.  
  
### <a name="custom-worker-tiers"></a>Piani di lavoro personalizzato   
  
**ID contatore**: *piani di lavoro personalizzato*  
**Nome misuratore**: i piani di lavoro personalizzato  
**Unità**: ore  
**Note sulla**: ID contatore deterministica viene creato in base a uno SKU e nome del livello di ruolo di lavoro personalizzati. Questo ID contatore è univoco per ogni livello di ruolo di lavoro personalizzati.  
  
**ID contatore**: 264ACB47-AD38-47F8-ADD3-47F01DC4F473  
**Nome misuratore**: SNI SSL  
**Unità**: per ogni associazione SNI SSL  
**Note sulla**: il servizio App supporta due tipi di connessioni SSL: connessioni SSL di indicazione nome Server (SNI) e le connessioni SSL di indirizzo IP. Il protocollo SSL basato su SNI funziona in browser moderni mentre quello basato su IP funziona in tutti i browser.  
  
**ID contatore**: 60B42D72-DC1C-472C-9895-6C516277EDB4  
**Nome misuratore**: IP SSL  
**Unità**: per ogni indirizzo IP associazione SSL basata su  
**Note sulla**: il servizio App supporta due tipi di connessioni SSL: connessioni SSL di indicazione nome Server (SNI) e le connessioni SSL di indirizzo IP. Il protocollo SSL basato su SNI funziona in browser moderni mentre quello basato su IP funziona in tutti i browser.  
  
**ID contatore**: 73215A6C-FA54-4284-B9C1-7E8EC871CC5B  
**Nome misuratore**: il processo Web  
**Unità**:  
**Note sulla**: calcolata per ogni sito attivo ogni ora.  
  
**ID contatore**: 5887D39B-0253-4E12-83C7-03E1A93DFFD9  
**Nome misuratore**: larghezza di banda in uscita esterna  
**Unità**: GB  
**Note sulla**: Totale richieste in ingresso risposta byte + richiesta in uscita totale byte + FTP in ingresso totale richiesta risposta byte + totale in ingresso web distribuisce byte di risposta richiesta.  
  

## <a name="how-do-the-azure-stack-usage-apis-compare-to-the-azure-usage-apihttpsdocsmicrosoftcomazurebillingbilling-usage-rate-card-overviewazure-resource-usage-api-preview-currently-in-public-preview"></a>Come fare l'utilizzo di Azure Stack API confrontare con il [utilizzo di Azure API](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview#azure-resource-usage-api-preview) (attualmente in anteprima pubblica)?
* L'API di utilizzo del Tenant è coerenza con l'API di Azure, con una sola eccezione: il *showDetails* flag attualmente non è supportato in Azure Stack.
* L'API di utilizzo del Provider si applica solo ad Azure Stack.
* Attualmente, il [API RateCard](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview#azure-resource-ratecard-api-preview) che è disponibile in Azure non è disponibile in Azure Stack.

## <a name="what-is-the-difference-between-usage-time-and-reported-time"></a>Che cos'è la differenza tra ora di utilizzo e ora segnalata?
Report sull'utilizzo dei dati con due valori dell'ora principale:

* **Gli orari segnalati**. L'ora in cui l'evento di utilizzo immessa nel sistema sull'utilizzo
* **Tempo di utilizzo**. Il tempo utilizzata quando la risorsa di Azure Stack

È possibile notare una differenza nei valori per tempo di utilizzo e ora segnalata per un evento di utilizzo specifici. Il ritardo può essere fino a più ore in qualsiasi ambiente.

Attualmente, è possibile eseguire query solo dai *segnalati ora*.

## <a name="what-do-these-usage-api-error-codes-mean"></a>Cosa significano questi codici di errore API di utilizzo?
| **Codice di stato HTTP** | **Codice errore** | **Descrizione** |
| --- | --- | --- |
| 400 /-richiesta non valida |*NoApiVersion* |Il *api-version* manca il parametro di query. |
| 400 /-richiesta non valida |*InvalidProperty* |Una proprietà manca o con un valore non valido. Il messaggio nel codice di errore nel corpo della risposta identifica la proprietà mancante. |
| 400 /-richiesta non valida |*RequestEndTimeIsInFuture* |Il valore per *ReportedEndTime* è nel futuro. I valori in futuro non sono consentiti per questo argomento. |
| 400 /-richiesta non valida |*SubscriberIdIsNotDirectTenant* |Una chiamata API del provider è utilizzato un ID di sottoscrizione che non è un tenant valido del chiamante. |
| 400 /-richiesta non valida |*SubscriptionIdMissingInRequest* |Manca l'ID sottoscrizione del chiamante. |
| 400 /-richiesta non valida |*InvalidAggregationGranularity* |È stata richiesta una granularità di aggregazione non è valido. I valori validi sono giornaliera e oraria. |
| 503 |*ServiceUnavailable* |Si è verificato un errore non irreversibile perché il servizio è occupato o la chiamata è limitata. |

## <a name="next-steps"></a>Fasi successive
[Fatturazione per i clienti e il chargeback in Azure Stack](azure-stack-billing-and-chargeback.md)

[Utilizzo del provider di risorse API](azure-stack-provider-resource-api.md)

[Utilizzo delle risorse API tenant](azure-stack-tenant-resource-usage-api.md)
