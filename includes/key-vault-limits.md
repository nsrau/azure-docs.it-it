---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: ed0c387f9785336fbf18b3fd3c0cd9a7b09df633
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2018
ms.locfileid: "52279718"
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

Segreti, chiavi di account di archiviazione gestiti e transazioni dell'insieme di credenziali:
| Tipo di transazioni | Transazioni max consentite entro 10 secondi, per ogni archivio per ogni area<sup>1</sup> |
| --- | --- |
| Tutte le transazioni |2000 |
|

Per informazioni su come gestire la limitazione delle richieste in caso di superamento dei limiti, vedere [Guida alla limitazione delle richieste per Azure Key Vault](../articles/key-vault/key-vault-ovw-throttling.md).

<sup>1</sup> La sottoscrizione prevede un limite globale per tutti i tipi di transazione, ovvero un valore 5 volte superiore al limite dell'insieme di credenziali delle chiavi. Ad esempio, le transazioni diverse da HSM hanno un limite di 5000 transazioni ogni 10 secondi per sottoscrizione.
