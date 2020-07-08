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
ms.openlocfilehash: 2284d015b451872753dd0855cac42e6f1926545c
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83712163"
---
# <a name="identity-providers-for-external-identities"></a>Provider di identità per Identità esterne

Un *provider di identità* crea, mantiene e gestisce le informazioni sulle identità fornendo al contempo servizi di autenticazione alle applicazioni. Quando si condividono app e risorse con utenti esterni, Azure AD è il provider di identità predefinito per la condivisione. Ciò significa che quando si invitano utenti esterni che hanno già un account Azure AD o Microsoft, questi possono accedere automaticamente senza ulteriori operazioni di configurazione da parte dell'utente.

È tuttavia possibile consentire agli utenti di accedere con diversi provider di identità.

- **Google**: Federazione Google consente agli utenti esterni di riscattare gli inviti dall'utente accedendo alle app con gli account Gmail personali. Federazione Google può essere usato anche nei flussi utente di iscrizione self-service.
   > [!NOTE]
   > Nell'anteprima corrente di iscrizione self-service, se un flusso utente è associato a un'app e si invia a un utente un invito a tale app, l'utente non sarà in grado di usare un account Gmail per riscattare l'invito. Come soluzione alternativa, l'utente può eseguire il processo di iscrizione self-service. In alternativa, è possibile riscattare l'invito accedendo a un'altra app o usando il portale App personali in https://myapps.microsoft.com.

- **Facebook**: Quando si crea un'app, è possibile configurare l'iscrizione self-service e abilitare la Federazione con Facebook in modo che gli utenti possano iscriversi all'app usando i propri account Facebook. Facebook può essere usato solo per i flussi utente di iscrizione self-service e non è disponibile come opzione di accesso quando gli utenti riscattano gli inviti.

- **Federazione diretta**: È anche possibile impostare la federazione diretta con qualsiasi provider di identità esterno che supporti i protocolli SAML o WS-Fed. Federazione diretta consente agli utenti esterni di riscattare gli inviti dall'utente accedendo alle app con gli account social o aziendali esistenti. 
   > [!NOTE]
   > I provider di identità di federazione diretta non possono essere usati nei flussi utente di iscrizione self-service.


## <a name="how-it-works"></a>Funzionamento

La funzionalità di iscrizione self-service di Azure AD per identità esterne consente agli utenti di iscriversi con l'account Azure AD, Google o Facebook. Per configurare i provider di identità sociale nel tenant di Azure AD, si crea un'applicazione per ogni provider di identità e si configurano le credenziali. Si otterrà un ID client o un ID app e un segreto client o un segreto app, che sarà quindi possibile aggiungere al tenant di Azure AD.

Dopo l'aggiunta di un provider di identità al tenant di Azure AD:

- Quando si invita un utente esterno in app o risorse all'interno dell'organizzazione, l'utente esterno può accedere usando il proprio account con tale provider di identità.
- Quando si abilita l'[iscrizione self-service](self-service-sign-up-overview.md) per le app, gli utenti esterni possono iscriversi alle app usando il proprio account con i provider di identità aggiunti.

> [!NOTE]
> Azure AD è abilitato per impostazione predefinita per l'iscrizione self-service, quindi gli utenti hanno sempre la possibilità di iscriversi usando un account di Azure AD.

Quando riscatta l'invito o si iscrive all'app, l'utente esterno ha la possibilità di accedere ed eseguire l'autenticazione con il provider di identità basato su social network:

![Screenshot che illustra la schermata di accesso con le opzioni Google e Facebook](media/identity-providers/sign-in-with-social-identity.png)

Per un'esperienza di accesso ottimale, creare la federazione con provider di identità ogni volta che sia possibile, in modo da offrire agli utenti guest invitati un'esperienza di accesso trasparente al momento dell'accesso alle app.  

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come aggiungere provider di identità per l'accesso alle applicazioni, fare riferimento agli articoli seguenti:

- [Aggiungere Google](google-federation.md) all'elenco di provider di identità basati su social network
- [Aggiungere Facebook](facebook-federation.md) all'elenco di provider di identità basati su social network
- [Configurare la federazione diretta](direct-federation.md) con qualsiasi organizzazione il cui provider di identità supporti il protocollo SAML 2.0 o WS-Fed. Si noti che la federazione diretta non è un'opzione per flussi utente di iscrizione self-service.
