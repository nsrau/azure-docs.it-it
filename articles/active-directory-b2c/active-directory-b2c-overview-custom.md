---
title: Criteri personalizzati di Azure Active Directory B2C | Microsoft Docs
description: Informazioni sui criteri personalizzati di Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: be6d54886f23b0fa219b1e4b8948b4a4c51f5864
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68716832"
---
# <a name="custom-policies-in-azure-active-directory-b2c"></a>Criteri personalizzati in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

I criteri personalizzati sono file di configurazione che definiscono il comportamento del tenant di Azure Active Directory (Azure AD) B2C. I flussi utente sono predefiniti nel portale di Azure AD B2C per le attività più comuni di identità. I criteri personalizzati possono essere modificati completamente da uno sviluppatore di identità per completare molte attività diverse.

## <a name="comparing-user-flows-and-custom-policies"></a>Confronto tra i criteri personalizzati e i flussi utente

| | Flussi degli utenti | Criteri personalizzati |
|-|-------------------|-----------------|
| Utenti di destinazione | Tutti gli sviluppatori di applicazioni con o senza competenze di identità. | Professionisti di identità, integratori di sistemi, consulenti e team interni per la gestione di identità. Sono confortevoli con i flussi di OpenID Connect e comprendono i provider di identità e l'autenticazione basata sulle attestazioni. |
| Metodo di configurazione | Portale di Azure con un'interfaccia utente (UI) intuitiva. | Modifica diretta dei file XML e caricamento nel portale di Azure. |
| Personalizzazione dell'interfaccia utente | Personalizzazione completa dell'interfaccia utente, inclusi HTML, CSS e JavaScript.<br><br>Supporto multilingue con stringhe personalizzate. | Uguale |
| Personalizzazione degli attributi | Attributi standard e personalizzati. | Uguale |
| Gestione delle sessioni e dei token | Token personalizzato e opzioni di sessione multiple. | Uguale |
| Provider di identità | Provider locale o sociale predefinito e la maggior parte dei provider di identità OIDC, ad esempio la Federazione con Azure Active Directory tenant. | OIDC, SAML e OAuth basati su standard.  L'autenticazione è inoltre possibile tramite l'integrazione con le API REST. |
| Attività relative all'identità | Iscrizione o accesso ad account social o locali multipli.<br><br>Reimpostazione della password self-service.<br><br>Modifica del profilo.<br><br>Autenticazione a più fattori.<br><br>Personalizzare token e sessioni.<br><br>Flussi di accesso ai token. | Completare le stesse attività dei flussi utente usando provider di identità personalizzati ambiti personalizzati.<br><br>Effettuare il provisioning di un account utente in un altro sistema al momento della registrazione.<br><br>Inviare un messaggio di posta elettronica di benvenuto con il proprio provider di servizi di posta elettronica.<br><br>Usare un archivio utente esterno Azure AD B2C.<br><br>Convalidare le informazioni date dall'utente con un sistema attendibile tramite un'API. |

## <a name="policy-files"></a>File dei criteri

Vengono usati questi tre tipi di file dei criteri:

- **File di base** - contiene la maggior parte delle definizioni. Si consiglia di apportare un numero minimo di modifiche a questo file che contribuisce alla risoluzione dei problemi e alla manutenzione a lungo termine dei criteri.
- **File di estensioni** - contiene le modifiche di configurazione univoche per il tenant.
- **File Relying Party (RP)** - L'unico file incentrato sulle attività che viene chiamato direttamente dell'applicazione o dal servizio (noto anche come Relying Party). Ogni attività univoca richiede un file Relying Party associato e, a seconda dei requisiti di personalizzazione, il numero potrebbe essere il "totale delle applicazioni per il numero totale di casi di uso".

I flussi utente in Azure AD B2C seguono il modello a 3 file descritto sopra, ma lo sviluppatore visualizza solo il file RP, mentre il portale di Azure apporta modifiche in background al file delle estensioni.

## <a name="custom-policy-core-concepts"></a>Concetti principali dei criteri personalizzati

L'identità del cliente e il servizio di gestione degli accessi (CIAM) in Azure comprende:

- Una directory utente sotto che è accessibile tramite Microsoft Graph e che contiene i dati utente per gli account locali e gli account federati.
- Accesso al **Framework dell'esperienza di gestione delle identità** che orchestra la relazione di trust tra gli utenti e le entità e passa le attestazioni tra questi per completare un'attività di gestione di identità o accessi.
- Un servizio token di sicurezza (STS) che emette gli ID token, i token di aggiornamento e i token di accesso (e le asserzioni SAML equivalenti), e li convalida in modo da proteggere le risorse.

Azure AD B2C interagisce in sequenza con i provider di identità, gli utenti, altri sistemi e la directory dell'utente locale per eseguire un'attività di identità. Ad esempio accesso di un utente, registrazione di un nuovo utente o reimpostazione di una password. Il Framework dell'esperienza di gestione delle identità e un criterio (chiamato anche percorso utente o criterio del framework attendibilità) stabilisce attendibilità tra più parti e definisce in modo esplicito gli attori, le azioni, i protocolli e la sequenza di passaggi da completare.

Il Framework dell'esperienza di identità è una piattaforma Azure completamente configurabile, basata su criteri e basata sul cloud che orchestra la relazione di trust tra le entità nei formati di protocollo standard, ad esempio OpenID Connect, OAuth, SAML, WSFed e alcune non standard, ad esempio REST Scambi di attestazioni da sistema a sistema basato su API. Il framework crea esperienze intuitive e generiche che supportano HTML e CSS.

Un criterio personalizzato è rappresentato come uno o più file in formato XML che fanno riferimento l'uno all'altro in una catena gerarchica. Gli elementi XML definiscono, tra gli altri elementi, lo schema delle attestazioni, le trasformazioni delle attestazioni, le definizioni di contenuto, i provider di attestazioni, i profili tecnici e le procedure di orchestrazione per i percorsi utente. Un criterio personalizzato è accessibile nella forma di uno o più file XML che vengono eseguiti dal framework dell'esperienza di gestione delle identità quando vengono chiamati da una Relying Party. Gli sviluppatori che configurano criteri personalizzati devono definire dettagliatamente le relazioni attendibili in modo da includere gli endpoint dei metadati e le definizioni di scambio per le attestazioni esatte e configurare i segreti, le chiavi e i certificati necessari per ogni provider di identità.

### <a name="inheritance-model"></a>Modello di ereditarietà

Quando un'applicazione chiama il file dei criteri di relying party, il Framework di esperienza di identità in Azure AD B2C aggiunge tutti gli elementi dal file di base, dal file delle estensioni e quindi dal relying party per assemblare i criteri correnti in vigore.  Gli elementi dello stesso tipo e nome nel file relying party eseguiranno l'override nelle estensioni e le estensioni eseguono l'override nel file di base.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Introduzione ai criteri personalizzati](active-directory-b2c-get-started-custom.md)
