---
title: Informazioni sull'autenticazione API-gemelli digitali di Azure | Microsoft Docs
description: Informazioni su come connettersi ed eseguire l'autenticazione con le API con i dispositivi gemelli digitali di Azure.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: 6c2b0ec5165652e77c92426bb62a30468eef04c2
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456907"
---
# <a name="connect-to-and-authenticate-with-apis"></a>Connettersi ed eseguire l'autenticazione con le API

Gemelli digitali di Azure usa Azure Active Directory (Azure AD) per l'autenticazione degli utenti e la protezione delle applicazioni. Azure AD supporta l'autenticazione per un'ampia gamma di architetture moderne. Tutte le architetture sono basate sui protocolli standard OAuth 2.0 oppure OpenID Connect. Inoltre, gli sviluppatori possono usare Azure AD per compilare applicazioni a tenant singolo e line-of-business (LOB). Gli sviluppatori possono anche usare Azure AD per sviluppare [applicazioni multi-tenant](how-to-multitenant-applications.md).

Per una panoramica di Azure AD, visitare la [pagina relativa ai concetti fondamentali](https://docs.microsoft.com/azure/active-directory/fundamentals/) per guide dettagliate, informazioni sui concetti e progetti di avvio rapido.

> [!TIP]
> Seguire l' [esercitazione](tutorial-facilities-setup.md) per configurare ed eseguire un'app di esempio per i dispositivi digitali gemelli di Azure.

Per integrare un'applicazione o un servizio con Azure AD, uno sviluppatore deve prima di tutto registrare l'applicazione con Azure AD. Per istruzioni dettagliate e gli screenshot, vedere [questo avvio rapido](../active-directory/develop/quickstart-register-app.md).

Azure AD supporta [cinque scenari applicativi principali](../active-directory/develop/v2-app-types.md):

* Applicazione a pagina singola: un utente ha necessità di accedere a un'applicazione a pagina singola protetta da Azure AD.
* Da Web browser ad applicazione Web: un utente ha necessità di accedere a un'applicazione Web protetta da Azure AD.
* Da applicazione nativa ad API Web: un'applicazione nativa in esecuzione su un telefono, un tablet o un PC deve autenticare un utente per ottenere le risorse da un'API Web protetta da Azure AD.
* Da applicazione Web ad API Web: un'applicazione Web deve ottenere le risorse da un'API Web protetta da Azure AD.
* Da applicazione server o daemon ad API Web: un'applicazione daemon o un'applicazione server priva di interfaccia utente Web deve ottenere le risorse da un'API Web protetta da Azure AD.

> [!IMPORTANT]
> I dispositivi gemelli digitali di Azure supportano entrambe le librerie di autenticazione seguenti:
> * [Microsoft Authentication Library più recente (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview)
> * [Libreria di autenticazione Azure Active Directory (adal)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)

## <a name="call-digital-twins-from-a-middle-tier-web-api"></a>Chiamare Gemelli digitali da un'API Web di livello intermedio

Quando progettano soluzioni per Gemelli digitali, in genere gli sviluppatori creano un'API o un'applicazione di livello intermedio. L'app o l'API chiama quindi l'API di Gemelli digitali downstream. Per supportare questa architettura di soluzione Web standard, assicurarsi che:

1. Gli utenti eseguano l'autenticazione con l'applicazione di livello intermedio

1. Durante l'autenticazione viene acquisito un token On-Behalf-Of di OAuth 2.0

1. Il token acquisito viene quindi usato per chiamare o eseguire l'autenticazione con API più a valle tramite il flusso On-Behalf-Of

Per istruzioni su come orchestrare il flusso on-behalf-of, vedere [Flusso On-Behalf-Of di OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow). È anche possibile visualizzare esempi di codice in [Calling a downstream web API](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof) (Chiamare un'API Web downstream).

## <a name="next-steps"></a>Passaggi successivi

Per configurare e testare Gemelli digitali di Azure usando il flusso di concessione implicita OAuth 2.0, leggere [Configurare Postman](./how-to-configure-postman.md).

Per informazioni sulla sicurezza di Gemelli digitali di Azure, vedere [Creare e gestire assegnazioni di ruolo](./security-create-manage-role-assignments.md).