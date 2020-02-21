---
title: Azure Active Directory l'accesso senza password (anteprima)
description: Informazioni sulle opzioni per l'accesso senza password per Azure Active Directory usando le chiavi di sicurezza FIDO2 o l'app Microsoft Authenticator
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba579d6da8c759a4653b729f1a471efdedc2baa7
ms.sourcegitcommit: 934776a860e4944f1a0e5e24763bfe3855bc6b60
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77505762"
---
# <a name="passwordless-authentication-options-for-azure-active-directory"></a>Opzioni di autenticazione con password per Azure Active Directory

Multi-factor authentication è un ottimo modo per proteggere l'organizzazione, ma gli utenti spesso si sentono frustrati con il livello di sicurezza aggiuntivo oltre a dover ricordare le password. I metodi di autenticazione senza password sono più convenienti perché la password viene rimossa e sostituita da un elemento, più un elemento o un elemento che si conosce.

|   | Un elemento | Un elemento o una conoscenza |
| --- | --- | --- |
| Accesso senza password | Dispositivo Windows 10, telefono o chiave di sicurezza | Biometrico o PIN |

Ogni organizzazione ha esigenze diverse per quanto riguarda l'autenticazione. Microsoft offre le seguenti tre opzioni di autenticazione con password:

- Windows Hello for Business
- App Microsoft Authenticator
- Chiavi di sicurezza FIDO2

![Autenticazione: sicurezza e convenienza](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

## <a name="windows-hello-for-business"></a>Windows Hello for Business

Windows Hello for business è ideale per gli Information Worker che dispongono di un proprio PC Windows designato. La biometria e il PIN sono direttamente collegati al PC dell'utente, che impedisce l'accesso da parte di utenti diversi dal proprietario. Con l'integrazione dell'infrastruttura a chiave pubblica (PKI) e il supporto integrato per Single Sign-On (SSO), Windows Hello for business offre un metodo pratico per accedere facilmente alle risorse aziendali in locale e nel cloud.

La [Guida alla pianificazione](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide) di Windows Hello for business può essere usata per prendere decisioni sul tipo di distribuzione di Windows Hello for business e sulle opzioni che è necessario prendere in considerazione.

## <a name="microsoft-authenticator-app"></a>App Microsoft Authenticator

Consentire al telefono del dipendente di diventare un metodo di autenticazione con password. È possibile che si stia già usando l'app Microsoft Authenticator come comoda opzione di autenticazione a più fattori, oltre a una password. È anche possibile usare l'app Authenticator come opzione con password.

![Accedere a Microsoft Edge con l'app Microsoft Authenticator](./media/concept-authentication-passwordless/concept-web-sign-in-microsoft-authenticator-app.png)

L'app Authenticator converte qualsiasi telefono iOS o Android in una credenziale complessa senza password. Gli utenti possono accedere a qualsiasi piattaforma o browser inviando una notifica al telefono, associando un numero visualizzato sullo schermo a quello sul telefono e quindi usando la relativa biometrica (tocco o viso) o PIN per confermare.

## <a name="fido2-security-keys"></a>Chiavi di sicurezza FIDO2

Le chiavi di sicurezza di FIDO2 sono un metodo di autenticazione senza password basato su standard unphishable che può provenire da qualsiasi fattore di forma. Fast Identity online (FIDO) è uno standard aperto per l'autenticazione con password. FIDO consente a utenti e organizzazioni di sfruttare lo standard per accedere alle risorse senza nome utente o password usando una chiave di sicurezza esterna o una chiave della piattaforma incorporata in un dispositivo.

Per la versione di anteprima pubblica, i dipendenti possono usare le chiavi di sicurezza per accedere ai dispositivi Azure AD o ibridi Azure AD aggiunti a dispositivi Windows 10 e ottenere l'accesso Single Sign-on alle risorse cloud e locali. Gli utenti possono anche accedere ai browser supportati. Le chiavi di sicurezza di FIDO2 sono un'ottima opzione per le aziende che hanno una sicurezza molto sensibile o hanno scenari o dipendenti che non sono disposti o in grado di usare il telefono come secondo fattore.

![Accedere a Microsoft Edge con una chiave di sicurezza](./media/concept-authentication-passwordless/concept-web-sign-in-security-key.png)

Sebbene esistano molte chiavi FIDO2 certificate dall'alleanza di FIDO, Microsoft richiede l'implementazione da parte del fornitore di alcune estensioni facoltative della specifica del protocollo client-to-Authenticator (CTAP) di FIDO2 per garantire la massima sicurezza e la migliore esperienza.

Una chiave di sicurezza **deve** implementare le seguenti funzionalità ed estensioni dal protocollo CTAP di FIDO2 per essere compatibile con Microsoft:

| # | Funzionalità/attendibilità dell'estensione | Perché questa funzionalità o estensione è necessaria? |
| --- | --- | --- |
| 1 | Chiave residente | Questa funzionalità consente la portabilità della chiave di sicurezza, in cui le credenziali sono archiviate nella chiave di sicurezza. |
| 2 | PIN client | Questa funzionalità consente di proteggere le credenziali con un secondo fattore e si applica alle chiavi di sicurezza che non dispongono di un'interfaccia utente. |
| 3 | HMAC-segreto | Questa estensione garantisce che sia possibile accedere al dispositivo quando è offline o in modalità aereo. |
| 4 | Più account per RP | Questa funzionalità garantisce la possibilità di usare la stessa chiave di sicurezza tra più servizi, ad esempio l'account Microsoft e Azure Active Directory. |

I provider seguenti offrono chiavi di sicurezza FIDO2 di diversi fattori di forma che sono noti come compatibili con l'esperienza senza password. Si consiglia di valutare le proprietà di sicurezza di queste chiavi contattando il fornitore e l'Alleanza FIDO.

| Provider | Contatto |
| --- | --- |
| Yubico | [https://www.yubico.com/support/contact/](https://www.yubico.com/support/contact/) |
| Feitian | [https://www.ftsafe.com/about/Contact_Us](https://www.ftsafe.com/about/Contact_Us) |
| HID | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| Ensurity | [https://www.ensurity.com/contact](https://www.ensurity.com/contact) |
| eWBM | [https://www.ewbm.com/support](https://www.ewbm.com/support) |
| AuthenTrend | [https://authentrend.com/about-us/#pg-35-3](https://authentrend.com/about-us/#pg-35-3) |

> [!NOTE]
> Se si acquista e si prevede di usare le chiavi di sicurezza basate su NFC, è necessario un lettore NFC supportato per la chiave di sicurezza. Il lettore NFC non è un requisito o una limitazione di Azure. Rivolgersi al fornitore della chiave di sicurezza basata su NFC per un elenco dei lettori NFC supportati.

Se si è un fornitore e si desidera ottenere il dispositivo in questo elenco di dispositivi supportati, contattare [Fido2Request@Microsoft.com](mailto:Fido2Request@Microsoft.com).

## <a name="what-scenarios-work-with-the-preview"></a>Quali scenari funzionano con l'anteprima?

- Gli amministratori possono abilitare i metodi di autenticazione con password per il tenant
- Gli amministratori possono fare riferimento a tutti gli utenti o selezionare utenti/gruppi nel tenant per ogni metodo
- Gli utenti finali possono registrare e gestire questi metodi di autenticazione con password nel portale per gli account
- Gli utenti finali possono accedere con questi metodi di autenticazione senza password
   - App Microsoft Authenticator: funziona in scenari in cui viene usata l'autenticazione Azure AD, incluso in tutti i browser, durante l'installazione di Windows 10 (configurazione guidata) e con app per dispositivi mobili integrate in qualsiasi sistema operativo.
   - Chiavi di sicurezza: usare la schermata di blocco per Windows 10 e il Web in browser supportati come Microsoft Edge.

## <a name="next-steps"></a>Passaggi successivi

[Abilitare le opzioni di passwordlesss della chiave di sicurezza FIDO2 nell'organizzazione](howto-authentication-passwordless-security-key.md)

[Abilita le opzioni per le password basate su telefono nell'organizzazione](howto-authentication-passwordless-phone.md)

### <a name="external-links"></a>Collegamenti esterni

[FIDO Alliance](https://fidoalliance.org/)

[Specifica FIDO2 CTAP](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)
