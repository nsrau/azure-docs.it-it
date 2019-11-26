---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 2d16febd4676ca7ba763eb7bc6dcecda4608ebb5
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224410"
---
#### <a name="key-transactions-maximum-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>Transazioni chiave (numero massimo di transazioni consentite in 10 secondi per ogni insieme di credenziali per area<sup>1</sup>):

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
> In un intervallo di 10 secondi specificato, un client Azure Key Vault può eseguire *solo una* delle operazioni seguenti prima di rilevarne una `429` il codice di stato http:
> - 2\.000 RSA 2.048-bit software-chiave GET Transactions
> - 1\.000 RSA 2.048-bit HSM-chiave GET Transactions
> - 125 RSA 4.096-bit HSM-chiave GET Transactions
> - 124 RSA a 4.096 bit-chiave HSM GET Transactions e 8 RSA 2.048-bit HSM-chiave GET Transactions

#### <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>Segreti, chiavi dell'account di archiviazione gestita e transazioni dell'insieme di credenziali:
| Tipo di transazioni | Numero massimo di transazioni consentite in 10 secondi, per ogni insieme di credenziali per area<sup>1</sup> |
| --- | --- |
| Tutte le transazioni |2\.000 |

Per informazioni su come gestire la limitazione delle richieste quando questi limiti vengono superati, vedere [Azure Key Vault indicazioni sulla limitazione delle richieste](../articles/key-vault/key-vault-ovw-throttling.md).

<sup>1</sup> un limite a livello di sottoscrizione per tutti i tipi di transazione è cinque volte per ogni limite di Key Vault. Ad esempio, le transazioni HSM-other per ogni sottoscrizione sono limitate a 5.000 transazioni in 10 secondi per sottoscrizione.
