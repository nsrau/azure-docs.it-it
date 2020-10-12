---
title: Protocollo VBS (Virtualization-based Security) per l'attestazione di Azure
description: Protocollo dell'attestazione VBS
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: e5cc3b5fb7ca38df196119de12d346f5d0346b58
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91343783"
---
# <a name="virtualization-based-security-vbs-attestation-protocol"></a>Protocollo di attestazione della sicurezza basata sulla virtualizzazione (VBS) 

Per Microsoft Azure attestazione per garantire una sicurezza elevata garantisce che i dati segnalati siano autentici, è necessario compilare una catena di trust dal firmware al lancio dell'hypervisor e del kernel protetto. Per ottenere questa attestazione di Azure, è necessario attestare lo stato di avvio del computer prima di poter stabilire la relazione di trust nell'enclave protetta. Il sistema operativo, l'hypervisor e i file binari del kernel protetti devono essere firmati dalle autorità Microsoft ufficiali corrette e configurati in modo sicuro. Dopo aver associato il trust tra il Trusted Platform Module (TPM) e l'integrità dell'hypervisor, è possibile considerare attendibile il IDKS VBS fornito nel log di avvio misurato. In questo modo è possibile verificare che una coppia di chiavi sia stata generata dall'enclave e mentare un rapporto di attestazione che associa l'attendibilità a tale chiave e contiene altre attestazioni, ad esempio il livello di sicurezza e le proprietà di attestazione di avvio.

## <a name="protocol-messages"></a>Messaggi del protocollo

### <a name="init-message"></a>Messaggio init

#### <a name="direction"></a>Direzione

Attestazione di Azure > client

#### <a name="payload"></a>Payload

```
{
  "type": "aikcert"
}
```

"Type" (stringa ASCII): rappresenta il tipo di attestazione richiesto. Attualmente è supportato solo "aikcert".

### <a name="challenge-message"></a>Messaggio di richiesta

#### <a name="direction"></a>Direzione

Attestazione di Azure-client >

#### <a name="payload"></a>Payload

```
{

  "challenge": "<BASE64URL(CHALLENGE)>",
  
  "service_context": "<BASE64URL(SERVICECONTEXT)>"
  
}
```

**Challenge** (BASE64URL (ottetti)): valore casuale emesso dal servizio.

**service_context** (BASE64URL (ottetti)): contesto opaco, crittografato creato dal servizio che include, tra gli altri, la richiesta e la data di scadenza per la richiesta.


### <a name="request-message"></a>Messaggio di richiesta

#### <a name="direction"></a>Direzione

Attestazione di Azure > client 

#### <a name="payload"></a>Payload

```
{

  "request": "<JWS>"
  
}
```

**Request** (JWS): la richiesta è costituita da una struttura JWS (JSON Web Signature). Di seguito sono riportati l'intestazione protetta JWS e il payload JWS. Come in qualsiasi struttura JWS, il valore finale è costituito da:

BASE64URL (UTF8 (intestazione protetta JWS)) | | '.' ||

BASE64URL (payload JWS) | | '.' ||

BASE64URL (firma JWS)

##### <a name="jws-protected-header"></a>Intestazione protetta JWS

```
{
  "alg": "PS256",
  "typ": "attReq"
  // no "kid" parameter as the key specified by attest_key MUST sign this JWS to prove possession.
}
```

##### <a name="jws-payload"></a>Payload JWS

Il payload JWS può essere di tipo Basic o VBS. Basic viene utilizzato quando l'evidenza di attestazione non include i dati VBS.

Esempio di base

``` 
{
  "att_type": "basic",
  "att_data": {
    "rp_id": "<URL>",
    "rp_data": "<BASE64URL(RPCUSTOMDATA)>",
    "challenge": "<BASE64URL(CHALLENGE)>",
    "tpm_att_data": {
      "srtm_boot_log": "<BASE64URL(SRTMBOOTLOG)>",
      "srtm_resume_log": "<BASE64URL(SRTMRESUMELOG)>",
      "drtm_boot_log": "<BASE64URL(DRTMBOOTLOG)>",
      "drtm_resume_log": "<BASE64URL(DRTMRESUMELOG)>",
      "aik_cert": "<BASE64URL(AIKCERTIFICATE)>",
      // aik_pub is represented as a JSON Web Key (JWK) object (RFC 7517).
      "aik_pub": {
        "kty": "RSA",
        "n": "<Base64urlUInt(MODULUS)>",
        "e": "<Base64urlUInt(EXPONENT)>"
      },
      "current_claim": "<BASE64URL(CURRENTCLAIM)>",
      "boot_claim": "<BASE64URL(BOOTCLAIM)>"
    },
    // attest_key is represented as a JSON Web Key (JWK) object (RFC 7517).
    "attest_key": {
      "kty": "RSA",
      "n": "<Base64urlUInt(MODULUS)>",
      "e": "<Base64urlUInt(EXPONENT)>"
    },
    "custom_claims": [
      {
        "name": "<name>",
        "value": "<value>",
        "value_type": "<value_type>"
      },
      {
        "name": "<name>",
        "value": "<value>",
        "value_type": "<value_type>"
      }
    ],
    "service_context": "<BASE64URL(SERVICECONTEXT)>"
  }
}
```

Esempio di VBS

``` 
{
  "att_type": "vbs",
  "att_data": {
    "report_signed": {
      "rp_id": "<URL>",
      "rp_data": "<BASE64URL(RPCUSTOMDATA)>",
      "challenge": "<BASE64URL(CHALLENGE)>",
      "tpm_att_data": {
        "srtm_boot_log": "<BASE64URL(SRTMBOOTLOG)>",
        "srtm_resume_log": "<BASE64URL(SRTMRESUMELOG)>",
        "drtm_boot_log": "<BASE64URL(DRTMBOOTLOG)>",
        "drtm_resume_log": "<BASE64URL(DRTMRESUMELOG)>",
        "aik_cert": "<BASE64URL(AIKCERTIFICATE)>",
        // aik_pub is represented as a JSON Web Key (JWK) object (RFC 7517).
        "aik_pub": {
          "kty": "RSA",
          "n": "<Base64urlUInt(MODULUS)>",
          "e": "<Base64urlUInt(EXPONENT)>"
        },
        "current_claim": "<BASE64URL(CURRENTCLAIM)>",
        "boot_claim": "<BASE64URL(BOOTCLAIM)>"
      },
      // attest_key is represented as a JSON Web Key (JWK) object (RFC 7517).
      "attest_key": {
        "kty": "RSA",
        "n": "<Base64urlUInt(MODULUS)>",
        "e": "<Base64urlUInt(EXPONENT)>"
      },
      "custom_claims": [
        {
          "name": "<name>",
          "value": "<value>",
          "value_type": "<value_type>"
        },
        {
          "name": "<name>",
          "value": "<value>",
          "value_type": "<value_type>"
        }
      ],
      "service_context": "<BASE64URL(SERVICECONTEXT)>"
    },
    "vbs_report": "<BASE64URL(REPORT)>"
  }
}
``` 

**rp_id** (StringOrURI): identificatore della relying party. Utilizzato dal servizio nel calcolo dell'attestazione ID computer

**rp_data** (BASE64URL (ottetti)): dati opachi passati dall'relying party. Viene in genere utilizzato dal relying party come parametro nonce per garantire l'aggiornamento del report

**Challenge** (BASE64URL (ottetti)): valore casuale emesso dal servizio

**tpm_att_data**: dati di attestazione correlati al TPM

- **srtm_boot_log (BASE64URL (ottetti))**: log di avvio SRTM come recuperato dalla funzione Tbsi_Get_TCG_Log_Ex con tipo di log = TBS_TCGLOG_SRTM_BOOT

- **srtm_resume_log (BASE64URL (ottetti))**: SRTM Resume log come recuperato dalla funzione Tbsi_Get_TCG_Log_Ex con tipo di log = TBS_TCGLOG_SRTM_RESUME

- **drtm_boot_log (BASE64URL (ottetti))**: log di avvio drtm come recuperato dalla funzione Tbsi_Get_TCG_Log_Ex con tipo di log = TBS_TCGLOG_DRTM_BOOT

- **drtm_resume_log (BASE64URL (ottetti))**: drtm Resume log come recuperato dalla funzione Tbsi_Get_TCG_Log_Ex con tipo di log = TBS_TCGLOG_DRTM_RESUME

- **aik_cert (BASE64URL (ottetti))**: il certificato X. 509 per AIK come restituito dalla funzione NCryptGetProperty con property = NCRYPT_CERTIFICATE_PROPERTY

- **aik_pub**: la parte pubblica di AIK rappresentata come oggetto chiave Web JSON (JWK) (RFC 7517)

- **current_claim (BASE64URL (ottetti))**: attestazione attestazione per lo stato PCR corrente restituita dalla funzione NCryptCreateClaim con dwClaimType = NCRYPT_CLAIM_PLATFORM e parametro NCRYPTBUFFER_TPM_PLATFORM_CLAIM_PCR_MASK impostati per includere tutti i PCRS. Il problema inviato dal servizio deve essere usato anche nel calcolo di questa attestazione

- **boot_claim (BASE64URL (ottetti))**: attestazione attestazione per lo stato PCR all'avvio restituita dalla funzione NCryptCreateClaim con dwClaimType = NCRYPT_CLAIM_PLATFORM e parametro NCRYPTBUFFER_TPM_PLATFORM_CLAIM_PCR_MASK impostato per includere tutti PCRS

**report vbs** (BASE64URL (ottetti)): report di attestazione dell'enclave vbs restituito dalla funzione EnclaveGetAttestationReport. Il parametro EnclaveData deve corrispondere all'hash SHA-512 del valore di report_signed (incluse le parentesi graffe di apertura e di chiusura). L'input della funzione hash è UTF8 (report_signed)

**attest_key**: la parte pubblica della chiave enclave rappresentata come oggetto chiave Web JSON (JWK) (RFC 7517)

**custom_claims**: matrice di attestazioni enclave personalizzate inviate al servizio che possono essere valutate dal criterio. Attestazione

- **nome (stringa)**: nome dell'attestazione. Questo nome verrà aggiunto a un URL determinato dal servizio di attestazione (per evitare conflitti) e la stringa concatenata diventerà il tipo dell'attestazione che può essere utilizzata nei criteri.

- **value (String)**: valore dell'attestazione

- **value_type (String)**: tipo di dati del valore dell'attestazione

**service_context** (BASE64URL (ottetti)): contesto opaco, crittografato creato dal servizio che include, tra gli altri, la richiesta e la data di scadenza per la richiesta.

### <a name="report-message"></a>Messaggio del report

#### <a name="direction"></a>Direzione

Attestazione di Azure-client >

#### <a name="payload"></a>Payload

```
{
  "report": "<JWT>"
}
```

**report** (JWT): il report di attestazione in formato JWT (JSON Web Token) (RFC 7519).
