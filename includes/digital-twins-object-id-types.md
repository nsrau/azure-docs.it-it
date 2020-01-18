---
title: File di inclusione
description: File di inclusione
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 01/15/2020
ms.custom: include file
ms.openlocfilehash: cb43c8b8c952d8db6cf450a7015c22c85e7fe4b5
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76268199"
---
`objectIdType` (o **tipo di identificatore di oggetto**) si riferisce al tipo di identità a cui viene assegnato un ruolo. Ad eccezione dei tipi `DeviceId` e `UserDefinedFunctionId`, i tipi di identificatore di oggetto corrispondono alle proprietà di oggetti di Azure Active Directory.

La tabella seguente elenca i tipi di identificatore di oggetto supportati in Gemelli digitali di Azure:

| Tipo | Description |
| --- | --- |
| UserId | Assegna un ruolo a un utente. |
| deviceId | Assegna un ruolo a un dispositivo. |
| DomainName | Assegna un ruolo a un nome di dominio. Ogni utente con il nome di dominio specificato ha i diritti di accesso del ruolo corrispondente. |
| TenantId | Assegna un ruolo a un tenant. Ogni utente appartenente all'ID tenant di Azure AD specificato ha i diritti di accesso del ruolo corrispondente. |
| ServicePrincipalId | Assegna un ruolo a un ID oggetto entità servizio. |
| UserDefinedFunctionId | Assegna un ruolo a una funzione definita dall'utente. |