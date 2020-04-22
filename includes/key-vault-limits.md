---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 04/21/2020
ms.author: jroth
ms.openlocfilehash: 8247b1cedc2c5ebc8577af6be485aed0fcd5d6af
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768765"
---
### <a name="key-transactions-maximum-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>Transazioni chiave (numero massimo di transazioni consentite in 10 secondi, per vault per area<sup>geografica 1):</sup>

|Tipo di chiave|Tasto HSM<br>Chiave CREA|Tasto HSM<br>Tutte le altre transazioni|Chiave software<br>Chiave CREA|Chiave software<br>Tutte le altre transazioni|
|:---|---:|---:|---:|---:|
|RSA 2.048 bit|5|1\.000|10|2\.000|
|RSA 3.072 bit|5|250|10|500|
|RSA 4.096 bit|5|125|10|250|
|ECC P-256|5|1\.000|10|2\.000|
|ECC P-384|5|1\.000|10|2\.000|
|ECC P-521|5|1\.000|10|2\.000|
|ECC SECP256K1|5|1\.000|10|2\.000|

> [!NOTE]
> Nella tabella precedente, vediamo che per RSA chiavi software a 2.048 bit, 2.000 transazioni GET per 10 secondi. Per le chiavi HSM rsA a 2.048 bit, sono consentite 1.000 transazioni GET per 10 secondi.
>
> Le soglie di limitazione vengono ponderate e l'imposizione è sulla relativa somma. Ad esempio, come illustrato nella tabella precedente, quando si eseguono operazioni GET su RSA HSM-keys, è otto volte più costoso utilizzare chiavi a 4.096 bit rispetto alle chiavi a 2.048 bit. Questo perché 1.000/125 - 8.
>
> In un intervallo di 10 secondi specificato, un client dell'insieme di `429` credenziali delle chiavi di Azure può eseguire solo una delle operazioni seguenti prima di incontrare un codice di stato HTTP di limitazione:In a given 10-second interval, an Azure Key Vault client can do *only one* of the following operations before it encounters a throttling HTTP status code:
> - Transazioni GET chiave software da 2.000 RSA a 2.048 bit
> - 1.000 transazioni GET chiave HSM a 2.048 bit
> - 125 transazioni GET chiave HSM a 4.096 bit
> - 124 transazioni GET chiave HSM a 4.096 bit e 8 transazioni GET RSA a 2.048 bit HSM-key

### <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>Segreti, chiavi dell'account di archiviazione gestita e transazioni dell'insieme di credenziali:Secrets, managed storage account keys, and vault transactions:

| Tipo di transazioni | Numero massimo di transazioni consentite in 10 secondi, per vault per regione<sup>1</sup> |
| --- | --- |
| Tutte le transazioni |2\.000 |

Per informazioni su come gestire la limitazione delle richieste quando questi limiti vengono superati, vedere Linee guida per la [limitazione delle richieste di Archiviazione delle chiavi](../articles/key-vault/key-vault-ovw-throttling.md)di Azure.For information on how to handle throttling when these limits are exceeded, see Azure Key Vault throttling guidance .

<sup>1</sup> Un limite a livello di sottoscrizione per tutti i tipi di transazione è cinque volte per limite dell'insieme di credenziali delle chiavi. Ad esempio, le altre transazioni HSM-per sottoscrizione sono limitate a 5.000 transazioni in 10 secondi per sottoscrizione.

### <a name="azure-private-link-integration"></a>Integrazione di Azure Private Link

| Risorsa | Limite |
| -------- | ----- |
| Endpoint privati per insieme di credenziali delle chiaviPrivate endpoints per key vault | 64 |
| Archivi delle chiavi con endpoint privati per sottoscrizioneKey vaults with private endpoints per subscription | 64 |
