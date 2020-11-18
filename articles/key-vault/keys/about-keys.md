---
title: Informazioni sulle chiavi - Azure Key Vault
description: Panoramica dei dettagli di sviluppo e dell'interfaccia REST di Azure Key Vault per le chiavi.
services: key-vault
author: amitbapat
manager: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: overview
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 2ae7b28d5e9e7a520ee8cbd090b6681d5ad7015a
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422757"
---
# <a name="about-keys"></a>Informazioni sulle chiavi

Azure Key Vault offre due tipi di risorse per l'archiviazione e la gestione delle chiavi crittografiche:

|Tipo di risorsa|Metodi di protezione della chiave|URL di base dell'endpoint del piano dati|
|--|--|--|
| **Insiemi di credenziali** | Protetta da software<br/><br/>e<br/><br/>Con protezione HSM (con SKU Premium)</li></ul> | https://{nome-insiemecredenziali}.vault.azure.net |
| **Pool di moduli di protezione hardware gestiti** | Con protezione HSM | https://{nome-hsm}.managedhsm.azure.net |
||||

- **Insiemi di credenziali**: gli insiemi di credenziali offrono una soluzione di gestione delle chiavi a basso costo, facile da distribuire, multi-tenant, con resilienza della zona (dove disponibile) e a disponibilità elevata adatta per gli scenari di applicazioni cloud più comuni.
- **Moduli di protezione hardware gestiti**: i moduli di protezione hardware gestiti sono moduli a singolo tenant, con resilienza della zona (se disponibile) e a disponibilità elevata per l'archiviazione e la gestione delle chiavi crittografiche. Sono particolarmente adatti per le applicazioni e gli scenari di utilizzo che gestiscono chiavi con valore elevato. Soddisfano inoltre i requisiti di sicurezza, conformità e normativi più rigorosi. 

> [!NOTE]
> Gli insiemi di credenziali consentono anche di archiviare e gestire diversi tipi di oggetti, come segreti, certificati e chiavi dell'account di archiviazione, oltre alle chiavi crittografiche.

Le chiavi crittografiche in Key Vault sono rappresentate come oggetti JSON Web Key [JWK]. Le specifiche per JSON (JavaScript Object Notation) e JOSE (JavaScript Object Signing and Encryption) sono:

-   [JSON Web Key (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key)  
-   [JSON Web Encryption (JWE)](http://tools.ietf.org/html/draft-ietf-jose-json-web-encryption)  
-   [JSON Web Algorithms (JWA)](http://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms)  
-   [JSON Web Signature (JWS)](https://tools.ietf.org/html/draft-ietf-jose-json-web-signature) 

Le specifiche JWK/JWA di base vengono inoltre estese per abilitare tipi di chiave univoci per le implementazioni di Azure Key Vault e dei moduli di protezione hardware gestiti. 

Le chiavi con protezione HSM vengono elaborate in un modulo di protezione hardware e rimangono sempre entro i limiti di questa protezione. 

- Gli insiemi di credenziali usano moduli di protezione hardware convalidati in base agli standard **FIPS 140-2 livello 2** per proteggere le chiavi con protezione HSM nell'infrastruttura back-end HSM. 
- I pool di moduli di protezione hardware gestiti usano moduli di protezione hardware convalidati in base agli standard **FIPS 140-2 livello 3** per proteggere le chiavi. Ogni pool di moduli di protezione hardware è un'istanza a tenant singolo isolata con il proprio [dominio di sicurezza](../managed-hsm/security-domain.md) che fornisce l'isolamento crittografico completo da tutti gli altri pool che condividono la stessa infrastruttura hardware.

Queste chiavi sono protette nei pool di moduli di protezione hardware a tenant singolo. È possibile importare una chiave RSA, EC o simmetrica protetta tramite software o mediante l'esportazione da un modulo di protezione hardware supportato. È possibile anche generare chiavi nei pool di moduli di protezione hardware. Quando si importano chiavi con protezione HSM usando il metodo descritto nella [specifica BYOK (Bring Your Own Key)](../keys/byok-specification.md), viene abilitato il materiale della chiave di trasporto sicuro nei pool di moduli di protezione hardware gestiti. 

Per ulteriori informazioni sui limiti geografici, vedere [Centro di protezione Microsoft Azure](https://azure.microsoft.com/support/trust-center/privacy/)

## <a name="key-types-and-protection-methods"></a>Tipi di chiave e metodi di protezione

Key Vault supporta le chiavi RSA, EC e simmetriche. 

### <a name="hsm-protected-keys"></a>Chiavi protette dal modulo di protezione hardware

|Tipo di chiave|Insiemi di credenziali (solo SKU Premium)|Pool di moduli di protezione hardware gestiti|
|--|--|--|--|
**EC-HSM**: chiave a curva ellittica|Modulo di protezione hardware FIPS 140-2 livello 2|Modulo di protezione hardware FIPS 140-2 livello 3
**RSA-HSM**: chiave RSA|Modulo di protezione hardware FIPS 140-2 livello 2|Modulo di protezione hardware FIPS 140-2 livello 3
**oct-HSM**: Simmetrica|Non supportato|Modulo di protezione hardware FIPS 140-2 livello 3
||||

### <a name="software-protected-keys"></a>Chiavi protette tramite software

|Tipo di chiave|Insiemi di credenziali|Pool di moduli di protezione hardware gestiti|
|--|--|--|--|
**RSA**: chiave RSA protetta tramite software|FIPS 140-2 livello 1|Non supportato
**EC**: chiave a curva ellittica protetta tramite software|FIPS 140-2 livello 1|Non supportato
||||

Per informazioni dettagliate su ogni tipo di chiave, oltre che su algoritmi, operazioni, attributi e tag, vedere [Tipi di chiave, algoritmi e operazioni](about-keys-details.md).

## <a name="next-steps"></a>Passaggi successivi
- [Informazioni su Key Vault](../general/overview.md)
- [Informazioni sul modulo di protezione hardware gestito](../managed-hsm/overview.md)
- [Informazioni sui segreti](../secrets/about-secrets.md)
- [Informazioni sui certificati](../certificates/about-certificates.md)
- [Panoramica dell'API REST di Key Vault](../general/about-keys-secrets-certificates.md)
- [Autenticazione, richieste e risposte](../general/authentication-requests-and-responses.md)
- [Guida per gli sviluppatori all'insieme di credenziali delle chiavi](../general/developers-guide.md)