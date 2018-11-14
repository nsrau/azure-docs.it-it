---
title: Informazioni sul controllo degli accessi in base al ruolo di Gemelli digitali di Azure | Microsoft Docs
description: Informazioni sull'autenticazione in Gemelli digitali con il controllo degli accessi in base al ruolo.
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: lyrana
ms.openlocfilehash: b9ccdb9030a24520be8f24f757c279241f3a07e1
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2018
ms.locfileid: "51014789"
---
# <a name="role-based-access-control"></a>Controllo degli accessi in base al ruolo

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

* Concedere a un utente la possibilità di gestire i dispositivi per un intero edificio o solo per una stanza o un piano specifico.
* Concedere a un amministratore l'accesso globale a tutti i nodi del grafico spaziale per un intero grafico oppure solo per una sezione del grafico.
* Concedere a uno specialista del supporto l'accesso in lettura al grafico, ad eccezione che per le chiavi di accesso.
* Concedere a ogni membro di un dominio l'accesso in lettura a tutti gli oggetti del grafico.

## <a name="rbac-best-practices"></a>Procedure consigliate per il controllo degli accessi in base al ruolo

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="roles"></a>Ruoli

### <a name="role-definitions"></a>Definizioni dei ruoli

Una definizione di ruolo è una raccolta di autorizzazioni, talvolta definita "ruolo". La definizione di ruolo elenca le operazioni consentite tra cui creazione, lettura, aggiornamento ed eliminazione. Specifica anche a quali tipi di oggetto si applicano le autorizzazioni.

In Gemelli digitali di Azure sono disponibili i ruoli seguenti:

* **Amministratore dello spazio**: autorizzazioni di creazione, lettura, aggiornamento ed eliminazione per lo spazio specificato e tutti i nodi sottostanti. Autorizzazione globale.
* **Amministratore degli utenti**: autorizzazioni di creazione, lettura, aggiornamento ed eliminazione per gli utenti e gli oggetti correlati agli utenti. Autorizzazione di lettura per gli spazi.
* **Amministratore dei dispositivi**: autorizzazioni di creazione, lettura, aggiornamento ed eliminazione per i dispositivi e gli oggetti correlati ai dispositivi. Autorizzazione di lettura per gli spazi.
* **Amministratore delle chiavi**: autorizzazioni di creazione, lettura, aggiornamento ed eliminazione per le chiavi di accesso. Autorizzazione di lettura per gli spazi.
* **Amministratore dei token**: autorizzazioni di lettura e aggiornamento per le chiavi di accesso. Autorizzazione di lettura per gli spazi.
* **Utente**: autorizzazione di lettura per gli spazi, i sensori e gli utenti, inclusi gli oggetti correlati corrispondenti.
* **Specialista del supporto**: autorizzazione di lettura per tutti gli elementi ad eccezione delle chiavi di accesso.
* **Installatore di dispositivi**: autorizzazioni di lettura e aggiornamento per i dispositivi e i sensori, inclusi gli oggetti correlati corrispondenti. Autorizzazione di lettura per gli spazi.
* **Dispositivo gateway**: autorizzazione di creazione per i sensori. Autorizzazione di lettura per i dispositivi e i sensori, inclusi gli oggetti correlati corrispondenti.

>[!NOTE]
> Per recuperare le definizioni complete dei ruoli precedenti, eseguire una query sull'API di sistema/dei ruoli.

### <a name="object-types"></a>Tipi di oggetto

`ObjectIdType` si riferisce al tipo di identità a cui viene assegnato un ruolo. Ad eccezione dei tipi `DeviceId` e `UserDefinedFunctionId`, i tipi corrispondono a una proprietà di un oggetto di Azure Active Directory (Azure AD):
  
* Il tipo `UserId` assegna un ruolo a un utente.
* Il tipo `DeviceId` assegna un ruolo a un dispositivo.
* Il tipo `DomainName` assegna un ruolo a un nome di dominio. Ogni utente con il nome di dominio specificato ha i diritti di accesso del ruolo corrispondente.
* Il tipo `TenantId` assegna un ruolo a un tenant. Ogni utente appartenente all'ID tenant di Azure AD specificato ha i diritti di accesso del ruolo corrispondente.
* Il tipo `ServicePrincipalId` assegna un ruolo a un ID oggetto entità servizio.
* Il tipo `UserDefinedFunctionId` assegna un ruolo a una funzione definita dall'utente.

> [!div class="nextstepaction"]
> [Query dell'ID oggetto per un utente](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0)

> [!div class="nextstepaction"]
> [Ottenere l'ID oggetto per un'entità servizio](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermadserviceprincipal?view=azurermps-6.8.1)

> [!div class="nextstepaction"]
> [Recuperare l'ID oggetto per un tenant di Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)

## <a name="role-assignments"></a>Assegnazioni di ruoli

Per concedere autorizzazioni a un destinatario, creare un'assegnazione di ruolo. Per revocare le autorizzazioni, rimuovere l'assegnazione di ruolo. Un'assegnazione di ruolo di Gemelli digitali di Azure associa un oggetto, ad esempio un utente o un tenant di Azure AD, a un ruolo e a uno spazio. Vengono quindi concesse le autorizzazioni a tutti gli oggetti che appartengono a tale spazio, incluso l'intero grafico spaziale sottostante.

A un utente, ad esempio, viene concessa l'assegnazione di ruolo per il ruolo `DeviceInstaller` per il nodo radice di un grafico spaziale, che rappresenta un edificio. L'utente può quindi leggere e aggiornare i dispositivi per il nodo e per tutti gli altri spazi figlio nell'edificio.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sulla sicurezza di Gemelli digitali di Azure, vedere [Creare e gestire assegnazioni di ruolo](./security-create-manage-role-assignments.md).
