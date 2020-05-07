---
title: Modello applicativo | Azure
titleSuffix: Microsoft identity platform
description: Informazioni sul processo di registrazione dell'applicazione in modo che possa integrarsi con Microsoft Identity Platform (v 2.0).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/28/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 774822eae5ab327f57da3eca4499ca14d3e0c7a1
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82584311"
---
# <a name="application-model"></a>Modello di applicazione

Le applicazioni possono accedere agli utenti stessi o delegare l'accesso a un provider di identità. In questo argomento vengono illustrati i passaggi necessari per registrare un'applicazione con la piattaforma di identità Microsoft.

## <a name="registering-an-application"></a>Registrazione di un'applicazione

Affinché un provider di identità sappia che un utente ha accesso a una determinata app, sia l'utente che l'applicazione devono essere registrati con il provider di identità. Quando si registra l'applicazione con Azure AD, viene offerta una configurazione di identità per l'applicazione che consente l'integrazione con la piattaforma di identità Microsoft. La registrazione dell'app consente anche di:

* Personalizzare la personalizzazione dell'applicazione nella finestra di dialogo di accesso. Questo è importante perché si tratta della prima esperienza che un utente avrà con l'app.
* Decidere se si desidera consentire agli utenti di accedere solo se appartengono all'organizzazione. Si tratta di un'applicazione single-tenant. In alternativa, consentire agli utenti di accedere usando un account aziendale o dell'Istituto di istruzione. Si tratta di un'applicazione multi-tenant. È anche possibile consentire account Microsoft personali o un account di social networking da LinkedIn, Google e così via.
* Autorizzazioni dell'ambito della richiesta. È ad esempio possibile richiedere l'ambito "User. Read", che concede l'autorizzazione per leggere il profilo dell'utente che ha eseguito l'accesso.
* Definire gli ambiti che definiscono l'accesso all'API Web. In genere, quando un'app vuole accedere all'API, sarà necessario richiedere le autorizzazioni agli ambiti definiti.
* Condividere un segreto con la piattaforma di identità Microsoft per dimostrare l'identità dell'app.  Questo è rilevante nel caso in cui l'app sia un'applicazione client riservata. Un'applicazione client riservata è un'applicazione in grado di conservare le credenziali in modo sicuro. Per archiviare le credenziali è necessario un server back-end attendibile.

Una volta effettuata la registrazione, all'applicazione verrà assegnato un identificatore univoco che l'app condivide con la piattaforma di identità Microsoft quando richiede i token. Se l'app è un' [applicazione client riservata](developer-glossary.md#client-application), condividerà anche il segreto o la chiave pubblica, a seconda che siano stati usati i certificati o i segreti.

Microsoft Identity Platform rappresenta le applicazioni che utilizzano un modello che soddisfa due funzioni principali:

* Identificare l'app in base ai protocolli di autenticazione supportati
* Specificare tutti gli identificatori, gli URL, i segreti e le informazioni correlate necessarie per l'autenticazione

Piattaforma di identità Microsoft:

* Include tutti i dati necessari per supportare l'autenticazione in fase di esecuzione
* Include tutti i dati per la decisione delle risorse a cui un'app potrebbe dover accedere e in quali circostanze deve essere soddisfatta una determinata richiesta
* Fornisce l'infrastruttura per implementare il provisioning delle app nel tenant dello sviluppatore di app e in qualsiasi altro tenant Azure AD
* Gestisce il consenso dell'utente durante il tempo di richiesta del token e facilita il provisioning dinamico delle app tra i tenant

Il **consenso** è il processo di un proprietario di risorse che concede l'autorizzazione a un'applicazione client per accedere alle risorse protette, in autorizzazioni specifiche, per conto del proprietario della risorsa. Piattaforma di identità Microsoft:

* Consente a utenti e amministratori di concedere o negare dinamicamente il consenso all'app di accedere alle risorse per loro conto.
* Consente agli amministratori di decidere quali app sono autorizzati a eseguire e quali utenti possono usare app specifiche, nonché la modalità di accesso alle risorse di directory.

## <a name="multi-tenant-apps"></a>App multi-tenant

In Microsoft Identity Platform, un [oggetto applicazione](developer-glossary.md#application-object) descrive un'applicazione. In fase di distribuzione, Microsoft Identity Platform usa l'oggetto applicazione come progetto per creare un' [entità servizio](developer-glossary.md#service-principal-object), che rappresenta un'istanza concreta di un'applicazione all'interno di una directory o di un tenant. L'entità servizio definisce le operazioni che l'app può effettivamente eseguire in una directory di destinazione specifica, chi può usarla, a quali risorse ha accesso e così via. Microsoft Identity Platform crea un'entità servizio da un oggetto applicazione tramite il [consenso](developer-glossary.md#consent).

Il diagramma seguente illustra un flusso di provisioning di Microsoft Identity Platform semplificato basato su consenso. Mostra due tenant: *a* e *B*.

* Il *tenant A* è proprietario dell'applicazione.
* Il *tenant B* sta creando un'istanza dell'applicazione tramite un'entità servizio.

![Flusso di provisioning semplificato basato su consenso](./media/authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

In questo flusso di provisioning:

1. Un utente del tenant B prova ad accedere con l'app, l'endpoint di autorizzazione richiede un token per l'applicazione.
1. Le credenziali utente vengono acquisite e verificate per l'autenticazione.
1. All'utente viene richiesto di fornire il consenso affinché l'app ottenga l'accesso al tenant B.
1. Microsoft Identity Platform usa l'oggetto applicazione nel tenant A come progetto per la creazione di un'entità servizio nel tenant B.
1. L'utente riceve il token richiesto.

È possibile ripetere questo processo per tenant aggiuntivi. Il tenant A mantiene il progetto per l'app (oggetto applicazione). Gli utenti e gli amministratori di tutti gli altri tenant in cui l'app dispone del consenso continuano a controllare ciò che l'applicazione può eseguire tramite l'oggetto entità servizio corrispondente in ogni tenant. Per altre informazioni, vedere [Oggetti applicazione e oggetti entità servizio in Microsoft Identity Platform](app-objects-and-service-principals.md).

## <a name="next-steps"></a>Passaggi successivi

Per altri argomenti relativi alle nozioni di base sull'autenticazione e sull'autorizzazione:

* Vedere [autenticazione e autorizzazione](authentication-vs-authorization.md) per informazioni sui concetti di base di autenticazione e autorizzazione nella piattaforma di identità Microsoft.
* Vedere [token di sicurezza](security-tokens.md) per informazioni sull'uso di token di accesso, token di aggiornamento e token ID nell'autenticazione e nell'autorizzazione.
* Vedere [flusso di accesso all'app](app-sign-in-flow.md) per informazioni sul flusso di accesso di app Web, desktop e per dispositivi mobili nella piattaforma di identità Microsoft.

Per ulteriori informazioni sul modello di applicazione:

* Vedere [come e perché le applicazioni vengono aggiunte a Azure ad](active-directory-how-applications-are-added.md) per altre informazioni sugli oggetti applicazione e le entità servizio nella piattaforma di identità Microsoft.
* Per altre informazioni sulle app a tenant singolo e sulle app multi-tenant, vedere tenant [in Azure Active Directory](single-and-multi-tenant-apps.md) .
* Vedere [Azure Active Directory B2C documentazione](https://docs.microsoft.com/azure/active-directory-b2c) per altre informazioni sul modo in cui Azure ad fornisce anche Azure Active Directory B2C in modo che le organizzazioni possano accedere agli utenti, in genere clienti, usando identità social come un account Google.
