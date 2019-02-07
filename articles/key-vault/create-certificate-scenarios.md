---
title: Monitorare e gestire la creazione di certificati
description: Scenari che illustrano diverse opzioni per creare e monitorare il processo di creazione di certificati con Key Vault e interagirvi.
services: key-vault
documentationcenter: ''
author: bryanla
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 0d0995aa-b60d-4811-be12-ba0a45390197
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: bryanla
ms.openlocfilehash: b6c77a2e7ae9cb4c463900ec34989ebaff22e5e1
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2019
ms.locfileid: "55726889"
---
# <a name="monitor-and-manage-certificate-creation"></a>Monitorare e gestire la creazione di certificati
Si applica a: Azure

Vedere di seguito 

In questo articolo sono illustrati gli scenari e le operazioni seguenti:

- Richiedere un certificato di Key Vault presso un'autorità di certificazione supportata
- Recuperare una richiesta in sospeso con stato "inProgress"
- Recuperare una richiesta in sospeso con stato "completed"
- Recuperare una richiesta in sospeso con stato "canceled" o "failed"
- Recuperare una richiesta in sospeso con stato "deleted" o "overwritten"
- Creare o importare quando esiste una richiesta in sospeso con stato "inProgress"
- Eseguire l'unione in caso di richiesta in sospeso creata con un'autorità di certificazione (ad esempio, DigiCert)
- Richiedere un annullamento mentre lo stato della richiesta in sospeso è "inProgress"
- Eliminare un oggetto richiesta in sospeso
- Creare un certificato di Key Vault manualmente
- Eseguire l'unione durante la creazione di una richiesta in sospeso (creazione manuale di certificati)

## <a name="request-a-kv-certificate-with-a-supported-issuer"></a>Richiedere un certificato di Key Vault presso un'autorità di certificazione supportata 

|Metodo|URI della richiesta|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/create?api-version={api-version}`|

Per gli esempi seguenti è necessario che sia già disponibile un oggetto denominato "mydigicert" nell'insieme di credenziali delle chiavi, con DigiCert come provider autorità di certificazione. L'autorità di certificazione è un'entità rappresentata in Azure Key Vault come risorsa CertificateIssuer. Viene usata per rendere disponibili informazioni sull'origine di un certificato di un insieme di credenziali delle chiavi: nome dell'autorità di certificazione, provider, credenziali e altri dettagli amministrativi.

### <a name="request"></a>Richiesta

```json
{
  "policy": {
    "x509_props": {
      "subject": "CN=MyCertSubject1"
    },
    "issuer": {
      "name": "mydigicert",
      "cty": "OV-SSL",
    }
  }
}
```

### <a name="response"></a>Risposta

```
StatusCode: 202, ReasonPhrase: 'Accepted'
Location: “https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "mydigicert"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "cancellation_requested": false,
  "status": "InProgress",
  "status_details": "Pending certificate created. Certificate request is in progress. This may take some time based on the issuer provider. Please check again later",
  "request_id": "a76827a18b63421c917da80f28e9913d"
}

```

## <a name="get-pending-request---request-status-is-inprogress"></a>Recuperare una richiesta in sospeso con stato "inProgress"

|Metodo|URI della richiesta|
|------------|-----------------|
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Richiesta
GET `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

Oppure

GET `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

> [!NOTE]
> Se specificato nella query, *request_id* funge da filtro. Se il valore di *request_id* nella query è diverso da quello nell'oggetto in sospeso, viene restituito il codice di stato HTTP 404.

### <a name="response"></a>Risposta

```
StatusCode: 200, ReasonPhrase: 'OK'
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "{issuer-name}"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "cancellation_requested": false,
  "status": "inProgress",
  "status_details": "…",
  "request_id": "a76827a18b63421c917da80f28e9913d"
}

```

## <a name="get-pending-request---request-status-is-complete"></a>Recuperare una richiesta in sospeso con stato "completed"

### <a name="request"></a>Richiesta

|Metodo|URI della richiesta|
|------------|-----------------|
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

GET `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

Oppure

GET `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

### <a name="response"></a>Risposta

```
StatusCode: 200, ReasonPhrase: 'OK'
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "{issuer-name}"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "cancellation_requested": false,
  "status": "completed",
  "request_id": "a76827a18b63421c917da80f28e9913d",
  "target": “https://mykeyvault.vault.azure.net/certificates/mycert1?api-version={api-version}"
}

```

## <a name="get-pending-request---pending-request-status-is-canceled-or-failed"></a>Recuperare una richiesta in sospeso con stato "canceled" o "failed"

### <a name="request"></a>Richiesta

|Metodo|URI della richiesta|
|------------|-----------------|
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

GET `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

Oppure

GET `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

### <a name="response"></a>Risposta

```
StatusCode: 200, ReasonPhrase: 'OK'
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "{issuer-name}"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "cancellation_requested": false,
  "status": "failed",
  "status_details": "",
  "request_id": "a76827a18b63421c917da80f28e9913d",
  "error": {
    "code": "<errorcode>",
    "message": "<message>"
  }
}

```

> [!NOTE]
> Il valore di *errorcode* può essere "Certificate issuer error" o "Request rejected" a seconda che l'errore riguardi, rispettivamente, l'autorità di certificazione o l'utente.

## <a name="get-pending-request---pending-request-status-is-deleted-or-overwritten"></a>Recuperare una richiesta in sospeso con stato "deleted" o "overwritten"
Un oggetto in sospeso può essere eliminato o sovrascritto da un'operazione di creazione/importazione quando lo stato dell'oggetto non è "inProgress".

|Metodo|URI della richiesta|
|------------|-----------------|
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Richiesta
GET `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

Oppure

GET `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

### <a name="response"></a>Risposta

```
StatusCode: 404, ReasonPhrase: 'Not Found'
{
  "error": {
    "code": "PendingCertificateNotFound",
    "message": "…"
  }
}

```

## <a name="create-or-import-when-pending-request-exists---status-is-inprogress"></a>Creare o importare quando esiste una richiesta in sospeso con stato "inProgress"
Gli stati possibili di un oggetto in sospeso sono quattro: "inProgress", "canceled", "failed" o "completed".

Quando lo stato di una richiesta in sospeso è "inProgress", le operazioni di creazione e importazione avranno esito negativo con codice di stato HTTP 409 (Conflitto).

Per risolvere un conflitto:

- Se il certificato viene creato manualmente, è possibile completare il certificato di Key Vault eseguendo un'operazione di unione o eliminazione dell'oggetto in sospeso.

- Se il certificato viene creato da un'autorità di certificazione, è possibile attendere fino al completamento, all'esito negativo o all'annullamento del certificato. In alternativa, è possibile eliminare l'oggetto in sospeso.

> [!NOTE]
> L'eliminazione di un oggetto in sospeso può annullare o meno la richiesta di certificato x509 presso il provider.

|Metodo|URI della richiesta|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/create?api-version={api-version}`|

### <a name="request"></a>Richiesta

```json
{
  "policy": {
    "x509_props": {
      "subject": "CN=MyCertSubject1"
    },
    "issuer": {
      "name": "mydigicert"
    }
  }
}
```

### <a name="response"></a>Risposta

```
StatusCode: 409, ReasonPhrase: 'Conflict'
{
  "error": {
    "code": "Forbidden",
    "message": "A new key vault certificate can not be created or imported while a pending key vault certificate's status is inProgress."
  }
}

```

## <a name="merge-when-pending-request-is-created-with-an-issuer"></a>Eseguire l'unione in caso di richiesta in sospeso creata con un'autorità di certificazione
L'unione non è consentita quando un oggetto in sospeso viene creato con un'autorità di certificazione, ma è consentita quando lo stato è "inProgress". 

Se la richiesta di creazione del certificato x509 ha esito negativo o viene annullata per qualche motivo ed è possibile recuperare un certificato x509 fuori banda, si può eseguire un'operazione di unione per completare il certificato di Key Vault.

|Metodo|URI della richiesta|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending/merge?api-version={api-version}`|

### <a name="request"></a>Richiesta

```json
{
  "x5c": [ "MIICxTCCAbi………………………trimmed for brevitiy……………………………………………EPAQj8=" ]
}

```

### <a name="response"></a>Risposta

```json
StatusCode: 403, ReasonPhrase: 'Forbidden'
{
  "error": {
    "code": "Forbidden",
    "message": "Merge is forbidden on pending object created with issuer : <issuer-name> while it is in progess."
  }
}

```

## <a name="request-a-cancellation-while-the-pending-request-status-is-inprogress"></a>Richiedere un annullamento mentre lo stato della richiesta in sospeso è "inProgress"
Un annullamento può solo essere richiesto. Una richiesta può essere annullata o meno. Se una richiesta non è "inProgress", viene restituito lo stato HTTP 400 (Richiesta non valida).

|Metodo|URI della richiesta|
|------------|-----------------|
|PATCH|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Richiesta
PATCH `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

Oppure

PATCH `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

```json
{
  "cancellation_requested": true
}

```

### <a name="response"></a>Risposta

```
StatusCode: 200, ReasonPhrase: 'OK'
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "{issuer-name}"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "cancellation_requested": true,
  "status": "inProgress",
  "status_details": "…",
  "request_id": "a76827a18b63421c917da80f28e9913d"
}
```

## <a name="delete-a-pending-request-object"></a>Eliminare un oggetto richiesta in sospeso

> [!NOTE]
> L'eliminazione dell'oggetto in sospeso può annullare o meno la richiesta di certificato x509 presso il provider.

|Metodo|URI della richiesta|
|------------|-----------------|
|DELETE|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Richiesta
DELETE `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

Oppure

DELETE `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

### <a name="response"></a>Risposta

```
StatusCode: 200, ReasonPhrase: 'OK'
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "{issuer-name}"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "cancellation_requested": false,
  "status": "inProgress",
  "request_id": "a76827a18b63421c917da80f28e9913d",
}
```

## <a name="create-a-kv-certificate-manually"></a>Creare un certificato di Key Vault manualmente
È possibile creare un certificato rilasciato con una CA di propria scelta tramite un processo di creazione manuale. Impostare il nome dell'autorità di certificazione su "Unknown" o non specificare tale campo.

|Metodo|URI della richiesta|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/create?api-version={api-version}`|

### <a name="request"></a>Richiesta

```json
{
  "policy": {
    "x509_props": {
      "subject": "CN=MyCertSubject1"
    },
    "issuer": {
      "name": "Unknown"
    }
  }
}

```

### <a name="response"></a>Risposta

```
StatusCode: 202, ReasonPhrase: 'Accepted'
Location: “https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "Unknown"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "status": "inProgress",
  "status_details": "Pending certificate created. Please Perform Merge to complete the request.",
  "request_id": "a76827a18b63421c917da80f28e9913d"
}

```

## <a name="merge-when-a-pending-request-is-created---manual-certificate-creation"></a>Eseguire l'unione durante la creazione di una richiesta in sospeso (creazione manuale di certificati)

|Metodo|URI della richiesta|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending/merge?api-version={api-version}`|

### <a name="request"></a>Richiesta

```json
{
  "x5c": [ "MIICxTCCAbi………………………trimmed for brevitiy……………………………………………EPAQj8=" ]
}

```

|Nome dell'elemento|Obbligatoria|Type|Versione|DESCRIZIONE|
|------------------|--------------|----------|-------------|-----------------|
|x5c|Sì|array|\<versione di introduzione>|Catena di certificati X509 sotto forma di matrice di stringhe Base 64.|

### <a name="response"></a>Risposta

```
StatusCode: 201, ReasonPhrase: 'Created'
Location: “https://mykeyvault.vault.azure.net/certificates/mycert1?api-version={api-version}"
{
    "id": "https mykeyvault.vault.azure.net/certificates/mycert1/f366e1a9dd774288ad84a45a5f620352",
    "kid": "https:// mykeyvault.vault.azure.net/keys/mycert1/f366e1a9dd774288ad84a45a5f620352",
    "sid": " mykeyvault.vault.azure.net/secrets/mycert1/f366e1a9dd774288ad84a45a5f620352",
    "cer": "……de34534……",
    "x5t": "n14q2wbvyXr71Pcb58NivuiwJKk",
    "attributes": {
        "enabled": true,
        "exp": 1530394215,
        "nbf": 1435699215,
        "created": 1435699919,
        "updated": 1435699919
    },
    "pending": {
        "id": "https:// mykeyvault.vault.azure.net/certificates/mycert1/pending"
    },
    "policy": {
        "id": "https:// mykeyvault.vault.azure.net/certificates/mycert1/policy",
        "key_props": {
            "exportable": false,
            "kty": "RSA",
            "key_size": 2048,
            "reuse_key": false
        },
        "secret_props": {
            "contentType": "application/x-pkcs12"
        },
        "x509_props": {
            "subject": "CN=Mycert1",
            "ekus": ["1.3.6.1.5.5.7.3.1", "1.3.6.1.5.5.7.3.2"],
            "validity_months": 12
        },
        "lifetime_actions": [{
            "trigger": {
                "lifetime_percentage": 80
            },
            "action": {
                "action_type": "EmailContacts"
            }
        }],
        "issuer": {
            "name": "Unknown"
        },
        "attributes": {
            "enabled": true,
            "created": 1435699811,
            "updated": 1435699811
        }
    }
}

```

## <a name="see-also"></a>Vedere anche
- [Informazioni su chiavi, segreti e certificati](about-keys-secrets-and-certificates.md)
