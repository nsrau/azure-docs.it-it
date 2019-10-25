---
title: Argomenti della guida del portale di registrazione delle app
titleSuffix: Microsoft identity platform
description: Descrizione delle varie funzionalità disponibili nel portale di registrazione delle app di Microsoft.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: f0507c28-9464-4d3e-bd53-de9053fd5278
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: ryanwi
ms.reviewer: lenalepa
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 61787e7a86a1c40ec04fb7a3d605158c85782d94
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803400"
---
# <a name="app-registration-reference"></a>Riferimento alla registrazione delle app
Questo documento fornisce il contesto e le descrizioni delle varie funzionalità disponibili nell' [registrazioni app](https://aka.ms/appregistrations) esperienza nel portale di Azure.

## <a name="my-applications-or-converged-applications"></a>Applicazioni personali o con convergenza
Questo elenco contiene tutte le applicazioni registrate per l'uso con l'endpoint della piattaforma Microsoft Identity Platform (v 2.0). Queste applicazioni permettono di eseguire l'accesso sia con account Microsoft personali che aziendali o dell'istituto d'istruzione da Azure Active Directory. Per ulteriori informazioni sull'endpoint della piattaforma di identità, vedere la [panoramica sulla versione 2.0](active-directory-appmodel-v2-overview.md). Queste applicazioni possono essere usate anche per l'integrazione con l'endpoint di autenticazione dell'account Microsoft, `https://login.live.com`.

## <a name="azure-ad-only-applications"></a>Solo applicazioni Azure AD
Questo elenco include tutte le applicazioni registrate per essere usate con l'endpoint Azure AD v1.0. A tali possono accedere solo gli utenti con account aziendale o dell'istituto di istruzione di Azure Active Directory. Questo elenco include le applicazioni registrate con l'esperienza **registrazioni app** nel [portale di Azure](https://portal.azure.com).

## <a name="live-sdk-applications"></a>Applicazioni Live SDK
Questo elenco include tutte le applicazioni registrate per l'uso solo con l'account Microsoft. Non sono abilitate per l'uso con Azure Active Directory. Qui è possibile trovare tutte le applicazioni registrate in precedenza con il portale per sviluppatori di account del servizio gestito, all'indirizzo `https://account.live.com/developers/applications`. È ora possibile eseguire in [registrazioni app](https://aka.ms/appregistrations)tutte le funzioni eseguite in precedenza in `https://account.live.com/developers/applications`.

## <a name="application-secrets"></a>Segreti applicazione
I segreti applicazione sono credenziali che consentono all'applicazione di eseguire [l'autenticazione client](https://tools.ietf.org/html/rfc6749#section-2.3) in modo affidabile con Azure AD. In OAuth e OpenID Connect il segreto dell'applicazione è noto come `client_secret`. Nel protocollo della versione 2.0, qualsiasi applicazione che riceve un token di sicurezza in una posizione con indirizzo Web, usando uno schema `https` , deve fare uso di un segreto applicazione per identificarsi in Azure AD al momento del riscatto del token di sicurezza. Inoltre i client nativi che ricevono i token in un dispositivo non possono usare un segreto dell'applicazione per eseguire l'autenticazione client. Questo scoraggia l'archiviazione dei segreti in ambienti non protetti.

Ogni app può contenere due segreti applicazione validi in qualsiasi momento. Mantenendo due segreti è possibile eseguire periodicamente un rollover della chiave nell'intero ambiente dell'applicazione. Dopo aver eseguito la migrazione dell'intera applicazione in un nuovo segreto, è possibile eliminare quello precedente ed eseguire il provisioning di un nuovo segreto.

Al momento nel portale di registrazione delle app sono consentiti solo due tipi di segreti applicazione. Scegliendo **Genera nuova password** viene generato e archiviato nell'archivio dati corrispondente un segreto condiviso, che è possibile usare nell'applicazione. Scegliendo **Genera nuova coppia di chiavi** viene creata una nuova coppia di chiavi pubblica/privata che può essere scaricata e usata per l'autenticazione client in Azure AD. Scegliendo **Carica la chiave pubblica** è possibile usare la propria coppia di chiavi pubblica e privata.
È necessario caricare un certificato che contiene una chiave pubblica.

## <a name="profile"></a>Profilo
È possibile usare la sezione relativa al profilo del portale di registrazione delle app per personalizzare la pagina di accesso dell'applicazione. Attualmente si può modificare il logo dell'applicazione della pagina di accesso, l'URL delle condizioni del servizio e quello dell'informativa sulla privacy. Il logo deve essere un'immagine trasparente da 48 x 48 o 50 x 50 pixel in un file GIF, PNG o JPEG di dimensioni non superiori a 15 KB. Provare a modificare i valori e a visualizzare la pagina di accesso risultante!

## <a name="live-sdk-support"></a>Supporto Live SDK
Quando si abilita il supporto Live SDK, per qualsiasi segreto applicazione creato viene eseguito il provisioning sia in Azure AD che negli archivi dati dell'account Microsoft. In questo modo l'applicazione può integrarsi direttamente con il servizio account Microsoft (login.live.com). Se si vuole creare un'app usando direttamente un account Microsoft (invece di usare l'endpoint 2.0), è necessario assicurarsi che il supporto Live SDK sia abilitato.

Disabilitando il supporto Live SDK, il segreto applicazione viene scritto unicamente nell'archivio dati di Azure AD. L'archivio dati di Azure AD incorpora normative di livello aziendale che permettono di soddisfare determinati standard, ad esempio la conformità FISMA. Se si abilita il supporto Live SDK, l'applicazione potrebbe non essere conforme in base ad alcuni di questi standard.

Se si prevede di usare solo l'endpoint v 2.0, è possibile disabilitare il supporto Live SDK.

