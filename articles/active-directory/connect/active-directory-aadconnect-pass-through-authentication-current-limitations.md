---
title: 'Azure AD Connect: Autenticazione pass-through - Limitazioni correnti | Microsoft Docs'
description: Questo articolo descrive le limitazioni correnti dell'autenticazione pass-through di Azure Active Directory (Azure AD).
services: active-directory
keywords: Autenticazione pass-through di Azure AD Connect, installare Active Directory, componenti necessari per Azure AD, SSO, Single Sign-On
documentationcenter: ''
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.author: billmath
ms.openlocfilehash: ac085bf972885819f7c79996b0f6638fc01fc00d
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Autenticazione pass-through di Azure Active Directory - Limitazioni correnti

>[!IMPORTANT]
>L'autenticazione pass-through di Azure Active Directory (Azure AD) è una funzionalità gratuita che può essere usata senza acquistare edizioni a pagamento. L'autenticazione pass-through è disponibile solo nell'istanza di Azure AD a livello globale, non nel [cloud Microsoft Azure Germania](http://www.microsoft.de/cloud-deutschland) o nel [cloud Microsoft Azure per enti pubblici](https://azure.microsoft.com/features/gov/).

## <a name="supported-scenarios"></a>Scenari supportati

Sono completamente supportati gli scenari seguenti:

- Accessi utente a tutte le applicazioni basate su Web browser
- Accessi utente ad applicazioni Office che supportano l'[autenticazione moderna](https://aka.ms/modernauthga), ovvero Office 2016 e Office 2013 _con_ l'autenticazione moderna
- Accessi utente ai client di Outlook usando protocolli legacy, ad esempio Exchange ActiveSync, SMTP, POP e IMAP.
- Accessi utente a versioni di Skype for Business che supportano l'autenticazione moderna, incluse le topologie Online e ibrida. Altre informazioni sulle topologie supportate sono disponibili [qui](https://technet.microsoft.com/library/mt803262.aspx).
- Aggiunte al dominio Azure AD per dispositivi Windows 10
- Password di app per Multi-Factor Authentication.

## <a name="unsupported-scenarios"></a>Scenari non supportati

_Non_ sono supportati gli scenari seguenti:

- Accessi utente ad applicazioni client legacy di Office ad esclusione di Outlook: Office 2010 e Office 2013 _senza_ autenticazione moderna. Le organizzazioni sono incoraggiate a passare all'autenticazione moderna, se possibile. L'autenticazione moderna consente il supporto dell'autenticazione pass-through. Consente anche di proteggere gli account utente tramite funzionalità di [accesso condizionale](../active-directory-conditional-access-azure-portal.md), ad esempio Azure Multi-Factor Authentication.
- Accessi utente per applicazioni client Skype for Business _senza_ autenticazione moderna.
- Accessi utente a PowerShell versione 1.0. È consigliabile usare PowerShell versione 2.0.
- Rilevamento di utenti con [credenziali perse](../active-directory-reporting-risk-events.md#leaked-credentials).
- Azure AD Domain Services necessita della sincronizzazione dell'hash delle password per l'abilitazione nel tenant. I tenant che usano _solo_ l'autenticazione pass-through non funzionano per gli scenari che richiedono Azure AD Domain Services.
- L'autenticazione pass-through non è integrata con [Azure AD Connect Health](../connect-health/active-directory-aadconnect-health.md).
- L'Apple Device Enrollment Program (Apple DEP) non supporta l'autenticazione moderna.  I dispositivi Apple DEP non possono registrarsi in Intune per i domini che usano l'autenticazione pass-through.

>[!IMPORTANT]
>Come soluzione alternativa _solo_ per gli scenari non supportati, abilitare la sincronizzazione dell'hash delle password nella pagina [Funzionalità facoltative](active-directory-aadconnect-get-started-custom.md#optional-features) della procedura guidata di Azure AD Connect.

>[!NOTE]
Abilitando questa funzionalità è possibile effettuare il failover dell'autenticazione se il funzionamento dell'infrastruttura locale è interrotto. Il failover dall'autenticazione pass-through alla sincronizzazione dell'hash delle password non avviene automaticamente, È necessario cambiare il metodo di accesso manualmente con Azure AD Connect. Se il server che esegue Azure AD Connect smette di funzionare, è necessario chiedere assistenza al supporto tecnico Microsoft per disattivare l'autenticazione pass-through.

## <a name="next-steps"></a>Passaggi successivi
- [Avvio rapido](active-directory-aadconnect-pass-through-authentication-quick-start.md): iniziare a usare l'autenticazione pass-through di Azure AD.
- [Blocco smart](active-directory-aadconnect-pass-through-authentication-smart-lockout.md): apprendere come configurare la funzionalità di blocco smart nel tenant per proteggere gli account utente.
- [Approfondimento tecnico](active-directory-aadconnect-pass-through-authentication-how-it-works.md): comprendere come funziona l'autenticazione pass-through.
- [Domande frequenti](active-directory-aadconnect-pass-through-authentication-faq.md): trovare risposte alle domande frequenti sulla funzionalità di autenticazione pass-through.
- [Risoluzione dei problemi](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): apprendere come risolvere i problemi comuni relativi alla funzionalità di autenticazione pass-through.
- [Approfondimento sulla sicurezza](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md): ottenere informazioni tecniche approfondite sulla funzionalità di autenticazione pass-through.
- [Accesso Single Sign-On facile di Azure AD](active-directory-aadconnect-sso.md): ottenere altre informazioni su questa funzionalità complementare.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): usare il forum di Azure Active Directory per inviare richieste di nuove funzionalità.
