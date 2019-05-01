---
title: Registrazioni per l'App nella Guida alla formazione del portale di Azure - Azure
description: Compilare flussi di autenticazione incorporati e senza browser usando la concessione del codice del dispositivo.
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
ms.openlocfilehash: 118c6ecb16d325a384246a0b3d9e685f6f6f04ee
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870118"
---
# <a name="training-guide-app-registrations-in-the-azure-portal"></a>Guida alla formazione: Registrazioni per l'app nel portale di Azure  

È possibile trovare numerosi miglioramenti nel nuovo [registrazioni per l'App](https://go.microsoft.com/fwlink/?linkid=2083908) esperienza nel portale di Azure. Se non ha maggiore familiarità con l'esperienza legacy, è possibile usare questa Guida alla formazione per iniziare a usare la nuova esperienza.

## <a name="key-changes"></a>Modifiche delle chiavi

- Registrazioni per l'App non avevano entrambi un **app web/API** o una **native** app. È possibile usare la registrazione dell'app stessa per tutti questi registrando il rispettivo URI di reindirizzamento.
- Le app di esperienza legacy supportato che l'accesso dell'organizzazione () account di Azure AD solo. Le app sono state registrate come a tenant singolo (supportano gli account aziendali solo dalla directory dell'app è stata registrata in) e può essere modificato per essere multi-tenant (supportano tutti gli account dell'organizzazione). La nuova esperienza consente di registrare le app che possono supportare entrambe queste opzioni, nonché una terza opzione: tutti gli account aziendali, nonché gli account Microsoft personali.
- L'esperienza legacy solo era disponibile durante l'accesso a portale di Azure usando un account aziendale. Con la nuova esperienza, è possibile usare gli account Microsoft personali che non sono associati a una directory.

## <a name="list-of-applications"></a>Elenco di applicazioni

- Il nuovo elenco di app Mostra le applicazioni che sono state registrate tramite l'app legacy registrazioni per l'esperienza nel portale di Azure (app che consentono l'accesso nell'account Azure AD), nonché le app registrate tramite il [portale di registrazione applicazione](https://apps.dev.microsoft.com/) (le app che consentono l'accesso in Azure AD e gli account Microsoft personali).
- Il nuovo elenco di app non ha un **tipo di applicazione** colonna (dato che una registrazione singola app può essere tipi diversi) e dispone di due colonne aggiuntive: un **creato in** colonna e un **certificati & segreti** colonna che mostra lo stato (corrente, scadenza imminente o avvenuta) delle credenziali che sono state registrate nell'app.

## <a name="new-app-registration"></a>Nuova registrazione di app

L'esperienza legacy, per registrare un'app veniva richiesto di fornire: **Nome**, **tipo di applicazione**, e **Sign-on di URL/Redirect URI**. Le app che sono state create sono state applicazioni solo a tenant singolo di Azure AD il che significa che è supportato solo gli account aziendali dalla directory che in è stata registrata l'app.

Nella nuova esperienza, è necessario specificare una **Name** per l'app e scegliere il **tipi di account supportati**. È anche possibile specificare una **URI di reindirizzamento**. Se si specifica un URI di reindirizzamento, è necessario specificare se si tratta di web/pubblico (per dispositivi mobili e desktop). Per altre informazioni su come registrare un'app usando le nuove registrazioni di app, vedere [questa Guida introduttiva](quickstart-register-app.md).

## <a name="the-legacy-properties-page"></a>La pagina delle proprietà legacy

L'esperienza legacy aveva una **proprietà** pagina che non ha la nuova esperienza. Il **proprietà** pannello aveva i campi seguenti: **Nome**, **ObjectID**, **ID applicazione**, **URI ID App**, **Logo**, **URL della home page** , **URL di disconnessione**, **condizioni per l'URL del servizio**, **URL informativa sulla Privacy**, **tipo di applicazione**, e  **Multi-tenant.**

Ecco dove è possibile trovare la funzionalità equivalente nella nuova esperienza:

- **Nome**, **Logo**, **URL della home page**, **condizioni per l'URL del servizio**, e **URL informativa sulla Privacy** trova ora sul dell'app **Informazioni personalizzate distintive del** pagina.
- **ID dell'oggetto** e **ID applicazione (client)** si trova nel **Panoramica** pagina.
- La funzionalità controllata dal **multi-tenant** attiva/disattiva l'esperienza legacy è stata sostituita da **tipi di account supportati** sul **Authentication** pagina. Per altre informazioni sulle modalità di mapping di multi-tenant per le opzioni del tipo di account supportati, vedere [questa Guida introduttiva](quickstart-modify-supported-accounts.md).
- **URL di disconnessione** ora è il **autenticazione** pagina.
- **Tipo di applicazione** non è più un campo valido. Invece, URI di reindirizzamento (che è possibile trovare nel **autenticazione** pagina) determinano i tipi di app supportati.
- **URI ID app** è ora denominata **URI dell'ID applicazione** ed è possibile trovare questo sul **esporre un'API** pannello. Nell'esperienza legacy, questa proprietà è stata automaticamente registrati usando il formato seguente: `https://{tenantdomain}/{appID}` (ad esempio, `https://microsoft.onmicrosoft.com/aeb4be67-a634-4f20-9a46-e0d4d4f1f96d`). Nel nuovo formato, viene generato automaticamente come `api://{appID}`, ma deve essere salvata in modo esplicito.

## <a name="reply-urlsredirect-urls"></a>URL di risposta/Reindirizzamento URL

L'esperienza legacy, era un **URL di risposta** pagina. Nella nuova esperienza, gli URL di risposta è reperibile in un'app **autenticazione** sezione. Inoltre, essi sono dette **URI di reindirizzamento**. Inoltre, il formato per redirect URI sono cambiato. Sono necessarie per essere associato a un tipo di app (web o pubblico). Inoltre, per motivi di sicurezza, i caratteri jolly e http:// schemi non sono supportati (ad eccezione di http://localhost).

## <a name="keyscertificates--secrets"></a>Le chiavi e i certificati e i segreti

Nell'esperienza legacy, disponeva di un'app **chiavi** pagina. Nella nuova esperienza, è stato rinominato in **certificati e i segreti**. È inoltre **le chiavi pubbliche** sono detti **certificati** e **password** sono detti **i segreti Client**.

## <a name="required-permissionsapi-permissions"></a>Autorizzazioni necessarie le autorizzazioni/API

- L'esperienza legacy, era un **autorizzazioni necessarie** pagina. Nella nuova esperienza, è stato rinominato in **le autorizzazioni API**.
- Quando si seleziona un'API nell'esperienza legacy, è possibile scegliere da un elenco ridotto di APIs Microsoft oppure cercare tra le entità servizio nel tenant. Nella nuova esperienza, è possibile scegliere tra più schede: **Microsoft APIs**, **API Usa la mia organizzazione**, o **API My**. La barra di ricerca nella **API la mia organizzazione** Usa ricerche scheda tramite le entità servizio nel tenant. 

   > [!NOTE]
   > Questa scheda non visibili se l'applicazione non è associato a un tenant. Per altre informazioni su come richiedere le autorizzazioni usando la nuova esperienza, vedi [questa Guida introduttiva](quickstart-configure-app-access-web-apis.md).

- L'esperienza legacy era un **concedere le autorizzazioni** nella parte superiore del **autorizzazioni richieste** pagina. Nella nuova esperienza, è presente una **concedere il consenso** sezione con un **concedere il consenso dell'amministratore** pulsante in un'app **autorizzazioni delle API** sezione. Inoltre, esistono alcune differenze nei modi la funzione di pulsanti:
   - L'esperienza legacy, la logica consentono di variare in base all'utente connesso e le autorizzazioni richieste. È stata la logica:
      - Se solo le autorizzazioni in grado di consenso dell'utente sono state richiesto e l'utente connesso non è un amministratore, l'utente è stato in grado di concedere il consenso dell'utente per le autorizzazioni richieste.
      - Se è stato richiesto almeno un'autorizzazione che richiede il consenso dell'amministratore e l'utente connesso non è un amministratore, l'utente ha ricevuto un errore durante il tentativo di concedere il consenso.
      - Se l'utente connesso è amministratore, è stato concesso il consenso dell'amministratore per tutte le autorizzazioni richieste.
   - Nella nuova esperienza, solo un amministratore può concedere il consenso. Quando un amministratore seleziona il **concedere il consenso dell'amministratore** pulsante, viene concesso il consenso dell'amministratore per tutte le autorizzazioni richieste.

## <a name="deleting-an-app-registration"></a>L'eliminazione di una registrazione dell'app

Nell'esperienza legacy, un'app doveva essere a tenant singolo da eliminare. Il pulsante di eliminazione è stato disabilitato per le app multi-tenant. Nella nuova esperienza, le app possono essere eliminate in qualsiasi stato, ma è necessario confermare l'azione. Per altre informazioni sull'eliminazione di registrazioni per l'app, vedere [questa Guida introduttiva](quickstart-remove-app.md).

## <a name="application-manifest"></a>Manifesto dell'applicazione

Esperienze legacy e nuovo usano versioni diverse per il formato di file JSON nell'editor del manifesto. Per altre informazioni, vedere [Manifesto dell'applicazione](reference-app-manifest.md).

## <a name="new-ui"></a>Nuova interfaccia utente

È disponibile la nuova interfaccia utente per le proprietà che è stato precedentemente essere impostato solo usando l'editor del manifesto o l'API o non esiste.

- **Flusso di concessione implicita** (oauth2AllowImplicitFlow) è reperibile nella **autenticazione** pagina. A differenza nell'esperienza legacy, è possibile abilitare **token di accesso** oppure **i token id**, o entrambi.
- **Gli ambiti definiti da questa API** (oauth2Permissions) e **autorizzato alle applicazioni client** (preAuthorizedApplications) possono essere configurati tramite il **esporre un'API** pagina. Per altre informazioni su come configurare un'app da un'API web e di esporre le autorizzazioni/ambiti, vedere [questa Guida introduttiva](quickstart-configure-app-expose-web-apis.md).
- **Dominio di pubblicazione** (che viene visualizzato agli utenti nel [richiesta di consenso dell'applicazione](application-consent-experience.md)) è reperibile nella **pannello Branding** pagina. Per altre informazioni su come configurare un dominio di pubblicazione, vedere [questa guida procedurale](howto-configure-publisher-domain.md).

## <a name="limitations"></a>Limitazioni

La nuova esperienza presenta le limitazioni seguenti:

- La nuova esperienza non è attualmente disponibile nei tenant di Azure AD B2C.
- Il formato dei segreti client (password dell'app) è diverso da quella dell'esperienza legacy e interruzioni della riga di comando.
- Modifica del valore per gli account supportati non è supportata nell'interfaccia utente. È necessario usare il manifesto dell'app, a meno che non si passa tra tenant singolo e multi-tenant di Azure AD.
