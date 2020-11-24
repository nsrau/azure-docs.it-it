---
author: rothja
ms.service: key-vault
ms.topic: include
ms.date: 04/21/2020
ms.author: jroth
ms.openlocfilehash: e4abbeadb0d30911d99fff57c0e99a3e427a6d8d
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95557136"
---
### <a name="key-transactions-maximum-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>Transazioni chiave (numero massimo di transazioni consentite entro 10 secondi, per ogni insieme di credenziali e ogni area<sup>1</sup>):

|Tipo di chiave|Chiave del modulo di protezione hardware<br>Chiave CREATE|Chiave del modulo di protezione hardware<br>Tutte le altre transazioni|Chiave software<br>Chiave CREATE|Chiave software<br>Tutte le altre transazioni|
|:---|---:|---:|---:|---:|
|RSA a 2.048 bit|5|1\.000|10|2.000|
|RSA a 3.072 bit|5|250|10|500|
|RSA a 4.096 bit|5|125|10|250|
|ECC P-256|5|1\.000|10|2.000|
|ECC P-384|5|1\.000|10|2.000|
|ECC P-521|5|1\.000|10|2.000|
|ECC SECP256K1|5|1\.000|10|2.000|

> [!NOTE]
> Nella tabella precedente si è visto ce per chiavi software RSA a 2.048 bit sono consentite 2.000 transazioni GET ogni 10 secondi. Per le chiavi del modulo di protezione hardware RSA a 2.048 bit sono consentite 1.000 transazioni GET ogni 10 secondi.
>
> Le soglie di limitazione delle richieste sono ponderate e l'imposizione viene applicata alla somma. Ad esempio, come illustrato nella tabella precedente, quando si eseguono operazioni GET su chiavi del modulo di protezione hardware RSA, usare chiavi a 4.096 bit risulta otto volte più oneroso rispetto a usare chiavi a 2.048 bit, in quanto 1.000/125 = 8.
>
> In un intervallo specificato di 10 secondi un client Azure Key Vault può eseguire *una sola* delle operazioni seguenti prima che venga restituito un codice di stato HTTP di limitazione delle richieste `429`:
> - 2\.000 transazioni GET con chiave software RSA a 2.048 bit
> - 1\.000 transazioni GET con chiave del modulo di protezione hardware RSA a 2.048 bit
> - 125 transazioni GET con chiave del modulo di protezione hardware RSA a 4.096 bit
> - 124 transazioni GET con chiave del modulo di protezione hardware RSA a 4.096 bit e 8 transazioni GET con chiave del modulo di protezione hardware RSA a 2.048 bit

### <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>Segreti, chiavi di account di archiviazione gestiti e transazioni dell'insieme di credenziali:

| Tipo di transazioni | Numero massimo di transazioni consentite entro 10 secondi, per ogni archivio e ogni area<sup>1</sup> |
| --- | --- |
| Tutte le transazioni |2.000 |

Per informazioni su come gestire la limitazione delle richieste in caso di superamento dei limiti, vedere [Guida alla limitazione delle richieste per Azure Key Vault](../articles/key-vault/general/overview-throttling.md).

<sup>1</sup> Il limite a livello di sottoscrizione per tutti i tipi di transazione corrisponde a un valore pari a cinque volte quello del limite a livello di insieme di credenziali delle chiavi. Ad esempio, le transazioni diverse da HSM hanno un limite di 5.000 transazioni ogni 10 secondi per sottoscrizione.

### <a name="azure-private-link-integration"></a>Integrazione del collegamento privato di Azure

> [!NOTE]
> Il numero di insiemi di credenziali delle chiavi con endpoint privati abilitati per ogni sottoscrizione è un limite regolabile. Il limite indicato di seguito è il limite predefinito. Se si vuole richiedere un aumento del limite per il servizio, inviare un messaggio di posta elettronica a akv-privatelink@microsoft.com. Queste richieste vengono approvate caso per caso.

| Risorsa | Limite |
| -------- | ----- |
| Endpoint privati per insieme di credenziali delle chiavi | 64 |
| Insiemi di credenziali delle chiavi con endpoint privati per sottoscrizione | 400 |