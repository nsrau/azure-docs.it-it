---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 0b9d87fd7929607da8407ae5bbfb2f6dd6d69dab
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67180552"
---
#### <a name="key-transactions-maximum-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>Transazioni chiave (numero massimo di transazioni consentito entro 10 secondi, per ogni insieme di credenziali per ogni area geografica<sup>1</sup>):

|Tipo di chiave|Chiave protetta tramite HSM<br>CREARE la chiave|Chiave protetta tramite HSM<br>Tutte le altre transazioni|Chiave software<br>CREARE la chiave|Chiave software<br>Tutte le altre transazioni|
|:---|---:|---:|---:|---:|
|RSA a 2048 bit|5|1\.000|10|2\.000|
|RSA 3.072 bit|5|250|10|500|
|RSA a 4096 bit|5|125|10|250|
|ECC P-256|5|1\.000|10|2\.000|
|ECC P-384|5|1\.000|10|2\.000|
|ECC P-521|5|1\.000|10|2\.000|
|ECC SECP256K1|5|1\.000|10|2\.000|

> [!NOTE]
> Nella tabella precedente, noteremo che per le chiavi di software RSA 2048 bit, sono consentite 2.000 transazioni GET per ogni 10 secondi. Per RSA di 2.048 bit modulo di protezione hardware le chiavi, sono consentite 1.000 transazioni GET per ogni 10 secondi.
>
> Le soglie di limitazione vengono ponderate e imposizione è attiva la somma. Ad esempio, come illustrato nella tabella precedente, quando si eseguono operazioni GET sulle chiavi di RSA HSM, è otto volte più costosa da usare le chiavi di 4096 bit rispetto alle chiavi di 2.048 bit. Infatti, 1.000/125 = 8.
>
> In un determinato intervallo di 10 secondi, è possibile eseguire un client di Azure Key Vault *sola* delle operazioni seguenti prima di incontra un `429` la limitazione delle richieste di codice di stato HTTP:
> - 2\.000 transazioni di GET-chiave del software RSA di 2.048 bit.
> - 1\.000 transazioni RSA di 2.048 bit chiave protetta tramite HSM GET
> - Transazioni di 125 GET chiave protetta tramite HSM RSA 4096 bit
> - Le transazioni di GET chiave di HSM RSA 4096 bit 124 e GET chiave protetta tramite HSM RSA di 2.048 bit 8

#### <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>I segreti, chiavi dell'account di archiviazione gestito e le transazioni dell'insieme di credenziali:
| Tipo di transazioni | Numero massimo di transazioni consentito entro 10 secondi, per ogni insieme di credenziali per ogni area<sup>1</sup> |
| --- | --- |
| Tutte le transazioni |2\.000 |

Per informazioni su come gestire la limitazione delle richieste quando questi limiti vengono superati, vedere [linee guida sulla limitazione di Azure Key Vault](../articles/key-vault/key-vault-ovw-throttling.md).

<sup>1</sup> un limite a livello di sottoscrizione per tutti i tipi di transazione è cinque volte per ogni limite dell'insieme di credenziali chiave. Ad esempio, HSM-altre transazioni per ogni sottoscrizione sono limitate a 5.000 transazioni entro 10 secondi per ogni sottoscrizione.
