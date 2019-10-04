---
title: File di inclusione
description: File di inclusione
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 09/30/2019
ms.custom: include file
ms.openlocfilehash: 12d4278171d43fdaf8613a1c91bdbffc269adc56
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949059"
---
`objectIdType` (o **tipo di identificatore di oggetto**) si riferisce al tipo di identità a cui viene assegnato un ruolo. Ad eccezione dei tipi `DeviceId` e `UserDefinedFunctionId`, i tipi di identificatore di oggetto corrispondono alle proprietà di oggetti di Azure Active Directory.

La tabella seguente elenca i tipi di identificatore di oggetto supportati in Gemelli digitali di Azure:

| Type | Descrizione |
| --- | --- |
| UserId | Assegna un ruolo a un utente. |
| DeviceId | Assegna un ruolo a un dispositivo. |
| DomainName | Assegna un ruolo a un nome di dominio. Ogni utente con il nome di dominio specificato ha i diritti di accesso del ruolo corrispondente. |
| TenantId | Assegna un ruolo a un tenant. Ogni utente appartenente all'ID tenant di Azure AD specificato ha i diritti di accesso del ruolo corrispondente. |
| ServicePrincipalId | Assegna un ruolo a un ID oggetto entità servizio. |
| UserDefinedFunctionId | Assegna un ruolo a una funzione definita dall'utente. |