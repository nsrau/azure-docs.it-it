---
title: Informazioni sulla connettività e sull'autenticazione dei dispositivi di Gemelli digitali di Azure | Microsoft Docs
description: Uso di Gemelli digitali di Azure per connettersi ai servizi e autenticarli
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: lyrana
ms.openlocfilehash: adfb4c369ea1b324da8562a5b0b245ebdecff602
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323793"
---
# <a name="create-and-manage-role-assignments"></a>Creare e gestire assegnazioni di ruolo

Gemelli digitali di Azure usa il [controllo degli accessi in base al ruolo](./security-role-based-access-control.md) per gestire l'accesso alle risorse.

Ogni assegnazione di ruolo include:

* Un **identificatore di oggetto** (ID Azure Active Directory, ID oggetto entità servizio o nome di dominio).
* Un **tipo di identificatore di oggetto**.
* Un **ID definizione di ruolo**.
* Un **percorso di spazio**.
* Un **ID tenant** di Azure Active Directory (nella maggior parte dei casi).

## <a name="role-definition-identifiers"></a>Identificatori delle definizioni di ruolo

La tabella seguente mostra che cosa è possibile ottenere eseguendo una query sull'API di sistema/dei ruoli:

| **Ruolo** | **Identificatore** |
| --- | --- |
| Amministratore dello spazio | 98e44ad7-28d4-4007-853b-b9968ad132d1 |
| Amministratore utenti| dfaac54c-f583-4dd2-b45d-8d4bbc0aa1ac |
| Amministratore del dispositivo | 3cdfde07-bc16-40d9-bed3-66d49a8f52ae |
| Amministratore delle chiavi | 5a0b1afc-e118-4068-969f-b50efb8e5da6 |
| Amministratore dei token | 38a3bb21-5424-43b4-b0bf-78ee228840c3 |
| Utente | b1ffdb77-c635-4e7e-ad25-948237d85b30 |
| Specialista del supporto tecnico | 6e46958b-dc62-4e7c-990c-c3da2e030969 |
| Programma di installazione dei dispositivi | b16dd9fe-4efe-467b-8c8c-720e2ff8817c |
| Dispositivo gateway | d4c69766-e9bd-4e61-bfc1-d8b6e686c7a8 |

## <a name="supported-objectidtypes"></a>ObjectIdType supportati

Gli elementi `ObjectIdTypes` supportati sono i seguenti:

* `UserId`
* `DeviceId`
* `DomainName`
* `TenantId`
* `ServicePrincipalId`
* `UserDefinedFunctionId`

## <a name="create-a-role-assignment"></a>Creare un'assegnazione di ruolo

```plaintext
HTTP POST /api/v1.0/roleassignments
```

| **Nome** | **Obbligatorio** | **Tipo** | **Descrizione** |
| --- | --- | --- | --- |
| RoleId| Yes |stringa | Identificatore della definizione di ruolo. Le definizioni di ruolo e i rispettivi identificatori possono essere trovati eseguendo una query sull'API di sistema. |
| objectId | Yes |stringa | ID oggetto per l'assegnazione di ruolo che deve essere formattato in base al tipo associato. Per l'elemento ObjectIdType `DomainName`, ObjectId deve iniziare con il carattere `“@”`. |
| objectIdType | Yes |stringa | Tipo dell'assegnazione di ruolo. Deve essere una delle righe seguenti in questa tabella. |
| TenantId | Variabile | stringa |Identificatore del tenant. Non consentito per gli elementi ObjectIdType `DeviceId` e `TenantId`. Obbligatorio per gli elementi ObjectIdType `UserId` e `ServicePrincipalId`. Facoltativo per l'elemento ObjectIdType DomainName. |
| path* | Yes | stringa |Percorso di accesso completo dell'oggetto `Space`. Ad esempio: `/{Guid}/{Guid}`. Se un identificatore richiede l'assegnazione di ruolo per l'intero grafico, specificare `"/"` (che designa la radice). Tuttavia, si tratta di un'operazione sconsigliata ed è **sempre preferibile seguire il principio del privilegio minimo**. |

## <a name="sample-configuration"></a>Configurazione di esempio

Un utente richiede l'accesso amministrativo a un piano di uno spazio tenant:

  ```JSON
    {
      "RoleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
      "ObjectId" : " 0fc863bb-eb51-4704-a312-7d635d70e599",
      "ObjectIdType" : "UserId",
      "TenantId": " a0c20ae6-e830-4c60-993d-a91ce6032724",
      "Path": "/ 091e349c-c0ea-43d4-93cf-6b57abd23a44/ d84e82e6-84d5-45a4-bd9d-006a118e3bab"
    }
  ```

Un'applicazione che esegue scenari di test che simulano dispositivi e sensori:

  ```JSON
    {
      "RoleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
      "ObjectId" : "cabf7acd-af0b-41c5-959a-ce2f4c26565b",
      "ObjectIdType" : "ServicePrincipalId",
      "TenantId": " a0c20ae6-e830-4c60-993d-a91ce6032724",
      "Path": "/"
    }
  ```

Tutti gli utenti che fanno parte di un dominio riceveranno l'accesso in lettura per gli spazi, i sensori e gli utenti, inclusi gli oggetti corrispondenti correlati:

  ```JSON
    {
      "RoleId": " b1ffdb77-c635-4e7e-ad25-948237d85b30",
      "ObjectId" : "@microsoft.com",
      "ObjectIdType" : "DomainName",
      "Path": "/091e349c-c0ea-43d4-93cf-6b57abd23a44"
    }
  ```

Per OTTENERE un'assegnazione di ruolo:

```plaintext
HTTP GET /api/v1/roleassignments?path={path}
```

| **Nome** | **In** | **Obbligatorio** |    **Tipo** |  **Descrizione** |
| --- | --- | --- | --- | --- |
| path | path | True  | string | Percorso completo dello spazio |

Per ELIMINARE un'assegnazione di ruolo:

```plaintext
HTTP DELETE /api/v1/roleassignments/{id}
```

| **Nome** | **In** | **Obbligatorio** | **Tipo** | **Descrizione** |
| --- | --- | --- | --- | --- |
| ID | path | True  | string |   ID assegnazione di ruolo |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla sicurezza di Gemelli digitali di Azure, vedere [Autenticazione delle API](./security-authenticating-apis.md).
