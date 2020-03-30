---
title: Nuova esperienza di registrazione di app nel portale di Azure
titleSuffix: Microsoft identity platform
description: Introduzione alla nuova esperienza di registrazione dell'app nel portale di AzureAn introduction to the new App registration experience in the Azure portal
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/8/2019
ms.author: marsma
ms.reviewer: lenalepa, alamaral
ms.custom: aaddev
ms.openlocfilehash: 50c88dd1785bd9177219054fed3800ca725a5274
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154594"
---
# <a name="the-new-azure-portal-app-registration-experience"></a>La nuova esperienza di registrazione dell'app del portale di Azure

Esistono molti miglioramenti nella nuova esperienza di registrazione di app nel portale di Azure.There are many improvements in the new [App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) experience in the Azure portal. Se si ha maggiore familiarità con l'esperienza del portale di registrazione delle applicazioni (apps.dev.microsoft.com) per la registrazione o la gestione di applicazioni convergenti, denominata esperienza precedente, questa guida alla formazione inizierà a usare la nuova esperienza.

## <a name="whats-not-changing"></a>Cosa non cambia?

- Le applicazioni e le configurazioni correlate sono disponibili così come sono nella nuova esperienza. Non è necessario registrare nuovamente le applicazioni e gli utenti delle applicazioni non dovranno eseguire nuovamente l'accesso.

    > [!NOTE]
    > È necessario accedere con l'account usato per registrare le applicazioni per trovarle nel portale di Azure.You must sign-in with the account you used to register applications to find them in the Azure portal. È consigliabile controllare che l'utente connesso nel portale di Azure corrisponda all'utente che ha eseguito l'accesso al portale di registrazione dell'applicazione confrontando l'indirizzo di posta elettronica del profilo.
    >
    > In alcuni casi, soprattutto quando si accede utilizzando account Microsoft personali (ad esempio Outlook, Live, Xbox e così via) con un indirizzo di posta elettronica di Azure AD, è stato rilevato che quando si accede al portale di Azure dall'esperienza precedente, si accede a un account diverso con lo stesso posta elettronica nel tenant di Azure AD. Se ritieni che le tue applicazioni siano mancanti, esci e accedi con l'account corretto.

- Le app Live SDK create con account Microsoft personali non sono ancora supportate nel portale di Azure e continueranno a rimanere nell'esperienza precedente nel prossimo futuro.

## <a name="key-changes"></a>Modifiche principali

-   Nella vecchia esperienza, le app erano registrate per impostazione predefinita come app convergenti che supportano tutti gli account aziendali (multitenant) e gli account Microsoft personali. Questo non poteva essere modificato tramite l'esperienza precedente, rendendo difficile la creazione di app che supportavano solo account aziendali (multi-tenant o tenant singolo).
    La nuova esperienza consente di registrare le app che supportano tutte queste opzioni. [Ulteriori informazioni sui tipi di app](active-directory-v2-registration-portal.md).

-   Nella nuova esperienza, se l'account Microsoft personale si trova anche in un tenant di Azure AD, verranno visualizzate tre schede, ovvero tutte le applicazioni nel tenant, le applicazioni di proprietà nel tenant e le applicazioni dell'account personale. Pertanto, se ritieni che le app registrate con il tuo account Microsoft personale non siano presenti, controlla la scheda **Applicazioni del tuo account personale.**

-   Nella nuova esperienza, è possibile passare facilmente da un tenant all'altro passando al profilo e scegliendo cambia directory.

## <a name="list-of-applications"></a>Elenco delle applicazioni

-   Il nuovo elenco di app mostra le applicazioni registrate tramite l'esperienza di registrazione delle app legacy nel portale di Azure (solo app che accedono ad account Azure AD) e le app registrate tramite il portale di [registrazione dell'applicazione](https://apps.dev.microsoft.com/) (app che accedono sia ad Azure AD che account Microsoft personali).

-   Il nuovo elenco di app include due colonne aggiuntive: **Creato nella** colonna e colonna Certificati **& segreti** che mostra lo stato (corrente, in scadenza o scaduto) delle credenziali registrate nell'app.

## <a name="new-app-registration"></a>Registrazione nuova applicazione

Nella vecchia esperienza, per registrare un'app convergente è stato richiesto solo di fornire un nome. Le app create sono state registrate come app convergenti che supportano tutte le directory dell'organizzazione (multitenant) e gli account Microsoft personali.  Questo non poteva essere modificato tramite l'esperienza precedente, rendendo difficile la creazione di app che supportavano solo account aziendali (multi-tenant o tenant singolo). [Ulteriori informazioni sui tipi di account supportati](v2-supported-account-types.md)

Nella nuova esperienza, è necessario specificare un nome per l'app e scegliere i tipi di account supportati. Facoltativamente, è possibile fornire un URI di reindirizzamento.
Se fornisci un URI di reindirizzamento, dovrai specificare se è web/public (nativo/mobile e desktop). Per altre info su come registrare un'app usando la nuova esperienza di registrazione dell'app, vedi [questa guida introduttiva](quickstart-register-app.md).

## <a name="app-management-page"></a>Pagina di gestione delle app

La vecchia esperienza aveva una singola pagina di gestione delle app per le app convergenti con le sezioni seguenti: Proprietà, Segreti dell'applicazione, Piattaforme, Proprietari, Autorizzazioni di Microsoft Graph, Profilo e Opzioni avanzate.

La nuova esperienza nel portale di Azure rappresenta queste funzionalità in pagine separate. Qui è dove è possibile trovare la funzionalità equivalente:

-   Proprietà: nome e ID applicazione sono nella pagina Panoramica.Properties - Name and Application ID is on the Overview page.

-   Segreti di applicazione nella pagina Certificati & segretiApplication Secrets is on the Certificates & secrets

-   La configurazione delle piattaforme è nella pagina Autenticazione

-   Le autorizzazioni di Microsoft Graph sono nella pagina delle autorizzazioni dell'API insieme ad altre autorizzazioni

-   Il profilo è nella pagina Branding

-   Opzione avanzata: il supporto di Live SDK è disponibile nella pagina Autenticazione.Advanced option - Live SDK support is on the Authentication page.

## <a name="application-secretscertificates--secrets"></a>Segreti di applicazione/certificati & segretiApplication secrets/Certificates & secrets

Nella nuova esperienza, i **segreti dell'applicazione** sono stati rinominati in **Certificati & segreti**. Inoltre, **le chiavi pubbliche** vengono definite **certificati** e **password** sono denominate **Segreti client**. Abbiamo scelto di non portare questa funzionalità insieme nella nuova esperienza per motivi di sicurezza, quindi, non è più possibile generare una nuova coppia di chiavi.

## <a name="platformsauthentication-reply-urlsredirect-uris"></a>Piattaforme/Autenticazione: URL di risposta/URI di reindirizzamento
Nell'esperienza precedente, un'app aveva la sezione Piattaforme per Web, native e API Web per configurare gli URL di reindirizzamento, l'URL di disconnessione e il flusso implicito.

Nella nuova esperienza, gli URL di risposta\'sono disponibili nella sezione Autenticazione s dell'app. Inoltre, vengono definiti URI di reindirizzamento e il formato per gli URI di reindirizzamento è stato modificato. Devono essere associati a un tipo di app (client web o pubblico - mobile e desktop). [Scopri di più](quickstart-configure-app-access-web-apis.md#add-redirect-uris-to-your-application)

Le API Web sono configurate in Esporre una pagina API.

> [!NOTE]
> Provare la nuova esperienza per le Impostazioni di autenticazione che consente di configurare le impostazioni dell'applicazione in base alla piattaforma o al dispositivo di destinazione. [Scopri di più](quickstart-configure-app-access-web-apis.md#configure-platform-settings-for-your-application)

## <a name="microsoft-graph-permissionsapi-permissions"></a>Autorizzazioni/autorizzazioni API di Microsoft Graph

-   Quando selezioni un'API nell'esperienza precedente, puoi scegliere solo tra le API di Microsoft Graph. Nella nuova esperienza, puoi scegliere tra molte API Microsoft, tra cui Microsoft Graph, API della tua organizzazione e le tue API, presentate in tre schede: API Microsoft, API utilizzate dall'organizzazione o API personali. La barra di ricerca nelle API dell'organizzazione usa le ricerche nelle schede tramite le entità servizio nel tenant.

    > [!NOTE]
    > Questa scheda non verrà visualizzata se l'applicazione non è associata a un tenant. Per altre info su come richiedere le autorizzazioni usando la nuova esperienza, vedi [questa guida introduttiva](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/develop/quickstart-configure-app-access-web-apis.md).

-   L'esperienza precedente non disponeva di un pulsante **Concedi autorizzazioni.** Nella nuova esperienza è disponibile una sezione Concedi consenso con un pulsante **Concedi consenso all'amministratore** nella sezione Autorizzazioni API di un'app. Solo un amministratore può concedere il consenso e questo pulsante è abilitato solo per gli amministratori. Quando un amministratore seleziona il pulsante **Concedi consenso all'amministratore,** il consenso dell'amministratore viene concesso a tutte le autorizzazioni richieste.

## <a name="profile"></a>Profilo
Nella vecchia esperienza, Profilo aveva Logo, URL home page, URL termini di servizio e configurazione DELL'URL dell'informativa sulla privacy. Nella nuova esperienza, questi possono essere trovati nella pagina Branding.

## <a name="application-manifest"></a>Manifesto dell'applicazione
Nella nuova esperienza, la pagina Manifesto consente di modificare e aggiornare gli attributi dell'app. Per altre informazioni, vedere [Manifesto dell'applicazione](reference-app-manifest.md).

## <a name="new-ui"></a>Nuova interfaccia utente
È disponibile una nuova interfaccia utente per le proprietà che in precedenza potevano essere impostate solo utilizzando l'editor del manifesto o l'API oppure che non esistevano.

-   Il flusso di concessione implicita (oauth2AllowImplicitFlow) è disponibile nella pagina Autenticazione. A differenza dell'esperienza precedente, è possibile abilitare i token di accesso o i token ID o entrambi.

-   Gli ambiti definiti da questa API (oauth2Permissions) e dalle applicazioni client autorizzate (preAuthorizedApplications) possono essere configurati tramite la pagina Esporre un'API. Per altre info su come configurare un'app come API Web ed esporre autorizzazioni/ambiti, vedi [questa guida introduttiva.](quickstart-configure-app-expose-web-apis.md)

-   Il dominio dell'editore (visualizzato agli utenti nella richiesta di [consenso dell'applicazione\')](application-consent-experience.md)è disponibile nella pagina del pannello Personalizzazione. Per ulteriori informazioni su come configurare un dominio dell'editore, vedere [questa procedura](howto-configure-publisher-domain.md).

## <a name="limitations"></a>Limitazioni

La nuova esperienza presenta le seguenti limitazioni:

-   La nuova esperienza non supporta ancora le registrazioni delle app per i tenant B2C di Azure AD.

-   La nuova esperienza non supporta ancora le app Live SDK create con account Microsoft personali.

-   La modifica del valore per gli account supportati non è supportata nell'interfaccia utente. È necessario usare il manifesto\'dell'app a meno che non si passi da Azure AD single-tenant a multi-tenant.

   > [!NOTE]
   > Se si è un utente dell'account Microsoft personale nel tenant di Azure AD e l'amministratore tenant ha limitato l'accesso al portale di Azure, è possibile che venga negato l'accesso. Tuttavia, se si arriva attraverso il collegamento digitando registrazioni App nella barra di ricerca o bloccandolo, sarete in grado di accedere alla nuova esperienza.
