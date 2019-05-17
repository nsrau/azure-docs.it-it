---
title: Tipi di applicazioni nella versione 1.0 | Azure
description: Vengono descritti i tipi di app e gli scenari supportati dall'endpoint v2.0 di Azure Active Directory.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: efed9e35aed729c9efa39b0772b681d8c53ba7b8
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/11/2019
ms.locfileid: "65540654"
---
# <a name="application-types-in-v10"></a>Tipi di applicazioni nella versione 1.0

Azure Active Directory supporta l'autenticazione di un'ampia gamma di architetture di app moderne, tutte basate sui protocolli standard del settore OAuth 2.0 o OpenID Connect.

Il diagramma seguente illustra questi scenari e tipi di applicazioni, oltre che il modo in cui è possibile aggiungere i vari componenti:

![Scenari e tipi di applicazione](./media/authentication-scenarios/application_types_and_scenarios.png)

Azure AD supporta i cinque scenari di applicazione principali descritti di seguito:

- **[Applicazione a pagina singola](single-page-application.md)**: un utente deve accedere a un'applicazione a pagina singola protetta da Azure AD.
- **[Da Web browser ad applicazione Web](web-app.md)**: un utente deve accedere a un'applicazione Web protetta da Azure AD.
- **[Da applicazione nativa ad API Web](native-app.md)**: un'applicazione nativa in esecuzione su un telefono, tablet o PC deve autenticare un utente per ottenere le risorse da un'API Web protetta da Azure AD.
- **[Da applicazione Web ad API Web](web-api.md)**: un'applicazione Web deve ottenere le risorse da un'API Web protetta da Azure AD.
- **[Da applicazione server o daemon ad API Web](service-to-service.md)**: un'applicazione daemon o un'applicazione server priva di interfaccia utente Web deve ottenere le risorse da un'API Web protetta da Azure AD.

Fare clic sui collegamenti per ottenere altre informazioni su ogni tipo di app e comprendere gli scenari generali prima di iniziare a usare il codice. È possibile ottenere anche informazioni sulle differenze che è necessario conoscere quando si scrive un'app specifica che funziona con l'endpoint v1.0 o v2.0.

> [!NOTE]
> Non tutti gli scenari e le funzionalità di Azure AD sono supportati dall'endpoint v2.0. Per determinare se è necessario usare l'endpoint 2.0, vedere l'articolo relativo alle [limitazioni della versione 2.0](active-directory-v2-limitations.md).

È possibile sviluppare le app e gli scenari descritti in questo articolo usando diversi linguaggi e piattaforme. In tutti i casi sono disponibili esempi di codice completi nella guida agli esempi di codice: [esempi di codice v1.0 in base agli scenari](sample-v1-code.md) ed [esempi di codice v2.0 in base agli scenari](sample-v2-code.md). È anche possibile scaricare gli esempi di codice direttamente dai [repository di esempi GitHub](https://github.com/Azure-Samples?q=active-directory) corrispondenti.

Inoltre, se l'applicazione necessita di una parte o di un segmento specifico di uno scenario end-to-end, nella maggior parte dei casi è possibile aggiungere tale funzionalità in modo indipendente. Ad esempio, se si ha un'applicazione nativa che chiama un'API Web, è possibile aggiungere facilmente un'applicazione Web che chiama la stessa API Web.

## <a name="app-registration"></a>Registrazione delle app

### <a name="registering-an-app-that-uses-the-azure-ad-v10-endpoint"></a>Registrazione di un'app che usa l'endpoint v1.0 di Azure AD

Le applicazioni che usano l'outsourcing per l'autenticazione ad Azure AD devono essere registrate in una directory. Questo passaggio include la comunicazione ad Azure AD delle informazioni sull'applicazione, compresi l'URL in cui si trova, l'URL per l'invio delle risposte dopo l'autenticazione, l'URI per identificare l'applicazione e così via. Queste informazioni sono necessarie per alcune ragioni fondamentali:

* Azure AD deve comunicare con l'applicazione durante la gestione dell'accesso o lo scambio di token. Le informazioni passate tra Azure AD e l'applicazione includono quanto segue:
  
  * **URI ID applicazione**: l'identificatore di un'applicazione. Questo valore viene inviato ad Azure AD durante l'autenticazione per specificare le applicazioni per cui il chiamante richiede un token. Questo valore è inoltre incluso nel token per indicare all'applicazione che era la destinazione prevista.
  * **URL di risposta** e **URI di reindirizzamento**: nel caso di un'API Web o di un'applicazione Web, l'URL di risposta è il percorso a cui Azure AD invierà la risposta di autenticazione, compreso un token, se l'autenticazione ha avuto esito positivo. Nel caso di un'applicazione nativa, l'URI di reindirizzamento è un identificatore univoco a cui Azure AD reindirizzerà l'agente utente in una richiesta OAuth 2.0.
  * **ID applicazione**: l'ID di un'applicazione generato da Azure AD quando l'applicazione viene registrata. Quando viene richiesto un codice di autorizzazione o token, l'ID applicazione e la chiave vengono inviati ad Azure AD durante l'autenticazione.
  * **Chiave**: la chiave inviata insieme a un ID applicazione durante l'autenticazione ad Azure AD per chiamare un'API Web.
* Azure AD deve assicurarsi che l'applicazione disponga delle autorizzazioni necessarie per accedere ai dati della directory, ad altre applicazioni nell'organizzazione e così via.

Per i dettagli, vedere come [registrare un'app](quickstart-register-app.md).

## <a name="single-tenant-and-multi-tenant-apps"></a>App a tenant singolo e multi-tenant

Per comprendere meglio il provisioning, è utile chiarire che esistono due categorie di applicazioni che possono essere sviluppate e integrate con Azure AD:

* **Applicazione con un singolo tenant**: un'applicazione con un singolo tenant è destinata all'uso in un'organizzazione. In genere si tratta di applicazioni line-of-business scritte da uno sviluppatore aziendale. Un'applicazione con un singolo tenant deve essere accessibile solo agli utenti di una directory e quindi è necessario eseguirne il provisioning in una sola directory. Queste applicazioni sono generalmente registrate da uno sviluppatore dell'organizzazione.
* **Applicazione multi-tenant**: un'applicazione multi-tenant è destinata all'uso in molte organizzazioni, non solo in una. Si tratta generalmente di applicazioni SaaS (Software-as-a-Service) scritte da un fornitore di software indipendente (ISV). È necessario eseguire il provisioning delle applicazioni multi-tenant in ogni directory in cui verranno usate ed è richiesto il consenso dell'utente o dell'amministratore per registrarle. Questo processo di consenso inizia quando un'applicazione viene registrata nella directory e può accedere all'API Graph o magari a un'altra API Web. Quando un utente o amministratore di un'altra organizzazione esegue l'accesso per usare l'applicazione, viene visualizzata una finestra di dialogo in cui sono indicate le autorizzazioni richieste dall'applicazione. L'utente o amministratore può quindi concedere il consenso all'applicazione, in modo da fornirle l'accesso ai dati indicati e infine di registrarla nella propria directory. Per altre informazioni, vedere [Panoramica del framework di consenso](consent-framework.md).

### <a name="additional-considerations-when-developing-single-tenant-or-multi-tenant-apps"></a>Considerazioni aggiuntive quando si sviluppano app multi-tenant o con un singolo tenant

Quando si sviluppa un'applicazione multi-tenant invece di un'applicazione con un singolo tenant, occorre considerare qualche altro aspetto. Ad esempio, se l'applicazione viene resa disponibile agli utenti in più directory, è necessario un meccanismo per determinare il tenant attivo. Un'applicazione con un singolo tenant deve solo cercare un utente nella propria directory, mentre un'applicazione multi-tenant deve identificare un utente specifico in tutte le directory in Azure AD. A questo scopo, Azure AD fornisce un endpoint di autenticazione comune in cui qualsiasi applicazione multi-tenant può indirizzare le richieste di accesso, invece di un endpoint specifico di un tenant. Questo endpoint è https://login.microsoftonline.com/common per tutte le directory in Azure AD, mentre un endpoint specifico del tenant può essere https://login.microsoftonline.com/contoso.onmicrosoft.com. L'endpoint comune deve essere valutato con particolare attenzione durante lo sviluppo di un'applicazione, perché occorrerà la logica necessaria per gestire più tenant durante l'accesso, la disconnessione e la convalida dei token.

Se si sviluppa un'applicazione con un singolo tenant, ma si intende metterla a disposizione di molte organizzazioni, è possibile modificare facilmente l'applicazione e la relativa configurazione in Azure AD per fare in modo che supporti più tenant. Azure AD usa inoltre la stessa chiave di firma per tutti i token in tutte le directory, a prescindere dal fatto che si fornisca l'autenticazione in un'applicazione con un singolo tenant o multi-tenant.

Ogni scenario indicato in questo documento include una sottosezione in cui sono descritti i relativi requisiti di provisioning. Per informazioni più dettagliate sul provisioning di un'applicazione in Azure AD e sulle differenze tra applicazioni con un singolo tenant e applicazioni multi-tenant, vedere [Integrazione di applicazioni con Azure Active Directory](quickstart-v1-integrate-apps-with-azure-ad.md). Di seguito sono illustrati gli scenari di applicazione più comuni in Azure AD.

## <a name="next-steps"></a>Passaggi successivi

- Leggere altre informazioni sulle [nozioni di base dell'autenticazione](authentication-scenarios.md) di Azure AD
