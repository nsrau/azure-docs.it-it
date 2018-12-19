---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: efa367157a8fd896cdc9680bf2ab6ba6a9e3dbb0
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2018
ms.locfileid: "53429986"
---
Transazioni chiave (transazioni max consentite entro 10 secondi, per ogni insieme di credenziali e per ogni area<sup>1</sup>):

|Tipo di chiave|Chiave HSM<br>Chiave CREATE|Chiave HSM<br>Tutte le altre transazioni|Chiave software<br>Chiave CREATE|Chiave software<br>Tutte le altre transazioni|
|:---|---:|---:|---:|---:|
|RSA a 2048 bit|5|1000|10|2000|
|RSA a 3072 bit|5|250|10|500|
|RSA a 4096 bit|5|125|10|250|
|ECC P-256|5|1000|10|2000|
|ECC P-384|5|1000|10|2000|
|ECC P-521|5|1000|10|2000|
|ECC SECP256K1|5|1000|10|2000|
|

> [!NOTE]
> Se si esamina la tabella seguente, si noterà che per le chiavi basate su software sono consentite 2000 transazioni ogni 10 secondi, mentre per le chiavi basate sul modulo di protezione hardware sono consentite 1000 transazioni ogni 10 secondi. Il rapporto delle transazioni basate su software per le chiavi a 3072 bit rispetto alle chiavi a 2048 bit è 500/2000 ovvero 0,4. In altre parole, se un cliente esegue 500 transazioni con chiave a 3072 bit in 10 secondi, viene raggiunto il limite massimo e non è possibile eseguire altre operazioni con chiave. 
   
|Tipo di chiave  | Chiave software |Chiave HSM  |
|---------|---------|---------|
|RSA a 2048 bit     |    2000     |   1000    |
|RSA a 3072 bit     |     500    |    250     |
|RSA a 4096 bit     |    125     |    250     |
|ECC P-256     |    2000     |  1000     |
|ECC P-384     |    2000     |  1000     |
|ECC P-521     |    2000     |  1000     |
|ECC SECP256K1     |    2000     |  1000     |


Segreti, chiavi di account di archiviazione gestiti e transazioni dell'insieme di credenziali:
| Tipo di transazioni | Transazioni max consentite entro 10 secondi, per ogni archivio per ogni area<sup>1</sup> |
| --- | --- |
| Tutte le transazioni |2000 |
|

Per informazioni su come gestire la limitazione delle richieste in caso di superamento dei limiti, vedere [Guida alla limitazione delle richieste per Azure Key Vault](../articles/key-vault/key-vault-ovw-throttling.md).

<sup>1</sup> La sottoscrizione prevede un limite globale per tutti i tipi di transazione, ovvero un valore 5 volte superiore al limite dell'insieme di credenziali delle chiavi. Ad esempio, le transazioni diverse da HSM hanno un limite di 5000 transazioni ogni 10 secondi per sottoscrizione.
