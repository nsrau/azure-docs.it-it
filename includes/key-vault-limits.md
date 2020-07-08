---
author: rothja
ms.service: key-vault
ms.topic: include
ms.date: 04/21/2020
ms.author: jroth
ms.openlocfilehash: 01b3c9584f3ecddbcdcc6938f5eb469510a47a4e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85839059"
---
### <a name="key-transactions-maximum-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>Transazioni chiave (numero massimo di transazioni consentite in 10 secondi per ogni insieme di credenziali per area<sup>1</sup>):

|Tipo di chiave|Chiave HSM<br>Crea chiave|Chiave HSM<br>Tutte le altre transazioni|Chiave software<br>Crea chiave|Chiave software<br>Tutte le altre transazioni|
|:---|---:|---:|---:|---:|
|RSA a 2.048 bit|5|1\.000|10|2\.000|
|RSA a 3.072 bit|5|250|10|500|
|RSA a 4.096 bit|5|125|10|250|
|ECC P-256|5|1\.000|10|2\.000|
|ECC P-384|5|1\.000|10|2\.000|
|ECC P-521|5|1\.000|10|2\.000|
|ECC SECP256K1|5|1\.000|10|2\.000|

> [!NOTE]
> Nella tabella precedente è possibile notare che per le chiavi software RSA a 2.048 bit, sono consentite 2.000 transazioni GET per 10 secondi. Per le chiavi HSM RSA a 2.048 bit, sono consentite 1.000 transazioni GET per 10 secondi.
>
> Le soglie di limitazione sono ponderate e l'imposizione è relativa alla somma. Ad esempio, come illustrato nella tabella precedente, quando si eseguono operazioni GET su chiavi HSM HSM, è più costoso usare chiavi a 4.096 bit per otto volte rispetto alle chiavi a 2.048 bit. Questo perché 1000/125 = 8.
>
> In un intervallo di 10 secondi specificato, un client Azure Key Vault può eseguire *solo una* delle operazioni seguenti prima di riscontrare un codice di `429` stato http di limitazione delle richieste:
> - 2.000 RSA 2.048-bit software-chiave GET Transactions
> - 1.000 RSA 2.048-bit HSM-chiave GET Transactions
> - 125 RSA 4.096-bit HSM-chiave GET Transactions
> - 124 RSA a 4.096 bit-chiave HSM GET Transactions e 8 RSA 2.048-bit HSM-chiave GET Transactions

### <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>Segreti, chiavi dell'account di archiviazione gestita e transazioni dell'insieme di credenziali:

| Tipo di transazioni | Numero massimo di transazioni consentite in 10 secondi, per ogni insieme di credenziali per area<sup>1</sup> |
| --- | --- |
| Tutte le transazioni |2\.000 |

Per informazioni su come gestire la limitazione delle richieste quando questi limiti vengono superati, vedere [Azure Key Vault indicazioni sulla limitazione delle richieste](../articles/key-vault/key-vault-ovw-throttling.md).

<sup>1</sup> un limite a livello di sottoscrizione per tutti i tipi di transazione è cinque volte per ogni limite di Key Vault. Ad esempio, le transazioni HSM-other per ogni sottoscrizione sono limitate a 5.000 transazioni in 10 secondi per sottoscrizione.

### <a name="azure-private-link-integration"></a>Integrazione di collegamento privato di Azure

> [!NOTE]
> Il numero di insiemi di credenziali delle chiavi con endpoint privati abilitati per ogni sottoscrizione è un limite regolabile. Il limite indicato di seguito è il limite predefinito. Se si vuole richiedere un aumento del limite per il servizio, inviare un messaggio di posta elettronica a akv-privatelink@microsoft.com. Queste richieste vengono approvate caso per caso.

| Risorsa | Limite |
| -------- | ----- |
| Endpoint privati per Key Vault | 64 |
| Insiemi di credenziali delle chiavi con endpoint privati per sottoscrizione | 400 |
