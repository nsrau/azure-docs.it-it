---
title: Registrazioni app nella Guida alla formazione portale di Azure-Azure
description: Introduzione alla nuova esperienza di registrazione delle applicazioni nella piattaforma di identità Microsoft.
services: active-directory
documentationcenter: ''
author: archieag
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: aragra
ms.reviewer: lenalepa, keyam
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a539859fb4853467863f3fd2ab4144bcb789b9f5
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73662510"
---
# <a name="training-guide-app-registrations-in-the-azure-portal"></a>Guida alla formazione: Registrazioni app nel portale di Azure

È possibile trovare numerosi miglioramenti nella nuova esperienza [registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908) nel portale di Azure. Se si ha familiarità con l'esperienza legacy, usare questa guida alla formazione per iniziare a usare la nuova esperienza.

In Azure Active Directory, la nuova esperienza di registrazione delle applicazioni descritta qui è disponibile a livello generale (GA). In Azure Active Directory B2C (Azure AD B2C) questa esperienza è in anteprima.

## <a name="key-changes"></a>Modifiche principali

- Registrazioni app non sono limitati a essere un'app Web o un' **API** o un'app **nativa** . È possibile usare la stessa registrazione dell'app per tutti questi registri registrando i rispettivi URI di reindirizzamento.
- L'esperienza legacy supportava le app che accedevano solo agli account aziendali (Azure AD). Le app sono state registrate come tenant singolo (supportando solo gli account aziendali dalla directory in cui è stata registrata l'app) e potrebbero essere modificate per essere multi-tenant (supportando tutti gli account aziendali). La nuova esperienza consente di registrare le app in grado di supportare entrambe le opzioni, oltre a una terza opzione, ovvero tutti gli account aziendali e gli account Microsoft personali.
- L'esperienza Legacy era disponibile solo quando è stato eseguito l'accesso al portale di Azure usando un account aziendale. Con la nuova esperienza, è possibile usare account Microsoft personali non associati a una directory.

## <a name="list-of-applications"></a>Elenco delle applicazioni

- Il nuovo elenco di app mostra le applicazioni registrate tramite l'esperienza di registrazione delle app legacy nel portale di Azure (app che firmano Azure AD account) e le app registrate nel portale di [registrazione](https://apps.dev.microsoft.com/) delle applicazioni (app che hanno effettuato l'accesso Account Microsoft Azure AD e personali).
- Il nuovo elenco di app non contiene una colonna del **tipo di applicazione** (poiché una singola registrazione dell'app può essere costituita da diversi tipi) e dispone di due colonne aggiuntive: una colonna **creata su** una colonna e un **certificato & colonna Secrets** che mostra lo stato (corrente, scadenza imminente o scaduta delle credenziali registrate nell'app.

## <a name="new-app-registration"></a>Nuova registrazione per l'app

Nell'esperienza legacy, per registrare un'app è necessario fornire: **nome**, **tipo di applicazione**e **URL di accesso/URI di reindirizzamento**. Le app create sono state Azure AD solo le applicazioni a tenant singolo, ovvero supportano solo account aziendali dalla directory in cui è stata registrata l'app.

Nella nuova esperienza è necessario fornire un **nome** per l'app e scegliere i tipi di **conto supportati**. Facoltativamente, è possibile specificare un **URI di reindirizzamento**. Se si specifica un URI di reindirizzamento, è necessario specificare se è Web/pubblico (mobile e desktop). Per altre informazioni su come registrare un'app usando la nuova esperienza di registrazione delle app, vedere [registrare un'app con la piattaforma di identità Microsoft](quickstart-register-app.md). Per Azure AD B2C, vedere [registrare un'applicazione nel Azure Active Directory B2C](../../active-directory-b2c/tutorial-register-applications.md).

## <a name="the-legacy-properties-page"></a>Pagina delle proprietà legacy

L'esperienza Legacy ha una pagina delle **Proprietà** che non è presente nella nuova esperienza. Il pannello **Proprietà** presenta i campi seguenti: **nome**, **ID oggetto**, **ID applicazione**, **URI ID app**, **logo**, **URL della Home page**, **URL di disconnessione**, URL **termini di servizio**, **informativa sulla privacy URL**, **tipo di applicazione**e **multi-tenant.**

Ecco dove è possibile trovare la funzionalità equivalente nella nuova esperienza:

- Il **nome**, il **logo**, l' **URL della Home page**, l'URL **delle condizioni di servizio**e l' **URL dell'informativa sulla privacy** sono ora nella pagina di **personalizzazione** dell'app.
- **ID oggetto** e **ID applicazione (client)** si trova nella pagina **Panoramica** .
- La funzionalità controllata dall'interruttore **multi-tenant** nell'esperienza legacy è stata sostituita dai tipi di **account supportati** nella pagina di **autenticazione** . Per altre informazioni su come viene eseguito il mapping multi-tenant alle opzioni del tipo di account supportate, vedere [questa Guida introduttiva](quickstart-modify-supported-accounts.md).
- L' **URL di disconnessione** è ora disponibile nella pagina di **autenticazione** .
- Il **tipo di applicazione** non è più un campo valido. Al contrario, gli URI di reindirizzamento, che è possibile trovare nella pagina di **autenticazione** , determinano quali tipi di app sono supportati.
- L' **URI ID app** è ora denominato **URI ID applicazione** . è possibile trovarlo nel pannello **esporre un'API** . Nell'esperienza legacy questa proprietà è stata registrata automaticamente usando il formato seguente: `https://{tenantdomain}/{appID}` (ad esempio, `https://microsoft.onmicrosoft.com/aeb4be67-a634-4f20-9a46-e0d4d4f1f96d`). Nella nuova esperienza, viene generato automaticamente come `api://{appID}`, ma deve essere salvato in modo esplicito. In Azure AD B2C tenant viene ancora usato il formato `https://{tenantdomain}/{appID}`.

## <a name="reply-urlsredirect-urls"></a>URL di risposta/URL di Reindirizzamento

Nell'esperienza legacy, un'app ha una pagina **URL di risposta** . Nella nuova esperienza è possibile trovare gli URL di risposta nella sezione di **autenticazione** di un'app. Vengono inoltre definiti **URI di reindirizzamento**. Inoltre, il formato degli URI di reindirizzamento è stato modificato. È necessario che siano associati a un tipo di app (Web o pubblico). Inoltre, per motivi di sicurezza, i caratteri jolly e gli schemi http://non sono supportati (ad eccezione di http://localhost).

## <a name="keyscertificates--secrets"></a>Chiavi/certificati & segreti

Nell'esperienza legacy, un'app aveva una pagina di **chiavi** . La nuova esperienza è stata rinominata in **certificati & segreti**. Inoltre, le **chiavi pubbliche** sono denominate **certificati** e le **password** sono denominate **segreti client**.

## <a name="required-permissionsapi-permissions"></a>Autorizzazioni necessarie/autorizzazioni API

- Nell'esperienza legacy, un'app aveva una pagina **delle autorizzazioni necessarie** . Nella nuova esperienza è stato rinominato in **autorizzazioni API**.
- Quando si seleziona un'API nell'esperienza legacy, è possibile scegliere da un piccolo elenco di API Microsoft o eseguire ricerche nelle entità servizio nel tenant. Nella nuova esperienza è possibile scegliere tra più schede: **API Microsoft**, **API utilizzate dall'organizzazione**o **API personali**. La barra di ricerca sulle API utilizzate dall' **organizzazione** Cerca le entità servizio nel tenant.

   > [!NOTE]
   > Questa scheda non viene visualizzata se l'applicazione non è associata a un tenant. Per altre informazioni su come richiedere le autorizzazioni usando la nuova esperienza, vedere [questa Guida introduttiva](quickstart-configure-app-access-web-apis.md).

- L'esperienza Legacy disponeva di un pulsante **Concedi autorizzazioni** nella parte superiore della pagina **autorizzazioni richieste** . Nella nuova esperienza è disponibile una sezione di **consenso** alla concessione con un pulsante di **autorizzazione Concedi amministratore** nella sezione **autorizzazioni API** di un'app. Inoltre, esistono alcune differenze nella modalità di funzionamento dei pulsanti:
   - Nell'esperienza Legacy la logica varia a seconda dell'utente che ha eseguito l'accesso e delle autorizzazioni richieste. La logica era:
      - Se sono state richieste solo le autorizzazioni per il consenso dell'utente e l'utente che ha eseguito l'accesso non è un amministratore, l'utente è stato in grado di concedere il consenso dell'utente per le autorizzazioni richieste.
      - Se è stata richiesta almeno un'autorizzazione che richiede il consenso dell'amministratore e l'utente che ha eseguito l'accesso non è un amministratore, l'utente ha ricevuto un errore durante il tentativo di concedere il consenso.
      - Se l'utente connesso è un amministratore, è stato concesso il consenso dell'amministratore per tutte le autorizzazioni richieste.
   - Nella nuova esperienza, solo un amministratore può concedere il consenso. Quando un amministratore seleziona il pulsante di **autorizzazione Concedi amministratore** , viene concesso il consenso dell'amministratore a tutte le autorizzazioni richieste.

## <a name="deleting-an-app-registration"></a>Eliminazione di una registrazione dell'app

Nell'esperienza legacy, un'app doveva essere a tenant singolo da eliminare. Il pulsante Elimina è stato disabilitato per le app multi-tenant. Nella nuova esperienza, le app possono essere eliminate in qualsiasi stato, ma è necessario confermare l'azione. Per ulteriori informazioni sull'eliminazione delle registrazioni per l'app, vedere [questa Guida introduttiva](quickstart-remove-app.md).

## <a name="application-manifest"></a>Manifesto dell'applicazione

Le esperienze legacy e nuove usano versioni diverse per il formato JSON nell'editor manifesto. Per altre informazioni, vedere [Manifesto dell'applicazione](reference-app-manifest.md).

## <a name="new-ui"></a>Nuova interfaccia utente

È disponibile una nuova interfaccia utente per le proprietà che in precedenza potevano essere impostate solo tramite l'editor del manifesto o l'API oppure non esistevano.

- Il **flusso di concessione implicito** (oauth2AllowImplicitFlow) si trova nella pagina di **autenticazione** . Diversamente dall'esperienza legacy, è possibile abilitare i **token di accesso** o i **token ID**o entrambi.
- Gli **ambiti definiti da questa API** (oauth2Permissions) e dalle **applicazioni client autorizzate** (preAuthorizedApplications) possono essere configurati tramite la pagina **esporre un'API** . Per altre informazioni su come configurare un'app in modo che sia un'API Web ed esporre le autorizzazioni e gli ambiti, vedere [questa Guida introduttiva](quickstart-configure-app-expose-web-apis.md).
- Il **dominio del server di pubblicazione** (visualizzato agli utenti nella [richiesta di consenso dell'applicazione](application-consent-experience.md)) si trova nella pagina del pannello **personalizzazione** . Per ulteriori informazioni su come configurare un dominio del server di pubblicazione, vedere [questa procedura](howto-configure-publisher-domain.md).

## <a name="limitations"></a>Limitazioni

La nuova esperienza presenta le limitazioni seguenti:

- Il formato dei segreti client (password dell'app) è diverso rispetto a quello dell'esperienza legacy e potrebbe interrompere l'interfaccia della riga di comando.
- La modifica del valore per gli account supportati non è supportata nell'interfaccia utente. È necessario usare il manifesto dell'app a meno che non si stia passando tra Azure AD single-tenant e multi-tenant.
