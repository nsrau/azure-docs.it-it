---
title: Guida alla formazione per la nuova registrazione di app nel portale di Azure
description: Introduce la nuova esperienza di registrazione dell'app del portale di AzureIntroduces the new Azure portal App registration experience
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: marsma
ms.reviewer: lenalepa, keyam
ms.custom: aaddev
ms.openlocfilehash: a437d54dac50be7ddaad899a1cf0a3e93aade8f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154577"
---
# <a name="new-azure-portal-app-registration-training-guide"></a>Guida alla formazione per la nuova registrazione di app nel portale di Azure

È possibile trovare molti miglioramenti nella nuova esperienza di registrazione di app nel portale di Azure.You can find many improvements in the new [App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) experience in the Azure portal. Se si ha familiarità con l'esperienza di registrazione delle app (legacy) nel portale di Azure, usare questa guida alla formazione per iniziare a usare la nuova esperienza.

In Azure Active Directory, la nuova esperienza di registrazione dell'applicazione descritta di seguito è generalmente disponibile (GA). In Azure Active Directory B2C (Azure AD B2C) questa esperienza è in anteprima.

## <a name="key-changes"></a>Modifiche principali

- Le registrazioni delle app non sono limitate a essere *un'app Web/API* o un'app *nativa.* Puoi usare la stessa registrazione dell'app per tutte queste app registrando i rispettivi URI di reindirizzamento.

- L'esperienza legacy supporta le app che accedono usando solo gli account dell'organizzazione (Azure AD). Le app sono state registrate come tenant singolo. Le app supportano solo gli account dell'organizzazione dalla directory in cui è stata registrata l'app. Le app possono essere modificate per essere multi-tenant e supportare tutti gli account dell'organizzazione. La nuova esperienza consente di registrare app in grado di supportare sia queste opzioni che una terza opzione: tutti gli account dell'organizzazione e gli account Microsoft personali.

- L'esperienza legacy era disponibile solo quando ha effettuato l'accesso al portale di Azure usando un account aziendale. Con la nuova esperienza, è possibile utilizzare account Microsoft personali non associati a una directory.

## <a name="list-of-applications"></a>Elenco delle applicazioni

Il nuovo elenco di app mostra le applicazioni registrate tramite l'esperienza di registrazione di app legacy nel portale di Azure.The new app list shows applications that were registered through the legacy app registrations experience in the Azure portal. Queste app accedono usando gli account di Azure AD. Il nuovo elenco di app mostra anche le app registrate tramite il portale di registrazione delle applicazioni. Queste app accedono usando Azure AD e gli account Microsoft personali.

>[!NOTE]
>Il portale di registrazione delle applicazioni è deprecato.

Il nuovo elenco di app non dispone di una colonna **Tipo di applicazione** perché la registrazione di una singola app può essere di diversi tipi. L'elenco include due colonne aggiuntive: **Creato in** e Certificati **& segreti**. **I & segreti dei certificati** mostrano lo stato delle credenziali registrate nell'app. Gli stati includono **Corrente**, **Scadenza a breve**e **Scaduto**.

## <a name="new-app-registration"></a>Registrazione nuova applicazione

Nell'esperienza legacy, per registrare un'app è necessario fornire: **Nome**, **Tipo di applicazione**e URI URL di **accesso/reindirizzamento**. Le app create erano solo applicazioni ad alto tenant di Azure AD. Hanno supportato solo gli account dell'organizzazione dalla directory in cui è stata registrata l'app.

Nella nuova esperienza, è necessario specificare un **nome** per l'app e scegliere i **tipi di account supportati**. Facoltativamente, è possibile fornire un URI di **reindirizzamento**. Se fornisci un URI di reindirizzamento, dovrai specificare se è web/public (mobile e desktop). Per ulteriori informazioni, vedere [Guida introduttiva: Registrare un'applicazione con la piattaforma di identità Microsoft](quickstart-register-app.md). Per Azure AD B2C, vedere [Registrare un'applicazione in Azure Active Directory B2C.](../../active-directory-b2c/tutorial-register-applications.md)

## <a name="differences-between-the-application-registration-portal-and-app-registrations-page"></a>Differenze tra il portale di registrazione dell'applicazione e la pagina di registrazione delle app

### <a name="the-legacy-properties-page"></a>Pagina Proprietà legacy

L'esperienza legacy ha avuto una pagina **proprietà.** **Le proprietà** avevano i seguenti campi:

- **Nome**
- **ID oggetto**
- **ID applicazione**
- **URI ID app**
- **Logo**
- **URL della home page**
- **URL di disconnessione**
- **URL dei termini di servizio**
- **URL informativa sulla privacy**
- **Tipo di applicazione**
- **Multi-tenant**

La nuova esperienza non ha quella pagina. Qui è dove è possibile trovare la funzionalità equivalente:

- **Nome**, **Logo**, **URL della home page**, URL termini di **servizio**e **URL informativa sulla privacy** sono ora disponibili nella pagina **Branding** dell'app.
- **L'ID oggetto** e **l'ID applicazione (client)** si trovano nella pagina **Panoramica.**
- La funzionalità controllata dall'interruttore **Multi-tenant** nell'esperienza legacy è stata sostituita da **Tipi di account supportati** nella pagina **Autenticazione.** Per ulteriori informazioni, vedere [Guida introduttiva: Modificare gli account supportati da un'applicazione](quickstart-modify-supported-accounts.md).
- **L'URL di disconnessione** è ora nella pagina **Autenticazione.**
- **Il tipo di applicazione** non è più un campo valido. Al contrario, gli URI di reindirizzamento, che è possibile trovare nella pagina **Autenticazione,** determinano quali tipi di app sono supportati.
- **L'URI dell'ID applicazione** è ora denominato **URI ID applicazione** ed è possibile **trovarlo**in Expose an API . Nell'esperienza precedente, la registrazione automatica di `https://{tenantdomain}/{appID}`questa proprietà `https://microsoft.onmicrosoft.com/492439af-3282-44c3-b297-45463339544b`è stata automaticamente utilizzando il seguente formato: , ad esempio . Nella nuova esperienza, viene generato `api://{appID}`automaticamente come , ma deve essere salvato in modo esplicito. Nei tenant B2C di `https://{tenantdomain}/{appID}` Azure AD il formato è ancora usato.

### <a name="reply-urlsredirect-urls"></a>URL di risposta/reindirizzare Gli URl

Nell'esperienza legacy, un'app ha una pagina URL di **risposta.** Nella nuova esperienza, gli URL di risposta sono disponibili nella pagina **Autenticazione** di un'app. Sono ora indicati come URI di **reindirizzamento**.

Il formato per gli URI di reindirizzamento è stato modificato. Devono essere associati a un tipo di app, Web o pubblico. Per motivi di sicurezza, i caratteri jolly `http://` *http://localhost*e gli schemi non sono supportati, ad eccezione di .

### <a name="keyscertificates--secrets"></a>Chiavi/Certificati & segreti

Nell'esperienza legacy, un'app ha la pagina **Chiavi.In** the legacy experience, an app had Keys page. Nella nuova esperienza, è stato rinominato in **Certificati & segreti**.

**Le chiavi pubbliche** sono ora denominate **Certificati**. **Le password** sono ora denominate **Segreti client**.

### <a name="required-permissionsapi-permissions"></a>Autorizzazioni/autorizzazioni API necessarie

Nell'esperienza legacy, un'app ha una pagina **Autorizzazioni necessarie.** Nella nuova esperienza, è stato rinominato **autorizzazioni API**.

Quando hai selezionato un'API nell'esperienza legacy, puoi scegliere da un piccolo elenco di API Microsoft. È anche possibile eseguire ricerche tra le entità servizio nel tenant. Nella nuova esperienza è possibile scegliere tra più schede: **API Microsoft**, API **utilizzate dall'organizzazione**o **API personali**. La barra di ricerca nelle **API dell'organizzazione** usa le ricerche nelle schede tramite le entità servizio nel tenant.

> [!NOTE]
> Questa scheda non verrà visualizzata se l'applicazione non è associata a un tenant. Per ulteriori informazioni su come richiedere le autorizzazioni, vedere [Guida introduttiva: Configurare un'applicazione client per l'accesso alle API Web](quickstart-configure-app-access-web-apis.md).

L'esperienza legacy ha un pulsante **Concedi autorizzazioni** nella parte superiore della pagina **Autorizzazioni richieste.** Nella nuova esperienza, la pagina **di consenso della sovvenzione** include un pulsante **Concedi consenso amministratore** nella sezione Autorizzazioni **API** di un'app. Ci sono anche alcune differenze nel modo in cui i pulsanti funzionano.

Nell'esperienza legacy, la logica variava a seconda dell'utente connesso e delle autorizzazioni richieste. La logica era:

- Se vengono richieste solo le autorizzazioni di autorizzazione dell'utente e l'utente che ha eseguito l'accesso non è un amministratore, l'utente potrebbe concedere il consenso dell'utente per le autorizzazioni richieste.
- Se è stata richiesta almeno un'autorizzazione che richiede il consenso dell'amministratore e l'utente che ha eseguito l'accesso non è un amministratore, l'utente ha ricevuto un errore durante il tentativo di concedere il consenso.
- Se l'utente connesso era un amministratore, è stato concesso il consenso dell'amministratore per tutte le autorizzazioni richieste.

Nella nuova esperienza, solo un amministratore può concedere il consenso. Quando un amministratore seleziona **Concedi consenso all'amministratore,** il consenso dell'amministratore viene concesso a tutte le autorizzazioni richieste.

## <a name="deleting-an-app-registration"></a>Eliminazione della registrazione di un'app

Nell'esperienza legacy, è possibile eliminare solo le app single-tenant. Il pulsante Elimina è stato disabilitato per le app multi-tenant. Nella nuova esperienza, puoi eliminare le app in qualsiasi stato, ma devi confermare l'azione. Per ulteriori informazioni, vedere [Guida introduttiva: Rimuovere un'applicazione registrata con la piattaforma](quickstart-remove-app.md)di identità Microsoft .

## <a name="application-manifest"></a>Manifesto dell'applicazione

Le esperienze legacy e nuove usano versioni diverse per il formato di JSON nell'editor del manifesto. Per altre informazioni, vedere [Manifesto dell'app Azure Active Directory](reference-app-manifest.md).

## <a name="new-ui"></a>Nuova interfaccia utente

La nuova esperienza aggiunge controlli dell'interfaccia utente per le proprietà seguenti:The new experience adds UI controls for the following properties:

- La pagina **Autenticazione** ha`oauth2AllowImplicitFlow`flusso di **concessione implicita** ( ). A differenza dell'esperienza legacy, è possibile abilitare **i token** di accesso o **i token ID**o entrambi.
- La pagina **Esponi un'API** contiene`oauth2Permissions`gli ambiti definiti da questa **API** ( ) e le **applicazioni client autorizzate** (`preAuthorizedApplications`). Per altre informazioni su come configurare un'app come API Web ed esporre autorizzazioni/ambiti, vedere [Guida introduttiva: Configurare un'applicazione per esporre le API Web.](quickstart-configure-app-expose-web-apis.md)
- La pagina **Personalizzazione** contiene il **dominio del server di pubblicazione**. Il dominio dell'editore viene visualizzato agli utenti alla [richiesta di consenso dell'applicazione.](application-consent-experience.md) Per ulteriori informazioni, vedere [Procedura: configurare il dominio dell'editore di un'applicazione](howto-configure-publisher-domain.md).

## <a name="limitations"></a>Limitazioni

La nuova esperienza presenta le seguenti limitazioni:

- Il formato dei segreti client (password delle app) è diverso da quello dell'esperienza legacy e può interrompere l'interfaccia della riga di comando.
- La modifica del valore per gli account supportati non è supportata nell'interfaccia utente. È necessario usare il manifesto dell'app a meno che non si passi da Azure AD single-tenant a multi-tenant.
