---
title: Informazioni su Azure Active Directory B2C | Microsoft Docs
description: Informazioni su come creare e gestire le esperienze di gestione delle identità, come iscrizione/accesso e gestione dei profili, in un'applicazione con Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 02/20/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ca9d8a8373bd73d527862864d436319eb45b5f48
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227161"
---
# <a name="what-is-azure-active-directory-b2c"></a>Informazioni su Azure Active Directory B2C

Azure Active Directory (Azure AD) B2C è un servizio di gestione delle identità Business to Consumer. Questo servizio consente di personalizzare e controllare l'interazione sicura degli utenti con applicazioni Web, desktop, per dispositivi mobili o a pagina singola. Con Azure AD B2C, gli utenti possono eseguire l'iscrizione, l'accesso, la reimpostazione delle password e la modifica dei profili. Azure AD B2C implementa una forma dei protocolli OAuth 2.0 e OpenID Connect. Il fattore chiave nell'implementazione di questi protocolli sono i token di sicurezza e le relative attestazioni, che consentono di offrire un accesso sicuro alle risorse.

Un *percorso utente* è una richiesta che specifica i criteri che controllano il comportamento dell'interazione dell'utente e dell'applicazione con Azure AD B2C. Per definire i percorsi utente in Azure AD B2C sono disponibili due modi.

Gli sviluppatori di applicazioni, con o senza competenze nella gestione delle identità, possono scegliere di definire comuni flussi utente di gestione delle identità con il portale di Azure. Gli integratori di sistemi, i consulenti, i professionisti o i team interni addetti alla gestione delle identità che hanno familiarità con i flussi di OpenID Connect e conoscono i provider di identità e l'autenticazione basata sulle attestazioni possono scegliere di usare criteri personalizzati basati su XML.

Prima di iniziare a definire un percorso utente, è necessario creare un tenant di Azure AD B2C e registrare l'applicazione e l'API nel tenant. Dopo aver completato queste attività, è possibile iniziare a definire un percorso utente con flussi utente o criteri personalizzati. Facoltativamente, è anche possibile aggiungere o modificare i provider di identità o personalizzare l'esperienza utente correlata al percorso.

## <a name="protocols-and-tokens"></a>Protocolli e token

Per i percorsi utente, Azure AD B2C supporta i [protocolli OpenID Connect e OAuth 2.0](active-directory-b2c-reference-protocols.md). Nell'implementazione di OpenID Connect in Azure AD B2C, l'applicazione avvia il percorso utente inviando richieste di autenticazione ad Azure AD B2C.

Il risultato di una richiesta ad Azure AD B2C è un token di sicurezza, ad esempio [un token ID o un token di accesso](active-directory-b2c-reference-tokens.md). Questo token di sicurezza definisce l'identità dell'utente. I token vengono ricevuti da endpoint di Azure AD B2C, ad esempio da un endpoint `/token` o `/authorize`. Da questi token è possibile accedere alle attestazioni utilizzabili per convalidare un'identità e consentire l'accesso a risorse sicure.

## <a name="tenants-and-applications"></a>Tenant e applicazioni

In Azure AD B2C, un *tenant* rappresenta l'organizzazione ed è una directory di utenti. Ogni tenant di Azure AD B2C è distinto e separato dagli altri tenant di Azure AD B2C. Potrebbe essere già disponibile un tenant di Azure Active Directory, ma il tenant di Azure AD B2C è un tenant diverso. Un tenant contiene informazioni sugli utenti che hanno effettuato l'iscrizione per usare l'applicazione, ad esempio le password, i dati di profilo e le autorizzazioni. Per altre informazioni, vedere [Esercitazione: Creare un tenant di Azure Active Directory B2C](tutorial-create-tenant.md).

Prima di configurare l'applicazione per l'uso di Azure AD B2C, è necessario registrarla nel tenant con il portale di Azure. Il processo di registrazione raccoglie e assegna all'applicazione alcuni valori. Questi valori includono un ID applicazione che identifica in modo univoco l'applicazione e un URI di reindirizzamento usato per indirizzare le risposte all'applicazione.

Ogni interazione di un'applicazione segue un modello generale simile al seguente:

1. L'applicazione indirizza l'utente per l'esecuzione dei criteri.
2. L'utente completa i criteri in base alla relativa definizione.
3. L'applicazione riceve un token.
4. L'applicazione usa il token per provare ad accedere a una risorsa.
5. Il server delle risorse convalida il token per verificare che sia possibile concedere l'accesso.
6. L'applicazione aggiorna periodicamente il token.

Per registrare un'applicazione Web, completare la procedura descritta in [Esercitazione: Registrare un'applicazione per consentire l'iscrizione e l'accesso tramite Azure AD B2C](tutorial-register-applications.md). È anche possibile [aggiungere un'applicazione per le API Web al tenant di Azure Active Directory B2C](add-web-application.md) oppure [aggiungere un'applicazione client nativa al tenant di Azure Active Directory B2C](add-native-application.md).

## <a name="user-journeys"></a>Percorsi utente

I criteri in un percorso utente possono essere definiti come [flusso utente](active-directory-b2c-reference-policies.md) o come [criteri personalizzati](active-directory-b2c-overview-custom.md). Nel portale di Azure sono disponibili flussi utente predefiniti per le attività di gestione delle identità più comuni, come iscrizione, accesso e modifica dei profili.

I percorsi utente consentono di controllare i comportamenti configurando le impostazioni seguenti:

- Account di social networking usati dall'utente per l'iscrizione all'applicazione
- Dati raccolti dall'utente, come nome o CAP
- Autenticazione a più fattori
- Aspetto delle pagine
- Informazioni restituite all'applicazione

I criteri personalizzati sono file di configurazione che definiscono il comportamento di [Identity Experience Framework](trustframeworkpolicy.md) nel tenant di Azure AD B2C. Identity Experience Framework è la piattaforma sottostante che stabilisce una relazione di trust tra più parti e completa i passaggi in un percorso utente.

I criteri personalizzati possono essere modificati per completare numerose attività. Un criterio personalizzato è costituito da uno o più file in formato XML che fanno riferimento l'uno all'altro in una catena gerarchica. È disponibile uno [starter pack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) per i criteri personalizzati per supportare attività comuni di gestione delle identità.

Criteri personalizzati o flussi utente di diversi tipi possono essere usati nel tenant di Azure AD B2C in base alle esigenze ed essere riutilizzati in più applicazioni. Questa flessibilità consente di definire e modificare le esperienze di gestione delle identità utente senza modificare il codice o con modifiche minime. I criteri vengono usati aggiungendo uno speciale parametro di query alle richieste di autenticazione HTTP. Per creare criteri personalizzati, vedere [Introduzione ai criteri personalizzati in Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).

## <a name="identity-providers"></a>Provider di identità

È possibile consentire agli utenti di accedere alle applicazioni con provider di identità diversi. Un *provider di identità* crea, mantiene e gestisce le informazioni sulle identità fornendo al contempo servizi di autenticazione alle applicazioni. È possibile aggiungere provider di identità supportati da Azure AD B2C con il portale di Azure.

In genere si usa un solo provider di identità in un'applicazione, ma è possibile aggiungerne altri. Per configurare un provider di identità nel tenant di Azure AD B2C, per prima cosa si crea un'applicazione nel sito per sviluppatori del provider di identità, quindi si registrano l'ID applicazione o client e la password o il segreto client dell'applicazione del provider di identità che è stata creata. Questo identificatore e la password vengono quindi usati per configurare l'applicazione.

Gli articoli seguenti descrivono i passaggi per aggiungere alcuni provider di identità comuni ai flussi utente:

- [Amazon](active-directory-b2c-setup-amzn-app.md)
- [Facebook](active-directory-b2c-setup-fb-app.md)
- [Account Microsoft](active-directory-b2c-setup-msa-app.md)

Gli articoli seguenti descrivono i passaggi per aggiungere alcuni provider di identità comuni ai criteri personalizzati:
- [Amazon](setup-amazon-custom.md)
- [Google](active-directory-b2c-custom-setup-goog-idp.md)
- [Account Microsoft](active-directory-b2c-custom-setup-msa-idp.md)

Per altre informazioni, vedere [Esercitazione: Aggiungere provider di identità alle applicazioni in Azure Active Directory B2C](tutorial-add-identity-providers.md).


## <a name="page-customization"></a>Personalizzazione delle pagine

È possibile controllare la maggior parte del contenuto HTML e CSS che viene presentato ai clienti in un percorso utente. Con la personalizzazione delle pagine, si può personalizzare l'aspetto di qualsiasi criterio personalizzato o flusso utente. Questa funzionalità di personalizzazione consente di mantenere la coerenza visiva e del marchio tra l'applicazione e Azure AD B2C.

Azure AD B2C esegue il codice nel browser dell'utente e usa un approccio moderno denominato condivisione di risorse tra le origini (CORS). In primo luogo, specificare un URL nel criterio con contenuto HTML personalizzato. Azure AD B2C unisce elementi dell'interfaccia utente con il contenuto HTML caricato dall'URL e quindi mostra la pagina all'utente.

I parametri vengono inviati ad Azure AD B2C in una stringa di query. Passando il parametro all'endpoint HTML, il contenuto della pagina viene modificato in modo dinamico. Ad esempio, si modifica l'immagine di sfondo della pagina di iscrizione o di accesso in base a un parametro passato dall'applicazione Web o per dispositivi mobili.

Per personalizzare le pagine in un flusso utente, vedere [Esercitazione: Personalizzare l'interfaccia delle esperienze utente in Azure Active Directory B2C](tutorial-customize-ui.md). Per personalizzare le pagine nei criteri personalizzati, vedere [Personalizzare l'interfaccia utente dell'applicazione usando un criterio personalizzato in Azure Active Directory B2C](active-directory-b2c-ui-customization-custom.md) o [Configurare l'interfaccia utente con contenuto dinamico usando criteri personalizzati in Azure Active Directory B2C](active-directory-b2c-ui-customization-custom-dynamic.md).

## <a name="developer-resources"></a>Risorse per sviluppatori

### <a name="client-applications"></a>Applicazioni client

È possibile scegliere applicazioni per [iOS](active-directory-b2c-devquickstarts-ios.md), [Android](active-directory-b2c-devquickstarts-android.md) e .NET, ad esempio. Azure AD B2C consente queste azioni proteggendo al tempo stesso le identità utente.

Gli sviluppatori di applicazioni Web ASP.NET possono configurare l'applicazione per l'autenticazione degli account con la procedura descritta in [Esercitazione: Abilitare un'applicazione Web per l'autenticazione con gli account usando Azure AD B2C](active-directory-b2c-tutorials-web-app.md).

Gli sviluppatori di applicazioni desktop possono configurare l'applicazione per l'autenticazione degli account con la procedura descritta in [Esercitazione: Abilitare un'applicazione desktop per l'autenticazione con gli account tramite Azure AD B2C](active-directory-b2c-tutorials-desktop-app.md).

Gli sviluppatori di applicazioni a pagina singola che usano Node.js possono configurare l'applicazione per l'autenticazione degli account con la procedura descritta in [Esercitazione: Abilitare un'applicazione a pagina singola per l'autenticazione con gli account tramite Azure AD B2C](active-directory-b2c-tutorials-spa.md).

### <a name="apis"></a>API
Se le applicazioni client o Web devono chiamare API, è possibile configurare l'accesso sicuro a tali risorse in Azure AD B2C.

Gli sviluppatori di applicazioni Web ASP.NET possono configurare l'applicazione per la chiamata di un'API protetta con la procedura descritta in [Esercitazione: Concedere l'accesso a un'API Web ASP.NET con Azure Active Directory B2C](active-directory-b2c-tutorials-web-api.md).

Gli sviluppatori di applicazioni desktop possono configurare l'applicazione per la chiamata di un'API protetta con la procedura descritta in [Esercitazione: Concedere l'accesso a un'API Web Node.js da un'app desktop usando Azure Active Directory B2C](active-directory-b2c-tutorials-desktop-app-webapi.md).

Gli sviluppatori di applicazioni a pagina singola che usano Node.js possono impostare l'applicazione per l'autenticazione degli account con la procedura descritta in [Esercitazione: Concedere l'accesso a un'API Web ASP.NET Core da un'applicazione a pagina singola con Azure Active Directory B2C](active-directory-b2c-tutorials-spa-webapi.md).

### <a name="javascript"></a>JavaScript

È possibile aggiungere codice lato client JavaScript alle applicazioni in Azure AD B2C. Per configurare JavaScript nell'applicazione, si definisce un [layout di pagina](page-layout.md) e si abilita [JavaScript](javascript-samples.md) nei flussi utente o nei criteri personalizzati.

### <a name="user-accounts"></a>Account utente

Molte attività comuni di gestione dei tenant devono essere eseguite a livello di codice, ad esempio la gestione degli utenti. Potrebbe essere necessario eseguire la migrazione di un archivio utenti esistente a un tenant di Azure AD B2C. Potrebbe essere necessario ospitare la registrazione degli utenti nella propria pagina e creare gli account utente nella directory Azure AD B2C in background. Questi tipi di attività richiedono la possibilità di creare, leggere, aggiornare ed eliminare gli account utente. È possibile eseguire queste attività usando l'[API Graph di Azure AD](active-directory-b2c-devquickstarts-graph-dotnet.md).

## <a name="next-steps"></a>Passaggi successivi

Avviare la configurazione dell'applicazione per l'esperienza di iscrizione e accesso passando all'esercitazione.

> [!div class="nextstepaction"]
> [Esercitazione: Creare un tenant di Azure Active Directory B2C](tutorial-create-tenant.md)
