---
title: Guida alla formazione per la transizione da Registrazioni app (legacy) alla nuova esperienza Registrazioni app nel portale di Azure
description: Introduzione alla nuova esperienza di registrazione delle app nel portale di Azure
services: active-directory
author: archieag
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: aragra
ms.reviewer: lenalepa, keyam
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f49cac5c8c49fb5c53d3320cb0ab52e0f61acda
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74844261"
---
# <a name="transitioning-from-app-registrations-legacy-to-the-new-app-registrations-experience-in-the-azure-portal"></a>Passaggio da Registrazioni app (legacy) alla nuova esperienza Registrazioni app nel portale di Azure

È possibile trovare molti miglioramenti nella nuova esperienza [registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908) nel portale di Azure. Se si ha familiarità con l'esperienza di Registrazioni app (legacy) nell'portale di Azure, usare questa guida alla formazione per iniziare a usare la nuova esperienza.

In Azure Active Directory, la nuova esperienza di registrazione delle applicazioni descritta qui è disponibile a livello generale (GA). In Azure Active Directory B2C (Azure AD B2C) questa esperienza è in anteprima.

## <a name="key-changes"></a>Modifiche principali

- Registrazioni app non sono limitati a essere un'app Web o un' *API* o un'app *nativa* . È possibile usare la stessa registrazione dell'app per tutte queste app registrando i rispettivi URI di reindirizzamento.

- L'esperienza legacy supportava le app che accedevano usando solo account aziendali (Azure AD). Le app sono state registrate come tenant singolo. Le app supportano solo account aziendali dalla directory in cui l'app è stata registrata. È possibile modificare le app in modo che siano multi-tenant e supportino tutti gli account dell'organizzazione. La nuova esperienza consente di registrare le app in grado di supportare entrambe le opzioni, oltre a una terza opzione, ovvero tutti gli account aziendali e gli account Microsoft personali.

- L'esperienza Legacy era disponibile solo quando è stato eseguito l'accesso al portale di Azure usando un account aziendale. Con la nuova esperienza, è possibile usare account Microsoft personali che non sono associati a una directory.

## <a name="list-of-applications"></a>Elenco delle applicazioni

Il nuovo elenco di app mostra le applicazioni registrate tramite l'esperienza di registrazione delle app legacy nel portale di Azure. Queste app effettuano l'accesso usando account Azure AD. Il nuovo elenco di app mostra anche le app registrate tramite il portale di registrazione delle applicazioni. Queste app effettuano l'accesso con Azure AD e account Microsoft personali.

>[!NOTE]
>Il portale di registrazione delle applicazioni è stato deprecato.

Il nuovo elenco di app non contiene una colonna di **tipo applicazione** perché una singola registrazione dell'app può essere di diversi tipi. L'elenco include due colonne aggiuntive: **create on** e **Certificates & Secrets**. **Certificati & segreti** Mostra lo stato delle credenziali registrate nell'app. Gli stati includono **Current**, **scadenza**imminente e **scaduto**.

## <a name="new-app-registration"></a>Registrazione nuova applicazione

Nell'esperienza legacy, per registrare un'app è necessario fornire: **nome**, **tipo di applicazione**e **URL di accesso/URI di reindirizzamento**. Le app create sono Azure AD solo applicazioni a tenant singolo. Sono supportati solo account aziendali dalla directory in cui è stata registrata l'app.

Nella nuova esperienza è necessario fornire un **nome** per l'app e scegliere i tipi di **conto supportati**. Facoltativamente, è possibile specificare un **URI di reindirizzamento**. Se si specifica un URI di reindirizzamento, è necessario specificare se è Web/pubblico (per dispositivi mobili e desktop). Per altre informazioni, vedere [Guida introduttiva: registrare un'applicazione con la piattaforma di identità Microsoft](quickstart-register-app.md). Per Azure AD B2C, vedere [registrare un'applicazione nel Azure Active Directory B2C](../../active-directory-b2c/tutorial-register-applications.md).

## <a name="differences-between-the-application-registration-portal-and-app-registrations-page"></a>Differenze tra il portale di registrazione delle applicazioni e la pagina Registrazioni app

### <a name="the-legacy-properties-page"></a>Pagina delle proprietà legacy

L'esperienza Legacy ha una pagina delle **Proprietà** . I campi delle **Proprietà** sono i seguenti:

- **Nome**
- **ID oggetto**
- **ID applicazione**
- **URI ID app**
- **Logo**
- **URL della Home page**
- **URL di disconnessione**
- **URL delle condizioni per il servizio**
- **URL dell'informativa sulla privacy**
- **Tipo di applicazione**
- **Multi-tenant**

La nuova esperienza non dispone di questa pagina. Qui è possibile trovare la funzionalità equivalente:

- Il **nome**, il **logo**, l' **URL della Home page**, l'URL **delle condizioni di servizio**e l' **URL dell'informativa sulla privacy** sono ora disponibili nella pagina di **personalizzazione** dell'app.
- ID **oggetto** e **ID applicazione (client)** sono disponibili nella pagina **Panoramica** .
- La funzionalità controllata dall'interruttore **multi-tenant** nell'esperienza legacy è stata sostituita dai tipi di **account supportati** nella pagina di **autenticazione** . Per altre informazioni, vedere [Guida introduttiva: modificare gli account supportati da un'applicazione](quickstart-modify-supported-accounts.md).
- L' **URL di disconnessione** è ora disponibile nella pagina di **autenticazione** .
- Il **tipo di applicazione** non è più un campo valido. Al contrario, gli URI di reindirizzamento, che è possibile trovare nella pagina di **autenticazione** , determinano quali tipi di app sono supportati.
- L' **URI ID app** è ora denominato **URI ID applicazione** ed è possibile trovarlo in **esporre un'API**. Nell'esperienza legacy questa proprietà è stata registrata con registrazione automatica usando il formato seguente: `https://{tenantdomain}/{appID}`, ad esempio `https://microsoft.onmicrosoft.com/492439af-3282-44c3-b297-45463339544b`. Nella nuova esperienza, viene generato automaticamente come `api://{appID}`, ma deve essere salvato in modo esplicito. In Azure AD B2C tenant viene ancora usato il formato `https://{tenantdomain}/{appID}`.

### <a name="reply-urlsredirect-urls"></a>URL di risposta/URL di Reindirizzamento

Nell'esperienza legacy, un'app ha una pagina **URL di risposta** . Nella nuova esperienza, gli URL di risposta sono reperibili nella pagina di **autenticazione** di un'app. Sono ora definiti **URI di reindirizzamento**.

Il formato degli URI di reindirizzamento è stato modificato. È necessario che siano associati a un tipo di app, ovvero Web o pubblico. Per motivi di sicurezza, i caratteri jolly e gli schemi di `http://` non sono supportati, ad eccezione di *http://localhost* .

### <a name="keyscertificates--secrets"></a>Chiavi/certificati & segreti

Nell'esperienza legacy, un'app aveva una pagina di **chiavi** . La nuova esperienza è stata rinominata in **certificati & segreti**.

Le **chiavi pubbliche** sono ora denominate **certificati**. Le **password** sono ora denominate **segreti client**.

### <a name="required-permissionsapi-permissions"></a>Autorizzazioni necessarie/autorizzazioni API

Nell'esperienza legacy, un'app aveva una pagina **delle autorizzazioni necessarie** . Nella nuova esperienza è stato rinominato in **autorizzazioni API**.

Quando si seleziona un'API nell'esperienza legacy, è possibile scegliere da un piccolo elenco di API Microsoft. È anche possibile eseguire ricerche nelle entità servizio nel tenant. Nella nuova esperienza è possibile scegliere tra più schede: **API Microsoft**, **API utilizzate dall'organizzazione**o **API personali**. La barra di ricerca sulle API utilizzate dall' **organizzazione** Cerca le entità servizio nel tenant.

> [!NOTE]
> Questa scheda non viene visualizzata se l'applicazione non è associata a un tenant. Per altre informazioni su come richiedere le autorizzazioni, vedere [Guida introduttiva: configurare un'applicazione client per accedere alle API Web](quickstart-configure-app-access-web-apis.md).

L'esperienza Legacy disponeva di un pulsante **Concedi autorizzazioni** nella parte superiore della pagina **autorizzazioni richieste** . Nella nuova esperienza la pagina di **consenso della concessione** dispone di un pulsante di **autorizzazione Concedi amministratore** nella sezione **autorizzazioni API** di un'app. Esistono inoltre alcune differenze nel modo in cui la funzione Buttons.

Nell'esperienza Legacy la logica varia a seconda dell'utente che ha eseguito l'accesso e delle autorizzazioni richieste. La logica era:

- Se sono state richieste solo le autorizzazioni per il consenso dell'utente e l'utente che ha eseguito l'accesso non è un amministratore, l'utente può concedere il consenso dell'utente per le autorizzazioni richieste.
- Se è stata richiesta almeno un'autorizzazione che richiede il consenso dell'amministratore e l'utente che ha eseguito l'accesso non è un amministratore, l'utente ha ricevuto un errore durante il tentativo di concedere il consenso.
- Se l'utente connesso è un amministratore, è stato concesso il consenso dell'amministratore per tutte le autorizzazioni richieste.

Nella nuova esperienza, solo un amministratore può concedere il consenso. Quando un amministratore seleziona **concedi il consenso dell'amministratore**, viene concesso il consenso dell'amministratore a tutte le autorizzazioni richieste.

## <a name="deleting-an-app-registration"></a>Eliminazione di una registrazione dell'app

Nell'esperienza legacy è possibile eliminare solo le app a tenant singolo. Il pulsante Elimina è stato disabilitato per le app multi-tenant. Nella nuova esperienza è possibile eliminare le app in qualsiasi stato, ma è necessario confermare l'azione. Per altre informazioni, vedere [Guida introduttiva: rimuovere un'applicazione registrata con la piattaforma di identità Microsoft](quickstart-remove-app.md).

## <a name="application-manifest"></a>Manifesto dell'applicazione

Le esperienze legacy e nuove usano versioni diverse per il formato JSON nell'editor manifesto. Per ulteriori informazioni, vedere [Azure Active Directory manifesto dell'applicazione](reference-app-manifest.md).

## <a name="new-ui"></a>Nuova interfaccia utente

La nuova esperienza aggiunge i controlli dell'interfaccia utente per le proprietà seguenti:

- La pagina di **autenticazione** ha un **flusso di concessione implicito** (`oauth2AllowImplicitFlow`). Diversamente dall'esperienza legacy, è possibile abilitare i **token di accesso** o i **token ID**o entrambi.
- La pagina **esporre un'API** contiene gli **ambiti definiti da questa API** (`oauth2Permissions`) e dalle **applicazioni client autorizzate** (`preAuthorizedApplications`). Per altre informazioni su come configurare un'app in modo che sia un'API Web ed esporre autorizzazioni/ambiti, vedere [Guida introduttiva: configurare un'applicazione per esporre le API Web](quickstart-configure-app-expose-web-apis.md).
- La pagina **personalizzazione** contiene il **dominio del server di pubblicazione**. Il dominio del server di pubblicazione viene visualizzato agli utenti nella [richiesta di consenso dell'applicazione](application-consent-experience.md). Per altre informazioni, vedere [procedura: configurare un dominio di pubblicazione di un'applicazione](howto-configure-publisher-domain.md).

## <a name="limitations"></a>Limitazioni

La nuova esperienza presenta le limitazioni seguenti:

- Il formato dei segreti client (password dell'app) è diverso rispetto a quello dell'esperienza legacy e potrebbe interrompere l'interfaccia della riga di comando.
- La modifica del valore per gli account supportati non è supportata nell'interfaccia utente. È necessario usare il manifesto dell'app a meno che non si stia passando tra Azure AD single-tenant e multi-tenant.
