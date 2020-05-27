---
title: Provider di identità per Identità esterne - Azure AD
description: La Collaborazione B2B di Azure Active Directory supporta l'autenticazione a più fattori (MFA) per l'accesso selettivo alle applicazioni aziendali
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 395473e70137ead2b7185d54d165f82e9b3f1f04
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83595710"
---
# <a name="identity-providers-for-external-identities"></a>Provider di identità per Identità esterne

Un *provider di identità* crea, mantiene e gestisce le informazioni sulle identità fornendo al contempo servizi di autenticazione alle applicazioni. Quando si condividono app e risorse con utenti esterni, Azure AD è il provider di identità predefinito per la condivisione. Ciò significa che quando si invitano utenti esterni che hanno già un account Azure AD o Microsoft, questi possono accedere automaticamente senza ulteriori operazioni di configurazione da parte dell'utente.

È tuttavia possibile consentire agli utenti di accedere con diversi provider di identità. È ad esempio possibile configurare la federazione con provider di identità basati su social network supportati da Azure AD, come Google e Facebook. È anche possibile eseguire la federazione con qualsiasi provider di identità esterno che supporti i protocolli SAML o WS-Fed. Con la federazione con provider di identità esterni è possibile offrire agli utenti esterni la possibilità di accedere alle app con i loro account social o aziendali esistenti.

## <a name="how-it-works"></a>Funzionamento

Azure AD per identità esterne è preconfigurato per la federazione con Google e Facebook. Per configurare questi provider di identità nel tenant di Azure AD, si crea un'applicazione per ogni provider di identità e si configurano le credenziali. Si otterrà un ID client o un ID app e un segreto client o un segreto app, che sarà quindi possibile aggiungere al tenant di Azure AD.

Dopo l'aggiunta di un provider di identità al tenant di Azure AD:

- Quando si invita un utente esterno in app o risorse all'interno dell'organizzazione, l'utente esterno può accedere usando il proprio account con tale provider di identità.
- Quando si abilita l'[iscrizione self-service](self-service-sign-up-overview.md) per le app, gli utenti esterni possono iscriversi alle app usando il proprio account con i provider di identità aggiunti. 

Quando riscatta l'invito o si iscrive all'app, l'utente esterno ha la possibilità di accedere ed eseguire l'autenticazione con il provider di identità basato su social network:

![Screenshot che illustra la schermata di accesso con le opzioni Google e Facebook](media/identity-providers/sign-in-with-social-identity.png)

Per un'esperienza di accesso ottimale, creare la federazione con provider di identità ogni volta che sia possibile, in modo da offrire agli utenti guest invitati un'esperienza di accesso trasparente al momento dell'accesso alle app.  

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come aggiungere provider di identità per l'accesso alle applicazioni, fare riferimento agli articoli seguenti:

- [Aggiungere Google](google-federation.md) all'elenco di provider di identità basati su social network
- [Aggiungere Facebook](facebook-federation.md) all'elenco di provider di identità basati su social network
- [Configurare la federazione diretta](direct-federation.md) con qualsiasi organizzazione il cui provider di identità supporti il protocollo SAML 2.0 o WS-Fed. Si noti che la federazione diretta non è un'opzione per flussi utente di iscrizione self-service.
