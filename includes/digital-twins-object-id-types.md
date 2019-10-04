---
title: File di inclusione
description: File di inclusione
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 09/30/2019
ms.author: v-adgera
ms.custom: include file
ms.openlocfilehash: 1f4cdf45fb643499f7d6ab717c6f36395ad292c8
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827713"
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