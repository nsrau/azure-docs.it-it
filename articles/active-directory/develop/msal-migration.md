---
title: Eseguire la migrazione a Microsoft Authentication Library (MSAL)
titleSuffix: Microsoft identity platform
description: Informazioni sulle differenze tra Microsoft Authentication Library (MSAL) e Autenticazione di Azure AD Library (ADAL) e su come eseguire la migrazione a MSAL.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/07/2020
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: ac9264ea8d6cc71d19d2c9bbd23b2123bdf1f924
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2020
ms.locfileid: "88224357"
---
# <a name="migrate-applications-to-microsoft-authentication-library-msal"></a>Eseguire la migrazione di applicazioni a Microsoft Authentication Library (MSAL)

Molti sviluppatori hanno compilato e distribuito applicazioni usando la libreria di autenticazione Azure Active Directory (ADAL). È ora consigliabile usare Microsoft Authentication Library (MSAL) per l'autenticazione e l'autorizzazione delle entità Azure AD.

Utilizzando MSAL anziché ADAL:

- È possibile autenticare un set più ampio di identità:
  - Identità Azure AD
  - Account Microsoft
  - Account social e locali tramite Azure AD B2C
- Gli utenti otterranno la migliore esperienza Single Sign-on.
- L'applicazione può abilitare il consenso incrementale.
- Il supporto dell'accesso condizionale è più semplice.
- È possibile trarre vantaggio dall'innovazione. Poiché tutte le attività di sviluppo Microsoft sono ora incentrate su MSAL, non verranno implementate nuove funzionalità in ADAL.

**MSAL è ora la libreria di autenticazione consigliata per l'uso con la piattaforma di identità Microsoft**.

## <a name="migration-guidance"></a>Indicazioni sulla migrazione

Gli articoli seguenti possono essere utili per eseguire la migrazione a MSAL:

- [Eseguire la migrazione a MSAL.Android](migrate-android-adal-msal.md)
- [Eseguire la migrazione a MSAL.iOS/macOS](migrate-objc-adal-msal.md)
- [Eseguire la migrazione a MSAL Java](migrate-adal-msal-java.md)
- [Eseguire la migrazione a MSAL.js](msal-compare-msal-js-and-adal-js.md)
- [Eseguire la migrazione a MSAL.NET](msal-net-migration.md)
- [Eseguire la migrazione a MSAL Python](migrate-python-adal-msal.md)
- [Eseguire la migrazione di app Xamarin tramite broker a MSAL.NET](msal-net-migration-ios-broker.md)

## <a name="frequently-asked-questions-faq"></a>Domande frequenti

__D: ADAL è deprecato?__  
A: Sì. A partire dal 30 giugno 2020, non si aggiungono più nuove funzionalità a ADAL. Si continuerà ad aggiungere correzioni critiche per la sicurezza a ADAL fino al 30 giugno 2022. Dopo questa data, le app che usano ADAL continueranno a funzionare, ma è consigliabile eseguire l'aggiornamento a MSAL per sfruttare i vantaggi delle funzionalità più recenti e per mantenere la sicurezza.

__D: le app ADAL esistenti smetteranno di funzionare?__  
R: No. Le app esistenti continueranno a funzionare senza alcuna modifica. Se si prevede di mantenerli oltre il 30 giugno 2022, è consigliabile aggiornare le app a MSAL per garantirne la sicurezza, ma non è necessario eseguire la migrazione a MSAL per mantenere la funzionalità esistente.

__D: Ricerca per categorie informazioni sulle app che usano ADAL?__  
R: se si dispone del codice sorgente per l'applicazione, è possibile fare riferimento alle guide alla migrazione sopra indicate per determinare la libreria utilizzata dall'app e la relativa modalità di migrazione a MSAL. Se collabori con un ISV, ti consigliamo di contattarlo direttamente per comprendere il percorso di migrazione a MSAL.

__D: perché è opportuno investire nel passaggio a MSAL?__  
R: MSAL contiene nuove funzionalità non incluse in ADAL, tra cui il consenso incrementale, la Single Sign-On e la gestione della cache dei token. Inoltre, a differenza di ADAL, MSAL continuerà a ricevere patch di sicurezza oltre il 30 giugno 2022. [Altre informazioni](msal-overview.md).

__D: Microsoft aggiornerà le proprie app a MSAL?__  
Sì. Microsoft sta eseguendo la migrazione delle proprie applicazioni a MSAL entro la scadenza del supporto tecnico, assicurando che possano trarre vantaggio dai miglioramenti apportati alla sicurezza e alle funzionalità di MSAL.

__D: si rilascia uno strumento che consente di spostare le app da ADAL a MSAL?__  
R: No. Per le differenze tra le librerie è necessario dedicare risorse allo sviluppo e alla manutenzione dello strumento che altrimenti andrebbe a migliorare MSAL. Tuttavia, viene fornito il set di guide alla migrazione precedente per apportare le modifiche necessarie nell'applicazione.

__D: come funziona MSAL con AD FS?__  
R: MSAL.NET supporta alcuni scenari per l'autenticazione con AD FS 2019. Se l'app deve acquisire i token direttamente dalla versione precedente di AD FS, è necessario rimanere in ADAL. [Altre informazioni](msal-net-adfs-support.md).

__D: Ricerca per categorie ottenere assistenza per la migrazione dell'applicazione?__  
R: vedere la sezione delle [linee guida](#migration-guidance) per la migrazione di questo articolo. Se, dopo aver letto la guida per la piattaforma dell'app, si hanno domande aggiuntive, è possibile pubblicare un post su Stack Overflow con il tag `[adal-deprecation]` o aprire un problema nel repository GitHub della libreria. Vedere la sezione [linguaggi e Framework](msal-overview.md#languages-and-frameworks) dell'articolo Panoramica di MSAL per i collegamenti al repository di ogni libreria.

## <a name="next-steps"></a>Passaggi successivi

- [Aggiornare le applicazioni per usare Microsoft Authentication Library e Microsoft Graph API](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363)
- [Panoramica della piattaforma di identità Microsoft](v2-overview.md)
- [Esaminare gli esempi di codice MSAL](sample-v2-code.md)
