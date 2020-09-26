---
title: Specifica della chiave Bring your own-Azure Key Vault | Microsoft Docs
description: Questo documento descrive la specifica di Bring your own key.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: conceptual
ms.date: 05/29/2020
ms.author: ambapat
ms.openlocfilehash: feef35ef86a933f32949468366fea85eb87d4866
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91315780"
---
# <a name="bring-your-own-key-specification"></a>Specifica di BYOK (Bring Your Own Key)

Questo documento descrive le specifiche per l'importazione di chiavi protette da HSM da HSM locali dei clienti in Key Vault.

## <a name="scenario"></a>Scenario

Un cliente Key Vault desidera trasferire in modo sicuro una chiave dal modulo di protezione hardware locale esterno ad Azure, nel Azure Key Vault di supporto HSM. Il processo di importazione di una chiave generata all'esterno Key Vault viene in genere indicato come Bring Your Own Key (BYOK).

Di seguito sono riportati i requisiti:
* La chiave da trasferire non esiste mai all'esterno di un modulo di protezione hardware in formato testo normale.
* Al di fuori di un modulo di protezione hardware, la chiave da trasferire è sempre protetta da una chiave mantenuta nel modulo di protezione hardware Azure Key Vault

## <a name="terminology"></a>Terminologia

|Nome chiave|Tipo chiave|Origine|Descrizione|
|---|---|---|---|
|Chiave KEK (Key Exchange Key)|RSA|Modulo di protezione hardware di Azure Key Vault|Coppia di chiavi RSA supportata da HSM generata in Azure Key Vault
Chiave di wrapping|AES|Modulo di protezione hardware del fornitore|Chiave AES [temporanea] generata da HSM locale
Chiave di destinazione|RSA, EC, AES|Modulo di protezione hardware del fornitore|Chiave da trasferire nel modulo di protezione hardware di Azure Key Vault

Chiave per lo **scambio delle chiavi**: chiave supportata da HSM generata dal cliente nell'insieme di credenziali delle chiavi in cui verrà importata la chiave BYOK. Questo KEK deve avere le proprietà seguenti:

* Si tratta di una chiave RSA-HSM (4096 bit o 3072 bit o 2048 bit)
* Il key_ops è stato risolto (solo ' Import '), che ne consentirà l'uso solo durante BYOK
* Deve trovarsi nello stesso insieme di credenziali in cui verrà importata la chiave di destinazione

## <a name="user-steps"></a>Passaggi utente

Per eseguire un trasferimento della chiave, un utente esegue le operazioni seguenti:

1. Genera KEK.
2. Recuperare la chiave pubblica di KEK.
3. Uso dello strumento BYOK fornito dal fornitore del modulo di protezione hardware: importare la chiave KEK nel modulo di protezione hardware di destinazione ed esportare la chiave di destinazione protetta da KEK.
4. Importare la chiave di destinazione protetta in Azure Key Vault.

I clienti utilizzano lo strumento BYOK e la documentazione fornita dal fornitore del modulo di protezione hardware per completare i passaggi 3. Produce un BLOB di trasferimento della chiave (un file ". Byok").


## <a name="hsm-constraints"></a>Vincoli HSM

Il modulo di protezione hardware esistente può applicare vincoli alla chiave che gestiscono, tra cui:
* Potrebbe essere necessario configurare il modulo di protezione hardware per consentire l'esportazione basata sul wrapping della chiave
* Potrebbe essere necessario contrassegnare la chiave di destinazione CKA_EXTRACTABLE per il modulo di protezione hardware per consentire l'esportazione controllata
* In alcuni casi, potrebbe essere necessario contrassegnare la chiave KEK e la chiave di wrapping come CKA_TRUSTED. Questa operazione consente di eseguire il wrapping delle chiavi nel modulo di protezione hardware.

La configurazione del modulo di protezione hardware di origine è, in genere, al di fuori dell'ambito di questa specifica. Microsoft si aspetta che il fornitore del modulo di protezione hardware produca la documentazione associata allo strumento BYOK per includere tali passaggi di configurazione.

> [!NOTE]
> I passaggi 1, 2 e 4 descritti di seguito possono essere eseguiti con altre interfacce, ad esempio Azure PowerShell e il portale di Azure. Possono essere eseguite anche a livello di codice usando funzioni equivalenti in Key Vault SDK.

### <a name="step-1-generate-kek"></a>Passaggio 1: generare KEK

Usare il comando **AZ Key Vault Key create** per creare KEK con le operazioni chiave impostate per l'importazione. Annotare l'identificatore di chiave ' Kid ' restituito dal comando seguente.

```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-retrieve-the-public-key-of-the-kek"></a>Passaggio 2: recuperare la chiave pubblica di KEK

Scaricare la parte relativa alla chiave pubblica di KEK e archiviarla in un file PEM.

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

### <a name="steps-3-generate-key-transfer-blob-using-hsm-vendor-provided-byok-tool"></a>Passaggi 3: generare un BLOB di trasferimento della chiave mediante lo strumento BYOK fornito dal fornitore di HSM

Il cliente userà lo strumento BYOK fornito dal fornitore del modulo di protezione hardware per creare un BLOB di trasferimento della chiave (archiviato come file ". BYOK"). Chiave pubblica KEK (come file con estensione PEM) sarà uno degli input per questo strumento.

#### <a name="key-transfer-blob"></a>BLOB di trasferimento della chiave
A lungo termine, Microsoft vuole usare il meccanismo di CKM_RSA_AES_KEY_WRAP PKCS # 11 per trasferire la chiave di destinazione Azure Key Vault poiché questo meccanismo produce un singolo BLOB e, soprattutto, la chiave AES intermedia viene gestita dai due HSM ed è sicuramente temporanea. Questo meccanismo non è attualmente disponibile in alcuni HSM, ma la combinazione di protezione della chiave di destinazione con CKM_AES_KEY_WRAP_PAD usando una chiave AES e la protezione della chiave AES con CKM_RSA_PKCS_OAEP produce un BLOB equivalente.

Il testo non crittografato della chiave di destinazione dipende dal tipo di chiave: 
* Per una chiave RSA, la chiave privata ASN. 1 DER Encoding [RFC3447] racchiusa in PKCS # 8 [RFC5208] 
* Per una chiave EC, la chiave privata ASN. 1 DER Encoding [RFC5915] racchiusa in PKCS # 8 [RFC5208]
* Per una chiave ottetto, byte non elaborati della chiave

I byte per la chiave di testo non crittografato vengono quindi trasformati utilizzando il meccanismo di CKM_RSA_AES_KEY_WRAP:
* Una chiave AES temporanea viene generata e crittografata con la chiave di wrapping RSA usando RSA-OAEP con SHA1.
* La chiave di testo non crittografato codificata viene crittografata con la chiave AES utilizzando il wrapping della chiave AES con riempimento.
* La chiave AES crittografata e la chiave crittografata in testo non crittografato sono concatenate per produrre il BLOB crittografato finale.

Il formato del BLOB di trasferimento usa JSON Web Encryption Compact Serialization (RFC7516) principalmente come veicolo per il recapito dei metadati necessari al servizio per una corretta decrittografia.

Se si usa CKM_RSA_AES_KEY_WRAP_PAD, la serializzazione JSON del BLOB di trasferimento sarà:

```json
{
  "schema_version": "1.0.0",
  "header":
  {
    "kid": "<key identifier of the KEK>",
    "alg": "dir",
    "enc": "CKM_RSA_AES_KEY_WRAP"
  },
  "ciphertext":"BASE64URL(<ciphertext contents>)",
  "generator": "BYOK tool name and version; source HSM name and firmware version"
}

```

* Kid = identificatore chiave di KEK. Per Key Vault chiavi, l'aspetto è simile al seguente: https://ContosoKeyVaultHSM.vault.azure.net/keys/mykek/eba63d27e4e34e028839b53fac905621
* ALG = algoritmo. 
* dir = modalità diretta, ovvero l'oggetto Kid a cui viene fatto riferimento viene usato per proteggere direttamente il testo crittografato, che è una rappresentazione accurata di CKM_RSA_AES_KEY_WRAP
* Generator = campo informativo che indica il nome e la versione dello strumento BYOK e il produttore e il modello del modulo di protezione hardware di origine. Queste informazioni vengono utilizzate per la risoluzione dei problemi e il supporto.

Il BLOB JSON viene archiviato in un file con estensione ". Byok" in modo che i client Azure PowerShell/CLI lo trattino correttamente quando si usano i comandi ' Add-AzKeyVaultKey ' (PSH) o ' AZ Key Vault Key Import ' (CLI).

### <a name="step-4-upload-key-transfer-blob-to-import-hsm-key"></a>Passaggio 4: caricare il BLOB di trasferimento della chiave per importare la chiave HSM

Il cliente trasferirà il BLOB di trasferimento della chiave (file ". Byok") a una workstation online, quindi eseguirà un comando **AZ Key Vault Key Import** per importare questo BLOB come una nuova chiave supportata da HSM in Key Vault. 

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok --ops encrypt decrypt
```

Quando viene eseguito il comando precedente, viene inviata una richiesta dell'API REST, come indicato di seguito:

```
PUT https://contosokeyvaulthsm.vault.azure.net/keys/ContosoFirstHSMKey?api-version=7.0
```

Corpo della richiesta:
```json
{
  "key": {
    "kty": "RSA-HSM",
    "key_ops": [
      "decrypt",
      "encrypt"
    ],
    "key_hsm": "<Base64 encoded BYOK_BLOB>"
  },
  "attributes": {
    "enabled": true
  }
}
```
il valore "key_hsm" è l'intero contenuto di KeyTransferPackage-ContosoFirstHSMkey. Byok codificato nel formato Base64.

## <a name="references"></a>Riferimenti
- [Guida per gli sviluppatori all'insieme di credenziali delle chiavi](../general/developers-guide.md)

## <a name="next-steps"></a>Passaggi successivi
* Istruzioni dettagliate per BYOK: [importare chiavi HSM protette in Key Vault (BYOK)](hsm-protected-keys-byok.md)

