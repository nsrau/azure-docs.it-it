---
title: Accesso senza password ad Azure Active Directory (anteprima)
description: Accesso senza password con Azure AD usando le chiavi di sicurezza FIDO2 o l'app Microsoft Authenticator (anteprima)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d80b359be0a6249327ba1ba1d51ffbc330bb073
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712085"
---
# <a name="what-is-passwordless"></a>Che cos'è senza password?

Multi-factor authentication (MFA) è un ottimo modo per proteggere l'organizzazione, ma gli utenti ottengono frustrati con il livello aggiuntive nella parte superiore di dover ricordare le password. Metodi di autenticazione senza password sono più comodi perché la password viene rimossa e sostituita con, bisogna oltre a essere qualcosa o un elemento che noto.

|   | È necessario | Un elemento che si sta o conoscere |
| --- | --- | --- |
| Accesso senza password | Chiave di sicurezza o telefono | Biometrico o PIN |

È possibile riconoscere che ogni organizzazione ha esigenze diverse per l'autenticazione. Microsoft offre attualmente Windows Hello, la nostra esperienza senza password premier per i PC Windows. Sono state aggiunte le nuove credenziali per la famiglia senza password: Chiavi di sicurezza FIDO2 e app Microsoft Authenticator.

## <a name="microsoft-authenticator-app"></a>App Microsoft Authenticator

Consente di telefono dei dipendenti diventare un metodo di autenticazione senza password. Si potrebbe già essere usando l'App Microsoft Authenticator come opzione di autenticazione a più fattori pratico oltre a una password. Ma a questo punto, è disponibile come opzione senza password.

Si trasforma qualsiasi iOS o Android phone in una credenziale senza password, sicura, consentendo agli utenti di accedere a qualsiasi piattaforma o browser come ottenere una notifica al telefono, corrispondenza di un numero visualizzato nella schermata a quella sul telefono e quindi usare loro (biometrica Touch o in una situazione) o un PIN per confermare.

## <a name="fido2-security-keys"></a>Chiavi di sicurezza FIDO2

Le chiavi di sicurezza FIDO2 sono un metodo di autenticazione senza password basati su standard unphishable che può verificarsi in qualsiasi fattore di forma. Fast Identity Online (FIDO) è uno standard aperto per l'autenticazione senza password. Consente agli utenti e organizzazioni di sfruttare lo standard accedere alle risorse senza un nome utente o password usando una chiave di protezione esterne o una chiave di piattaforma incorporato in un dispositivo.

Per l'anteprima pubblica, i dipendenti possono usare le chiavi di sicurezza esterni per accedere alle macchine di Azure Active Directory Windows 10 aggiunti ad (in esecuzione versione 1809 o versione successiva) e ottenere single sign-on le risorse cloud. È possibile anche accedere al browser supportati.

Sebbene vi siano molte le chiavi FIDO2 certificata da FIDO Alliance, Microsoft richiede alcune estensioni facoltative della specifica FIDO2 CTAP per essere implementata dal fornitore per garantire la massima sicurezza e un'esperienza ottimale.

Una chiave di sicurezza **necessario** implementare le funzionalità e le estensioni del protocollo FIDO2 CTAP sia compatibile con Microsoft seguenti:

| # | Funzionalità / estensione attendibile | Il motivo per cui è richiesta? |
| --- | --- | --- |
| 1 | Chiave residente | Questa funzionalità consente la chiave di sicurezza per garantire la portabilità, in cui sono memorizzate le credenziali per la chiave di sicurezza. |
| 2 | Aggiungi client | Questa funzionalità consente di proteggere le credenziali con un secondo fattore e si applica alle chiavi di sicurezza che non è un'interfaccia utente. |
| 3 | HMAC-secret | Questa estensione assicura che è possibile accedere al dispositivo quando è offline o in modalità aereo. |
| 4 | Più account per ogni applicazione relying Party | In questo modo che è possibile usare la stessa chiave di sicurezza tra più servizi, ad esempio Account Microsoft e Azure Active Directory. |

I provider seguenti offrono FIDO2 le chiavi di sicurezza di diversi fattori di forma che sono noti per essere compatibile con l'esperienza paswordless. Microsoft invita i clienti a valutare le proprietà di sicurezza di queste chiavi, contattare il fornitore, nonché FIDO Alliance.

| Provider | Contatto |
| --- | --- |
| Yubico | [https://www.yubico.com/support/contact/](https://www.yubico.com/support/contact/) |
| Feitian | [https://www.ftsafe.com/about/Contact_Us](https://www.ftsafe.com/about/Contact_Us) |
| HID | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| Ensurity | [https://ensurity.com/contact-us.html](https://ensurity.com/contact-us.html) |
| eWBM | [https://www.ewbm.com/page/sub1_5](https://www.ewbm.com/page/sub1_5) |

Se sei un fornitore e si desidera ottenere il dispositivo in questo elenco, rivolgersi [ Fido2Request@Microsoft.com ](mailto:Fido2Request@Microsoft.com).

Le chiavi di sicurezza FIDO2 sono un'ottima opzione per le aziende che sono molto sensibili alla sicurezza o avere scenari o i dipendenti che non sono disposti o in grado di usare il proprio telefono come secondo fattore.

## <a name="what-scenarios-work-with-the-preview"></a>Quali scenari sono compatibili con l'anteprima?

1. Gli amministratori possono abilitare metodi di autenticazione senza password per il tenant
1. Gli amministratori possono tutti gli utenti di destinazione o selezionare utenti o gruppi nel tenant per ogni metodo
1. Gli utenti finali possono registrare e gestire questi metodi di autenticazione senza password nel portale di account
1. Gli utenti finali possono accedere con questi metodi di autenticazione senza password
   1. App Microsoft Authenticator: Verranno work in tutti gli scenari in cui viene utilizzata l'autenticazione di Azure AD, inclusi i browser, nelle finestre di 10 orizzontale della configurazione guidata (OOBE) e con integrati App per dispositivi mobili su qualsiasi sistema operativo.
   1. Chiavi di sicurezza: Funzionerà nella schermata di blocco per Windows 10 versione 1809 o versione successiva e il web browser supportati, ad esempio Microsoft Edge.

## <a name="next-steps"></a>Passaggi successivi

[Abilitare le opzioni senza password nell'organizzazione](howto-authentication-passwordless-enable.md)

### <a name="external-links"></a>Collegamenti esterni

[FIDO Alliance](https://fidoalliance.org/)

[Specifica FIDO2 CTAP](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)
