---
title: Set di attestazioni di Attestazione di Azure
description: Il set di attestazioni di Attestazione di Azure.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 555dccbd3c2dfe61bac5891514deface6f8a877d
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "89236932"
---
# <a name="claim-sets"></a>Set di attestazioni

Le attestazioni generate nel processo di attestazione di enclavi usando Attestazione di Microsoft Azure possono essere divise nelle categorie seguenti:

- **Attestazioni in ingresso**: le attestazioni generate da Attestazione di Microsoft Azure dopo l'analisi dell'evidenza dell'attestazione.

- **Attestazioni in uscita**: le attestazioni create come output da Attestazione di Azure. Sono incluse tutte le attestazioni che dovranno finire nel token di attestazione.

- **Attestazioni di proprietà**: le attestazioni create come output da Attestazione di Azure. Sono incluse tutte le attestazioni che rappresentano proprietà del token di attestazione, ad esempio la codifica del report, la durata di validità del report e così via.

Le attestazioni seguenti sono definite da JWT RFC e vengono usate da Attestazione di Azure nell'oggetto della risposta:

- **Attestazione "iss" (issuer)** : l'attestazione "iss" (issuer) identifica l'entità che ha emesso il token JWT. L'elaborazione di questa attestazione è in genere specifica dell'applicazione. Il valore "iss" è una stringa con distinzione tra maiuscole e minuscole contenente un valore StringOrURI.
- **Attestazione "iat" (issued at)** : l'attestazione "iat" (issued at) identifica l'ora in cui è stato emesso il token JWT. Questa attestazione può essere usata per determinare l'età del token JWT. Il valore DEVE essere un numero contenente un valore NumericDate.
- **Attestazione "exp" (expiration time)** : L'attestazione "exp" (expiration time) identifica l'ora di scadenza a partire dalla quale o successivamente alla quale il token JWT non deve essere accettato per l'elaborazione. L'elaborazione dell'attestazione "exp" richiede che la data/ora corrente sia precedente alla data/ora di scadenza indicata nell'attestazione "exp".

  Nota: viene aggiunto un margine di tempo di 5 minuti all'ora di emissione (iat) per tenere conto dello sfasamento dell'orologio.
- **Attestazione "nbf" (not before)** : l'attestazione "nbf" (not before) identifica l'ora prima della quale il token JWT NON deve essere accettato per l'elaborazione. L'elaborazione dell'attestazione "nbf" richiede che la data/ora corrente sia successiva o uguale alla data/ora "non prima di" indicata nell'attestazione "nbf".
  Nota: viene aggiunto un margine di tempo di 5 minuti all'ora di emissione (iat) per tenere conto dello sfasamento dell'orologio.

## <a name="claims-issued-by-azure-attestation-in-sgx-enclaves"></a>Attestazioni emesse da Attestazione di Azure nelle enclavi SGX

### <a name="incoming-claims"></a>Attestazioni in ingresso 

- **$is-debuggable**: valore booleano che indica se per l'enclave è abilitato o meno il debug
- **$sgx-mrsigner**: valore con codifica esadecimale del campo "mrsigner" dell'offerta
- **$sgx-mrenclave**: valore con codifica esadecimale del campo "mrenclave" dell'offerta
- **$product-id**
- **$svn**: numero di versione di sicurezza codificato nell'offerta 
- **$tee**: tipo di enclave 

### <a name="outgoing-claims"></a>Attestazioni in uscita

- **is-debuggable**: valore booleano che indica se per l'enclave è abilitato o meno il debug
- **sgx-mrsigner**: valore con codifica esadecimale del campo "mrsigner" dell'offerta
- **sgx-mrenclave**: valore con codifica esadecimale del campo "mrenclave" dell'offerta
- **product-id**
- **svn**: numero di versione di sicurezza codificato nell'offerta 
- **tee**: tipo di enclave 
- **maa-ehd**:  versione con codifica Base64Url dei "dati contenuti nell'enclave" specificata nella richiesta di attestazione 
- **aas-ehd**:  versione con codifica Base64Url dei "dati contenuti nell'enclave" specificata nella richiesta di attestazione 

## <a name="claims-issued-by-azure-attestation-in-vbs-enclaves"></a>Attestazioni emesse da Attestazione di Azure negli enclavi VBS

### <a name="incoming-claims-can-also-be-used-as-outgoing-claims"></a>Attestazioni in ingresso (possono anche essere usate come attestazioni in uscita)

- **aikValidated**:  valore booleano che indica se il certificato AIK (Attestation Identity Key) è stato convalidato o meno.
- **aikPubHash**:  stringa contenente base64(SHA256(chiave pubblica AIK nel formato DER)).
- **tpmVersion**:   valore intero contenente la versione principale di TPM (Trusted Platform Module).
- **secureBootEnabled**: valore booleano che indica se l'avvio protetto è abilitato.
- **iommuEnabled**:  valore booleano che indica se l'unità Iommu (Input-output memory management) è abilitata.
- **bootDebuggingDisabled**: valore booleano che indica se il debug di avvio è disabilitato.
- **notSafeMode**:  valore booleano che indica se Windows non è in esecuzione in modalità sicura.
- **notWinPE**:  valore booleano che indica se Windows non è in esecuzione in modalità WinPE.
- **vbsEnabled**:  valore booleano che indica se VBS è abilitato.
- **vbsReportPresent**:  valore booleano che indica se è disponibile un report dell'enclave VBS.
- **enclaveAuthorId**:  valore stringa contenente il valore con codifica Base64Url dell'ID autore dell'enclave - L'identificatore dell'autore del modulo primario per l'enclave.
- **enclaveImageId**:  valore stringa contenente il valore con codifica Base64Url dell'ID immagine dell'enclave - L'identificatore dell'immagine del modulo primario per l'enclave.
- **enclaveOwnerId**:  valore stringa contenente il valore con codifica Base64Url dell'ID proprietario dell'enclave - L'identificatore del proprietario dell'enclave.
- **enclaveFamilyId**:  valore stringa contenente il valore con codifica Base64Url dell'ID famiglia dell'enclave. L'identificatore della famiglia del modulo primario per l'enclave.
- **enclaveSvn**:  valore intero contenente il numero di versione di sicurezza del modulo primario per l'enclave.
- **enclavePlatformSvn**:  valore intero contenente il numero di versione di sicurezza della piattaforma che ospita l'enclave.
- **enclaveFlags**:  l'attestazione enclaveFlags è un valore intero contenente i flag che descrivono i criteri di runtime per l'enclave.
  
### <a name="outgoing-claims"></a>Attestazioni in uscita

- **policy_hash**:  valore stringa contenente l'hash SHA256 del testo del criterio calcolato da BASE64URL(SHA256(BASE64URL(UTF8(testo del criterio)))).
- **policy_signer**:  valore stringa contenente un token JWK con la chiave pubblica o la catena di certificati presente nell'intestazione del criterio firmato.
- **ver (Version)** :  valore stringa contenente una descrizione del report. Attualmente 1.0.
- **Attestazione cnf (confirmation)** :  l'attestazione "cnf" viene usata per identificare la chiave di prova del possesso. L'attestazione di conferma, come definito in RFC 7800, contiene la parte pubblica della chiave dell'enclave attestata rappresentata come oggetto JWK (JSON Web Key) (RFC 7517).
- **rp_data (relying party data)** :  I dati della relying party, se presenti, specificati nella richiesta, usati dalla relying party come nonce per garantire l'aggiornamento del report.
- **Attestazione "jti" (JWT ID)** : l'attestazione "jti" (JWT ID) fornisce un identificatore univoco per il token JWT. Il valore dell'identificatore viene assegnato in modo da garantire che esista una probabilità trascurabile che lo stesso valore venga assegnato accidentalmente a un oggetto dati diverso.

### <a name="property-claims"></a>Attestazioni di proprietà

- **report_validity_in_minutes**: attestazione di tipo intero che indica la durata di validità del token.
  - **Default value(time)** : un giorno in minuti.
  - **Maximum value(time)** : un anno in minuti.
- **omit_x5c**: attestazione di tipo booleano che indica se Attestazione di Azure deve omettere il certificato usato per fornire la prova dell'autenticità del servizio. Se true, x5t verrà aggiunto al token di attestazione. Se false, x5c verrà aggiunto al token di attestazione.

## <a name="next-steps"></a>Passaggi successivi
- [Come creare e firmare un criterio di attestazione](author-sign-policy.md)
- [Configurare Attestazione di Azure con PowerShell](quickstart-powershell.md)
