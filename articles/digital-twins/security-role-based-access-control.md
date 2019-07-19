---
title: Informazioni sul controllo degli accessi in base al ruolo di Gemelli digitali di Azure | Microsoft Docs
description: Informazioni sull'autenticazione in Gemelli digitali con il controllo degli accessi in base al ruolo.
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: lyhughes
ms.openlocfilehash: 33e09ad52722665e6162b18159012d69ec1463bd
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849283"
---
# <a name="role-based-access-control-in-azure-digital-twins"></a>Controllo degli accessi in base al ruolo in Gemelli digitali di Azure

Il servizio Gemelli digitali di Azure consente un controllo preciso degli accessi a risorse, azioni e dati specifici nel grafico spaziale. A tale scopo viene usato un meccanismo di gestione granulare di ruoli e autorizzazioni detto "controllo degli accessi in base al ruolo". Il controllo degli accessi in base al ruolo legge i _ruoli_ e le _assegnazioni di ruoli_. I ruoli identificano il livello di autorizzazioni, mentre le assegnazioni di ruolo associano un ruolo a un utente o un dispositivo.

Con il controllo degli accessi in base al ruolo, è possibile concedere un'autorizzazione a:

- Un utente.
- Un dispositivo.
- Un'entità servizio.
- Una funzione definita dall'utente.
- Tutti gli utenti che appartengono a un dominio.
- Un tenant.

È anche possibile ottimizzare il livello di accesso.

Il controllo degli accessi in base al ruolo permette l'ereditarietà delle autorizzazioni verso il basso nel grafico spaziale.

## <a name="what-can-i-do-with-rbac"></a>Quali operazioni si possono eseguire con il controllo degli accessi in base al ruolo?

Uno sviluppatore può usare il controllo degli accessi in base al ruolo per:

- Concedere a un utente la possibilità di gestire i dispositivi per un intero edificio o solo per una stanza o un piano specifico.
- Concedere a un amministratore l'accesso globale a tutti i nodi del grafico spaziale per un intero grafico oppure solo per una sezione del grafico.
- Concedere a uno specialista del supporto l'accesso in lettura al grafico, ad eccezione che per le chiavi di accesso.
- Concedere a ogni membro di un dominio l'accesso in lettura a tutti gli oggetti del grafico.

## <a name="rbac-best-practices"></a>Procedure consigliate per il controllo degli accessi in base al ruolo

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="roles"></a>Ruoli

### <a name="role-definitions"></a>Definizioni dei ruoli

Una definizione di ruolo è una raccolta di autorizzazioni e altri attributi che costituiscono un ruolo. Una definizione del ruolo elenca le operazioni consentite che possono essere effettuate da qualsiasi oggetto con quel ruolo, tra cui *CREATE*, *READ*, *UPDATE* e *DELETE*. Specifica anche a quali tipi di oggetto si applicano le autorizzazioni.

[!INCLUDE [digital-twins-roles](../../includes/digital-twins-roles.md)]

>[!NOTE]
> Per recuperare le definizioni complete dei ruoli precedenti, eseguire una query sull'API di sistema/dei ruoli.
> Per altre informazioni, leggere [Creazione e gestione delle assegnazioni di ruolo](./security-create-manage-role-assignments.md#all).

### <a name="object-identifier-types"></a>Tipi di identificatori di oggetto

[!INCLUDE [digital-twins-object-types](../../includes/digital-twins-object-id-types.md)]

>[!TIP]
> Per informazioni su come concedere le autorizzazioni all'entità servizio, leggere [Creazione e gestione delle assegnazioni di ruolo](./security-create-manage-role-assignments.md#grant).

Gli articoli della documentazione di riferimento seguenti descrivono:

- Come [effettuare query per l'ID oggetto per un utente](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0).
- Come [ottenere l'ID oggetto per un'entità servizio](https://docs.microsoft.com/powershell/module/az.resources/get-azadserviceprincipal).
- Come [recuperare l'ID oggetto per un tenant di Azure AD](../active-directory/develop/quickstart-create-new-tenant.md).

## <a name="role-assignments"></a>Assegnazioni di ruoli

Un'assegnazione di ruolo di Gemelli digitali di Azure associa un oggetto, ad esempio un utente o un tenant di Azure AD, a un ruolo e a uno spazio. Vengono quindi concesse le autorizzazioni a tutti gli oggetti che appartengono a tale spazio, incluso l'intero grafico spaziale sottostante.

A un utente, ad esempio, viene concessa l'assegnazione di ruolo per il ruolo `DeviceInstaller` per il nodo radice di un grafico spaziale, che rappresenta un edificio. L'utente può quindi leggere e aggiornare i dispositivi per il nodo e per tutti gli altri spazi figlio nell'edificio.

Per concedere autorizzazioni a un destinatario, creare un'assegnazione di ruolo. Per revocare le autorizzazioni, rimuovere l'assegnazione di ruolo.

>[!IMPORTANT]
> Per altre informazioni sulle assegnazioni di ruolo, leggere [Creazione e gestione delle assegnazioni di ruolo](./security-create-manage-role-assignments.md).

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni su creazione e gestione delle assegnazioni di ruolo di Gemelli digitali di Azure, leggere [Creazione e gestione delle assegnazioni di ruolo](./security-create-manage-role-assignments.md).
